# Calibre Web on QNAP Container Station

## 環境說明

1. QNAP Nas 我的帳號是 pigo，經由帳戶管理介面得知 UID = 500
2. QSync 的存放目錄是 `/share/homes/pigo/.Qsync`
3. Windows 有兩個書庫分別是 `漫畫` 與 `漫畫-升頻版`

## docker-compose.yml 概念說明

1. service `calibre-web-comic` 與 `calibre-web-comic-upscale` 分別提供 `漫畫` 與 `漫畫-升頻版` 的 Web 與 OPDS 服務。
   - 環境變數 PUID 與 PGID 代表執行時的用戶，一定要設定與 QSync 存放目錄相同的權限，也就是同步的帳號。
   - 環境變數 CALIBRE_RECONNECT 必須設定為 1，才能提供 `/reconnect` API 讓 `Calibre Web` 可以重連資料庫。
2. service `intofy` 用來監控書庫的 `metadata.db` 是否異動，然後以 `curl` 呼叫 `Calibre Web` 的 `/reconnect` API。
   - 使用 `dockerfile_inline` 語法建立 Image，如此就不用 Dockerfile，可直接於 QNap Container Station 啟動時自動 Build image。
   - `user` 設定 "500:500" 就是同步的帳戶 UID 與 GID，以此用戶才有權限讀取 volume。
   - entryscript 中 `declare -A file_to_api` 那邊定義了監控的檔案與要呼叫的 API。
3. 所有書庫 volume 都是用唯讀模式，避免書庫資料被程式修改，因此，`Calibre Web` 的網頁提供修改書籍的功能會不能用，因為我們主要還是要在 Windows 端來管理書籍。