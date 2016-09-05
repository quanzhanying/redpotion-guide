# 目標：

* 可以登入

![](https://cdn.filepicker.io/api/file/RbslfTbSQlCUzbQB3XKq)

![](https://cdn.filepicker.io/api/file/aP3GD5cERGqdvHSwwjD8)


![](https://cdn.filepicker.io/api/file/psnyJGyjRyruxfjDo4KQ)


# 步驟：

## Step 1 :

修改 Gemfile

加入

``` ruby
gem "motion-authentication"
gem "ProMotion-XLForm"
```

* bundle install
* rake pod:install

## Step 2 :

修改 `app/screens/home_screen.rb`

修改 `on_road`

``` ruby

def on_load
  if Auth.signed_in?
    set_nav_bar_button :right, title: "Logout", action: :sign_out_button
  else
    set_nav_bar_button :right, title: "Sign In", action: :sign_in_button
  end

  @jobs = []
  load_jobs
end

```

加入

``` ruby
def sign_out_button
  Auth.sign_out do
    open_tab_bar HomeScreen.new(nav_bar: true)
  end
end

def sign_in_button
  open SignInScreen.new(nav_bar: true)
end

```

## Step 3 : 加入 SignInScreen

* potion g screen sign_in


``` ruby

class SignInScreen < PM::XLFormScreen
  title "Sign In"
  stylesheet SignInScreenStylesheet

  def form_data
    [
      {
        cells: [
          {
            title:       "Email",
            name:        :email,
            type:        :email,
            placeholder: "Enter your email",
            required:    true
          },
          {
            title:       "Password",
            name:        :password,
            type:        :password,
            placeholder: "Enter your password",
            required:    true
          },
          {
            title: "Sign In",
            name: :save,
            type: :button,
            on_click: lambda do |_cell|
              authenticate
            end
          }
        ]
      }
    ]
  end

  def authenticate
    Auth.sign_in(email: values["email"], password: values["password"]) do |response|
      if response.success?
        ApiClient.update_authorization_header(Auth.authorization_header)
        app.delegate.open_authenticated_root
      elsif response.object
        app.alert response.object["error"]
      else
        app.alert "Sorry, there was an error. #{response.error.localizedDescription}"
      end
    end
  end

  def will_animate_rotate(_orientation, _duration)
    reapply_styles
  end
end

```

## Step 4 : 新增 auth

* touch `app/models/auth.rb`


``` ruby
class Auth < Motion::Authentication
  strategy DeviseTokenAuth
  sign_in_url "http://localhost:3000/users/sign_in"
end

```

## Step 5 : compile

* rake
