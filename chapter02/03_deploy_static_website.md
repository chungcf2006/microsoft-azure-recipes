# 2.3 部署靜態網站

要部署靜態網站到 Microsoft Azure 靜態網站，只要把網頁資料上傳到網站服務的根目錄，就可以直接作為網頁空間使用。

## 事前準備

在開始之前，要先在 Microsoft Azure 網站服務建立網站，建立網站實體的方式可以參考 [2.1 建立 Microsoft Azure 網站實體](01_create_a_website.md)這篇文章。

另一方面，也要先設定好部署認證，設定的方式可以參考 [2.2 設定部署驗證](02_configure_authentication.md)這篇文章。

## 如何操作

### 設定網站根目錄

部署靜態檔案到 Microsoft Azure 網站服務，首先要知道網站的根目錄是哪一個，這個設定在管理後台中可以看到，打開網站的管理頁面，在**設定**的頁籤，拉到最下面看到虛擬應用程式和目錄:

![設定頁](http://i.imgur.com/OFDsQCH.png)

![設定對應目錄](http://i.imgur.com/H9pGNvm.png)

預設的狀況下，網站的根目錄（**/**）對應到 **site\wwwroot** 目錄，所以網站要上傳時就傳到這個目錄下，當然也可以直接修改設定，或是增加其它網站路徑的對應關係。

### 使用 FTP 上傳檔案



### 使用 Dropbox 同步