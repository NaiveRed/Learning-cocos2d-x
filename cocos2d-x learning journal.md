
cocos2d-x learning journal
=========================
**NOTE**:這只是我學習cocos2d-x的筆記順便學學markdown，因此正確性不敢保證，
網路上有更好的教學網站!

- - -

1. [環境準備](#environment)
2. [專案創建](#creatNew)
3. [Hello World](#helloWorld)

- - -

<h2 id="environment">環境準備</h2>

先去[官網](http://www.cocos2d-x.org/download)下載cocos2d-x (我用的是v3.5)
，下載解壓縮後可以看到裡面有個 **setup.py**，需要去下載python來完成安裝
，先到python[官網](https://www.python.org/downloads/)下載符合你電腦版本的python2.7+(不確定3.x的行不行)，安裝好後去設定系統變數或是command cd到python的資料夾，接著安裝cocos2d-x，
在command輸入:

`python setup.py`  (會因你setup.py的位置而有差異，可將setup.py直接改成檔案路徑)

過程會有  NDK_ROOT、ANDROID_SDK_ROOT、ANT_ROOT 這三個是跟Android有關的，暫且無視他按Enter就好，完成安裝，它也會幫你設置好cocos2d-x的系統變數。

<h2 id="creatNew">專案創建</h2>

在command上輸入cocos可以看到相關指令，現在再輸入
`cocos new -h` (看創建說明)，依照說明在桌面創一個新的專案:

`cocos new -l cpp -d C:\Users\User\Desktop MySimpleTest` 

打開資料夾可以看見
Win 32、iOS、Android、win8 和 Linux 5種專案平台(詳細說明可自行搜尋)

我們選win32的然後用Visual Studio 2013 開啟專案檔。

<h2 id="helloWorld">Hello World</h2>
[gHelloWorld]: resource/HelloWorld.jpg
在專案檔內可以看見有
AppDelegate.* 為程式引擎啟動的code，再由main裡來呼叫run() function
`Application::getInstance()->run()`

HelloWorldScene.cpp內
`bool HelloWorld::init()` 有詳細的說明註解
，接著build and run即可。

輸出結果:
![輸出結果][gHelloWorld]
    




