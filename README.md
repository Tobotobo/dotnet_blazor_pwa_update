# dotnet_blazor_pwa_update

## 概要
* PWA アプリのアップデート方法を調査する

Blazor WebAssembly オフライン対応 PWA をリロードしても更新できないのはなぜか?  
https://qiita.com/jsakamoto/items/39c434aecab5b771f824  

Blazor WASM PWA — Adding a “New Update Available” notification  
https://whuysentruit.medium.com/blazor-wasm-pwa-adding-a-new-update-available-notification-d9f65c4ad13  

Blazor WASM PWA – Applications updates, cache busting with notification or force refresh  
https://techcommunity.microsoft.com/t5/web-development/blazor-wasm-pwa-applications-updates-cache-busting-with/m-p/3920976  

## 詳細

```
dotnet new blazorwasm --pwa 
```

```
dotnet run
dotnet publish -o ./publish/pwa
```

## メモ

### 素の状態
* F5 では更新されない　※タブで表示していなくても F5 では更新されない
* 当該PWAサイトを表示しているタブとインストールしたPWAアプリを閉じる  
  →　この状態で PWA アプリを起動するとその時に更新される

### service-worker.published.js に skipWaiting() を追加

wwwroot\service-worker.published.js
```js
async function onInstall(event) {
    console.info('Service worker: Install');

    self.skipWaiting(); // 追加
```

* F5 で更新される  
  ※ただし、裏でダウンロードして更新待機状態になっていないと更新されない  
  ※上記の状態には自動的になるが、少し時間がかかる場合があり、まだ更新待機状態で無い場合 F5 しても更新されない？  
  ※何度か F5 押してると更新される

### アップデート通知
* 前提：self.skipWaiting(); // 追加
* publish\wwwroot\sw-registrator.js
* Layout\WhateverYouCalledTheComponent.razor
* 更新があったらページ上部に更新がある旨のポップアップが表示される仕組みを追加
* 更新があったかのチェックが F5 等で更新された時かアプリを一旦閉じて次開いた時にしか行われていないっぽい
* そこそこの時間待ったが自動ではポップアップされず...