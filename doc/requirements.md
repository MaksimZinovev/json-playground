# JSON Profile Snapshot Playground - Requirements

## 1. Feature: JSON Profile Snapshot Generation

### 1.1 Scenario: Generate snapshot from JSON input
  Given a user has loaded a JSON file or pasted JSON content
  When the user clicks "Generate Snapshot"
  Then the system should analyze the JSON structure
  And display a visual tree map with collapsible nodes
  And show statistics including total keys, max depth, null ratio
  And detect patterns such as uniform arrays and enum fields

### 1.2 Scenario: Generate snapshot with configurable depth
  Given a user has loaded JSON content
  When the user sets the traversal depth slider to a value between 1 and 10
  And the user clicks "Generate Snapshot"
  Then the system should only analyze the JSON up to the specified depth
  And the visualization should reflect the limited depth

### 1.3 Scenario: Generate snapshot with sample size limit for arrays
  Given a user has loaded JSON content containing large arrays
  When the user sets the array sample size slider
  And the user clicks "Generate Snapshot"
  Then the system should only analyze the specified number of array items
  And the visualization should indicate the total array size

## 2. Feature: Visual Tree Map Visualization

### 2.1 Scenario: Display collapsible tree structure
  Given a JSON snapshot has been generated
  When the tree visualization is rendered
  Then each object should be displayed as a collapsible node
  And each array should be displayed as a collapsible node
  And each node should show a type badge (object, array, string, number, boolean, null)
  And each node should show count badges (number of keys or items)
  And each node should show null count badges if nulls are present

### 2.2 Scenario: Expand and collapse tree nodes
  Given a tree visualization is displayed
  When the user clicks on a tree node header
  Then the node should toggle between expanded and collapsed states
  And child nodes should be shown or hidden accordingly
  And the expander arrow should rotate to indicate state

### 2.3 Scenario: Display type distribution for nodes
  Given a tree visualization is displayed
  When show type counts option is enabled
  Then each node should display the count of each child type
  And a mini type distribution bar should be shown

## 3. Feature: Null-Signature Analysis

### 3.1 Scenario: Compute null-signature for comparison
  Given a user has selected "Comparison Snapshot" output format
  When the snapshot is generated
  Then the system should compute null-signature for each path
  And null-signature should count empty values (null, "", [], {})
  And null-signature should count non-empty values

### 3.2 Scenario: Null-signature for arrays
  Given a JSON structure contains an array of unified objects (same set of keys), non-primitives
  When null-signature is computed
  Then the array should have a single null-signature
  And the single null-signature should be represented by signature of the 0-index item

### 3.3 Scenario: Null-signature for nested objects in arrays
  Given a JSON structure contains an array of objects with nested objects
  When null-signature is computed
  Then the array should have its own null-signature
  And each unique nested object type should have its own null-signature
  And nested objects with the same key set should share a single null-signature

## Feature: Heatmap Visualization

### Scenario: Display heatmap aligned with tree
  Given a JSON snapshot has been generated
  When the preview is rendered
  Then the heatmap should be displayed in a column to the left of the tree
  And the top heatmap block should be vertically aligned with the root tree node
  And both columns should scroll together

### Scenario: Heatmap block styling
  Given a heatmap is being rendered
  When a block represents an object
  Then the block should be a fixed W px by H px square
  And the block width and height should match the current width of [class="stat-value"] in ./json-profile-snapshot.html
  And the block should be split horizontally into two parts
  And the right part should show null ratio using grey color
  And the left part should show value ratio using blue color
  And the width of each part should be proportional to its count

### Scenario: Heatmap block for fully filled objects
  Given an object has zero null values
  When the corresponding heatmap block is rendered
  Then the block should be 100 percent blue
  And no grey portion should be visible

### Scenario: Heatmap block for sparse objects
  Given an object has 4 null values and 1 non-empty value
  When the corresponding heatmap block is rendered
  Then the block should be 80 percent grey
  And the block should be 20 percent blue

### Scenario: Heatmap block labels and tooltips
  Given a heatmap block is displayed
  Then the block should show a object name label in the center
  And the block should show a tooltip with exact counts (e.g. "∅1 ✓20")

