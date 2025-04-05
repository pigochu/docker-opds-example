# Docker 架設 OPDS 服務的範例

這是我自己嘗試自架 OPDS 服務的經驗，將設定方式記錄下來，以我的情況來說，是習慣 Windows 上開啟 Calibre 來管理書庫，而書庫則會透過 QSync 同步至 QNAP Nas，並且於 QNAP Container Station 建立 OPDS 服務提供閱讀器使用。

若有跟我一樣的管理方式，理論上只要搞懂我在寫甚麼，應該都能改寫符合自己的環境。

## Calibre Web on QNAP Container Station

Calibre-Web 官方 Image 利用 inotifywait 監控書庫的 metadata.db 異動，達成自動 reconnect database

請參考 [calibre-web](calibre-web/README.md)

## Calibre Content-Server on QNAP Container Station

自己 Build Calibre Contet-Server image , 不開 VNC/UI 單純只跑 Content-Server 以節省記憶體，並且利用 inotifywait 監控書庫的 metadata.db 異動，達成自動自動 restart server。

請參考 [calibre-content-server](calibre-content-server/README.md)