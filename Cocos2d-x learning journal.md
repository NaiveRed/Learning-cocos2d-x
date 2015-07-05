
cocos2d-x learning journal
=========================
**NOTE**:這只是我學習Cocos2d-x的筆記順便學學Markdown，大多是自己的理解，因此正確性不敢保證，如果有錯煩請告知，
網路上有更好的教學網站!

- - -

1. [環境準備](#environment)
2. [專案創建](#creatNew)
3. [Hello World](#helloWorld)
4. [核心概念](#coreClasses)
5. [計時器](#scheduler)
6. [位置](#position)
7. [座標系](#coordinates)

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

<h2 id="coreClasses">核心概念</h2>

Cocos2d-x採用樹狀結構來管理Scene,Layer,Sprite,Menu...等物件(Node)，
就不詳細介紹Director,Scene,Layer...等class的架構和類別圖了，詳細可從[這裡](http://www.cocos2d-x.org/reference/native-cpp/V3.2alpha0/index.html)查詢!

<h2 id="scheduler">計時器</h2>

大致上就是可以對Sprite等物件來進行排程

可以透過Access物件的Tag `label->setTag(42)` `this->getChildByTag(42)`，然後使用

`void scheduleUpdate(void)` 或 `void schedule(SEL_SCHEDULE selector,float interval)` 來進行每次的更新(猜測:跟fps有關)
，在欲進行刷新的物件中新增`update(float dt)` function，記得不再使用排程時需要停止排程，可呼叫`unscheduleUpdate()`就是停止剛剛新增的update function，
如果是要停止其他的函式可以用unschedule或unscheduleAllSelectors停止。

<h2 id="position">位置</h2>

為了使目標更精準的放置在想要的位置上，就有了錨點(Anchor Point)這個東西，可以當作Node及其衍生物件的定位點，而Anchor Point是相對於position的比例，
如: `Vec2(0.5,0.5)` 就是此對象的中間。

p.s 可透過更改錨點和設定位置，會更加了解。

<h2 id="coordinates">座標系</h2>

[CartesianCoordinates]:resource/CartesianCoordinates.png

Cocos2d-x的座標系統採用笛卡爾座標系中的**右手座標系統**，左下角為原點，向右為x正，向上為y正，z軸指向螢幕外。

笛卡爾座標系(左手/右手):

![笛卡爾座標系][CartesianCoordinates]

以下介紹幾種座標系:

* **OpenGL 座標**

使用右手座標系。(Cocos2d-x底層採用OpenGL繪製)

* **UI 座標**

也稱為屏幕座標系統，原點在左上角，向右為x正，向下為y正，
在觸控事件或某些時候會用到。

* **世界座標和相對座標**

也有看到相對座標又叫做本地座標、模型座標，大致上的理解是世界座標為絕對位置，而相對座標則是以另一Node物件為基準，
兩者中又分為是不是以AnchorPoint為基準，怕我認知有錯，詳細可再去google。

**轉換的方法**

這部分花了不少時間看例子和理解，雖然還有點不知所以...
直接用例子來說明:

官網例子:

	Sprite *sprite1 = Sprite::create("CloseNormal.png");
	sprite1->setPosition(Vec2(20,40));
	sprite1->setAnchorPoint(Vec2(0,0));
	this->addChild(sprite1);

	Sprite *sprite2 = Sprite::create("CloseNormal.png");
	sprite2->setPosition(Vec2(-5,-20));
	sprite2->setAnchorPoint(Vec2(1,1));
	this->addChild(sprite2);

	Vec2 point1 = sprite1->convertToNodeSpace(sprite2->getPosition());
	Vec2 point2 = sprite1->convertToWorldSpace(sprite2->getPosition());
	Vec2 point3 = sprite1->convertToNodeSpaceAR(sprite2->getPosition());
	Vec2 point4 = sprite1->convertToWorldSpaceAR(sprite2->getPosition());

	log("position = (%f,%f)",point1.x,point1.y);
	log("position = (%f,%f)",point2.x,point2.y);
	log("position = (%f,%f)",point3.x,point3.y);
	log("position = (%f,%f)",point4.x,point4.y);
	
結果:
	
	position = (-25.000000,-60.000000)
	position = (15.000000,20.000000)
	position = (-25.000000,-60.000000)
	position = (15.000000,20.000000)
	
convertToNodeSpace: 將世界座標轉為相對座標，轉換方式: **以sprite1的左下角為原點**，重新計算新的座標。

convertToWorldSpace: 將sprite2的位置座標轉為世界座標，轉換方式: sprite1的位置不變，世界座標的座標軸也不變，
**以sprite1的左下角頂點再建立一個新的座標系(本地座標)，將sprite2假設放置到新建的這個座標系(座標不變)，然後以原來的世界座標為參考重新計算sprite2的位置**。

convertToNodeSpaceAR: 將世界座標轉為相對座標，轉換方式: **以sprite1的錨點為原點**，重新計算新的座標。

convertToWorldSpaceAR: 將sprite2的位置座標轉為世界座標，轉換方式: sprite1的位置不變，世界座標的座標軸也不變，
**以sprite1的錨點再建立一個新的座標系(本地座標)，將sprite2假設放置到新建的這個座標系(座標不變)，然後以原來的世界座標為參考重新計算sprite2的位置**。
   




