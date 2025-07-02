# Angular Implementation

This guide covers implementing AdUnblock in Angular applications.

## Service-Based Implementation

### Create AdUnblock Service

```typescript
// services/adunblock.service.ts
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';

export interface AdBlockerInfo {
  name: string;
  version?: string;
  type: 'extension' | 'dns' | 'network';
}

@Injectable({
  providedIn: 'root'
})
export class AdUnblockService {
  private isBlockedSubject = new BehaviorSubject<boolean | null>(null);
  private blockerInfoSubject = new BehaviorSubject<AdBlockerInfo | null>(null);
  private isLoadedSubject = new BehaviorSubject<boolean>(false);

  public isBlocked$: Observable<boolean | null> = this.isBlockedSubject.asObservable();
  public blockerInfo$: Observable<AdBlockerInfo | null> = this.blockerInfoSubject.asObservable();
  public isLoaded$: Observable<boolean> = this.isLoadedSubject.asObservable();

  private siteId: string = '';

  constructor() {}

  initialize(siteId: string): void {
    this.siteId = siteId;
    this.loadScript();
  }

  private loadScript(): void {
    const script = document.createElement('script');
    script.src = 'https://cdn.ad-unblock.com/sdk.js';
    script.async = true;
    script.setAttribute('data-site-id', this.siteId);

    script.onload = () => {
      this.isLoadedSubject.next(true);
      this.setupEventListeners();
    };

    script.onerror = () => {
      console.error('Failed to load AdUnblock SDK');
    };

    document.head.appendChild(script);
  }

  private setupEventListeners(): void {
    if (window.AdUnblock) {
      window.AdUnblock.onDetected = (blocker: AdBlockerInfo) => {
        this.isBlockedSubject.next(true);
        this.blockerInfoSubject.next(blocker);
      };

      window.AdUnblock.onNotDetected = () => {
        this.isBlockedSubject.next(false);
        this.blockerInfoSubject.next(null);
      };

      window.AdUnblock.onError = (error: any) => {
        console.error('AdUnblock error:', error);
      };
    }
  }

  trackPageView(page: string): void {
    if (window.AdUnblock) {
      window.AdUnblock.trackPageView(page);
    }
  }

  track(event: string, data?: any): void {
    if (window.AdUnblock) {
      window.AdUnblock.track(event, data);
    }
  }
}
```

### Type Declarations

```typescript
// types/adunblock.d.ts
declare global {
  interface Window {
    AdUnblock: {
      onDetected: (callback: (blocker: any) => void) => void;
      onNotDetected: (callback: () => void) => void;
      onError: (callback: (error: any) => void) => void;
      trackPageView: (page: string) => void;
      track: (event: string, data?: any) => void;
    };
  }
}

export {};
```

### App Module Configuration

```typescript
// app.module.ts
import { NgModule, APP_INITIALIZER } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { environment } from '../environments/environment';

import { AppComponent } from './app.component';
import { AdUnblockService } from './services/adunblock.service';
import { AdBlockerMessageComponent } from './components/adblocker-message/adblocker-message.component';

export function initializeAdUnblock(adUnblockService: AdUnblockService) {
  return () => {
    adUnblockService.initialize(environment.adUnblockSiteId);
  };
}

@NgModule({
  declarations: [
    AppComponent,
    AdBlockerMessageComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [
    {
      provide: APP_INITIALIZER,
      useFactory: initializeAdUnblock,
      deps: [AdUnblockService],
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### Environment Configuration

```typescript
// environments/environment.ts
export const environment = {
  production: false,
  adUnblockSiteId: 'YOUR_SITE_ID'
};

// environments/environment.prod.ts
export const environment = {
  production: true,
  adUnblockSiteId: 'YOUR_PRODUCTION_SITE_ID'
};
```

## Component Implementation

### AdBlocker Message Component

```typescript
// components/adblocker-message/adblocker-message.component.ts
import { Component, OnInit, OnDestroy, Input, Output, EventEmitter } from '@angular/core';
import { Subject } from 'rxjs';
import { takeUntil, combineLatest } from 'rxjs/operators';
import { AdUnblockService } from '../../services/adunblock.service';

