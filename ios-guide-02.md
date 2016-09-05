# 目標：

* 點擊工作，可以看到工作內容

# 步驟：

## Step 1

在 `app/screens/homr_screen.rb` 加入

``` ruby
def view_job(args)
  open JobScreen.new(args)
end

```

## Step 2

* potion g screen job


## Step 3

修改 `app/screens/job_screen.rb`

加入

``` ruby

class JobScreen < PM::Screen
  stylesheet JobScreenStylesheet

  attr_accessor :job

  def on_load
    self.title = @job.title

    @content = append!(UILabel, :job_content)
    @content.text = @job.content

    @image = append!(UIImageView, :job_image).style { |st| st.remote_image = @job.image_url }
  end

  def will_animate_rotate(_orientation, _duration)
    reapply_styles
  end
end

```

修改 `app/stylesheets/job_screen_stylesheet.rb`

加入

``` ruby

class JobScreenStylesheet < ApplicationStylesheet
  # Add your view stylesheets here. You can then override styles if needed,
  # example: include FooStylesheet

  def setup
    # Add stylesheet specific setup stuff here.
    # Add application specific setup stuff in application_stylesheet.rb
  end

  def job_content(st)
    st.frame = { top: 100, left: 20, width: 100, height: 30 }
    st.color = color.black
  end

  def job_image(st)
    st.frame = { top: 200, left: 10, width: 200, height: 200 }
    st.background_color = color.black
  end

  def root_view(st)
    st.background_color = color.white
  end
end


```


修改 `app/models/job.rb` 加入 image_url 一個欄位


``` ruby

class Job
  attr_accessor :id, :title, :content, :image_url

  def initialize(data)
    @id = data['id']
    @title = data['title']
    @content = data['content']
    @image_url = data['image_url']
  end

  # ....
end
```

## Step 4 :

* rake

最後會生成

![](https://cdn.filepicker.io/api/file/7z35aRxfRriQO0dZXBKK)
