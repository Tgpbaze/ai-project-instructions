
### 15. `PLUGIN.md` - Plugin Development Guide
```markdown
# PLUGIN.md - Plugin Development Standards

## Plugin Architecture

### Plugin Structure

your-plugin/
├── src/
│ ├── core/
│ │ ├── Plugin.php # Main plugin class
│ │ ├── Activator.php # Activation hooks
│ │ └── Deactivator.php # Deactivation hooks
│ ├── admin/
│ │ ├── Admin.php # Admin interface
│ │ └── Settings.php # Settings page
│ │ └── assets/
│ ├── public/
│ │ ├── Frontend.php # Public interface
│ │ └── assets/
│ ├── includes/
│ │ ├── Database.php # Database operations
│ │ ├── API.php # API endpoints
│ │ └── Helpers.php # Utility functions
│ └── vendor/ # Composer dependencies
├── languages/ # Translation files
├── tests/ # Unit tests
├── composer.json # Composer config
├── package.json # NPM config
├── webpack.config.js # Build config
├── README.md # Documentation
└── CHANGELOG.md # Version history


### Plugin Class
```php
// ✅ Good: Main plugin class
<?php
namespace YourPlugin\Core;

defined('ABSPATH') || exit;

class Plugin {
    private static $instance;
    private $version = '1.0.0';
    private $slug = 'your-plugin';
    private $prefix = 'yp_';

    public static function getInstance(): self {
        if (!self::$instance) {
            self::$instance = new self();
        }
        return self::$instance;
    }

    private function __construct() {
        $this->init();
    }

    private function init(): void {
        add_action('init', [$this, 'loadTextDomain']);
        add_action('plugins_loaded', [$this, 'initiateComponents']);
        register_activation_hook(YP_PLUGIN_FILE, ['YourPlugin\Core\Activator', 'activate']);
        register_deactivation_hook(YP_PLUGIN_FILE, ['YourPlugin\Core\Deactivator', 'deactivate']);
    }

    public function loadTextDomain(): void {
        load_plugin_textdomain(
            $this->slug,
            false,
            dirname(plugin_basename(YP_PLUGIN_FILE)) . '/languages'
        );
    }

    public function initiateComponents(): void {
        // Initialize admin/backend components
        if (is_admin()) {
            new \YourPlugin\Admin\Admin();
            new \YourPlugin\Admin\Settings();
        }

        // Initialize frontend components
        if (!is_admin()) {
            new \YourPlugin\Public\Frontend();
        }

        // Initialize common components
        new \YourPlugin\Includes\Database();
        new \YourPlugin\Includes\API();
    }

    public function getVersion(): string {
        return $this->version;
    }

    public function getSlug(): string {
        return $this->slug;
    }
}

// ✅ Good: Activation/Deactivation hooks
class Activator {
    public static function activate(): void {
        // Check PHP version
        if (version_compare(PHP_VERSION, '7.4', '<')) {
            deactivate_plugins(plugin_basename(YP_PLUGIN_FILE));
            wp_die('Your Plugin requires PHP 7.4 or higher.');
        }

        // Create database tables
        self::createTables();

        // Set default options
        add_option('yp_options', self::defaultOptions());

        // Flush rewrite rules
        flush_rewrite_rules();

        // Create upload directories
        wp_mkdir_p(WP_CONTENT_DIR . '/uploads/your-plugin');
    }

    private static function createTables(): void {
        global $wpdb;
        $charset_collate = $wpdb->get_charset_collate();
        $table_name = $wpdb->prefix . 'yp_items';

        $sql = "CREATE TABLE $table_name (
            id bigint(20) NOT NULL AUTO_INCREMENT,
            name varchar(255) NOT NULL,
            content longtext,
            created_at datetime DEFAULT CURRENT_TIMESTAMP,
            updated_at datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
            PRIMARY KEY (id)
        ) $charset_collate;";

        require_once ABSPATH . 'wp-admin/includes/upgrade.php';
        dbDelta($sql);
    }
}

class Deactivator {
    public static function deactivate(): void {
        // Clean up
        flush_rewrite_rules();
        delete_option('yp_options');
    }
}

// ✅ Good: Admin settings page
class Settings {
    public function __construct() {
        add_action('admin_menu', [$this, 'addMenuPage']);
        add_action('admin_init', [$this, 'registerSettings']);
    }