@Component({
  selector: 'app-adblocker-message',
  templateUrl: './adblocker-message.component.html',
  styleUrls: ['./adblocker-message.component.css']
})
export class AdBlockerMessageComponent implements OnInit, OnDestroy {
  @Input() title: string = 'Ad Blocker Detected';
  @Input() message: string = 'Please disable your ad blocker to support our site';
  @Input() showWhitelistButton: boolean = true;
  @Input() showPremiumButton: boolean = false;

  @Output() whitelist = new EventEmitter<void>();
  @Output() premium = new EventEmitter<void>();
  @Output() dismiss = new EventEmitter<void>();

  public showModal: boolean = false;
  public isDismissed: boolean = false;

  private destroy$ = new Subject<void>();

  constructor(private adUnblockService: AdUnblockService) {}

  ngOnInit(): void {
    combineLatest([
      this.adUnblockService.isBlocked$,
      this.adUnblockService.isLoaded$
    ]).pipe(
      takeUntil(this.destroy$)
    ).subscribe(([isBlocked, isLoaded]) => {
      this.showModal = Boolean(isBlocked && isLoaded && !this.isDismissed);
    });
  }

  ngOnDestroy(): void {
    this.destroy$.next();
    this.destroy$.complete();
  }

  onWhitelist(): void {
    this.whitelist.emit();
    this.adUnblockService.track('whitelist_clicked');
  }

  onPremium(): void {
    this.premium.emit();
    this.adUnblockService.track('premium_clicked');
  }

  onDismiss(): void {
    this.isDismissed = true;
    this.showModal = false;
    this.dismiss.emit();
    this.adUnblockService.track('message_dismissed');
  }
}
```

### Component Template

```html
<!-- components/adblocker-message/adblocker-message.component.html -->
<div *ngIf="showModal" class="adblock-overlay" (click)="onDismiss()">
  <div class="adblock-modal" (click)="$event.stopPropagation()">
    <h2>{{ title }}</h2>
    <p>{{ message }}</p>
    <ul>
      <li>Add us to your ad blocker's whitelist</li>
      <li>Disable your ad blocker for this site</li>
      <li *ngIf="showPremiumButton">Subscribe to our premium ad-free experience</li>
    </ul>
    <div class="adblock-actions">
      <button 
        *ngIf="showWhitelistButton" 
        (click)="onWhitelist()" 
        class="btn btn-primary">
        Whitelist Site
      </button>
      <button 
        *ngIf="showPremiumButton" 
        (click)="onPremium()" 
        class="btn btn-premium">
        Go Premium
      </button>
      <button 
        (click)="onDismiss()" 
        class="btn btn-secondary">
        Continue Anyway
      </button>
    </div>
  </div>
</div>
```

### Component Styles

```css
/* components/adblocker-message/adblocker-message.component.css */
.adblock-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.8);
  z-index: 10000;
  display: flex;
  align-items: center;
  justify-content: center;
}

.adblock-modal {
  background: white;
  padding: 30px;
  border-radius: 10px;
  max-width: 500px;
  text-align: center;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
}

.adblock-modal h2 {
  color: #333;
  margin-bottom: 15px;
}

.adblock-modal p {
  color: #666;
  margin-bottom: 20px;
}

.adblock-modal ul {
  text-align: left;
  margin-bottom: 25px;
}

.adblock-actions {
  display: flex;
  gap: 10px;
  justify-content: center;
  flex-wrap: wrap;
}

.btn {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 14px;
  transition: opacity 0.2s;
}

.btn:hover {
  opacity: 0.8;
}

