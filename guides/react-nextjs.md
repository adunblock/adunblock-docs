# React/Next.js Implementation

This guide covers implementing AdUnblock in React applications and Next.js projects.

## React Implementation

### Basic Setup

#### Install via npm (if package is available)

```bash
npm install @adunblock/react
```

#### Manual Implementation

If using the CDN version, create a React hook:

```jsx
// hooks/useAdUnblock.js
import { useEffect, useState } from 'react';

const useAdUnblock = (siteId, options = {}) => {
  const [isBlocked, setIsBlocked] = useState(null);
  const [blockerInfo, setBlockerInfo] = useState(null);
  const [isLoaded, setIsLoaded] = useState(false);

  useEffect(() => {
    const script = document.createElement('script');
    script.src = 'https://cdn.ad-unblock.com/sdk.js';
    script.async = true;
    script.setAttribute('data-site-id', siteId);
    
    // Add configuration attributes
    Object.keys(options).forEach(key => {
      script.setAttribute(`data-${key}`, options[key]);
    });

    script.onload = () => {
      setIsLoaded(true);
      
      if (window.AdUnblock) {
        window.AdUnblock.onDetected = (blocker) => {
          setIsBlocked(true);
          setBlockerInfo(blocker);
        };
        
        window.AdUnblock.onNotDetected = () => {
          setIsBlocked(false);
          setBlockerInfo(null);
        };
        
        window.AdUnblock.onError = (error) => {
          console.error('AdUnblock error:', error);
        };
      }
    };

    document.head.appendChild(script);

    return () => {
      if (document.head.contains(script)) {
        document.head.removeChild(script);
      }
    };
  }, [siteId, options]);

  return { isBlocked, blockerInfo, isLoaded };
};

export default useAdUnblock;
```

### Component Implementation

#### AdUnblock Provider Component

```jsx
// components/AdUnblockProvider.jsx
import React, { createContext, useContext } from 'react';
import useAdUnblock from '../hooks/useAdUnblock';

const AdUnblockContext = createContext();

export const useAdUnblockContext = () => {
  const context = useContext(AdUnblockContext);
  if (!context) {
    throw new Error('useAdUnblockContext must be used within AdUnblockProvider');
  }
  return context;
};

const AdUnblockProvider = ({ children, siteId, options = {} }) => {
  const adUnblockData = useAdUnblock(siteId, options);

  return (
    <AdUnblockContext.Provider value={adUnblockData}>
      {children}
    </AdUnblockContext.Provider>
  );
};

export default AdUnblockProvider;
```

#### AdBlocker Message Component

```jsx
// components/AdBlockerMessage.jsx
import React, { useState } from 'react';
import { useAdUnblockContext } from './AdUnblockProvider';
import './AdBlockerMessage.css';

const AdBlockerMessage = ({ 
  title = "Ad Blocker Detected",
  message = "Please disable your ad blocker to support our site",
  showWhitelistButton = true,
  showPremiumButton = false,
  onWhitelist,
  onPremium,
  onDismiss
}) => {
  const { isBlocked, isLoaded } = useAdUnblockContext();
  const [isDismissed, setIsDismissed] = useState(false);

  const handleDismiss = () => {
    setIsDismissed(true);
    if (onDismiss) onDismiss();
  };

  const handleWhitelist = () => {
    if (onWhitelist) onWhitelist();
  };

  const handlePremium = () => {
    if (onPremium) onPremium();
  };

  if (!isLoaded || !isBlocked || isDismissed) {
    return null;
  }

  return (
    <div className="adblock-overlay">
      <div className="adblock-modal">
        <h2>{title}</h2>
        <p>{message}</p>
        <ul>
          <li>Add us to your ad blocker's whitelist</li>
          <li>Disable your ad blocker for this site</li>
          {showPremiumButton && <li>Subscribe to our premium ad-free experience</li>}
        </ul>
        <div className="adblock-actions">
          {showWhitelistButton && (
            <button onClick={handleWhitelist} className="btn-primary">
              Whitelist Site
            </button>
          )}
          {showPremiumButton && (
            <button onClick={handlePremium} className="btn-premium">
              Go Premium
            </button>
          )}
          <button onClick={handleDismiss} className="btn-secondary">
            Continue Anyway
          </button>
        </div>
      </div>
    </div>
  );
};

export default AdBlockerMessage;
```

#### CSS for AdBlocker Message

```css
/* components/AdBlockerMessage.css */
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

.adblock-actions button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 14px;
  transition: opacity 0.2s;
}

.adblock-actions button:hover {
  opacity: 0.8;
}

.btn-primary {
  background: #007cba;
  color: white;
}

.btn-premium {
  background: #28a745;
  color: white;
}

.btn-secondary {
  background: #6c757d;
  color: white;
}
```

