function snowreports_shortcode($atts){
    extract( shortcode_atts( array(
        'count' => 10,
    ), $atts) );
    $i = 0;
    $q = new WP_Query(
        array(
            'posts_per_page' => $count, 
            'post_type' => 'posttype', 
            'paged'     => get_query_var('paged'),
        )
    );      
          
    $list = '<div class="custom-post-list">';
    while($q->have_posts()) : $q->the_post();
        $idd = get_the_ID();
        $i++;
        $custom_field = get_post_meta($idd, 'custom_field', true);
        $post_content = get_the_excerpt();
        $list .= '
        <div class="single-post-item">
            <h2><a href="'.get_permalink().'">' .do_shortcode( get_the_title() ). '</a></h2>
            '.wpautop( $post_content ).'
        </div>
        ';        
    endwhile;
     
    $total_pages = $q->max_num_pages;
    $big = 999999999;
    if ($total_pages > 1){  
        $current_page = max(1, get_query_var('paged'));  
        $list.= '<nav class="page-nav">';  
        $list.= paginate_links(array(  
            'base'      => str_replace( $big, '%#%', esc_url( get_pagenum_link( $big ) ) ),
            'format'    => '?paged=%#%',  
            'current'   => $current_page,  
            'total'     => $total_pages,  
            'prev_text' => 'Prev',  
            'next_text' => 'Next' 
        ));  
        $list.= '</nav>';  
    }     
     
     
    $list.= '</div>';
    wp_reset_query();
    return $list;
}
add_shortcode('snowreports', 'snowreports_shortcode');
