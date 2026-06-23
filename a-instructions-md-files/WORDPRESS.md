### 12. WordPress Development Guide
```markdown
# WORDPRESS.md - WordPress Development Standards

## WordPress Architecture

### WordPress File Structure
wordpress/
├── wp-admin/ # Admin panel
├── wp-content/ # Custom content
│ ├── themes/ # Theme files
│ │ └── your-theme/
│ │ ├── inc/ # PHP includes
│ │ ├── assets/ # CSS, JS, images
│ │ ├── templates/ # Template parts
│ │ ├── functions.php
│ │ └── style.css
│ ├── plugins/ # Plugin files
│ │ └── your-plugin/
│ │ ├── includes/
│ │ ├── public/
│ │ └── plugin.php
│ └── uploads/ # Media files
├── wp-includes/ # Core files (do not modify)
├── wp-config.php # Configuration
└── .htaccess # Server config

text

### Development Environment
```bash
# ✅ Good: Local development setup
# Using Docker
docker-compose up -d

# Using Local by Flywheel
# Using MAMP/XAMPP
# Using Bedrock for better security

# ✅ Good: Environment configuration
define('WP_ENVIRONMENT_TYPE', 'local');
define('WP_DEBUG', true);
define('WP_DEBUG_LOG', true);
define('WP_DEBUG_DISPLAY', false);
Theme Development
Theme Setup
php
// ✅ Good: functions.php setup
<?php
// Theme setup
function mytheme_setup() {
    // Add theme support
    add_theme_support('title-tag');
    add_theme_support('post-thumbnails');
    add_theme_support('html5', [
        'search-form',
        'comment-form',
        'comment-list',
        'gallery',
        'caption',
    ]);
    add_theme_support('custom-logo', [
        'height' => 100,
        'width'  => 400,
    ]);
    add_theme_support('responsive-embeds');

    // Register menus
    register_nav_menus([
        'primary' => 'Primary Menu',
        'footer'  => 'Footer Menu',
    ]);

    // Set thumbnail sizes
    add_image_size('featured', 1200, 630, true);
    add_image_size('medium-custom', 600, 400, true);
}
add_action('after_setup_theme', 'mytheme_setup');
Template Hierarchy
text
archive.php
  ├── category.php
  ├── tag.php
  ├── taxonomy.php
  ├── author.php
  ├── date.php
  └── search.php
home.php
  ├── index.php
  └── front-page.php
single.php
  ├── singular.php
  ├── single-{post_type}.php
  └── attachment.php
page.php
  └── page-{slug}.php
404.php
Custom Post Types
php
// ✅ Good: Custom post type registration
function register_blog_post_type() {
    $args = [
        'labels' => [
            'name' => 'Blog Posts',
            'singular_name' => 'Blog Post',
            'add_new' => 'Add New Post',
            'add_new_item' => 'Add New Blog Post',
            'edit_item' => 'Edit Blog Post',
            'new_item' => 'New Blog Post',
            'view_item' => 'View Blog Post',
            'search_items' => 'Search Blog Posts',
            'not_found' => 'No blog posts found',
        ],
        'public' => true,
        'has_archive' => true,
        'rewrite' => ['slug' => 'blog'],
        'supports' => ['title', 'editor', 'thumbnail', 'excerpt', 'comments'],
        'show_in_rest' => true,
        'menu_position' => 5,
        'menu_icon' => 'dashicons-admin-post',
    ];
    register_post_type('blog_post', $args);
}
add_action('init', 'register_blog_post_type');
Custom Fields (ACF)
php
// ✅ Good: ACF field registration
add_action('acf/init', function() {
    acf_add_local_field_group([
        'key' => 'group_blog_post_settings',
        'title' => 'Blog Post Settings',
        'fields' => [
            [
                'key' => 'field_hero_image',
                'label' => 'Hero Image',
                'name' => 'hero_image',
                'type' => 'image',
                'return_format' => 'url',
                'preview_size' => 'medium',
            ],
            [
                'key' => 'field_reading_time',
                'label' => 'Reading Time (minutes)',
                'name' => 'reading_time',
                'type' => 'number',
                'min' => 1,
                'max' => 60,
            ],
        ],
        'location' => [
            [
                [
                    'param' => 'post_type',
                    'operator' => '==',
                    'value' => 'blog_post',
                ],
            ],
        ],
    ]);
});
Plugin Development
Plugin Structure
php
<?php
/**
 * Plugin Name: My Custom Plugin
 * Plugin URI: https://example.com/my-plugin
 * Description: Description of the plugin
 * Version: 1.0.0
 * Author: Your Name
 * License: GPL v2 or later
 */

// ✅ Good: Security check
if (!defined('ABSPATH')) {
    exit; // Exit if accessed directly
}

// ✅ Good: Plugin activation/deactivation
function my_plugin_activate() {
    // Create database tables
    // Set default options
}
register_activation_hook(__FILE__, 'my_plugin_activate');

function my_plugin_deactivate() {
    // Clean up
}
register_deactivation_hook(__FILE__, 'my_plugin_deactivate');

// ✅ Good: Main plugin class
class MyPlugin {
    public function __construct() {
        add_action('init', [$this, 'init']);
    }

