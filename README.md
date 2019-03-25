# wookomerce__remove-product_category

```php
// Удалить из url Woocommerce  /product/,  /product-category/ и /shop/
add_filter( 'request', 'change_requerst_vars_for_product_cat' );
add_filter( 'term_link', 'term_link_filter', 10, 3 );
add_filter( 'post_type_link', 'wpp_remove_slug', 10, 3 );
add_action( 'pre_get_posts', 'wpp_change_request' );
 
function change_requerst_vars_for_product_cat($vars) {
 
    global $wpdb;
    if ( ! empty( $vars[ 'pagename' ] ) || ! empty( $vars[ 'category_name' ] ) || ! empty( $vars[ 'name' ] ) || ! empty( $vars[ 'attachment' ] ) ) {
      $slug   = ! empty( $vars[ 'pagename' ] ) ? $vars[ 'pagename' ] : ( ! empty( $vars[ 'name' ] ) ? $vars[ 'name' ] : ( ! empty( $vars[ 'category_name' ] ) ? $vars[ 'category_name' ] : $vars[ 'attachment' ] ) );
      $exists = $wpdb->get_var( $wpdb->prepare( "SELECT t.term_id FROM $wpdb->terms t LEFT JOIN $wpdb->term_taxonomy tt ON tt.term_id = t.term_id WHERE tt.taxonomy = 'product_cat' AND t.slug = %s", array( $slug ) ) );
      if ( $exists ) {
        $old_vars = $vars;
        $vars     = array( 'product_cat' => $slug );
        if ( ! empty( $old_vars[ 'paged' ] ) || ! empty( $old_vars[ 'page' ] ) ) {
          $vars[ 'paged' ] = ! empty( $old_vars[ 'paged' ] ) ? $old_vars[ 'paged' ] : $old_vars[ 'page' ];
        }
        if ( ! empty( $old_vars[ 'orderby' ] ) ) {
          $vars[ 'orderby' ] = $old_vars[ 'orderby' ];
        }
        if ( ! empty( $old_vars[ 'order' ] ) ) {
          $vars[ 'order' ] = $old_vars[ 'order' ];
        }
      }
    }
 
    return $vars;
 
  }
  
function term_link_filter( $url, $term, $taxonomy ) {
 
    $url = str_replace( "/product-category/", "/", $url );
    return $url;
 
  }
 
function wpp_remove_slug( $post_link, $post, $name ) {
 
    if ( 'product' != $post->post_type || 'publish' != $post->post_status ) {
      return $post_link;
    }
    $post_link = str_replace( '/' . $post->post_type . '/', '/', $post_link );
 
    return $post_link;
 
  }
 
function wpp_change_request( $query ) {
 
    if ( ! $query->is_main_query() || 2 != count( $query->query ) || ! isset( $query->query[ 'page' ] ) ) {
      return;
    }
    if ( ! empty( $query->query[ 'name' ] ) ) {
      $query->set( 'post_type', array( 'post', 'product', 'page' ) );
    }
 
}
```
