# customizer-framework
## Introduction
The Customizer framework is a great tool to add options to your plugin so users can customize it. The framework that allows you to add controls to the customizer, in a simple, fast and easily maintainable way. In this guide, I'm going to introduce you to customizer framework. We'll also create several settings and demonstrate how they work.

## Table of Contents

- [Installation](#installation)
- [Configuration](#configuration)
- [Header Controls](#header-controls)
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

<pre><code>//Hooks
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
}</code></pre>

By including this code in your main plugin file, you ensure that the customizer functionality is integrated into your plugin. This allows you to utilize the features provided by the `customizer-admin.php` file located in the `customizer` folder you added to your plugin directory.

## Configuration
Locate the customizer configuration file in your plugin folder. You can find it at `your-plugin-folder/customizer/customizer-admin.php`. Open this PHP file in your plugin directory, as it contains all the controls and basic configuration settings.

Within the `customizer-admin.php` file, you'll need to make a couple of changes to customize it for your specific admin page:

- `private static $screen_id = 'customizer_framework';`<br>
- `private static $screen_title = 'Customizer Framework';`

Replace `customizer_framework` and `Customizer Framework` with your desired admin page ID and customizer title.

Next, you should set the following localized script parameters according to your requirements:
<pre><code>wp_localize_script( self::$screen_id, self::$screen_id, array(
	'main_title'	=> self::$screen_title,		// Customizer main title
	'admin_email' => get_option('admin_email'),	// Admin email address
	'send_test_email_btn' => true,		// Show/Hide a send test email button in framework
	'iframeUrl'	=> array('processing' => admin_url('admin-ajax.php?action=' . self::$screen_id . '_email_preview&preview=processing',)	// Preview irameURL as order status and preview id
	'back_to_wordpress_link' => admin_url(),	// redirect after close customizer framework
	'rest_nonce'	=> wp_create_nonce('wp_rest'),	// Nonce for security perpose
	'rest_base'	=> esc_url_raw( rest_url() ),	// Rest base URL for API
));</code></pre>

These settings allow you to customize various aspects of your customizer framework, such as the main title, admin email address, the presence of a test email button, iframe URLs, redirection link, and security features.

Finally, use filter hooks to add controls and preview content to your customizer:
> For returning settings options:
<pre><code>apply_filters(  self::$screen_id . '_email_options' , $settings = array(), $preview );</code></pre>
> For returning preview content:
<pre><code>apply_filters( self::$screen_id . '_preview_content' , $preview );</code></pre>

These filter hooks allow you to define and customize the settings and preview content for your customizer framework.

By following these steps, you can configure the Customizer Framework to suit your specific needs and preferences.
## Header Controls
To add header controls, you need to provide arguments such as the title, previewType, default and type and nav when registering them.

> [!NOTE]
> When you registering them you need to pass all controls code inside setting variable in filter hook and return it.

**Arguments:**<br>
- `previewType` : Use the value 'true' to update the preview content on change email type.<br>
- `nav` : The nav is position of header.<br>
- `options` : The array of the previews list"
- `parent` : This argument refers to the parent panel or sub-panel ID to which the control belongs. It typically points to the ID of the panel to which the control is attached.
- `show` : Use the value 'true' to indicate that the field should be displayed.
- `option_type` : Set this to 'array' if you want to store the option value in an array with a key based on the ID.
- `unique_key` : The unique key is employed to store the option value when the `option_name` and `option_key` arguments are not provided.
- `default` : Use the store value to set default when user come first time on customizer.
- `type` : This argument refers to the type of field or option

<pre><code>//Header Controls
'email_type' => array(
	'type'     => 'select',
	'options'  => array(
		'preview1'  => 'Preview 1',
		'preview2'  => 'Preview 2',
	),
	'show'     => true,
	'previewType' => true,
	'nav' => 'header',
	'default'  => !empty(value) ? value : 'preview1',
),
</code></pre>

## Panels and Controls
Panels serve as containers for grouping multiple sections together in the Customizer framework. Let's explore how to create panels in the Customizer and the arguments required when registering them.

> [!NOTE]
> When you registering them you need to pass all controls code inside setting variable in filter hook and return it.

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

To create controls, it is essential to supply various arguments during their registration. These arguments include specifying the parent panel or sub-panel ID, title, default value, placeholder, visibility, option name, option type, option key, and the control type.

**Required Arguments:**

- `parent` : This argument refers to the parent panel or sub-panel ID to which the control belongs. It typically points to the ID of the panel to which the control is attached.
- `show` : Use the value 'true' to indicate that the field should be displayed.
- `option_type` : Set this to 'array' if you want to store the option value in an array with a key based on the ID.
- `unique_key` : The unique key is employed to store the option value when the `option_name` and `option_key` arguments are not provided.
- `default` : Use the value to set default when  user come first time on customizer.
- `type` : This argument refers to the type of field or option

**Optional Arguments:**

- `option_name` : Optionally, you can provide a unique ID to store the option value under this specific ID.
- `option_key` : This is used to specify the key under which the option value will be stored in an array associated with the unique ID.
- `refresh` : If set to 'true', it indicates that the iframe in the preview should be refreshed when there is a change.
- `placeholder` : The "placeholder" parameter is a providing context and guidance for data entry.
- `class` : The "class" parameter will add a class in options.
- `desc` : This is used to add description text under the options.

> [!NOTE]
> When you registering them you need to pass all controls code inside setting variable in filter hook and return it.

### Text
![image](https://github.com/zorem/customizer-framework/assets/69037744/65b3ff17-9e14-4cdd-9865-e8b0a388e06f)

The `Text` controls allow you to add a simple, single-line text input.

**Example**
<pre><code>'unique_key' => array(
	'parent'=> 'panel_id',
	'title'    => esc_html__( 'Text Control', 'text-domian' ),
	'default'  => !empty('value') ? 'value' : 'defualt value', 
	'type'     => 'text',
	'show'     => true,
	'option_name' => 'unique_id',
	'option_type' => 'array',
),</code></pre>

### Textarea
![image](https://github.com/zorem/customizer-framework/assets/69037744/38c73f12-9c99-4478-9383-0382bcafee97)

**Example**
<pre><code>'unique_key' => array(
	'parent'=> 'panel_id',
	'title'    => esc_html__( 'Textarea Control', 'text-domian' ),
	'default'  => !empty('value') ? 'value' : 'defualt value', 
	'type'     => 'Textarea',
	'show'     => true,
	'option_name' => 'unique_id',
	'option_type' => 'array',
),</code></pre>

### Select
![image](https://github.com/zorem/customizer-framework/assets/69037744/e4d3940a-3209-49c1-b257-d7317400722e)

**Example**
<pre><code>'unique_key' => array(
	'parent'=> 'panel_id',
	'title'    => esc_html__( 'Select Control', 'text-domian' ),
	'type'     => 'select',
	'default'  => !empty('value') ? 'value' : 'key1',
	'show'     => true,
	'options'  => array(
		'key1' => 'Option 1',
		'key2' => 'Option 2',
		'key3' => 'Option 3',
	),
	'option_name' => 'unique_id',
	'option_type' => 'array',
),</code></pre>

### Toggle
![image](https://github.com/zorem/customizer-framework/assets/69037744/deb080c6-a0db-46d6-ab85-40183c9060fb)

**Example**
<pre><code>'unique_key' => array(
	'parent'=> 'panel_id',
	'title'    => esc_html__( 'Toggle Control', 'text-domian' ),
	'default'  => !empty(value) && 'no' == value ? 0 : 1,
	'type'     => 'tgl-btn',
	'show'     => true,
	'option_name'=> 'unique_id',
	'option_type'=> 'array',
),</code></pre>


### Checkbox
![image](https://github.com/zorem/customizer-framework/assets/69037744/8a3da748-19db-4925-941a-5ccef7931681)

**Example**
<pre><code>'unique_key' => array(
	'parent'=> 'panel_id',
	'title'    => esc_html__( 'Checkbox Control', 'text-domian' ),
	'default'  => !empty(value) ? value : 1,
	'type'     => 'checkbox',
	'show'     => true,
	'option_name'=> 'unique_id',
	'option_type'=> 'array',
),</code></pre>

### Color
![image](https://github.com/zorem/customizer-framework/assets/69037744/f6d19d5a-afd7-4e84-bdb8-9386f6d18dd8)

**Example**
<pre><code>'unique_key' => array(
	'parent'=> 'panel_id',
	'title'    => esc_html__( 'Color control', 'text-domian' ),
	'default'  => !empty(value) ? value : #000,
	'type'     => 'color',
	'show'     => true,
	'option_name'=> 'unique_id',
	'option_type'=> 'array',
),</code></pre>


### Tags Input
![image](https://github.com/zorem/customizer-framework/assets/69037744/b7409806-e5f2-4e2e-a48e-153f69cd6b64)

**Example**
<pre><code>'unique_key' => array(
	'parent'=> 'panel_id',
	'title'    => esc_html__( 'Tags Input control', 'text-domian' ),
	'default'  => !empty(value) ? value : '',
	'type'     => 'tags-input',
	'show'     => true,
	'option_name'=> 'unique_id',
	'option_type'=> 'array',
),</code></pre>

### Codeinfo
![image](https://github.com/zorem/customizer-framework/assets/69037744/1e871725-188a-4d19-89ce-173d80b85db8)

**Example**
<pre><code>'unique_key' => array(
	'parent'=> 'panel_id',
	'title'    => esc_html__( 'Available Placeholder:', 'text-domian' ),
	'default'  => '{customer_first_name}<br>{customer_last_name}<br>{site_title}<br>{order_number}',
	'type'     => 'codeinfo',
	'show'     => true,
),</code></pre>

### Upload Image Control
![image](https://github.com/zorem/customizer-framework/assets/69037744/b9793b8e-c046-4e20-8f6b-08f3fcabfa47)

**Example**
<pre><code>'unique_key' => array(
	'parent'=> 'panel_id',
	'title'    => esc_html__( 'Upload Image Control', 'text-domian' ),
	'type'     => 'media',
	'show'     => true,
	'option_name'=> 'unique_id',
	'option_type' => 'array',
	'desc'     => esc_html( 'image size requirements: 200px/40px.', 'sales-report-email-pro' ),
	'default'	=> ''
),</code></pre>


### Radio
![image](https://github.com/zorem/customizer-framework/assets/69037744/bc1a5e85-20a6-458f-b06c-1c95380aa358)

**Example**
<pre><code>'unique_key' => array(
	'parent'=> 'panel_id',
	'title'    => esc_html__( 'Radio Control', 'text-domian' ),
	'type'     => 'radio',
	'show'     => true,
	'option_name'=> 'unique_id',
	'option_type' => 'array',
	'choices'  => array(
		'2colums' => '2 Columns',
		'1colums' => '1 Column'
	),	
	'default' => !empty(value) ? value : '2colums',
),</code></pre>

### Range
![image](https://github.com/zorem/customizer-framework/assets/69037744/a63bcbc0-1c59-4c09-b47d-af7623b05d1e)

**Example**
<pre><code>'unique_key' => array(
	'parent'=> 'panel_id',
	'title'    => esc_html__( 'Range Control', 'text-domian' ),
	'type'     => 'range',
	'show'     => true,
	'option_name'=> 'unique_id',
	'option_type' => 'array',	
	'default' => !empty(value) ? value : '10',
	'min' => "0",
	'max' => "30",
	'unit' => "px"
),</code></pre>

### Daterange
![image](https://github.com/zorem/customizer-framework/assets/69037744/fb403bd7-d51b-4f17-948d-272fd375381b)

**Example**
<pre><code>'unique_key' => array(
	'parent'=> 'panel_id',
	'title'    => esc_html__( 'Daterange Control', 'text-domian' ),
	'type'     => 'daterange',
	'show'     => true,
	'option_name'=> 'unique_id',
	'option_type' => 'array',	
	'default' => !empty(value) ? value : array(),
	
),</code></pre>