.btn-primary { background: #007cba; color: white; }
.btn-premium { background: #28a745; color: white; }
.btn-secondary { background: #6c757d; color: white; }
```

## Usage in App Component

```typescript
// app.component.ts
import { Component } from '@angular/core';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  template: `
    <app-adblocker-message
      [showPremiumButton]="true"
      (whitelist)="handleWhitelist()"
      (premium)="handlePremium()"
      (dismiss)="handleDismiss()">
    </app-adblocker-message>
    
    <router-outlet></router-outlet>
  `
})
export class AppComponent {
  constructor(private router: Router) {}

  handleWhitelist(): void {
    alert('Please add our site to your ad blocker\'s whitelist and refresh the page.');
  }

  handlePremium(): void {
    this.router.navigate(['/premium']);
  }

  handleDismiss(): void {
    console.log('User dismissed the ad blocker message');
  }
}
```

## Router Integration

### Route Tracking

```typescript
// app.component.ts
import { Component, OnInit } from '@angular/core';
import { Router, NavigationEnd } from '@angular/router';
import { filter } from 'rxjs/operators';
import { AdUnblockService } from './services/adunblock.service';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html'
})
export class AppComponent implements OnInit {
  constructor(
    private router: Router,
    private adUnblockService: AdUnblockService
  ) {}

  ngOnInit(): void {
    this.router.events.pipe(
      filter(event => event instanceof NavigationEnd)
    ).subscribe((event: NavigationEnd) => {
      this.adUnblockService.trackPageView(event.urlAfterRedirects);
    });
  }
}
```

### Route Guard

```typescript
// guards/adunblock.guard.ts
import { Injectable } from '@angular/core';
import { CanActivate, Router } from '@angular/router';
import { Observable } from 'rxjs';
import { map, filter, take } from 'rxjs/operators';
import { AdUnblockService } from '../services/adunblock.service';

@Injectable({
  providedIn: 'root'
})
export class AdUnblockGuard implements CanActivate {
  constructor(
    private adUnblockService: AdUnblockService,
    private router: Router
  ) {}

  canActivate(): Observable<boolean> {
    return this.adUnblockService.isBlocked$.pipe(
      filter(isBlocked => isBlocked !== null),
      take(1),
      map(isBlocked => {
        if (isBlocked) {
          this.router.navigate(['/adblock-detected']);
          return false;
        }
        return true;
      })
    );
  }
}
```

## Testing

### Service Tests

```typescript
// services/adunblock.service.spec.ts
import { TestBed } from '@angular/core/testing';
import { AdUnblockService } from './adunblock.service';

describe('AdUnblockService', () => {
  let service: AdUnblockService;

  beforeEach(() => {
    TestBed.configureTestingModule({});
    service = TestBed.inject(AdUnblockService);

    // Mock window.AdUnblock
    (window as any).AdUnblock = {
      onDetected: jasmine.createSpy('onDetected'),
      onNotDetected: jasmine.createSpy('onNotDetected'),
      onError: jasmine.createSpy('onError'),
      trackPageView: jasmine.createSpy('trackPageView'),
      track: jasmine.createSpy('track')
    };
  });

  it('should be created', () => {
    expect(service).toBeTruthy();
  });

  it('should initialize with site ID', () => {
    service.initialize('test-site-id');
    expect(service).toBeTruthy();
  });

  it('should track page views', () => {
    service.trackPageView('/test-page');
    expect((window as any).AdUnblock.trackPageView).toHaveBeenCalledWith('/test-page');
  });
});
```

### Component Tests

```typescript
// components/adblocker-message/adblocker-message.component.spec.ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { of } from 'rxjs';
import { AdBlockerMessageComponent } from './adblocker-message.component';
import { AdUnblockService } from '../../services/adunblock.service';