    public function addMenuPage(): void {
        add_options_page(
            'Plugin Settings',
            'Plugin Settings',
            'manage_options',
            'plugin-settings',
            [$this, 'renderSettingsPage']
        );
    }

    public function registerSettings(): void {
        register_setting('plugin_settings', 'plugin_options', [
            'sanitize_callback' => [$this, 'sanitizeOptions'],
        ]);

        add_settings_section(
            'plugin_general_section',
            'General Settings',
            [$this, 'renderGeneralSection'],
            'plugin_settings'
        );

        add_settings_field(
            'api_key',
            'API Key',
            [$this, 'renderApiKeyField'],
            'plugin_settings',
            'plugin_general_section'
        );
    }

    public function sanitizeOptions($input): array {
        $output = [];
        $output['api_key'] = sanitize_text_field($input['api_key']);
        return $output;
    }

    public function renderSettingsPage(): void {
        ?>
        <div class="wrap">
            <h1><?php echo esc_html(get_admin_page_title()); ?></h1>
            <form method="post" action="options.php">
                <?php settings_fields('plugin_settings'); ?>
                <?php do_settings_sections('plugin_settings'); ?>
                <?php submit_button('Save Settings'); ?>
            </form>
        </div>
        <?php
    }
}

// ✅ Good: Shortcode registration
class Shortcodes {
    public function __construct() {
        add_shortcode('plugin_list', [$this, 'renderList']);
        add_shortcode('plugin_form', [$this, 'renderForm']);
    }

    public function renderList($atts, $content = null): string {
        // Set default attributes
        $atts = shortcode_atts([
            'limit' => 10,
            'category' => '',
            'show_thumbnail' => true,
        ], $atts, 'plugin_list');

        $items = $this->getItems($atts['limit'], $atts['category']);

        ob_start();
        ?>
        <div class="plugin-list">
            <?php foreach ($items as $item): ?>
                <div class="plugin-item">
                    <?php if ($atts['show_thumbnail'] && has_post_thumbnail($item->ID)): ?>
                        <?php echo get_the_post_thumbnail($item->ID, 'thumbnail'); ?>
                    <?php endif; ?>
                    <h3><?php echo esc_html($item->post_title); ?></h3>
                    <?php echo apply_filters('the_content', $item->post_content); ?>
                </div>
            <?php endforeach; ?>
        </div>
        <?php
        return ob_get_clean();
    }

    private function getItems($limit, $category): array {
        return get_posts([
            'post_type' => 'plugin_item',
            'posts_per_page' => $limit,
            'category_name' => $category,
        ]);
    }
}

// ✅ Good: Widget registration
class PluginWidget extends WP_Widget {
    public function __construct() {
        parent::__construct(
            'plugin_widget',
            'Plugin Widget',
            ['description' => 'Displays plugin items']
        );
    }

    public function widget($args, $instance): void {
        $title = apply_filters(
            'widget_title',
            $instance['title'],
            $instance,
            $this->id_base
        );

        echo $args['before_widget'];
        if ($title) {
            echo $args['before_title'] . esc_html($title) . $args['after_title'];
        }

        // Widget content
        $items = get_posts([
            'post_type' => 'plugin_item',
            'posts_per_page' => $instance['count'] ?? 5,
        ]);

        if (!empty($items)) {
            echo '<ul>';
            foreach ($items as $item) {
                echo '<li><a href="' . get_permalink($item->ID) . '">' . esc_html($item->post_title) . '</a></li>';
            }
            echo '</ul>';
        }

        echo $args['after_widget'];
    }

    public function form($instance): void {
        $title = $instance['title'] ?? '';
        $count = $instance['count'] ?? 5;
        ?>
        <p>
            <label for="<?php echo esc_attr($this->get_field_id('title')); ?>">
                Title:
                <input
                    class="widefat"
                    id="<?php echo esc_attr($this->get_field_id('title')); ?>"
                    name="<?php echo esc_attr($this->get_field_name('title')); ?>"
                    type="text"
                    value="<?php echo esc_attr($title); ?>"
                />
            </label>
        </p>
        <p>
            <label for="<?php echo esc_attr($this->get_field_id('count')); ?>">
                Number of items:
                <input
                    class="small-text"
                    id="<?php echo esc_attr($this->get_field_id('count')); ?>"
                    name="<?php echo esc_attr($this->get_field_name('count')); ?>"
                    type="number"
                    min="1"
                    max="20"
                    value="<?php echo esc_attr($count); ?>"
                />
            </label>
        </p>
        <?php
    }
}

