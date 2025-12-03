# Plugins<?php
/**
 * Plugin Name: Agentic Workflow Publisher (Timed)
 * Description: Runs the Fetcher → Writer → Publisher workflow automatically on a daily cycle.
 * Version: 1.1
 * Author: Lucas
 */

// 1. Fetcher Agent – simulate pulling a story seed
function agentic_fetcher() {
    // Replace with API calls, feeds, or your own source logic
    return array(
        'title' => 'Finance Wiz Daily Tip',
        'seed'  => 'Review subscriptions to free cash flow...'
    );
}

// 2. Writer Agent – expand the seed into a full draft
function agentic_writer($seed) {
    // Replace with AI orchestration or custom logic
    $draft = "# Finance Wiz Daily Tip\n\n" .
             "Today’s wisdom: $seed\n\n" .
             "Small cuts in recurring costs build resilience.";
    return $draft;
}

// 3. Publisher Agent – insert into WordPress
function agentic_publisher() {
    $story = agentic_fetcher();
    $draft = agentic_writer($story['seed']);

    $post_data = array(
        'post_title'   => wp_strip_all_tags($story['title']),
        'post_content' => $draft,
        'post_status'  => 'publish', // or 'draft' if you want to review first
        'post_author'  => 1,         // change to your user ID
        'post_category'=> array(1)   // default category ID
    );

    wp_insert_post($post_data);
}

// 4. Schedule the event if not already scheduled
function agentic_schedule_event() {
    if (!wp_next_scheduled('agentic_daily_event')) {
        wp_schedule_event(time(), 'daily', 'agentic_daily_event');
    }
}
add_action('wp', 'agentic_schedule_event');

// 5. Hook the publisher to the scheduled event
add_action('agentic_daily_event', 'agentic_publisher');

// 6. Cleanup on plugin deactivation
function agentic_clear_schedule() {
    $timestamp = wp_next_scheduled('agentic_daily_event');
    if ($timestamp) {
        wp_unschedule_event($timestamp, 'agentic_daily_event');
    }
}
register_deactivation_hook(__FILE__, 'agentic_clear_schedule');






