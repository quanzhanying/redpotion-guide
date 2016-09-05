# 如何安裝 RedPotion

- 安裝 Xcode 9.3
- 再去 rubymotion 官網下載 http://www.rubymotion.com/
- gem install cocoapods
- pod setup （要非常久，因為 782mb )
- gem install redpotion
- potion new meme
- cd meme; rake pod:install
- rake

## 如何 確定自己下載 cocoapods 的進度

```
while true; do
  du -sh ~/.cocoapods/
  sleep 3
done
```
在 terminal 輸入這四行，可以確定自己下載 cocoapods 的進度

http://stackoverflow.com/questions/21022638/pod-install-is-staying-on-setting-up-cocoapods-master-repo

## 特別注意：

* 記得不要過 VPN 抓 rubymotion，否則會超慢
