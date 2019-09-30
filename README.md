# WordCamp Bari 2019
Questi sono gli snippet di codice presentati durante il talk.\
Al 18 maggio 2019, sono aggiornati all'ultima versione di Font Awesome (5.8.2)

## Caricare il foglio di stile del webfont (tutti gli stili)
Inserisci nel file `functions.php` (del tema child) la seguente funzione.
```php
/**
 * Enqueue Font Awesome
 */
function fontawesome_enqueue() {
	$version = '5.8.2';
	$integrity_key = 'sha384-oS3vJWv+0UjzBfQzYUhtDYW+Pj2yciDJxpsK1OYPAYjqT085Qq/1cq5FLXAZQ7Ay';
	wp_enqueue_style(
		'font-awesome',
		'https://use.fontawesome.com/releases/v' . $version . '/css/all.css',
		array(),
		null
	);
	/* Filter the link tag to add the integrity and crossorigin attributes for
	 * completeness.
	 */
	add_filter(
		'style_loader_tag',
		function( $html, $handle ) {
			if ( in_array( $handle, ['font-awesome'], true ) ) {
				return preg_replace(
					'/\/>$/',
					'integrity="' . $integrity_key . '" crossorigin="anonymous" />',
					$html,
					1
				);
			} else {
				return $html;
			}
		},
		10,
		2
	);
}
add_action( 'wp_enqueue_scripts', 'fontawesome_enqueue' );
```

## Caricare lo script per inserire le icone SVG con JavaScript (tutti gli stili)
Inserisci nel file `functions.php` (del tema child) la seguente funzione.

```php
/**
 * Enqueue Font Awesome
 */
function fontawesome_enqueue() {
	$version = '5.8.2';
	$integrity_key = 'sha384-DJ25uNYET2XCl5ZF++U8eNxPWqcKohUUBUpKGlNLMchM7q4Wjg2CUpjHLaL8yYPH';
	wp_enqueue_script(
		'font-awesome',
		'https://use.fontawesome.com/releases/v' . $version . '/js/all.js',
		array(),
		null
	);
	/* Filter the script tag to add additional attributes for integrity, crossorigin,
	 * defer.
	 */
	add_filter(
		'script_loader_tag',
		function( $tag, $handle ) use ( $integrity_key ) {
			if ( in_array( $handle, ['font-awesome'], true ) ) {
				$extra_tag_attributes = 'defer crossorigin="anonymous"';
				$extra_tag_attributes .= ' integrity="' . $integrity_key . '"';
				$modified_script_tag = preg_replace(
					'/<script\s*(.*?src=.*?)>/',
					"<script $extra_tag_attributes " . '\1>',
					$tag,
					1
				);
				return $modified_script_tag;
			} else {
				return $tag;
			}
		},
		10,
		2
	);
}
add_action( 'wp_enqueue_scripts', 'fontawesome_enqueue' );
```

## Caricare gli sprite SVG
### functions.php
Inserisci nel file `functions.php` (del tema child) le seguenti funzioni, sostituendo al posto di `childthemename` il nome del tuo tema child.

```php
/**
 * Get unique ID.
 *
 * This is a PHP implementation of Underscore's uniqueId method. A static variable
 * contains an integer that is incremented with each call. This number is returned
 * with the optional prefix. As such the returned value is not universally unique,
 * but it is unique across the life of the PHP process.
 *
 * @see wp_unique_id() Themes requiring WordPress 5.0.3 and greater
 * should use this instead.
 * @staticvar int $id_counter
 *
 * @param string $prefix Prefix for the returned ID.
 * @return string Unique ID.
 */
function childthemename_unique_id( $prefix = '' ) {
  static $id_counter = 0;
  if ( function_exists( 'wp_unique_id' ) ) {
    return wp_unique_id( $prefix );
  }
 return $prefix . (string) ++$id_counter;
}

/**
 * SVG icons functions and filters.
 */
require get_theme_file_path( '/inc/icon-functions.php' );
```

Nel file `functions.php` del tema child, se non esiste la funzione `childthemename_setup`, inseriscila usando il seguente codice e sostituendo al posto di `childthemename` il nome del tuo tema child.

```php
/**
 * Sets up theme defaults and registers support for various WordPress features.
 *
 * Note that this function is hooked into the after_setup_theme hook, which runs
 * before the init hook. The init hook is too late for some features, such as
 * indicating support for post thumbnails.
 */
function childthemename_setup() {
	// ... 
}
add_action( 'after_setup_theme', 'childthemename_setup' );
```

Aggiungete all'interno di `childthemename_setup` le seguenti righe di codice (sostituendole a `// ...`), sostituendo al posto di `childthemename` il nome del tema child.

```php
	/*
	 * Make theme available for translation. Translations can be filed in the
	 * childthemename/languages/ directory.
	 * 
	 */
	load_child_theme_textdomain( 'childthemename', get_stylesheet_directory() . '/languages' );
```

### /inc/icon-functions.php
Se non esiste, crea nella cartella del tema la sottocartella `/inc/`.

Crea nella sottocartella `/inc/` il file `icon-functions.php`.

Inserisci nel file `/inc/icon-functions.php` le seguenti righe di codice, sostituendo al posto di `Child Theme Name`, `Child_Theme_Name` e `childthemename` il nome del tuo tema child e al posto di `x.x.x` il numero di versione del tuo tema.

