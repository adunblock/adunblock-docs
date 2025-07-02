# WordPress Implementation

This guide covers implementing AdUnblock in WordPress websites using both plugin and custom code methods.

## Method 1: WordPress Plugin (Recommended)

### Installation

1. **From WordPress Admin Dashboard:**
   - Go to `Plugins` → `Add New`
   - Search for "AdUnblock"
   - Click "Install Now" then "Activate"

2. **Manual Installation:**
   - Download the plugin from [WordPress.org](https://wordpress.org/plugins/adunblock)
   - Upload to `/wp-content/plugins/` directory
   - Activate through the WordPress admin

### Configuration

1. **Navigate to Settings:**
   - Go to `Settings` → `AdUnblock` in your WordPress admin

2. **Enter Your Site ID:**
   - Paste your site ID from the AdUnblock dashboard
   - Save settings

3. **Configure Options:**
   - Choose pages where AdUnblock should be active
   - Configure custom messages
   - Set up analytics tracking

### Plugin Features

#### Basic Settings
- **Site ID**: Your unique AdUnblock identifier
- **Auto-Detection**: Automatically detect ad blockers
- **Custom Messages**: Configure what users see
- **Page Targeting**: Choose specific pages/posts

#### Advanced Settings
- **Shortcode Support**: Use `[adunblock]` shortcode
- **Widget Support**: Add AdUnblock widget to sidebars
- **Hook Integration**: Integrate with WordPress hooks
- **Cache Compatibility**: Works with popular caching plugins

## Method 2: Manual Implementation

### Adding Code to Theme

#### Option A: Using wp_head Hook

Add to your theme's `functions.php` file:

```php
function add_adunblock_script() {
    ?>
    <script src="https://cdn.ad-unblock.com/sdk.js" data-site-id="YOUR_SITE_ID"></script>
    <?php
}
add_action('wp_head', 'add_adunblock_script');
```

#### Option B: Enqueue Script Properly

Better approach using WordPress script enqueuing:

```php
function enqueue_adunblock_script() {
    wp_enqueue_script(
        'adunblock-sdk',
        'https://cdn.ad-unblock.com/sdk.js',
        array(),
        '1.0.0',
        false
    );
    
    // Add site ID as data attribute
    wp_script_add_data('adunblock-sdk', 'data-site-id', 'YOUR_SITE_ID');
}
add_action('wp_enqueue_scripts', 'enqueue_adunblock_script');
```

#### Option C: Direct Theme Integration

Add directly to your theme's `header.php` file:

```php
<script src="https://cdn.ad-unblock.com/sdk.js" data-site-id="YOUR_SITE_ID"></script>
```

### Custom Implementation with PHP

Create a more sophisticated implementation:

```php
class AdUnblock_WordPress {
    private $site_id;
    private $options;
    
    public function __construct($site_id) {
        $this->site_id = $site_id;
        $this->options = get_option('adunblock_options', array());
        
        add_action('wp_enqueue_scripts', array($this, 'enqueue_scripts'));
        add_action('wp_head', array($this, 'add_configuration'));
        add_shortcode('adunblock', array($this, 'shortcode'));
    }
    
    public function enqueue_scripts() {
        // Only load on frontend
        if (!is_admin()) {
            wp_enqueue_script(
                'adunblock-sdk',
                'https://cdn.ad-unblock.com/sdk.js',
                array(),
                '1.0.0',
                false
            );
            
            // Add custom JavaScript
            wp_add_inline_script('adunblock-sdk', $this->get_custom_js());
        }
    }
    
    public function add_configuration() {
        echo '<script>window.AdUnblockConfig = ' . json_encode(array(
            'siteId' => $this->site_id,
            'debug' => WP_DEBUG,
            'ajaxUrl' => admin_url('admin-ajax.php'),
            'nonce' => wp_create_nonce('adunblock_nonce')
        )) . ';</script>';
    }
    
    public function get_custom_js() {
        return "
        document.addEventListener('DOMContentLoaded', function() {
            if (window.AdUnblock) {
                window.AdUnblock.onDetected = function(blocker) {
                    // Send data to WordPress
                    fetch(window.AdUnblockConfig.ajaxUrl, {
                        method: 'POST',
                        headers: {
                            'Content-Type': 'application/x-www-form-urlencoded',
                        },
                        body: new URLSearchParams({
                            action: 'adunblock_detected',
                            blocker: blocker.name,
                            nonce: window.AdUnblockConfig.nonce
                        })
                    });
                };
            }
        });
        ";
    }
    
    public function shortcode($atts) {
        $atts = shortcode_atts(array(
            'message' => 'Please disable your ad blocker',
            'class' => 'adunblock-message'
        ), $atts);
        
        return '<div class="' . esc_attr($atts['class']) . '" style="display:none;">' . 
               esc_html($atts['message']) . '</div>';
    }
}

// Initialize AdUnblock
new AdUnblock_WordPress('YOUR_SITE_ID');
```

## WordPress-Specific Features

### Conditional Loading

Load AdUnblock only on specific pages:

```php
function conditional_adunblock() {
    // Only on posts and pages
    if (is_single() || is_page()) {
        wp_enqueue_script(
            'adunblock-sdk',
            'https://cdn.ad-unblock.com/sdk.js',
            array(),
            '1.0.0',
            false
        );
    }
}
add_action('wp_enqueue_scripts', 'conditional_adunblock');
```

### Custom Post Type Integration

Target specific post types:

```php
function adunblock_for_custom_posts() {
    if (is_singular('product') || is_singular('article')) {
        // Load AdUnblock for products and articles
        wp_enqueue_script('adunblock-sdk', 'https://cdn.ad-unblock.com/sdk.js');
    }
}
add_action('wp_enqueue_scripts', 'adunblock_for_custom_posts');
```

### User Role Targeting

Show AdUnblock based on user roles:

```php
function adunblock_for_visitors() {
    // Only show to non-logged-in users
    if (!is_user_logged_in()) {
        wp_enqueue_script('adunblock-sdk', 'https://cdn.ad-unblock.com/sdk.js');
    }
}
add_action('wp_enqueue_scripts', 'adunblock_for_visitors');
```

## Shortcode Usage

### Basic Shortcode

Use the `[adunblock]` shortcode in posts or pages:

```
[adunblock]
```

### Customized Shortcode

Add custom attributes:

```
[adunblock message="Support our site by disabling your ad blocker" class="custom-adblock-notice"]
```

### Advanced Shortcode Implementation

Create a more advanced shortcode:

```php
function adunblock_message_shortcode($atts, $content = null) {
    $atts = shortcode_atts(array(
        'type' => 'modal',
        'delay' => '2000',
        'dismissible' => 'true',
        'title' => 'Ad Blocker Detected'
    ), $atts);
    
    $output = '<div class="adunblock-shortcode" data-type="' . esc_attr($atts['type']) . '" 
                   data-delay="' . esc_attr($atts['delay']) . '" 
                   data-dismissible="' . esc_attr($atts['dismissible']) . '" 
                   style="display:none;">';
    
    $output .= '<h3>' . esc_html($atts['title']) . '</h3>';
    
    if ($content) {
        $output .= '<div class="adunblock-content">' . do_shortcode($content) . '</div>';
    }
    
    $output .= '</div>';
    
    return $output;
}
add_shortcode('adunblock_message', 'adunblock_message_shortcode');
```

Usage:
```
[adunblock_message type="banner" delay="1000" title="Please Support Us"]
We rely on ads to keep our content free. Please consider whitelisting our site.
[/adunblock_message]
```

## Widget Integration

### Create AdUnblock Widget

Add AdUnblock as a WordPress widget:

```php
class AdUnblock_Widget extends WP_Widget {
    public function __construct() {
        parent::__construct(
            'adunblock_widget',
            'AdUnblock Message',
            array('description' => 'Display AdUnblock message in sidebar')
        );
    }
    
    public function widget($args, $instance) {
        echo $args['before_widget'];
        
        if (!empty($instance['title'])) {
            echo $args['before_title'] . apply_filters('widget_title', $instance['title']) . $args['after_title'];
        }
        
        echo '<div class="adunblock-widget" style="display:none;">';
        echo esc_html($instance['message']);
        echo '</div>';
        
        echo $args['after_widget'];
    }
    
    public function form($instance) {
        $title = !empty($instance['title']) ? $instance['title'] : 'Ad Blocker Notice';
        $message = !empty($instance['message']) ? $instance['message'] : 'Please disable your ad blocker';
        ?>
        <p>
            <label for="<?php echo $this->get_field_id('title'); ?>">Title:</label>
            <input class="widefat" id="<?php echo $this->get_field_id('title'); ?>" 
                   name="<?php echo $this->get_field_name('title'); ?>" type="text" 
                   value="<?php echo esc_attr($title); ?>">
        </p>
        <p>
            <label for="<?php echo $this->get_field_id('message'); ?>">Message:</label>
            <textarea class="widefat" id="<?php echo $this->get_field_id('message'); ?>" 
                      name="<?php echo $this->get_field_name('message'); ?>"><?php echo esc_attr($message); ?></textarea>
        </p>
        <?php
    }
    
    public function update($new_instance, $old_instance) {
        $instance = array();
        $instance['title'] = (!empty($new_instance['title'])) ? strip_tags($new_instance['title']) : '';
        $instance['message'] = (!empty($new_instance['message'])) ? strip_tags($new_instance['message']) : '';
        return $instance;
    }
}

// Register widget
function register_adunblock_widget() {
    register_widget('AdUnblock_Widget');
}
add_action('widgets_init', 'register_adunblock_widget');
```

## Caching Compatibility

### Popular Caching Plugins

AdUnblock works with:
- **WP Rocket**
- **W3 Total Cache**
- **WP Super Cache**
- **LiteSpeed Cache**
- **Cloudflare**

### Cache Configuration

Ensure AdUnblock scripts are not cached:

```php
// Exclude AdUnblock from caching
function exclude_adunblock_from_cache($excluded_files) {
    $excluded_files[] = 'cdn.ad-unblock.com';
    return $excluded_files;
}
add_filter('rocket_exclude_js', 'exclude_adunblock_from_cache');
```

## Advanced WordPress Integration

### AJAX Handlers

Handle AdUnblock events with WordPress AJAX:

```php
// Handle ad blocker detection
function handle_adunblock_detection() {
    check_ajax_referer('adunblock_nonce', 'nonce');
    
    $blocker = sanitize_text_field($_POST['blocker']);
    $user_id = get_current_user_id();
    
    // Store detection data
    update_user_meta($user_id, 'uses_adblock', true);
    update_user_meta($user_id, 'adblock_type', $blocker);
    
    // Log for analytics
    error_log("AdBlock detected: $blocker for user: $user_id");
    
    wp_die();
}
add_action('wp_ajax_adunblock_detected', 'handle_adunblock_detection');
add_action('wp_ajax_nopriv_adunblock_detected', 'handle_adunblock_detection');
```

### Custom Database Tables

Store AdUnblock data in custom tables:

```php
function create_adunblock_table() {
    global $wpdb;
    
    $table_name = $wpdb->prefix . 'adunblock_detections';
    
    $charset_collate = $wpdb->get_charset_collate();
    
    $sql = "CREATE TABLE $table_name (
        id mediumint(9) NOT NULL AUTO_INCREMENT,
        user_ip varchar(45) NOT NULL,
        blocker_name varchar(100) NOT NULL,
        detection_time datetime DEFAULT CURRENT_TIMESTAMP,
        page_url text NOT NULL,
        user_agent text,
        PRIMARY KEY (id)
    ) $charset_collate;";
    
    require_once(ABSPATH . 'wp-admin/includes/upgrade.php');
    dbDelta($sql);
}
register_activation_hook(__FILE__, 'create_adunblock_table');
```

## Theme Integration Examples

### Twenty Twenty-Four Theme

Add to your child theme's `functions.php`:

```php
function twentytwentyfour_adunblock() {
    if (!is_admin()) {
        wp_enqueue_script(
            'adunblock-sdk',
            'https://cdn.ad-unblock.com/sdk.js',
            array(),
            '1.0.0',
            false
        );
        
        wp_script_add_data('adunblock-sdk', 'data-site-id', 'YOUR_SITE_ID');
    }
}
add_action('wp_enqueue_scripts', 'twentytwentyfour_adunblock');
```

### Elementor Integration

Create custom Elementor widget:

```php
class Elementor_AdUnblock_Widget extends \Elementor\Widget_Base {
    public function get_name() {
        return 'adunblock';
    }
    
    public function get_title() {
        return 'AdUnblock Message';
    }
    
    public function get_icon() {
        return 'fa fa-ban';
    }
    
    public function get_categories() {
        return ['general'];
    }
    
    protected function render() {
        echo '<div class="adunblock-elementor" style="display:none;">';
        echo 'Please disable your ad blocker to support our site.';
        echo '</div>';
    }
}

// Register widget
function register_adunblock_elementor_widget() {
    \Elementor\Plugin::instance()->widgets_manager->register_widget_type(new Elementor_AdUnblock_Widget());
}
add_action('elementor/widgets/widgets_registered', 'register_adunblock_elementor_widget');
```

## Testing in WordPress

### WordPress-Specific Testing

1. **Test with different themes**
2. **Check plugin compatibility**
3. **Verify caching behavior**
4. **Test user role targeting**
5. **Validate shortcode functionality**

### Debug Mode

Enable WordPress debug mode in `wp-config.php`:

```php
define('WP_DEBUG', true);
define('WP_DEBUG_LOG', true);
define('WP_DEBUG_DISPLAY', false);
```

## Troubleshooting WordPress Issues

### Common Problems

**Script not loading:**
- Check plugin is activated
- Verify site ID is correct
- Check for theme conflicts

**Caching issues:**
- Clear all caches
- Exclude AdUnblock from cache
- Test in incognito mode

**Theme conflicts:**
- Switch to default theme
- Check for JavaScript errors
- Disable other plugins temporarily

### Performance Optimization

```php
// Optimize loading
function optimize_adunblock_loading() {
    // Only load on content pages
    if (is_single() || is_page()) {
        // Load after user interaction
        wp_add_inline_script('jquery', '
            $(document).one("scroll mousedown touchstart", function() {
                if (!window.adunblockLoaded) {
                    window.adunblockLoaded = true;
                    var script = document.createElement("script");
                    script.src = "https://cdn.ad-unblock.com/sdk.js";
                    script.setAttribute("data-site-id", "YOUR_SITE_ID");
                    document.head.appendChild(script);
                }
            });
        ');
    }
}
add_action('wp_enqueue_scripts', 'optimize_adunblock_loading');
```

## Next Steps

After implementing AdUnblock in WordPress:

1. **Test thoroughly** across different browsers and devices
2. **Monitor performance** impact on your site
3. **Track analytics** in the AdUnblock dashboard
4. **Optimize based on data** collected
5. **Consider premium features** for advanced functionality

For more advanced customization, check out our [API Reference](../api-reference/rest-api.md) guide. 