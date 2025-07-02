# Vue/Nuxt.js Implementation

This guide covers implementing AdUnblock in Vue.js applications and Nuxt.js projects.

## Vue.js Implementation

### Installation

#### Option 1: CDN Integration

Create a Vue plugin for AdUnblock:

```javascript
// plugins/adunblock.js
export default {
  install(app, options) {
    const { siteId } = options;
    
    // Load AdUnblock SDK
    const script = document.createElement('script');
    script.src = 'https://cdn.ad-unblock.com/sdk.js';
    script.async = true;
    script.setAttribute('data-site-id', siteId);
    document.head.appendChild(script);
    
    // Create reactive state
    const adUnblockState = Vue.reactive({
      isBlocked: null,
      blockerInfo: null,
      isLoaded: false
    });
    
    script.onload = () => {
      adUnblockState.isLoaded = true;
      
      if (window.AdUnblock) {
        window.AdUnblock.onDetected = (blocker) => {
          adUnblockState.isBlocked = true;
          adUnblockState.blockerInfo = blocker;
        };
        
        window.AdUnblock.onNotDetected = () => {
          adUnblockState.isBlocked = false;
          adUnblockState.blockerInfo = null;
        };
      }
    };
    
    app.config.globalProperties.$adUnblock = adUnblockState;
    app.provide('adUnblock', adUnblockState);
  }
};
```

#### Register the Plugin

```javascript
// main.js
import { createApp } from 'vue';
import App from './App.vue';
import AdUnblockPlugin from './plugins/adunblock';

const app = createApp(App);

app.use(AdUnblockPlugin, {
  siteId: 'YOUR_SITE_ID'
});

app.mount('#app');
```

### Composable Approach (Vue 3)

Create a composable for better reactivity:

```javascript
// composables/useAdUnblock.js
import { ref, onMounted, onUnmounted } from 'vue';

export function useAdUnblock(siteId) {
  const isBlocked = ref(null);
  const blockerInfo = ref(null);
  const isLoaded = ref(false);
  
  let script = null;
  
  const loadAdUnblock = () => {
    script = document.createElement('script');
    script.src = 'https://cdn.ad-unblock.com/sdk.js';
    script.async = true;
    script.setAttribute('data-site-id', siteId);
    
    script.onload = () => {
      isLoaded.value = true;
      
      if (window.AdUnblock) {
        window.AdUnblock.onDetected = (blocker) => {
          isBlocked.value = true;
          blockerInfo.value = blocker;
        };
        
        window.AdUnblock.onNotDetected = () => {
          isBlocked.value = false;
          blockerInfo.value = null;
        };
      }
    };
    
    document.head.appendChild(script);
  };
  
  onMounted(() => {
    loadAdUnblock();
  });
  
  onUnmounted(() => {
    if (script && document.head.contains(script)) {
      document.head.removeChild(script);
    }
  });
  
  return {
    isBlocked,
    blockerInfo,
    isLoaded
  };
}
```

### Component Examples

#### AdBlocker Message Component