```php
<?php
/**
 * SVG icons related functions and filters
 *
 * @package Child_Theme_Name
 * @since x.x.x
 */

/**
 * Add SVG definitions to the footer.
 */
function childthemename_include_svg_icons() {
	$svg_icons = get_theme_file_path( '/assets/images/svg-icons.svg' );

	if ( file_exists( $svg_icons ) ) {
		require_once( $svg_icons );
	}
}
add_action( 'wp_footer', 'childthemename_include_svg_icons', 9999 );

/**
 * Return SVG markup.
 *
 * @param string $icon SVG icon filename. Default empty.
 * @param array  $args {
 *     Optional. Parameters used to add information to SVG icons.
 *
 *     @type string $title    SVG title. Default empty.
 *     @type string $desc     SVG description, displays only if the title is set.
 *                            Default empty.
 *     @type bool   $fallback Whether to add fallback for browsers not supporting
 *                            SVGs. Default false.
 * }
 * @return string SVG markup.
 */
function childthemename_get_svg( $icon = '' , $args = array() ) {
	// Define an icon.
	if ( empty ( $icon ) && '0' !== (string) $icon ) {
		return __( 'Please define an SVG icon filename.', 'childthemename' );
	}

	// Make sure $args are an array.
	if ( ! is_array( $args ) ) {
		return __( 'Please define optional parameters in the form of an array.', 'childthemename' );
	}

	// Set defaults.
	$defaults = array(
		'title' => '',
		'desc'  => '',
		'fallback' => false,
	);

	// Parse args.
	$args = wp_parse_args ( $args, $defaults );

	// Set aria-hidden.
	$aria_hidden = ' aria-hidden="true"';

	// Set ARIA.
	$aria_labelledby = '';

	/*
	 * Child Theme Name non-decorative SVG icons should be described to add
	 * information and improve accessibility. You can:
	 * - use the .screen-reader-text class or
	 * - use the SVG title or description attributes. 
	 * Choose one method and stick to it.
	 *
	 * Example 1 with title:
	 *
	 *     <?php echo childthemename_get_svg( 'arrow-right', array( 'title' => 'This is the title' ) ); ?>
	 *
	 * Example 2 with title and description:
	 *
	 *     <?php echo childthemename_get_svg( 'arrow-right', array(
	 *         'title' => 'This is the title',
	 *         'desc'  => 'This is the description',
	 *     ) ); ?>
	 *
	 * See https://www.paciellogroup.com/blog/2013/12/using-aria-enhance-svg-accessibility/.
	 */
	if ( $args['title'] ) {
		$aria_hidden = '';
		$unique_id = childthemename_unique_id();
		$aria_labelledby = ' aria-labelledby="title-' . $unique_id . '"';

		if ( $args['desc'] ) {
			$aria_labelledby = ' aria-labelledby="title-' . $unique_id . ' desc-' . $unique_id . '"';
		}
	}

	// Begin SVG markup.
	$svg = '<svg class="icon icon-' . esc_attr( $icon ) . '"' . $aria_hidden . $aria_labelledby . ' role="img">';

	// Display the title.
	if ( $args['title'] ) {
		$svg .= '<title id="title-' . $unique_id . '">' . esc_html__( $args['title'], 'childthemename' ) . '</title>';

		// Display the desc only if the title is set.
		if ( $args['desc'] ) {
			$svg .= '<desc id="desc-' . $unique_id . '">' . esc_html__( $args['desc'], 'childthemename' ) . '</desc>';
		}
	}

	/*
	 * Display the icon.
	 *
	 * The whitespace around `<use>` is intentional - it is a work around to a keyboard
	 * navigation bug in Safari 10.
	 *
	 * See https://core.trac.wordpress.org/ticket/38387.
	 */
	$svg .= ' <use href="#icon-' . esc_attr( $icon ) . '" xlink:href="#icon-' . esc_attr( $icon ) . '"></use> ';

	// Add some markup to use as a fallback for browsers that do not support SVGs.
	if ( $args['fallback'] ) {
		$svg .= '<span class="svg-fallback icon-' . esc_attr( $icon ) . '"></span>';
	}

	$svg .= '</svg>';

	return $svg;
}
```

### style.css
Aggiungi al foglio di stile style.css del tuo tema child le seguenti regole.

```css
/* SVG Icons base styles */
.icon {
	display: inline-block;
	fill: currentColor;
	height: 1em;
	position: relative; /* Align more nicely with capital letters */
	top: -0.0625em;
	vertical-align: middle;
	width: 1em;
}

.svg-fallback {
	display: none;
}
```

### /assets/images/svg-icons.svg
Se non esiste, crea nella cartella del tuo tema la sottocartella `/assets/`.

Se non esiste, crea nella sottocartella `/assets/` la sottocartella `/assets/images/`.

Crea nella sottocartella `/assets/images/` il file `svg-icons.svg`.

Inserisci nel file `/assets/images/svg-icons.svg` le seguenti righe.
```svg
<!--
Font Awesome Free 5.8.2 by @fontawesome - https://fontawesome.com
License - https://fontawesome.com/license/free (Icons: CC BY 4.0)
-->
<svg xmlns="https://www.w3.org/2000/svg" style="display: none;">
	// ...
</svg>
```

Al posto di `// ...`, inserisci per ciascuna icona le seguenti righe di codice:
```svg
	<symbol id="icon-iconname" viewBox="0 0 width height">
		<path d="<!-- ... -->"></path>
	</symbol>
```
sostituendo al posto di `iconname` il nome dell'icona, al posto di `width` e `height` la larghezza e l'altezza dell'icona e al posto di `<!-- ... -->` il tracciato dell'icona.