### Usage in React App

```jsx
// App.jsx
import React from 'react';
import AdUnblockProvider from './components/AdUnblockProvider';
import AdBlockerMessage from './components/AdBlockerMessage';
import Header from './components/Header';
import Content from './components/Content';

function App() {
  const handleWhitelist = () => {
    alert('Please add our site to your ad blocker\'s whitelist and refresh the page.');
  };

  const handlePremium = () => {
    window.location.href = '/premium';
  };

  return (
    <AdUnblockProvider siteId="YOUR_SITE_ID">
      <div className="App">
        <AdBlockerMessage 
          onWhitelist={handleWhitelist}
          onPremium={handlePremium}
          showPremiumButton={true}
        />
        <Header />
        <Content />
      </div>
    </AdUnblockProvider>
  );
}

export default App;
```

## Next.js Implementation

### App Router (Next.js 13+)

#### Create AdUnblock Component

```jsx
// components/AdUnblock.jsx
'use client';

import { useEffect, useState } from 'react';
import { usePathname } from 'next/navigation';

const AdUnblock = ({ siteId, children }) => {
  const [isBlocked, setIsBlocked] = useState(null);
  const [isLoaded, setIsLoaded] = useState(false);
  const pathname = usePathname();

  useEffect(() => {
    // Only load on client side
    if (typeof window === 'undefined') return;

    const script = document.createElement('script');
    script.src = 'https://cdn.ad-unblock.com/sdk.js';
    script.async = true;
    script.setAttribute('data-site-id', siteId);

    script.onload = () => {
      setIsLoaded(true);
      
      if (window.AdUnblock) {
        window.AdUnblock.onDetected = () => {
          setIsBlocked(true);
        };
        
        window.AdUnblock.onNotDetected = () => {
          setIsBlocked(false);
        };
      }
    };

    document.head.appendChild(script);

    return () => {
      if (document.head.contains(script)) {
        document.head.removeChild(script);
      }
    };
  }, [siteId]);

  // Track page views
  useEffect(() => {
    if (isLoaded && window.AdUnblock) {
      window.AdUnblock.trackPageView(pathname);
    }
  }, [pathname, isLoaded]);

  return children({ isBlocked, isLoaded });
};

export default AdUnblock;
```

#### Layout Integration

```jsx
// app/layout.js
import AdUnblock from '../components/AdUnblock';
import AdBlockerBanner from '../components/AdBlockerBanner';

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <AdUnblock siteId={process.env.NEXT_PUBLIC_ADUNBLOCK_SITE_ID}>
          {({ isBlocked, isLoaded }) => (
            <>
              {isBlocked && <AdBlockerBanner />}
              {children}
            </>
          )}
        </AdUnblock>
      </body>
    </html>
  );
}
```

### Pages Router (Next.js 12 and below)

#### Custom Document

```jsx
// pages/_document.js
import Document, { Html, Head, Main, NextScript } from 'next/document';

class MyDocument extends Document {
  render() {
    return (
      <Html>
        <Head>
          <script 
            src="https://cdn.ad-unblock.com/sdk.js" 
            data-site-id={process.env.NEXT_PUBLIC_ADUNBLOCK_SITE_ID}
            async
          />
        </Head>
        <body>
          <Main />
          <NextScript />
        </body>
      </Html>
    );
  }
}

export default MyDocument;
```

#### Custom App

```jsx
// pages/_app.js
import { useEffect, useState } from 'react';
import { useRouter } from 'next/router';
import AdBlockerMessage from '../components/AdBlockerMessage';

function MyApp({ Component, pageProps }) {
  const [isBlocked, setIsBlocked] = useState(null);
  const router = useRouter();

  useEffect(() => {
    const initAdUnblock = () => {
      if (window.AdUnblock) {
        window.AdUnblock.onDetected = () => setIsBlocked(true);
        window.AdUnblock.onNotDetected = () => setIsBlocked(false);
      } else {
        // Retry if AdUnblock hasn't loaded yet
        setTimeout(initAdUnblock, 100);
      }
    };

    initAdUnblock();
  }, []);

  // Track route changes
  useEffect(() => {
    const handleRouteChange = (url) => {
      if (window.AdUnblock) {
        window.AdUnblock.trackPageView(url);
      }
    };

    router.events.on('routeChangeComplete', handleRouteChange);
    return () => {
      router.events.off('routeChangeComplete', handleRouteChange);
    };
  }, [router.events]);

  return (
    <>
      {isBlocked && <AdBlockerMessage />}
      <Component {...pageProps} />
    </>
  );
}

export default MyApp;
```

