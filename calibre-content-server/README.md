# Calibre Content-Server on QNAP Container Station

跑 Content Server 會有個問題，如果不是由同一台機器的 Calibre 軟體去更新資料庫，則 Server 不會自動更新資料，而我們不跑 VNC/UI，所以必須自己監控檔案異動來重啟 Server，而且 Content Server 會需要有寫入資料庫的權限，但我們不想讓 Content Server 去修改我們任何的檔案，因此必須在 Container 內以 overlayfs 提供一層保護，讓所有異動都不會影響原來的檔案。

## 環境說明

1. QNAP Nas 我的帳號是 pigo，經由帳戶管理介面得知 UID = 500
2. QSync 的存放目錄是 `/share/homes/pigo/.Qsync`
3. Windows 有兩個書庫分別是 `漫畫` 與 `漫畫-升頻版`

## docker-compose.yml 概念說明

1. service `calibre-content-server` 分別提供 `漫畫` 與 `漫畫-升頻版` 的 Web 與 OPDS 服務。
2. 使用 `dockerfile_inline` 語法建立一個乾淨的 Image，並且 entryscript 只啟動 `calibre-server`。
3. `cap_add: ` 設定為 `SYS_ADMIN` 才能於 container 內 mount 一個 overlayfs 目錄。
4. entryscript 內建立了 `/books` 並 mount 為 overlayfs 來對應 `/books-ro`，`/books` 才是讓 `calibre-server` 存取用的。
5. inotifywait 則分別監控各書庫的 `metadata.db` 是否異動，若有異動，則重新掛載 `/books` 並啟動 `calibre-server`。
