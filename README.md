# customizer-framework
## Introduction
The Customizer framework is a great tool to add options to your plugin so users can customize it. The framework that allows you to add controls to the customizer, in a simple, fast and easily maintainable way. In this guide, I'm going to introduce you to customizer framework. We'll also create several settings and demonstrate how they work.

## Table of Contents

- [Installation](#installation)
- [Configuration](#configuration)
- [Panels and Controls](#panels-and-controls)
- [Adding Controls](#adding-controls)

## Installation
You can easily incorporate the files from this repository into your plugin by following these steps:

First, you have the option to either download the repository as a zip file or clone it from Git. Here are the instructions for both methods:

**1. To Clone the Repository:**

Navigate to the Git repository using the following command:

<pre><code>git clone https://github.com/kuldip-navadiya/customizer-framework.git</code></pre>

This command will clone the repository into your designated directory. You will find a `customizer` folder inside your plugin directory.

**2. Alternatively, To Download the Zip File:**

You can also download the repository as a zip file by visiting the following link:
[Download Zip](https://github.com/kuldip-navadiya/customizer-framework/archive/refs/heads/main.zip)

Once downloaded, unzip the zip file, and you will find the 'customizer' folder. Copy this folder into your plugin directory.

After adding the necessary files to your plugin, you should include the following code in the main plugin file to enable the customizer functionality:

<pre><code>
//Hooks
add_action( 'plugins_loaded', array( $this, 'on_plugins_loaded' ) );

/*
* include file on plugin load
*/
public function on_plugins_loaded() {
	// customizer
	require_once $this->get_plugin_path() . '/customizer/customizer-admin.php';	
	$this->customizer = Customizer_Admin::get_instance();
}
	
/**
* Gets the absolute plugin path without a trailing slash, e.g.
* /path/to/wp-content/plugins/plugin-directory.
*
* @return string plugin path
*/
public function get_plugin_path() {
	if ( isset( $this->plugin_path ) ) {
		return $this->plugin_path;
	}
	$this->plugin_path = untrailingslashit( plugin_dir_path( __FILE__ ) );
	return $this->plugin_path;
}
</code></pre>

By including this code in your main plugin file, you ensure that the customizer functionality is integrated into your plugin. This allows you to utilize the features provided by the 'customizer-admin.php' file located in the 'customizer' folder you added to your plugin directory.

## Configuration
Locate the customizer configuration file in your plugin folder. You can find it at `your-plugin-folder/customizer/customizer-admin.php`. Open this PHP file in your plugin directory, as it contains all the controls and basic configuration settings.

Within the `customizer-admin.php` file, you'll need to make a couple of changes to customize it for your specific admin page:

<pre><code>private static $screen_id = 'customizer_framework';<br>
private static $screen_title = 'Customizer Framework';</code></pre>

Replace `customizer_framework` and `Customizer Framework` with your desired admin page ID and customizer title.

Next, you should set the following localized script parameters according to your requirements:
<pre><code>
wp_localize_script( self::$screen_id, self::$screen_id, array(
	'main_title'	=> self::$screen_title,		// Customizer main title
	'admin_email' => get_option('admin_email'),	// Admin email address
	'send_test_email_btn' => true,		// Show/Hide a send test email button in framework
	'iframeUrl'	=> array('processing' => admin_url('admin-ajax.php?action=' . self::$screen_id . '_email_preview&preview=processing',)	// Preview irameURL as order status and preview id
	'back_to_wordpress_link' => admin_url(),	// redirect after close customizer framework
	'rest_nonce'	=> wp_create_nonce('wp_rest'),	// Nonce for security perpose
	'rest_base'	=> esc_url_raw( rest_url() ),	// Rest base URL for API
));
</code></pre>

These settings allow you to customize various aspects of your customizer framework, such as the main title, admin email address, the presence of a test email button, iframe URLs, redirection link, and security features.

Finally, use filter hooks to add controls and preview content to your customizer:
> For returning settings options:
<pre><code>apply_filters(  self::$screen_id . '_email_options' , $settings = array(), $preview );</code></pre>
> For returning preview content:
<pre><code>apply_filters( self::$screen_id . '_preview_content' , $preview );</code></pre>

These filter hooks allow you to define and customize the settings and preview content for your customizer framework.

By following these steps, you can configure the Customizer Framework to suit your specific needs and preferences.

## Panels and Controls
Panels serve as containers for grouping multiple sections together in the Customizer framework. Let's explore how to create panels in the Customizer and the arguments required when registering them.

### Creating Panels:
To create panels, you need to provide arguments such as the panel ID, title, and type when registering them. Here's how you can do it:

**Arguments:**<br>
- `panel_id` : The unique identifier for the panel.<br>
- `Title` : The display name of the panel.<br>
- `Type` : The type of the panel, which can be either "panel" or "sub-panel."

Here's an example of adding panels in the Customizer:
<pre><code>//panels
'panel_id' => array(
	'title'	=> esc_html__( 'Panel Name', 'text-domian' ),
	'type'	=> 'panel',
),
</code></pre>
Panels are useful for organizing and categorizing sections within the Customizer.

### Creating Sub-Panels:
Sub-panels, on the other hand, are wrappers for grouping multiple sections within a panel. To create sub-panels using the Customizer framework, you need to provide arguments like title, type, and the parent panel ID when registering them.

**Arguments:**<br>
- `sub-panel1` : The unique identifier for the sub-panel.<br>
- `Title` : The display name of the sub-panel.<br>
- `Type` : The type of the sub-panel, which can be "panel" or "sub-panel."<br>
- `parent` : The parent panel ID to which the sub-panel belongs, typically referencing a panel's ID.

Here's an example of adding sub-panels in the Customizer:
<pre><code>//sub-panels
'sub-panel1' => array(
	'title'		=> esc_html__( 'Sub Panel Name', 'text-domian' ),
	'type'		=> 'sub-panel',
	'parent'	=> 'panel_id',
),
</code></pre>

Sub-panels are beneficial when you want to further structure and organize sections within a specific panel.

In summary, panels are used to group sections together in the Customizer, and sub-panels allow you to create an additional level of organization within a panel by grouping related sections. When registering panels and sub-panels, make sure to define the necessary arguments such as the unique IDs, titles, types, and parent panel references as needed for your specific customization requirements.

## Adding Controls