### Scenario: Root object vs nested object block rendering
  Given a JSON structure with a root object containing nested non-primitives
  When the heatmap is rendered
  Then blocks should be rendered for all non-primitive values (objects, arrays) at any depth
  And if root contains multiple keys (mix of primitives and non-primitives), root gets a block showing aggregate null ratio
  And if root contains only non-primitive children, only those children get blocks
  And nested non-primitives always get their own blocks regardless of root structure

### Scenario: Array of non-unique objects with simple structure
  Given a JSON array where each item is a non-unique object with the same set of keys
  When the heatmap is rendered
  Then a single block should represent the array
  And no separate blocks should be shown for individual items

### Scenario: Array of objects with nested objects
  Given a JSON array where items contain nested non-primitive objects (two objects are uniqu when they have different set of keys)
  When the heatmap is rendered
  Then a block should represent the array itself
  And a block should represent each unique nested object type
  And blocks should be named using [] notation for array items (e.g., "[].ability")

### Scenario: Empty arrays and empty dictionaries
  Given a JSON structure contains an empty array [] or empty dictionary {}
  When the heatmap is rendered
  Then a separate block should be rendered for the empty array or empty dictionary
  And the block should be 100 percent grey (indicating all null/empty)

## Feature: Preset Configurations

### Scenario: Quick Overview preset
  Given a user selects the "Quick Overview" preset
  When the preset is applied
  Then traversal depth should be set to 3
  And pattern detection should be disabled
  And type counts should be enabled
  And visual tree format should be selected

### Scenario: Deep Analysis preset
  Given a user selects the "Deep Analysis" preset
  When the preset is applied
  Then traversal depth should be set to 8
  And all analysis options should be enabled
  And array sample size should be 20
  And visual tree format should be selected

### Scenario: Schema Extraction preset
  Given a user selects the "Schema Extraction" preset
  When the preset is applied
  Then traversal depth should be set to 5
  And JSON Schema format should be selected
  And pattern detection should be enabled

### Scenario: Comparison Mode preset
  Given a user selects the "Comparison Mode" preset
  When the preset is applied
  Then traversal depth should be set to 5
  And Comparison Snapshot format should be selected
  And pattern detection should be disabled

### Scenario: Pattern Focus preset
  Given a user selects the "Pattern Focus" preset
  When the preset is applied
  Then traversal depth should be set to 6
  And pattern detection should be enabled
  And array sample size should be 15

## Feature: Sample JSON Loaders

### Scenario: Load GitHub Issues sample
  Given the user clicks the "GitHub Issues" sample button
  When the sample is loaded
  Then the textarea should be populated with GitHub issues JSON
  And the JSON should represent an array of issue objects
  And each issue should contain nested user, labels, and reactions objects

### Scenario: Load Pokemon API sample
  Given the user clicks the "Pokemon API" sample button
  When the sample is loaded
  Then the textarea should be populated with Pokemon data
  And the JSON should represent a deeply nested structure
  And it should contain arrays with nested objects (abilities, forms, game_indices)

### Scenario: Load Products sample
  Given the user clicks the "Products" sample button
  When the sample is loaded
  Then the textarea should be populated with products data
  And the JSON should contain a products array
  And each product should have nested dimensions, reviews, and meta objects

## Feature: Output Format Generation

### Scenario: Visual Tree output
  Given a user selects "Visual Tree" output format
  When the snapshot is generated
  Then the output should show a summary section with key statistics
  And the output should show type distribution with percentages
  And the output should list structure branches with type information
  And the output should list null fields if null analysis is enabled
  And the output should list uniform arrays if pattern detection is enabled

### Scenario: JSON Schema output
  Given a user selects "JSON Schema" output format
  When the snapshot is generated
  Then the output should contain a valid JSON Schema Draft 7
  And the schema should define types for all properties
  And the schema should include format hints for dates and URLs