// Register widget
function register_plugin_widget(): void {
    register_widget('PluginWidget');
}
add_action('widgets_init', 'register_plugin_widget');

// ✅ Good: Custom REST API endpoints
class API {
    public function __construct() {
        add_action('rest_api_init', [$this, 'registerRoutes']);
    }

    public function registerRoutes(): void {
        register_rest_route('plugin/v1', '/items', [
            'methods' => 'GET',
            'callback' => [$this, 'getItems'],
            'permission_callback' => [$this, 'checkPermission'],
        ]);

        register_rest_route('plugin/v1', '/items/(?P<id>\d+)', [
            'methods' => 'GET',
            'callback' => [$this, 'getItem'],
            'permission_callback' => [$this, 'checkPermission'],
        ]);

        register_rest_route('plugin/v1', '/items', [
            'methods' => 'POST',
            'callback' => [$this, 'createItem'],
            'permission_callback' => [$this, 'checkAdminPermission'],
            'args' => [
                'title' => [
                    'required' => true,
                    'type' => 'string',
                    'sanitize_callback' => 'sanitize_text_field',
                ],
                'content' => [
                    'required' => true,
                    'type' => 'string',
                    'sanitize_callback' => 'wp_kses_post',
                ],
            ],
        ]);
    }

    public function getItems($request): WP_REST_Response {
        $limit = $request->get_param('limit') ?: 10;
        $items = get_posts([
            'post_type' => 'plugin_item',
            'posts_per_page' => $limit,
        ]);

        $response = [];
        foreach ($items as $item) {
            $response[] = [
                'id' => $item->ID,
                'title' => $item->post_title,
                'content' => $item->post_content,
                'created_at' => $item->post_date,
            ];
        }

        return new WP_REST_Response($response, 200);
    }

    public function checkPermission($request): bool {
        return true; // Public endpoint
    }

    public function checkAdminPermission($request): bool {
        return current_user_can('manage_options');
    }
}

// ✅ Good: Input validation and sanitization
function validateInput($input): array {
    $errors = [];

    // Required fields
    if (empty($input['name'])) {
        $errors[] = 'Name is required';
    }

    // Email validation
    if (!empty($input['email']) && !is_email($input['email'])) {
        $errors[] = 'Invalid email format';
    }

    // URL validation
    if (!empty($input['url']) && !wp_http_validate_url($input['url'])) {
        $errors[] = 'Invalid URL';
    }

    // Sanitize
    $sanitized = [
        'name' => sanitize_text_field($input['name']),
        'email' => sanitize_email($input['email']),
        'url' => esc_url($input['url']),
        'content' => wp_kses_post($input['content']),
    ];

    return ['errors' => $errors, 'data' => $sanitized];
}

// ✅ Good: Nonce verification
function processForm(): void {
    if (!isset($_POST['_wpnonce']) || !wp_verify_nonce($_POST['_wpnonce'], 'plugin_action')) {
        wp_die('Security check failed');
    }

    // Process form
}

// In form
<?php wp_nonce_field('plugin_action', '_wpnonce'); ?>

// ✅ Good: Unit test
use PHPUnit\Framework\TestCase;

class PluginTest extends TestCase {
    public function testPluginActivation(): void {
        $this->assertTrue(class_exists('YourPlugin\Core\Plugin'));
        // Test activation
    }

    public function testShortcodeRendering(): void {
        $shortcode = new Shortcodes();
        $result = $shortcode->renderList(['limit' => 1]);
        $this->assertStringContainsString('plugin-list', $result);
    }
}

Plugin Checklist
Unique plugin slug and prefix

Proper header in main plugin file

Security checks (ABSPATH defined)

Activation/deactivation hooks

Uninstall method

Text domain for translations

PHP version compatibility checked

WordPress version compatibility checked

Proper escaping (esc_html, esc_attr)

Nonce verification for forms

Capability checks for admin

Database tables properly created

Uninstall removes all data

README.txt with documentation

CHANGELOG.md version history

Unit tests written

WordPress Coding Standards followed

Performance optimized

Security audit completed