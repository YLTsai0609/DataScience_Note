# bash
> 紀錄工作中常用到的一些bash指令，以及使用場景
> 查看當前bash版本 `help`
* [TODO] 週一問問題
* 有沒有快捷鍵可以前選一個字? (已經查過網路了，可是不work)
* 怎麼看目前terminal的一些short cut

# NetWork
|命令|使用場景|備註|
|---|-------|---|
|`ifconfig`|查自己的這台的ip||
|`ipython` `import socket` `socket.gethostbyname('deviceName')`|從A主機經由同網域的連線查詢B主機的ip|ipython要pytho3.5以上才支援|
|`ping` ip adress|從A主機經由同網域的連線查詢B主機目前的連接狀況及速度(time out, or speed xx/ms)|ping = 戳，測自己網速可以搭配內部迴圈網路 127.0.0.1|
|`scp userName@ip adress:filePath MyfilePath`|已知主機filePath，從主機抓檔案/資料夾到本地端||
|`scp MyfilePath userName@ip adress:filePath`|把本地端的檔案copy到主機端的目錄|
|`ssh userName@ip adress`|經由ssh連線到主機端，但主機端需要是一個ssh server，或是有給予ssh金鑰|

* [鳥哥一下 : [第11章、遠端連線伺服器 SSH/XDMCP/VNC/RDP]](http://linux.vbird.org/linux_server/0310telnetssh.php#scp)


## 觀念
### 關於固定IP與浮動IP
真實IP, 實體IP, 虛IP, 假的IP, 其實沒有分沒有那麼多種，在IPv4裡面，只有兩種IP類別，其他你聽到的基本上是IP的取得方式
<br>
  * Public IP : 公共IP，經由INTERNIC所統一規劃的IP，有這種IP才能上Internet
  * Private IP : 私有IP或是保留IP，不能直接連上Internet的IP，主要用於區域網路內的主機連線規劃

IPv4規劃時就擔心IP會不足，而且為了應付某些企業內部的網路設定，於是有了私有IP的產生，私有IP也分別在A, B, C三個Class中個保留一段作為私有IP網段

  * Class A : 10.0.0.0 - 10.255.255.255
  * Class B : 172.16.0.0 - 172.31.255.255
  * Class C : 192.168.0.0 - 192.168.255.255

### 還有一個特殊網段 loopback IP

  * 127.0.0.1 : 內部迴圈網路 當你要測試你的TCP/IP封包與狀態是否正常時，就可以使用
* 所謂ip取得方式 

### 所以主機的IP是如何設定的?

  * 直接手動設定(static) 所謂的固定ip : 你可以直接向你的網管詢問可用的ip相關參數，然後直接編輯設定檔(或使用某些軟體功能來設定你的網路)。常見於校園網路環境、或是自架封閉區網、或是像ISP申請固定ip的連線環境
  
  * 撥接取得 : 向你的ISP註冊，取得帳號密碼後，直接撥接到ISP，你的ISP或透過他自己的設定，讓你的作業系統取得正確的網路參數。此時你**不用手動編輯與設定相關的網路參數**。目前台灣的ADSL，光纖到大樓、光纖到府，大部分都是使用撥接方式。為了因應客戶需求，某些ISP也提供很多不同的IP分配機制。包含hinet, seednet等都有提供ADSL撥接後取得固定ip的方式，詳情可以向自己的ISP洽詢XD。

  * 自動取得網路參數(DHCP - Dynamic Host Configguration Protocol) 所謂浮動ip : 在區域網路內會有一部主機負責管理所有電腦的網路參數，**你的網路啟動時，就會主動向伺服器要求IP參數**，若取得網路相關參數後，你的儲機就能夠自行設定好所有伺服器給你的網路參數。最常使用於企業內部、IP分享器後端、校園網路與宿舍環境、纜線寬頻等連線方式。

* [鳥哥一下 - 第二章、網路基礎概念 2.3.3 IP的種類與取得方式](http://linux.vbird.org/linux_server/0110network_basic.php#tcpip_network_type)

* [terminal 設定固定ip](https://blog.toright.com/posts/6293/ubuntu-18-04-%E9%80%8F%E9%81%8E-netplan-%E8%A8%AD%E5%AE%9A%E7%B6%B2%E8%B7%AF%E5%8D%A1-ip.html)

<br>

### 總結 : 

  > 只有公共IP和私有IP，固定IP及浮動IP指的是取得方式，手一揮，假的

### 即時串流相關協定
* 動機 : 在樹莓派上利用TPU做Edge Computing，將結果及影片即時串流回主機Server，原本使用base64將 video image 壓縮成base64字串，在Client端再用based64 decode，但這個方法扔然會延遲。
* 概念 : 回想一下我們對影片的上傳下載，由於多媒體檔案通常都比較大，所需要的儲存容量也大，由於網路頻寬的限制，下載多媒體檔案都需要花費數分鐘甚至數小時，所以如果是這種下載方法來傳送多媒體檔案的延遲會非常大
* 串流(Streamming)的概念，只將開始部分存入記憶體，資料流隨時傳送隨時播放，只會在開始時有一些延遲，中間過程通常都會非常順暢，下列為網路串流發展的一些歷史 : 
 
|協定|解釋|
|---|----|
|RTP(Real-time Transport Protocol) 即時傳輸協議|串流協定剛開始發展的協定之一|
|RTMP：即時傳輸訊息協議(Real Time Messaging Protocol)|被Flash用於物件, 影片, 音訊傳輸, 這個協定建立在TCP或是HTTP之上，由於被包裹在TCP裏頭，實作出來畫質還可以，但延遲時間有2秒左右，對於Realtime的要求來說還是較長。|
|RTSP：即時串流協議(Real Time Streaming Protocol)|除了控制聲音或是影像外，還可以允許同時多個串流需求控制，並且可以自己選擇要用TCP或是UPD來進行串流內容，在這個Protocol中可以傳送播放、錄製、暫停等等控制協定、蠻像遙控錄影機的。|
* 上述中的RTSP也是我們採用的協定，[其Wiki在這裡](https://zh.wikipedia.org/wiki/%E5%8D%B3%E6%99%82%E4%B8%B2%E6%B5%81%E5%8D%94%E5%AE%9A)
* 套件實作 GStreamer, based on C，可以使用樹莓派硬體進行H.264編碼，串流品質優

# Permission

|命令|使用場景|備註|
|---|-------|---|
|`chmod`|更改檔案權限|需要惡補一下檔案權限的部分|

# Backup, packing, unpacking

|命令|使用場景|備註|
|---|-------|---|
|`dd`|備份磁碟、樹莓派microSD|可以調整寫入速度|
|`gzip`|壓縮、解壓縮||
|`zip` `unzip`|壓縮、解壓縮||
|`tar`|打包、解包| 打包/解包 實際上並沒有壓縮，檔名是.tar 即打包解包, 檔名是tar.gz 表示有壓縮|
|`tar xvf filePath -C dirpath`|解包, 解壓縮(加z)||


# Shell script
## 動機
編寫一個bash script
使用場景 : 其實跟python script一樣，當你的bash指令太長，或是一次要執行3個指令，就直接開一個 fileName.sh
例如 : 要定時備份、要定時check model performance然後retrain、每天的半夜retrain等等
效益 : 部署時常常使用的機器你也不知道哪哪個型號，但是是Unix like，基本上bash和vi這時候就會是你的武器，畢竟，你也不知道上面到底有沒有裝python
## 再多一點概念
基本上就是早期DOS年代的批次檔(.bat)，基本上就是把一堆指令堆在一起，
而且其實 shell script也提供陣列、迴圈、條件、邏輯判斷等，很多場景下寫起來會更快解決問題，同樣的，也不用編譯

vi example.sh
```
#!/bin/bash
echo "Start"

# 平行處理多個工作
sleep 3 && echo "Job 1 is done" &
sleep 2 && echo "Job 2 is done" &
sleep 1 && echo "Job 3 is done" &

# 等待所有工作完成
wait

echo "Done"
```

[鳥哥一下 - 第12章、學習 Shell Scripts](http://linux.vbird.org/linux_basic/0340bashshell-scripts.php#script_why)

# 環境變數/路徑 PATH

# pipe
* [鳥哥 第十章 10.6 管線命令, 6-1, 6-2](http://linux.vbird.org/linux_basic/0320bash.php)
* pipe是幹嘛的? bash命令執行的時候會有輸出資料出現，如果你要的資料需要經過幾道手續之後才會得到我們要的格式
  就要透過pipe來設定了，管令命令用的是 **|** 這個界定符號，另外，管線命令與 **連續下達命令**是不一樣的，下面會說明，不過我們先來舉個例子
> 我們想要知道 /etc/ 底下有多少檔案，那麼可以利用 ls / etc 來查閱，不過，因為 /etc底下的檔案太多，導致於一口氣就將螢幕塞滿了，不知道前面輸出的內容是啥，我們可以透過less指令來協助
`ls -al /etc | less`
這樣我們就能夠翻頁來查找剛剛吐出來的訊息了，同樣的道理，你也可以使用 `ls -al /etc | head -n 5`
### pipe做了什麼?
其實這個管線命令，**僅能處理經由前面一個指令傳來得正確資訊，也就是 standard output，對於 standard error並沒有直接處理的能力**

<img src = './images/bash_pipe_1.png'></img>

* 在每個管線後面接的第一個資料必定是**命令**才行，例如 `less` `more` `head` `tail` 等都是可以接受 standrar input，至於`ls`, `cp`, `mv`就不是管線命令了，因為後面這3個命令並不會接受 stdin的資料
* 管線命令僅會處理 standard output，對於 standard error output 會予以忽略
* 管線命令必須要能夠接受來自前一個指令的資料成為 standard input 繼續處理才行。

### 擷取命令 cut, grep
* cut : 切
`cut -d '分隔字元' -f fields <--- 用於有特定分隔字元`
`cut -c '字元區間' <--- 用於排列整齊的訊息`
```
E.g 1 將PATH取出，第5個路徑
echo ${PATH} <--- 叫出路徑
echo ${PATH} | cut -d ':' -f 5 <---  叫出路徑，然後切出第5個
E.g 2 將 export 輸出的訊息，取得第 12 字元以後的所有字串
export | cut -c 12-
```
cut這樣的功能在看log的時候特別實用
* grep : 剛剛的 cut 是將一行訊息當中，取出某部分我們想要的，而 grep 則是分析一行訊息， 若當中有我們所需要的資訊，就將該行拿出來～簡單的語法是這樣的：

```
grep -[acinv] [--color=auto] '搜尋字串' filename
-a ：將 binary 檔案以 text 檔案的方式搜尋資料
-c ：計算找到 '搜尋字串' 的次數
-i ：忽略大小寫的不同，所以大小寫視為相同
-n ：順便輸出行號
-v ：反向選擇，亦即顯示出沒有 '搜尋字串' 內容的那一行！
--color=auto ：可以將找到的關鍵字部分加上顏色的顯示喔！

E.g 1 : 將last當中，有出現root就取出來
last | grep 'root'
E.g 2 : pip list中，有tensorflow就取出來
pip list | grep 'tensorflow'
E.g 3 將last中，"沒有" root的取出來
last | grep -v 'root'
E.g 4 在last中，只要有root就取出，而且只取取第1欄
last | grep 'root' | cut -d '' -f1
E.g 5 取出 tec/man_db/conf 內含有MANPATH得那幾行
grep --corlor=auto 'MANPATH' /etc/man_db.conf
```
`grep`是個非常好用的命令，基本上就是一行的if-else, 用在正規表達式裡面更是一絕，值得學習

# 資料流重定向
* [鳥哥 第十章 10.5 資料流重定向](http://linux.vbird.org/linux_basic/0320bash.php)
* 什麼是資料流重定向? : 把資料傳導到其他地方去，也就是不從顯示在terminal, 可以傳到檔案裡面, 或是印表機, 等等

<img src = './images/bash_dataflow_redirection_1.png'></img>

## stdout, stderr
* 如上圖，我們執行一個指令時，這個指令會由檔案讀入資料，處理後，輸出到螢幕上，分別為**標準輸出(STDOUT)**，以及**標準錯誤輸出(STDERR)**
我們能不能透過某些機制將這兩股資料分開呢? 當然可以，這就是所謂資重定向


|概念|用法|
|---|----|
|標準輸入 (stdin )|代號 0, 使用 < 或是 <<|
|標準輸出 (stdout)| 代號 1, 使用 > 或是 >>|
|標準錯誤輸出(stderr)|代號 2, 使用 2> 或是 2>>|

例如 : `ls > test.txt`
資料就會盡到`test.txt`
* 注意 : 如果系統原本沒有該檔案，會新開出來寫，但是如果已經存在，就會覆寫
* 這時候要使用累加， `>>`
* 將正確資料與錯誤訊息分流 `ls ~/Desktop/aaa 1>> correct.txt 2>> error_log.txt`
* 黑洞裝置 : /dev/null 可以吃掉任何東西 `find /home -name .bashrc 2> /dev/null`

## stdin 
* <, << 將原本需要由鍵盤輸入的資料，改由檔案內容來取代
`echo 'ls ~/Desktop' > showDesktop.txt`

需要stdin, stdout, stderr的場景
1. 螢幕輸出訊息很重要，我們需要將它存下來時
2. 背景執行中的城市，不希望他干擾螢幕正常輸出結果時
3. 系統例行命令希望可以存下來時(例如crontab)
4. 一些執行命令可能已知錯誤訊息，想以 2>/dev/null丟掉時
5. 錯誤訊息和鄭瘸訊息需要分別輸出時

## 命令執行的判斷依據 : ; && ||

|指令|舉例|說明|
|----|--|----|
|cmd1; cmd2; cmd3|sync; sync; shotdown -h now|先執行兩次sync同步寫入磁碟，然後關機，輸出的commend有前後依賴關係時|
|cmd1 && cmd2|ls /tmp/abbc && touch /tmp/abc/hehe| ls指令執行完畢而且正確執行($?=0)，則開始執行cm2，cmd1執行完畢且為錯誤($?!=0)，則cmd2不執行|
|cmd1 || cmd2|ls /tmp/abbc && touch /tmp/abc/hehe| 和上面一個反過來|

linux命令是由左至右的，所以`&&`和`||`的位置不要放反喔!



# Job Control 工作控制

|命令|使用場景|備註|
|---|-------|---|
|`nohup`|放到主機算，然後自己登出，搭配 `ctrl + z`, `bg` |nohup = no hang up|
|`bg`|把執行緒丟到後台，搭配nohup||
|`fg`|把執行緒拉回前台，佔住自己的terminal XD||
|`kill`|根據Job ID 把程式殺掉||
|`wait`|等待某Job跑完，或是並行處理完||

# file profiling  
|命令|使用場景|備註|
|---|-------|---|
|cat|檔案不大，覺得vi很難操作，複製程式碼時|cat會看整個檔案|
|less|一頁一頁看，而且可以往前翻頁|忘記less怎麼用的時候，less file 然後 h，基本操作 f - forward, b - backword, 可以設定一次看幾行|
|head|看個前幾行，意思一下，跟dataframe.head()一樣||
|tail|看個後面幾行，意思一下，跟dataframe.head()一樣||
|echo|叫一段文字、或是叫一個檔案、叫環境變數|怎麼叫環境變數? echo $PATH|
|tocuh|叫一個檔案，或是創建一個檔案||

# System profiling
|命令|使用場景|備註|
|---|-------|---|
|top|最簡易版的工作管理員，可以看CPU，跟thread還有ProcessID|10秒更新，都黑白，可以用htop|
|htop|豐富版，支援filter，自動提示，排序，比較美等等|Mac :brew install htop-osx|
|df - h|Dsik Free，顯示磁碟空間資訊||
|uname -a|顯示系統核心資訊||
|w|顯示上線使用者清單|可以在Server現在有誰連，有沒有怪人這樣|
|whoami|顯示目前使用者名稱||
|free|顯示記憶體與Swap區的用量||

[htop使用](https://blog.gtwang.org/linux/linux-htop-interactive-process-viewer-tutorial/)

# 快捷鍵
**GNU bash，版本 4.4.23(1)-release (x86_64-apple-darwin16.7.0)**

|命令|說明|
|---|---|
|ctrl + A|移到行首|
|ctrl + Ｅ|移到行尾|
|ctrl + W|清空畫面|
|ctrl + U|從光標刪除到字首|
|ctrl + k|從光標刪除到字尾|
|ctrl + XX|在命令列首和光標之間移動|