describe('AdBlockerMessageComponent', () => {
  let component: AdBlockerMessageComponent;
  let fixture: ComponentFixture<AdBlockerMessageComponent>;
  let mockAdUnblockService: jasmine.SpyObj<AdUnblockService>;

  beforeEach(async () => {
    const spy = jasmine.createSpyObj('AdUnblockService', ['track'], {
      isBlocked$: of(true),
      isLoaded$: of(true),
      blockerInfo$: of(null)
    });

    await TestBed.configureTestingModule({
      declarations: [AdBlockerMessageComponent],
      providers: [
        { provide: AdUnblockService, useValue: spy }
      ]
    }).compileComponents();

    mockAdUnblockService = TestBed.inject(AdUnblockService) as jasmine.SpyObj<AdUnblockService>;
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(AdBlockerMessageComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should show modal when ad blocker is detected', () => {
    expect(component.showModal).toBe(true);
  });

  it('should emit whitelist event when whitelist button is clicked', () => {
    spyOn(component.whitelist, 'emit');
    component.onWhitelist();
    expect(component.whitelist.emit).toHaveBeenCalled();
  });
});
```

## Advanced Features

### Interceptor for API Calls

```typescript
// interceptors/adunblock.interceptor.ts
import { Injectable } from '@angular/core';
import { HttpInterceptor, HttpRequest, HttpHandler } from '@angular/common/http';
import { AdUnblockService } from '../services/adunblock.service';

@Injectable()
export class AdUnblockInterceptor implements HttpInterceptor {
  constructor(private adUnblockService: AdUnblockService) {}

  intercept(req: HttpRequest<any>, next: HttpHandler) {
    // Add AdUnblock headers to API requests
    const modifiedReq = req.clone({
      setHeaders: {
        'X-AdUnblock-Enabled': 'true'
      }
    });

    return next.handle(modifiedReq);
  }
}
```

### Directive for Ad Slots

```typescript
// directives/ad-slot.directive.ts
import { Directive, ElementRef, OnInit, OnDestroy } from '@angular/core';
import { Subject } from 'rxjs';
import { takeUntil } from 'rxjs/operators';
import { AdUnblockService } from '../services/adunblock.service';

@Directive({
  selector: '[appAdSlot]'
})
export class AdSlotDirective implements OnInit, OnDestroy {
  private destroy$ = new Subject<void>();

  constructor(
    private el: ElementRef,
    private adUnblockService: AdUnblockService
  ) {}

  ngOnInit(): void {
    this.adUnblockService.isBlocked$.pipe(
      takeUntil(this.destroy$)
    ).subscribe(isBlocked => {
      if (isBlocked) {
        this.showAlternativeContent();
      } else {
        this.showAds();
      }
    });
  }

  ngOnDestroy(): void {
    this.destroy$.next();
    this.destroy$.complete();
  }

  private showAlternativeContent(): void {
    this.el.nativeElement.innerHTML = `
      <div class="alternative-content">
        <p>Support our site by disabling your ad blocker</p>
      </div>
    `;
  }

  private showAds(): void {
    // Load your ad content here
    this.el.nativeElement.innerHTML = `
      <div class="ad-content">
        <!-- Your ad code here -->
      </div>
    `;
  }
}
```

## Best Practices

### 1. Performance
- Use OnPush change detection for AdBlocker components
- Implement proper unsubscription to prevent memory leaks
- Lazy load AdBlocker-related modules when possible

### 2. User Experience
- Provide clear feedback during AdUnblock initialization
- Use Angular animations for smoother transitions
- Implement proper error handling and fallbacks

### 3. Testing
- Mock AdUnblock service in tests
- Test component behavior with different AdUnblock states
- Use Angular testing utilities for proper component testing

## Troubleshooting

### Common Issues

**Service not initializing:**
- Check APP_INITIALIZER configuration
- Verify environment variables are set correctly
- Ensure script loading doesn't fail

**Observables not updating:**
- Check proper subscription handling
- Verify takeUntil pattern for cleanup
- Ensure BehaviorSubjects are used correctly

**Tests failing:**
- Mock window.AdUnblock properly
- Use TestBed for service injection
- Check async operations in tests

## Next Steps

After implementing AdUnblock in Angular:

1. **Integrate with Angular Universal** for SSR compatibility
2. **Add analytics tracking** for better insights
3. **Implement A/B testing** for different strategies
4. **Create reusable modules** for different projects
5. **Monitor performance** and optimize as needed

For more advanced features, check out our [API Reference](../api-reference/rest-api.md) guide. 