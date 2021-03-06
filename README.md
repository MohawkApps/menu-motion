# MenuMotion

[![Build Status](https://travis-ci.org/codelation/menu-motion.svg)](https://travis-ci.org/codelation/menu-motion)
[![Code Climate](https://codeclimate.com/github/codelation/menu-motion.png)](https://codeclimate.com/github/codelation/menu-motion)

MenuMotion is a [RubyMotion](http://www.rubymotion.com) wrapper inspired by [Formotion](https://github.com/clayallsopp/formotion) for creating OS X menus with a syntax that should feel familiar if you've used Formotion.

## Installation

Add this line to your application's Gemfile:

```ruby
gem "menu-motion"
```

And then execute:

```sh
$ bundle
```

Or install it yourself as:

```sh
$ gem install menu-motion
```

## Usage

Here's an awesome graphic of a menu:

```
|‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾|
| [icon] First Item > |‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾|
|---------------------| First Subitem > |‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾|
| ۞ About MenuMotion |-----------------| First Action    |
| Quit                | Some Action     | ✓ Second Action |
|_____________________|_________________|_________________|
```

And the Ruby to generate this menu:

```ruby
menu = MenuMotion::Menu.new({
  sections: [{
    rows: [{
      icon: "icon.png",
      title: "First Item",
      sections: [{
        rows: [{
          title: "First Subitem",
          rows: [{
            title: "First Action",
            target: self,
            action: "first_action:",
            object: some_object
          }, {
            title: "Second Action",
            target: self,
            action: "second_action",
            checked: true
          }]
        }]
      }, {
        rows: [{
          title: "Some Action",
          target: self,
          action: :some_action
        }]
      }]
    }]
  }, {
    rows: [{
      title: "About MenuMotion",
      action: "orderFrontStandardAboutPanel:",
      image: "gear"
    }, {
      title: "Quit",
      action: "terminate:"
    }]
  }]
})
```

### Sections

Sections are used to add dividers between sets of "rows" (menu items).

```ruby
menu = MenuMotion::Menu.new({
  sections: [{
    rows: []
  }, {
    rows: []
  }
})
```

### Submenus

To link a menu item to a submenu, simply define sections
or rows within the row item that should display the submenu.

```ruby
menu = MenuMotion::Menu.new({
  rows: [{
    title: "Menu item",
    rows: [{
      title: "Submenu item 1"
    }, {
      title: "Submenu item 2"
    }]
  }]
})
```

### Actions

Adding an action to a menu item is easy. Just define the
target and action parameters. Actions that don't require the `sender` to be passed can be defined as a `String` or a `:symbol`.

```ruby
menu = MenuMotion::Menu.new({
  rows: [{
    title: "Basic Action",
    target: self,
    action: :basic_action
  }, {
    title: "Pass the menu item to the action",
    target: self,
    action: "action_with_sender:"
  }]
})

def basic_action
  puts "Hello World"
end

def action_with_sender(sender)
  puts "Hello from #{sender}"
end
```

### Keyboard Shortcuts

Keyboard shortcuts can be assigned to menu items with a simple string
assigned to the `:shortcut` parameter.
The string can include multiple modifier keys, followed by the final
key to be assigned (`{modifier+}{modifier+}{key}`):

```ruby
menu = MenuMotion::Menu.new({
  rows: [{
    title: "Item 1",
    shortcut: "command+1"
  }, {
    title: "Item 2",
    shortcut: "control+shift+2"
  }]
})
```

#### Modifier Key Options

- **`shift`**
- **`control`**, `ctl`, `ctrl`
- **`option`**, `opt`, `alt`, `alternate`
- **`command`**, `cmd`

### Validation

MenuMotion implements the [NSMenuValidation](https://developer.apple.com/library/mac/documentation/cocoa/reference/applicationkit/Protocols/NSMenuValidation_Protocol/Reference/Reference.html)
protocol for determining whether a menu item should be enabled or not.
Simply pass a `Proc` to the `:validate` parameter that returns `true` if the
menu item should be enabled or `false` if the menu item should be disabled:

```ruby
menu = MenuMotion::Menu.new({
  rows: [{
    title: "Menu item",
    tag: :main_item
    rows: [{
      title: "Submenu item 1",
      tag: :submenu_item1,
      target: self,
      action: "do_something:",
      validate: ->(menu_item) {
        true # or false
      }
    }]
  }]
})
```

### Images

You can assign an image to a menu item with the `image` option. This option can be sent as a `String` or an `NSImage`.

```ruby
menu = MenuMotion::Menu.new({
  rows: [{
    title: "Menu item",
    tag: :main_item,
    image: 'main_item_image' # the file extension is optional
    # or:
    # image: NSImage.imageNamed('main_item_image')
  }]
})
```

### Custom Views

Menu items can have custom views applied to them with the `view` option. In order for the view to be interactable, you need to set a `target` and `action` on the menu item _even if_ the custom view handles clicks for you.

```ruby
menu = MenuMotion::Menu.new({
  rows: [{
    title: "Menu item",
    tag: :main_item,
    view: MyNSViewSubclass.new,
    target: self,
    action: :blank_action
  }]
})

def blank_action
  # Nothing to see here, move along please.
end
```

### Updating Menu Items

Assign tags to menu items that will need to be updated.

```ruby
menu = MenuMotion::Menu.new({
  rows: [{
    title: "Menu item",
    tag: :main_item
    rows: [{
      title: "Submenu item 1",
      tag: :submenu_item1,
      target: self,
      action: "do_something:"
    }, {
      title: "Submenu item 2",
      tag: :submenu_item2,
      target: self,
      action: "do_something:"
    }]
  }]
})

# Let's update the first item's title:
menu.update_item_with_tag(:main_item, {
  title: "Hello World"
})

# And give the first submenu item a submenu.
# The target and action will not be used if a submenu is defined.
menu.update_item_with_tag(:submenu_item1, {
  rows: [{
    title: "Click me",
    target: self,
    action: "clicked"
  }]
})
```

## TODO

- Menu Item Icons

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am "Add some feature"`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
