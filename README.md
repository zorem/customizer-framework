# customizer-framework
## Introduction
The Customizer framework is a great tool to add options to your plugin so users can customize it. The framework that allows you to add controls to the customizer, in a simple, fast and easily maintainable way. In this guide, I'm going to introduce you to customizer framework. We'll also create several settings and demonstrate how they work.

## Table of Contents

- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Examples](#examples)

## Installation
You can simply copy the files in your plugin and then include the main plugin file.

Go to the Github and download the zip file OR clone the repository from Git.

### where its located in Git
<p>// Clone this repo into your own directory.<br><code>git clone https://github.com/kuldip-navadiya/customizer-framework.git</code></p>

After, clone this repo into your own directory and you will see in directory <code>customizer</code> folder in your plugin folder is created.

<p>// Download this repo from the link.<br><code>https://github.com/kuldip-navadiya/customizer-framework/archive/refs/heads/main.zip</code></p>

After, Download this repo from the link. you need to unzip this zip file and copy the <code>customizer</code> folder in your plugin.

Then, Add the following code to include the main plugin file in your plugin's.

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