### Environment Configuration

Create `.env.local`:

```env
NEXT_PUBLIC_ADUNBLOCK_SITE_ID=your_site_id_here
```

### SSR Considerations

#### Server-Side Safe Component

```jsx
// components/ClientOnlyAdUnblock.jsx
import { useEffect, useState } from 'react';

const ClientOnlyAdUnblock = ({ children }) => {
  const [hasMounted, setHasMounted] = useState(false);

  useEffect(() => {
    setHasMounted(true);
  }, []);

  if (!hasMounted) {
    return null;
  }

  return children;
};

export default ClientOnlyAdUnblock;
```

Usage:
```jsx
import ClientOnlyAdUnblock from './ClientOnlyAdUnblock';
import AdBlockerMessage from './AdBlockerMessage';

const Layout = ({ children }) => {
  return (
    <div>
      <ClientOnlyAdUnblock>
        <AdBlockerMessage />
      </ClientOnlyAdUnblock>
      {children}
    </div>
  );
};
```

### Advanced Next.js Features

#### API Route Integration

```jsx
// pages/api/adblock-detected.js
export default function handler(req, res) {
  if (req.method === 'POST') {
    const { blocker, page, timestamp } = req.body;
    
    // Log to your analytics service
    console.log('AdBlock detected:', { blocker, page, timestamp });
    
    // Store in database if needed
    // await db.collection('adblock_detections').add({
    //   blocker,
    //   page,
    //   timestamp,
    //   ip: req.headers['x-forwarded-for'] || req.connection.remoteAddress
    // });
    
    res.status(200).json({ success: true });
  } else {
    res.status(405).json({ error: 'Method Not Allowed' });
  }
}
```

#### Middleware Integration

```jsx
// middleware.js
import { NextResponse } from 'next/server';

export function middleware(request) {
  // Add custom headers for AdUnblock
  const response = NextResponse.next();
  
  response.headers.set('X-AdUnblock-Enabled', 'true');
  response.headers.set('X-Site-ID', process.env.NEXT_PUBLIC_ADUNBLOCK_SITE_ID);
  
  return response;
}

export const config = {
  matcher: [
    '/((?!api|_next/static|_next/image|favicon.ico).*)',
  ],
};
```

### TypeScript Support

#### Type Definitions

```typescript
// types/adunblock.d.ts
declare global {
  interface Window {
    AdUnblock: {
      onDetected: (callback: (blocker: AdBlockerInfo) => void) => void;
      onNotDetected: (callback: () => void) => void;
      onError: (callback: (error: Error) => void) => void;
      trackPageView: (page: string) => void;
      track: (event: string, data?: any) => void;
    };
  }
}

export interface AdBlockerInfo {
  name: string;
  version?: string;
  type: 'extension' | 'dns' | 'network';
}

export interface UseAdUnblockReturn {
  isBlocked: boolean | null;
  blockerInfo: AdBlockerInfo | null;
  isLoaded: boolean;
}
```

#### Typed Hook

```typescript
// hooks/useAdUnblock.ts
import { useEffect, useState } from 'react';
import { AdBlockerInfo, UseAdUnblockReturn } from '../types/adunblock';

const useAdUnblock = (siteId: string): UseAdUnblockReturn => {
  const [isBlocked, setIsBlocked] = useState<boolean | null>(null);
  const [blockerInfo, setBlockerInfo] = useState<AdBlockerInfo | null>(null);
  const [isLoaded, setIsLoaded] = useState<boolean>(false);

  useEffect(() => {
    const script = document.createElement('script');
    script.src = 'https://cdn.ad-unblock.com/sdk.js';
    script.async = true;
    script.setAttribute('data-site-id', siteId);

    script.onload = () => {
      setIsLoaded(true);
      
      if (window.AdUnblock) {
        window.AdUnblock.onDetected = (blocker: AdBlockerInfo) => {
          setIsBlocked(true);
          setBlockerInfo(blocker);
        };
        
        window.AdUnblock.onNotDetected = () => {
          setIsBlocked(false);
          setBlockerInfo(null);
        };
      }
    };

    document.head.appendChild(script);

    return () => {
      if (document.head.contains(script)) {
        document.head.removeChild(script);
      }
    };
  }, [siteId]);

  return { isBlocked, blockerInfo, isLoaded };
};

export default useAdUnblock;
```

### Testing

#### Jest Configuration

