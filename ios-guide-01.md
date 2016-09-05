# 目標

* 生成第一個 redpotion 專案
* 拉 API 繪成第一個主畫面


## Step 1: 新增第一個 Project

* potion new ios-job-list
* rake

會顯示第一個畫面 Hello World 畫面。

![](https://cdn.filepicker.io/api/file/T3Ltl0RRW4aP8xO9jmTA)

## Step 2 : 修改標題

* 打開 `app/screens/home_screen.rb` 修改 title 為 '職缺列表'
* rake

![](https://cdn.filepicker.io/api/file/0K9SCgprSyOcGvXK2ysY)

## Step 3 : 準備 jobs 資料

* 在負責吐資料的另一個網址，我們準備了職缺的資料。資料位址是 <http://localhost:3000/jobs.json>



![](https://cdn.filepicker.io/api/file/nDjhLrM2Q8eOft6rBNLW)
## Step 4 : 接 api

修改 `app/screens/home_screen.rb` 變成

``` ruby
class HomeScreen < PM::TableScreen
  title '職缺一覽'
  stylesheet HomeScreenStylesheet

  def on_load
    @jobs = []
    load_jobs
  end

  def load_jobs
    Job.all do |response, jobs|
      if response.success?
        @jobs = jobs
        stop_refreshing
        update_table_data
      else
        app.alert 'Sorry, there was an error fetching the jobs.'
        mp response.error.localizedDescription
      end
    end
  end

  def table_data
    [{
      cells: @jobs.map do |job|
        {
          height: 100,
          title: job.title,
          action: :view_job,
          arguments: { job: job }
        }
      end
    }]
  end

  def will_animate_rotate(_orientation, _duration)
    find.all.reapply_styles
  end
end

```

新增 `app/models/api_client.rb`

``` ruby

class ApiClient
  class << self
    def client
      @client ||= AFMotion::SessionClient.build('http://localhost:3000/') do
        header 'Accept', 'application/json'
        response_serializer :json
      end
    end

    def update_authorization_header(header)
      client.headers['Authorization'] = header
    end
  end
end

```

新增 `app/models/job.rb`

``` ruby

class Job
  attr_accessor :id, :title

  def initialize(data)
    @id = data['id']
    @title = data['title']
  end

  def self.all(&callback)
    ApiClient.client.get 'jobs' do |response|
      models = []
      models = response.object.map { |data| new(data) } if response.success?
      callback.call(response, models)
    end
  end
end

```

記得要去 `Rakefile` 裡面加入這一行

```
app.info_plist['NSAppTransportSecurity'] = { 'NSAllowsArbitraryLoads' => true } # allow any HTTP request
```

這樣 app 的網路才會通。

然後執行

* rake

這樣你就收到剛剛的資料了。

![](https://cdn.filepicker.io/api/file/5WzgJhWLSDKJVlNn7CmP)


## 新增職缺敘述

我們想在列表上新增職缺敘述


![](https://cdn.filepicker.io/api/file/qqQTD3qiR2WgV9cuQLB1)

修改 `app/models/job.rb`

``` ruby

class Job
  attr_accessor :id, :title, :content

  def initialize(data)
    @id = data['id']
    @title = data['title']
    @content = data['content']
  end

```

修改 `app/screens/home_screen.rb` 中的 table_data，加入 subtitle

``` ruby
def table_data
  [{
    cells: @jobs.map do |job|
      {
        height: 100,
        title: job.title,
        subtitle: job.content,
        action: :view_job,
        arguments: { job: job }
      }
    end
  }]
end
```

# 資源：

* Table Screen https://promotion.readthedocs.io/en/master/Reference/ProMotion%20TableScreen/