```vue
<!-- components/AdBlockerMessage.vue -->
<template>
  <div v-if="isBlocked && isLoaded && !isDismissed" class="adblock-overlay">
    <div class="adblock-modal">
      <h2>{{ title }}</h2>
      <p>{{ message }}</p>
      <ul>
        <li>Add us to your ad blocker's whitelist</li>
        <li>Disable your ad blocker for this site</li>
        <li v-if="showPremiumButton">Subscribe to our premium experience</li>
      </ul>
      <div class="adblock-actions">
        <button v-if="showWhitelistButton" @click="handleWhitelist" class="btn-primary">
          Whitelist Site
        </button>
        <button v-if="showPremiumButton" @click="handlePremium" class="btn-premium">
          Go Premium
        </button>
        <button @click="handleDismiss" class="btn-secondary">
          Continue Anyway
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';
import { useAdUnblock } from '../composables/useAdUnblock';

export default {
  name: 'AdBlockerMessage',
  props: {
    siteId: {
      type: String,
      required: true
    },
    title: {
      type: String,
      default: 'Ad Blocker Detected'
    },
    message: {
      type: String,
      default: 'Please disable your ad blocker to support our site'
    },
    showWhitelistButton: {
      type: Boolean,
      default: true
    },
    showPremiumButton: {
      type: Boolean,
      default: false
    }
  },
  emits: ['whitelist', 'premium', 'dismiss'],
  setup(props, { emit }) {
    const { isBlocked, isLoaded } = useAdUnblock(props.siteId);
    const isDismissed = ref(false);
    
    const handleWhitelist = () => {
      emit('whitelist');
    };
    
    const handlePremium = () => {
      emit('premium');
    };
    
    const handleDismiss = () => {
      isDismissed.value = true;
      emit('dismiss');
    };
    
    return {
      isBlocked,
      isLoaded,
      isDismissed,
      handleWhitelist,
      handlePremium,
      handleDismiss
    };
  }
};
</script>

<style scoped>
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
}

.btn-primary { background: #007cba; color: white; }
.btn-premium { background: #28a745; color: white; }
.btn-secondary { background: #6c757d; color: white; }
</style>
```

## Nuxt.js Implementation

### Nuxt 3 Setup

#### Create a Plugin

```javascript
// plugins/adunblock.client.js
export default defineNuxtPlugin(() => {
  const config = useRuntimeConfig();
  
  const adUnblockState = reactive({
    isBlocked: null,
    blockerInfo: null,
    isLoaded: false
  });
  
  const loadAdUnblock = () => {
    const script = document.createElement('script');
    script.src = 'https://cdn.ad-unblock.com/sdk.js';
    script.async = true;
    script.setAttribute('data-site-id', config.public.adUnblockSiteId);
    
    script.onload = () => {
      adUnblockState.isLoaded = true;
      
      if (window.AdUnblock) {
        window.AdUnblock.onDetected = (blocker) => {
          adUnblockState.isBlocked = true;
          adUnblockState.blockerInfo = blocker;
        };
        
        window.AdUnblock.onNotDetected = () => {
          adUnblockState.isBlocked = false;
          adUnblockState.blockerInfo = null;
        };
      }
    };
    
    document.head.appendChild(script);
  };
  
  onMounted(() => {
    loadAdUnblock();
  });
  
  return {
    provide: {
      adUnblock: adUnblockState
    }
  };
});
```

#### Configuration

```javascript
// nuxt.config.ts
export default defineNuxtConfig({
  runtimeConfig: {
    public: {
      adUnblockSiteId: process.env.ADUNBLOCK_SITE_ID || 'YOUR_SITE_ID'
    }
  }
});
```

#### Environment Variables

```env
# .env
ADUNBLOCK_SITE_ID=your_site_id_here
```

### Nuxt 2 Setup

#### Plugin Implementation

```javascript
// plugins/adunblock.client.js
export default function ({ $config }, inject) {
  const adUnblockState = Vue.observable({
    isBlocked: null,
    blockerInfo: null,
    isLoaded: false
  });
  
  const script = document.createElement('script');
  script.src = 'https://cdn.ad-unblock.com/sdk.js';
  script.async = true;
  script.setAttribute('data-site-id', $config.adUnblockSiteId);
  
  script.onload = () => {
    adUnblockState.isLoaded = true;
    
    if (window.AdUnblock) {
      window.AdUnblock.onDetected = (blocker) => {
        adUnblockState.isBlocked = true;
        adUnblockState.blockerInfo = blocker;
      };
      
      window.AdUnblock.onNotDetected = () => {
        adUnblockState.isBlocked = false;
        adUnblockState.blockerInfo = null;
      };
    }
  };
  
  document.head.appendChild(script);
  
  inject('adUnblock', adUnblockState);
}
```

#### Configuration

```javascript
// nuxt.config.js
export default {
  plugins: [
    { src: '~/plugins/adunblock.client.js', mode: 'client' }
  ],
  publicRuntimeConfig: {
    adUnblockSiteId: process.env.ADUNBLOCK_SITE_ID || 'YOUR_SITE_ID'
  }
};
```

### Usage in Components