```javascript
// jest.config.js
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
  moduleNameMapping: {
    '^@/(.*)$': '<rootDir>/$1',
  },
};
```

#### Test Setup

```javascript
// jest.setup.js
// Mock AdUnblock for testing
Object.defineProperty(window, 'AdUnblock', {
  value: {
    onDetected: jest.fn(),
    onNotDetected: jest.fn(),
    onError: jest.fn(),
    trackPageView: jest.fn(),
    track: jest.fn(),
  },
  writable: true,
});
```

#### Component Tests

```jsx
// __tests__/AdBlockerMessage.test.jsx
import { render, screen, fireEvent } from '@testing-library/react';
import AdBlockerMessage from '../components/AdBlockerMessage';
import { useAdUnblockContext } from '../components/AdUnblockProvider';

// Mock the context
jest.mock('../components/AdUnblockProvider');

describe('AdBlockerMessage', () => {
  beforeEach(() => {
    useAdUnblockContext.mockReturnValue({
      isBlocked: true,
      isLoaded: true,
      blockerInfo: { name: 'uBlock Origin' }
    });
  });

  test('renders when ad blocker is detected', () => {
    render(<AdBlockerMessage />);
    expect(screen.getByText('Ad Blocker Detected')).toBeInTheDocument();
  });

  test('calls onDismiss when dismiss button is clicked', () => {
    const onDismiss = jest.fn();
    render(<AdBlockerMessage onDismiss={onDismiss} />);
    
    fireEvent.click(screen.getByText('Continue Anyway'));
    expect(onDismiss).toHaveBeenCalled();
  });
});
```

### Performance Optimization

#### Code Splitting

```jsx
// components/LazyAdBlockerMessage.jsx
import { lazy, Suspense } from 'react';

const AdBlockerMessage = lazy(() => import('./AdBlockerMessage'));

const LazyAdBlockerMessage = (props) => {
  return (
    <Suspense fallback={null}>
      <AdBlockerMessage {...props} />
    </Suspense>
  );
};

export default LazyAdBlockerMessage;
```

#### Conditional Loading

```jsx
// hooks/useConditionalAdUnblock.js
import { useEffect, useState } from 'react';
import { useRouter } from 'next/router';

const useConditionalAdUnblock = (siteId, shouldLoad = true) => {
  const [isLoaded, setIsLoaded] = useState(false);
  const router = useRouter();
  
  useEffect(() => {
    // Only load on specific pages
    const protectedRoutes = ['/articles', '/blog', '/news'];
    const shouldLoadForRoute = protectedRoutes.some(route => 
      router.pathname.startsWith(route)
    );
    
    if (!shouldLoad || !shouldLoadForRoute) return;
    
    const script = document.createElement('script');
    script.src = 'https://cdn.ad-unblock.com/sdk.js';
    script.async = true;
    script.setAttribute('data-site-id', siteId);
    
    script.onload = () => setIsLoaded(true);
    document.head.appendChild(script);
    
    return () => {
      if (document.head.contains(script)) {
        document.head.removeChild(script);
      }
    };
  }, [siteId, shouldLoad, router.pathname]);
  
  return isLoaded;
};
```

## Best Practices for React/Next.js

### 1. Performance
- Load AdUnblock asynchronously
- Use code splitting for AdBlocker components
- Implement conditional loading based on routes

### 2. User Experience
- Show loading states while AdUnblock initializes
- Provide graceful fallbacks for detection failures
- Make dismissal options easily accessible

### 3. SEO Considerations
- Ensure AdUnblock doesn't block server-side rendering
- Use proper meta tags and structured data
- Avoid layout shifts from AdBlocker messages

### 4. Accessibility
- Ensure proper focus management for modals
- Use semantic HTML and ARIA attributes
- Provide keyboard navigation support

## Troubleshooting

### Common Issues

**AdUnblock not loading in Next.js:**
- Ensure script is loaded client-side only
- Check environment variables are properly set
- Verify CSP headers allow the AdUnblock domain

**State not updating:**
- Make sure event listeners are set up after SDK loads
- Check for memory leaks in useEffect cleanup
- Verify component re-renders are handled correctly

**TypeScript errors:**
- Ensure type definitions are properly imported
- Check window object extensions are declared
- Verify all props have proper type annotations

## Next Steps

After implementing AdUnblock in your React/Next.js application:

1. **Test thoroughly** across different browsers and devices
2. **Monitor performance** impact on your application
3. **Track user interactions** with AdBlocker messages
4. **A/B test** different message strategies
5. **Integrate with analytics** platforms for better insights

For more advanced features, check out our [API Reference](../api-reference/rest-api.md) guide. 