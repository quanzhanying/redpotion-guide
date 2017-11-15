# 做抽屜

![](https://cdn.filepicker.io/api/file/ZbQas77vSHCOlghBDYBn)

# 步驟：

## Step 1:

安裝 Gem

``` ruby

gem "ProMotion-menu"
gem "motion-fontawesome"

```

* bundle install
* rake pod:install

## Step 2 :

* 修改 `app/screens/home_screen.rb`


``` ruby

class HomeScreen < PM::TableScreen
  title "職缺一覽"
  stylesheet HomeScreenStylesheet

  include NavigationHelper

  def on_load
    add_side_menu

    @jobs = []
    load_jobs
  end

  def sign_out_button
    Auth.sign_out do
      app.delegate.open_authenticated_root
    end
  end

  def sign_in_button
    open SignInScreen.new(nav_bar: true)
  end

```


## Step 3 :

* touch app/helpers/navigation_helpper.rb

``` ruby
module NavigationHelper
  def add_side_menu
    label = UILabel.alloc.initWithFrame([[0, 0], [20, 20]])
    label.font = FontAwesome.fontWithSize(16.0)
    label.text = FontAwesome.icon("list")
    label.color = rmq.color.black

    set_nav_bar_button :left, custom_view: label, action: :tapped_left_nav

    rmq(label).on(:tap) do |_sender|
      app_delegate.menu.show(:left)
    end
  end

  def tapped_left_nav
    app_delegate.show_menu
  end
end

```


## Step 4:

修改 `app/app_delegate.rb`

``` ruby

class AppDelegate < PM::Delegate
  include CDQ # Remove this if you aren't using CDQ

  status_bar true, animation: :fade

  # Without this, settings in StandardAppearance will not be correctly applied
  # Remove this if you aren't using StandardAppearance
  ApplicationStylesheet.new(nil).application_setup

  def on_load(_app, _options)
    cdq.setup # Remove this if you aren't using CDQ
    open_authenticated_root
  end

  def show_menu
    @menu.show :left
  end

  # Remove this if you are only supporting portrait
  def application(_application, willChangeStatusBarOrientation: new_orientation, duration: duration)
    # Manually set RMQ's orientation before the device is actually oriented
    # So that we can do stuff like style views before the rotation begins
    device.orientation = new_orientation
  end

  def open_authenticated_root
    open_tab_bar HomeScreen.new(nav_bar: true)
    @menu = open MenuDrawer
  end
end

```

## Step 5 :

* touch app/drawers/menu_drawer.rb

``` ruby
class MenuDrawer < PM::Menu::Drawer
  def setup
    self.center = HomeScreen.new(nav_bar: true)
    self.left = NavigationScreen
    self.to_show = [:pan_bezel, :pan_center]
    self.transition_animation = :slide
    self.max_left_width = 200
    self.shadow = false
  end
end
```

## Step 6 :

* potion g screen navigation

修改 app/screens/navigation_screen.rb
```
class NavigationScreen < PM::TableScreen

  def on_load
    rmq.stylesheet = NavigationScreenStylesheet
  end

  def table_data

    [{
      title: nil,
      cells: [
        { title: '' },
        { title: sign_in_out_title ,
          action: :sign_in_out_button,
        },
        { title: 'Sign Up',
          action: :sign_up_button,
        },
        add_credit_card_menu
      ]
    }]
  end

  def add_credit_card_menu
    if Auth.signed_in?
      { title: "Add Credit Card", action: :add_credit_card_action }
    end
  end

  def sign_in_out_title
    if Auth.signed_in?
      "Logout"
    else
      "Sign In"
    end
  end

  def sign_in_out_button
    if Auth.signed_in?
      sign_out_button
    else
      sign_in_button
    end
  end

  def sign_in_button
    open SignInScreen.new(nav_bar: true)
  end

  def sign_up_button
    open SignUpScreen.new(nav_bar: true)
  end

  def sign_out_button
    Auth.sign_out do
      app.delegate.open_authenticated_root
    end
  end

  def add_credit_card_action
    open AddCreditCardScreen.new(nav_bar: true)
#    open PaymentViewController.new
#    mp StripeConnection.connectionWithPublishableKey("pk_test_APfRAnKNxAO78IG481nvbXqq")
  end


  def swap_center_controller(screen_class)
    # Just use screen_class if you don't need a navigation bar
    app_delegate.menu.center_controller = screen_class
  end


  # You don't have to reapply styles to all UIViews, if you want to optimize, another way to do it
  # is tag the views you need to restyle in your stylesheet, then only reapply the tagged views, like so:
  #   def logo(st)
  #     st.frame = {t: 10, w: 200, h: 96}
  #     st.centered = :horizontal
  #     st.image = image.resource('logo')
  #     st.tag(:reapply_style)
  #   end
  #
  # Then in will_animate_rotate
  #   find(:reapply_style).reapply_styles#

  # Remove the following if you're only using portrait
  def will_animate_rotate(orientation, duration)
    reapply_styles
  end
end

```

修改 app/stylesheets/navigation_screen_stylesheet.rb

```
class NavigationScreenStylesheet < ApplicationStylesheet
  # Add your view stylesheets here. You can then override styles if needed,
  # example: include FooStylesheet

  def setup
    # Add stylesheet specific setup stuff here.
    # Add application specific setup stuff in application_stylesheet.rb
  end

  def root_view(st)
    st.background_color = color.white
  end

end

```


## Step 7 :

* rake
