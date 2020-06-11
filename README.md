# Repeater Depth Helper

A module for ProcessWire CMS/CMF. This module does two things relating to Repeater fields that have the "Item depth" option enabled:

1. It enforces some depth rules for Repeater fields on save. Those rules are:
    1. The first item must have a depth of zero.
    2. Each item depth must not be more than one greater than previous item depth.

2. It provides a `RepeaterPageArray::getDepthStructure` helper method that returns a nested depth structure for a Repeater field value.

## Helper method

The module adds a `RepeaterPageArray::getDepthStructure` method that returns a multi-dimensional array where the key is the page ID and the value is an array of nested "child" items, or null if there are no nested children.

### Example

![rdh-tracy](https://user-images.githubusercontent.com/1538852/84331009-1f54e180-abdd-11ea-9c3e-c6513e9088b2.png)

The module doesn't make any assumptions about how you might want to use the depth structure array, but here is a way you might use it to output a nested unordered list.

```php
// Output a nested unordered list from a depth structure array
function outputNestedList($depth_structure, $repeater_items) {
    $out = "<ul>";
    foreach($depth_structure as $page_id => $nested_children) {
        $out .= "<li>{$repeater_items->get("id=$page_id")->title}";
        // Go recursive if there are nested children
        if(is_array($nested_children)) $out .= outputNestedList($nested_children, $repeater_items);
        $out .= "</li>";
    }
    $out .= "</ul>";
    return $out;
}

$repeater_items = $page->my_repeater;
$depth_structure = $repeater_items->getDepthStructure();
echo outputNestedList($depth_structure, $repeater_items);
```

![rdh-list](https://user-images.githubusercontent.com/1538852/84331008-1d8b1e00-abdd-11ea-9e6e-fc73966d0e71.png)