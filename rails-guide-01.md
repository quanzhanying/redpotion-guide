# 產生 API 所需資料

* rails g scaffold job title:string content:text

## 使用 ActiveRecordSerializers 取代掉原本的 jbuilder

* https://github.com/rails-api/active_model_serializers

``` ruby
gem "active_model_serializers"
```

生成 `app/serializers/job_serializer.rb`

內容

``` ruby
 class JobSerializer < ActiveModel::Serializer
  attributes :id, :title, :content, :image_url
end
```

然後砍掉 `app/views/jobs/*.jbuilder`

## 檢驗是否有成功

* 訪問 http://localhost:3000/jobs
* （記得在 db/seed.rb 先塞點假資料）