#### Basic Usage

```vue
<template>
  <div>
    <AdBlockerMessage 
      v-if="$adUnblock.isBlocked"
      @whitelist="handleWhitelist"
      @premium="handlePremium"
    />
    <main>
      <!-- Your content -->
    </main>
  </div>
</template>

<script>
export default {
  methods: {
    handleWhitelist() {
      alert('Please whitelist our site');
    },
    handlePremium() {
      this.$router.push('/premium');
    }
  }
};
</script>
```

#### With Composables (Nuxt 3)

```vue
<template>
  <div>
    <AdBlockerBanner v-if="adUnblock.isBlocked" />
    <NuxtPage />
  </div>
</template>

<script setup>
const { $adUnblock: adUnblock } = useNuxtApp();
</script>
```

### Advanced Features

#### Page-Specific Loading

```javascript
// composables/usePageAdUnblock.js
export const usePageAdUnblock = (shouldLoad = true) => {
  const route = useRoute();
  const { $adUnblock } = useNuxtApp();
  
  watch(() => route.path, (newPath) => {
    if (shouldLoad && window.AdUnblock) {
      window.AdUnblock.trackPageView(newPath);
    }
  }, { immediate: true });
  
  return $adUnblock;
};
```

#### Middleware Integration

```javascript
// middleware/adunblock.js
export default function ({ route, redirect, $adUnblock }) {
  // Redirect premium users if they have ad blockers
  if (route.path.startsWith('/premium') && $adUnblock.isBlocked) {
    return redirect('/premium/adblock-detected');
  }
}
```

## Testing

### Jest Configuration

```javascript
// jest.config.js
module.exports = {
  moduleNameMapping: {
    '^@/(.*)$': '<rootDir>/$1',
    '^~/(.*)$': '<rootDir>/$1'
  },
  transform: {
    '^.+\\.vue$': '@vue/vue3-jest'
  },
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/tests/setup.js']
};
```

### Test Setup

```javascript
// tests/setup.js
global.window.AdUnblock = {
  onDetected: jest.fn(),
  onNotDetected: jest.fn(),
  onError: jest.fn(),
  trackPageView: jest.fn()
};
```

### Component Tests

```javascript
// tests/AdBlockerMessage.spec.js
import { mount } from '@vue/test-utils';
import AdBlockerMessage from '@/components/AdBlockerMessage.vue';

describe('AdBlockerMessage', () => {
  test('renders when ad blocker is detected', async () => {
    const wrapper = mount(AdBlockerMessage, {
      props: {
        siteId: 'test-site-id'
      }
    });
    
    // Simulate ad blocker detection
    await wrapper.setData({ isBlocked: true, isLoaded: true });
    
    expect(wrapper.find('.adblock-modal').exists()).toBe(true);
  });
});
```

## Best Practices

### 1. Performance
- Use client-side only plugins to avoid SSR issues
- Implement lazy loading for AdBlocker components
- Cache AdUnblock state properly

### 2. User Experience
- Provide clear dismissal options
- Avoid blocking critical content
- Use non-intrusive banner styles when possible

### 3. SEO Considerations
- Ensure AdUnblock doesn't affect server-side rendering
- Use proper meta tags for pages with AdUnblock
- Avoid content layout shifts

## Troubleshooting

### Common Issues

**Plugin not loading:**
- Ensure plugin is marked as client-side only
- Check environment variables are properly configured
- Verify script URL accessibility

**State not reactive:**
- Use Vue.observable() in Vue 2 or reactive() in Vue 3
- Ensure proper cleanup in component lifecycle

**SSR/Hydration issues:**
- Always use client-side plugins for AdUnblock
- Implement proper hydration handling
- Use process.client checks when needed

## Next Steps

After implementing AdUnblock in Vue/Nuxt.js:

1. **Test across different browsers** and ad blockers
2. **Monitor performance impact** on your application
3. **Integrate with analytics** for tracking
4. **Customize messages** based on user behavior
5. **Implement A/B testing** for different strategies

For more advanced features, check out our [API Reference](../api-reference/rest-api.md) guide. 