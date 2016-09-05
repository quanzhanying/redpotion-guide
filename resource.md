# 資源簡介

## RedPotion

RedPotion 是集成了 RMQ, ProMotion, CDQ, AFMotion, MotionPrint 以及許多 Library 的一套 template，可以快速的搭建 rubymotion App

* 官方文檔 http://docs.redpotion.org/en/latest/

## ProMotion

* https://github.com/infinitered/ProMotion

ProMotion 提供了各式各樣的 "Screen" 以及「框架」。你除了可以使用 ProMotion 提供的 Screen 快速拉出版面外，還可以使用以下的 Gem 做出更多進階的效果。

比如說使用 `ProMotion-XLForm` 快速實現表單設計

``` ruby

gem "ProMotion-XLForm"
gem "ProMotion-push", "~> 0.2" # Push Notifications
gem "ProMotion-map", "~> 0.3"  # PM::MapScreen
gem "ProMotion-iap" # PM In-app purchases
gem "ProMotion-menu" # PM Side menu

```

## RMQ

* http://rubymotionquery.com/

提供了類似 jQuery 的語法，可以讓開發者以類似「DOM 操作」的方式，進行 iOS 手機排版以及特效實踐。

在排版上非常省時。

## CDQ

* https://github.com/infinitered/cdq

提供了一套 `ActiveRecord` like 的語法，支援讓開發者寫出易維護的語法，操作 CoreData ( 等同 iOS 裡的 database )

其中的 Schema 機制類似於 db migration

## AFMotion

https://github.com/clayallsopp/afmotion

是 AFNetworking 的 RubyMotion 版 wrapper。方便操作 API


## MotionToolbox

* http://motion-toolbox.com/

可以在上面找到你想要的第三方元件工具