### Scenario: Comparison Snapshot output
  Given a user selects "Comparison Snapshot" output format
  When the snapshot is generated
  Then the output should contain a metadata section with key counts
  And the output should contain a types section with all type counts
  And the output should contain a null-signature section
  And null-signatures are outputted only for non-primitives (exclude single keys:  string, number, boolean) 
  And null-signatures are outputted for objects (dictionaries, arrays) 
  And the null-signature should map paths to null and value counts (compressed single line json ,e.g. "slot": {"null": 0, "value": 2})

## Feature: Heatmap and Tree Synchronization

### Scenario: Click heatmap block to toggle tree
  Given a heatmap is displayed with blocks
  When the user clicks on a heatmap block
  Then the corresponding tree node should toggle its expanded state
  And the heatmap visibility should update accordingly

### Scenario: Expand tree shows heatmap blocks
  Given a tree node is collapsed
  When the user expands the tree node
  Then heatmap blocks for descendant objects should become visible
  And the blocks should be aligned with their corresponding tree nodes

### Scenario: Collapse tree hides heatmap blocks
  Given a tree node is expanded
  When the user collapses the tree node
  Then heatmap blocks for descendant objects should be hidden
  And only the block for the collapsed node should remain visible

## Feature: Console Logging for Debugging

### Scenario: Log heatmap computation
  Given the system is computing object signatures
  Then each object signature should be logged to console
  And array signatures should be logged
  And nested object signatures should be logged
  And the total count of signatures should be logged

### Scenario: Log heatmap rendering
  Given the system is rendering heatmap blocks
  Then each block's path and counts should be logged
  And the rendering completion should be logged

### Scenario: Log tree state changes
  Given a tree node is toggled
  Then the path and new state should be logged
  And the resulting heatmap visibility updates should be logged


## Example 1

Heatmap shows: `held_items` array block (root has single non-primitive child), `held_items[].item` object block (100% blue, 2 values), `held_items[].version_details[].version` object block (100% blue, 2 values per item).
Number of blocks: 3

```json
 {"held_items": [
        {
            "item": {
                "name": "metal-powder",
                "url": "https://pokeapi.co/api/v2/item/234/"
            },
            "version_details": [
                {
                    "rarity": 5,
                    "version": {
                        "name": "ruby",
                        "url": "https://pokeapi.co/api/v2/version/7/"
                    }
                },
                {
                    "rarity": 5,
                    "version": {
                        "name": "sapphire",
                        "url": "https://pokeapi.co/api/v2/version/8/"
                    }
                },
            ]
        }
    ]}

```


## Example 2

`user` object block (root has single non-primitive child, 35% grey, 65% blue): 7 empty strings count as nulls (gravatar_id, following_url, starred_url, repos_url, user_view_type, site_admin), 13 non-null values.
Number of blocks: 1

```json
{ "user": {
      "login": "Abrahanm7",
      "id": 102440005,
      "node_id": "U_kgDOBhscRQ",
      "avatar_url": "https://avatars.githubusercontent.com/u/102440005?v=4",
      "gravatar_id": "",
      "url": "https://api.github.com/users/Abrahanm7",
      "html_url": "https://github.com/Abrahanm7",
      "followers_url": "https://api.github.com/users/Abrahanm7/followers",
      "following_url": "",
      "gists_url": "https://api.github.com/users/Abrahanm7/gists{/gist_id}",
      "starred_url": "",
      "subscriptions_url": "https://api.github.com/users/Abrahanm7/subscriptions",
      "organizations_url": "https://api.github.com/users/Abrahanm7/orgs",
      "repos_url": "",
      "events_url": "https://api.github.com/users/Abrahanm7/events{/privacy}",
      "received_events_url": "h",
      "type": "User",
      "user_view_type": "",
      "site_admin": ""
    }}
```


## Example 3

Two blocks: root block (25% grey, 75% blue for 1 null, 3 non-nulls) and `assignees` block (100% grey for empty array).
Number of blocks: 2

```json
{
"state": "closed",
    "locked": false,
    "assignee": null,
    "assignees": [
    ]
}
```



## Example 4

Single `sub_issues_summary` block (100% blue): nested object with 3 keys, all non-null (zeros are valid numbers, not empty values).
Number of blocks: 1

```json
 {
   "sub_issues_summary": {
      "total": 0,
      "completed": 0,
      "percent_completed": 0
    }
 }

```