    public function init() {
        // Register custom post types
        // Register taxonomies
        // Add shortcodes
    }
}
new MyPlugin();
Shortcodes
php
// ✅ Good: Shortcode registration
function my_shortcode($atts, $content = null) {
    // Default attributes
    $atts = shortcode_atts([
        'title' => 'Default Title',
        'count' => 5,
        'category' => '',
    ], $atts);

    ob_start();
    ?>
    <div class="my-shortcode">
        <h2><?php echo esc_html($atts['title']); ?></h2>
        <?php
        $posts = get_posts([
            'posts_per_page' => $atts['count'],
            'category_name' => $atts['category'],
        ]);
        foreach ($posts as $post) {
            setup_postdata($post);
            ?>
            <div class="post-item">
                <h3><?php the_title(); ?></h3>
                <?php the_excerpt(); ?>
            </div>
            <?php
        }
        wp_reset_postdata();
        ?>
    </div>
    <?php
    return ob_get_clean();
}
add_shortcode('my_custom', 'my_shortcode');
WordPress Security
Hardening WordPress
php
// ✅ Good: Security measures
// wp-config.php
define('DISALLOW_FILE_EDIT', true);
define('DISALLOW_FILE_MODS', true);
define('AUTOMATIC_UPDATER_DISABLED', false);

// ✅ Good: .htaccess security
<Files wp-config.php>
    Order Allow,Deny
    Deny from all
</Files>

<Files .htaccess>
    Order Allow,Deny
    Deny from all
</Files>

# Protect wp-content
<IfModule mod_rewrite.c>
    RewriteRule ^wp-content/uploads/(.*\.php)$ - [F,NC]
</IfModule>

// ✅ Good: Remove version info
remove_action('wp_head', 'wp_generator');
add_filter('the_generator', '__return_empty_string');

// ✅ Good: Login security
function limit_login_attempts() {
    $attempts = get_transient('login_attempts_' . $_SERVER['REMOTE_ADDR']);
    if ($attempts && $attempts > 5) {
        wp_die('Too many login attempts. Try again later.');
    }
}
add_action('login_init', 'limit_login_attempts');
Security Plugins
Wordfence: Comprehensive security

Sucuri: Website firewall

iThemes Security: Hardening

Really Simple SSL: SSL enforcement

WPS Hide Login: Change login URL

WordPress Performance
Optimization
php
// ✅ Good: Remove unnecessary scripts
function remove_unnecessary_scripts() {
    wp_dequeue_script('wp-embed');
    wp_dequeue_style('wp-block-library');
}
add_action('wp_enqueue_scripts', 'remove_unnecessary_scripts');

// ✅ Good: Disable emojis
function disable_emojis() {
    remove_action('wp_head', 'print_emoji_detection_script', 7);
    remove_action('wp_print_styles', 'print_emoji_styles');
}
add_action('init', 'disable_emojis');

// ✅ Good: Cache headers
function add_cache_headers() {
    header('Cache-Control: public, max-age=3600');
}
add_action('send_headers', 'add_cache_headers');
Caching Solutions
WP Rocket: All-in-one caching

W3 Total Cache: Comprehensive caching

LiteSpeed Cache: Server-level caching

Nginx FastCGI Cache: Server-level caching

Cloudflare: CDN + caching

WordPress SEO
Yoast/Rank Math Setup
php
// ✅ Good: SEO meta tags
add_action('wp_head', 'add_custom_seo_meta');
function add_custom_seo_meta() {
    if (is_single() || is_page()) {
        global $post;
        $seo_title = get_post_meta($post->ID, '_seo_title', true);
        $seo_description = get_post_meta($post->ID, '_seo_description', true);
        ?>
        <title><?php echo esc_html($seo_title ?: get_the_title()); ?></title>
        <meta name="description" content="<?php echo esc_attr($seo_description); ?>">
        <?php
    }
}
WordPress Maintenance
Regular Tasks
Task	Frequency	Tool
Update plugins	Weekly	WordPress admin
Update themes	Weekly	WordPress admin
Update WordPress	Monthly	WordPress admin
Backup database	Daily	UpdraftPlus
Backup files	Daily	UpdraftPlus
Security scan	Weekly	Wordfence
Performance audit	Monthly	GTmetrix
Check broken links	Monthly	Broken Link Checker
Database Optimization
sql
-- ✅ Good: Remove post revisions
DELETE FROM wp_posts WHERE post_type = 'revision';

-- ✅ Good: Remove spam comments
DELETE FROM wp_comments WHERE comment_approved = 'spam';

-- ✅ Good: Clear transients
DELETE FROM wp_options WHERE option_name LIKE '_transient_%';
WordPress CLI (WP-CLI)
Useful Commands
bash
# ✅ Good: WP-CLI commands
# Update WordPress
wp core update

# Update plugins
wp plugin update --all

# Update themes
wp theme update --all

# Clear cache
wp cache flush

# Backup database
wp db export backup.sql

# Import content
wp import content.xml

# Search and replace
wp search-replace 'old-domain.com' 'new-domain.com'
WordPress Checklist
Child theme used (if modifying parent)

All custom functions in functions.php

Translations ready (__(), _e())

Security best practices followed

Database optimized

Images optimized

Cache plugins configured

CDN enabled

SSL certificate installed

Backup system in place

Security monitoring active

SEO plugins configured

Google Analytics installed

Privacy policy compliant (GDPR)

Accessibility standards met