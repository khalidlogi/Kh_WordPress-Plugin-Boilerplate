```php
public function is_plugin_installed( $basename ) {
		if ( ! function_exists( 'get_plugins' ) ) {
			include_once ABSPATH . '/wp-admin/includes/plugin.php';
		}

		$installed_plugins = get_plugins();

		return isset( $installed_plugins[ $basename ] );
	}

public function wpforms_not_loaded_notice() {
		if ( ! current_user_can( 'activate_plugins' ) ) {
			return;
		}

		$wpforms      = 'wpforms/wpforms.php';
		$wpforms_lite = 'wpforms-lite/wpforms.php';

		$is_wpforms_installed      = $this->is_plugin_installed( $wpforms );
		$is_wpforms_lite_installed = $this->is_plugin_installed( $wpforms_lite );

		if ( $is_wpforms_installed ) {
			$activation_url = wp_nonce_url( 'plugins.php?action=activate&amp;plugin=' . $wpforms . '&amp;plugin_status=all&amp;paged=1&amp;s', 'activate-plugin_' . $wpforms );

			$message = sprintf( __( '%1$sUpload Fields for WPForms%2$s requires %1$sWPForms%2$s plugin to be active. Please activate WPForms to continue.', 'upload-fields-for-wpforms' ), "<strong>", "</strong>" );

			$button_text = __( 'Activate WPForms', 'upload-fields-for-wpforms' );
		} elseif ( ! $is_wpforms_installed && $is_wpforms_lite_installed ) {
			$activation_url = wp_nonce_url( 'plugins.php?action=activate&amp;plugin=' . $wpforms_lite . '&amp;plugin_status=all&amp;paged=1&amp;s', 'activate-plugin_' . $wpforms_lite );

			$message = sprintf( __( '%1$sUpload Fields for WPForms%2$s requires %1$sWPForms%2$s plugin to be active. Please activate WPForms to continue.', 'upload-fields-for-wpforms' ), "<strong>", "</strong>" );

			$button_text = __( 'Activate WPForms', 'upload-fields-for-wpforms' );
		} else {

			$activation_url = wp_nonce_url( self_admin_url( 'update.php?action=install-plugin&plugin=wpforms-lite' ), 'install-plugin_wpforms-lite' );

			$message     = sprintf( __( '%1$sUpload Fields for WPForms%2$s requires %1$sWPForms%2$s plugin to be installed and activated. Please install WPForms to continue.', 'upload-fields-for-wpforms' ), '<strong>', '</strong>' );
			$button_text = __( 'Install WPForms', 'upload-fields-for-wpforms' );
		}

		$button = '<p><a href="' . esc_url( $activation_url ) . '" class="button-primary">' . esc_html( $button_text ) . '</a></p>';

		printf( '<div class="error"><p>%1$s</p>%2$s</div>',  $message , $button );
	}

	private function check_environment() {
		$environment = true;

		if ( ! class_exists( 'WPForms\WPForms' ) ) {
			$environment = false;

			if ( is_admin() ) {
				add_action( 'admin_notices', [ $this, 'wpforms_not_loaded_notice' ] );
			}
		}

		return $environment;
	}


```

