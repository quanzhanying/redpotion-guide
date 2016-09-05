# 讓 Rails 支援 API 登入

## Step 1 :  安裝 Devise

* rails g devise:install
* rails g devise user
* rake db:migrate

## Step 2 : 新增認證 Token 到 user

* rails g migration AddAuthTokenToUsers

``` ruby
class AddAuthTokenToUsers < ActiveRecord::Migration[5.0]
  def change
    add_column :users, :authentication_token, :string
    add_index :users, :authentication_token
  end
end

```

* rake db:migrate

## Step 3 : 修改 user model


``` ruby

class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable

  before_save do
    if authentication_token.blank?
      self.authentication_token = generate_authentication_token
    end
  end

  def generate_authentication_token
    loop do
      token = Devise.friendly_token
      break token unless User.where(authentication_token: token).first
    end
  end
end

```

## Step 4 : 修改 application_controller 關掉 CSRF



``` ruby
class ApplicationController < ActionController::Base
  protect_from_forgery with: :null_session

  before_action :authenticate_user_from_token!

  def authenticate_user_from_token!
    authenticate_with_http_token do |token, options|
      user = User.find_by(email: options.fetch(:email))
      if user && Devise.secure_compare(user.authentication_token, token)
        sign_in user, store: false
      end
    end
  end
end

```


## Step 5 : 使用客製 sessions_controller

* rails g controller sessions


``` ruby
class SessionsController < Devise::SessionsController
  respond_to :json

  def create
    super do |user|
      data = {
        token: user.authentication_token,
        email: user.email
      }
      render(json: data, status: 201) && return
    end
  end
end
```

## Step 6 : 修改 config/routes.rb


``` ruby
Rails.application.routes.draw do
  devise_for :users, controllers: { sessions: "sessions" }
  # .....
end

```
