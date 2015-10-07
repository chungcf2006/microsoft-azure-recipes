# 製作動態位元率的影音串流服務

## 需求

想要提供影音串流的服務，但同時希望能夠根據條件（如：網路傳輸速度）動態調整串流影音的位元率，以提供順暢的播放串流體驗。

## 目標

完成後取得 HLS、MPEG-DASH 或是 SmoothStreaming 的 URL。

## 事前準備

* 擁有 Microsoft Azure 訂閱服務
* 需要串流的影音原始檔案

## 解決方法

### 保留編碼單位

如果我們要透過 Azure 媒體服務來轉檔，而且又是要轉成調適型位元率（adaptive bitrate），那就**一定要保留編碼單位**，因為預設的狀況是用整個服務共享的資源來編碼轉檔，而共享的資源無法處理 adaptive bitrate 的需求，所以需要來設定保留編碼單位，而保留的編碼單位會持續計費，所以不需要轉檔時可以再_把單位數調整為 0_，以確保沒有多花不必要的花費。

在媒體服務的管理介面中，到 _編碼_ 的頁面，選擇希望的保留單位類型，愈高等級代表轉檔編碼的速度愈快；再來就是在 _ENCODING_ 欄位選擇保留單位，如果同一時間只會轉檔一個檔案的話，只要保留一個單位就可以了。

![](https://skgitbook.blob.core.windows.net/azurerecipestw/ch06/reserve_encoding_unit.png)

_**圖 1**_ 保留編碼單位

設定完成，按下方的 _儲存_ 確認保留。

### 上傳影音並開始轉檔

_（下述的操作皆可以 Azure Media Service SDK 以程式完成，這裡介面透過網頁操作介面的方式來完成）_

在媒體服務的管理介面中，切換到 _內容_ 的頁面然後上傳要轉檔的影音檔案，這裡你可以從本機上傳，或者是從儲存體中取出檔案（建立媒體服務時都會要設定搭配的儲存體帳號）來處理，直接在網頁上從本機上傳的話，單檔最多只能 200MB，所以若有更大的檔案要處理，就要先把檔案上傳到儲存體。

最後，在 _內容名稱_ 欄位取一個好記的名稱作為識別。

![](https://skgitbook.blob.core.windows.net/azurerecipestw/ch06/uploading_media_file.png)

_**圖 2**_ 上傳影音檔案

上傳完成後，就可以在 _內容_ 的頁面中看到目錄媒體服務中的媒體內容：

![](https://skgitbook.blob.core.windows.net/azurerecipestw/ch06/media_content.png)

_**圖 3**_ 目前在媒體服務中的媒體檔案


這時點選剛才上傳的內容，然後點擊下方的 **處理序** 按鈕，就會跳出要編碼轉檔的設定，因為我們剛才保留的是標準的編碼單位，所以在**處理器**欄位選擇預設的 _Azure 媒體編碼器_ 就好了；而**編碼設定**就可以挑選不同的類型讓編碼器去轉檔，如果要做到動態調整畫質的服務，就要選擇有 _調適型位元速率_ 名稱的設定。最後，就是設定一個好記的名稱以記得編碼好的內容為何。

![](https://skgitbook.blob.core.windows.net/azurerecipestw/ch06/setting_encoding_job.png)

按下右下角的勾勾後，Azure 媒體服務便會開始啟動轉檔的工作，你可以在管理介面的 _工作_ 頁面觀看轉檔工作的狀態及進度。

![](https://skgitbook.blob.core.windows.net/azurerecipestw/ch06/encoding_job.png)

_**圖 4**_ 觀看目前轉檔編碼工作的狀態

接下來就是等待它編碼轉檔了，如果轉好檔案，你到對應的儲存體容器下就會看到原本一個檔案被壓了很多份不同的位元速率的檔案，但光這樣還不夠，目前還沒有提到動態封裝的機制，也就是稍後會提到的串流單位。

![](https://skgitbook.blob.core.windows.net/azurerecipestw/ch06/multiple_bitrate.png)


### 設定串流單位

如果只是一個單一位元率的影音檔案，其實透過 Azure 媒體服務編碼轉檔後，也放在儲存體中，其實直接透過 URL 就能直接（漸進式）下載觀看。