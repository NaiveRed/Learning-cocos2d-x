
Cocos2d-x learning journal
=========================
**NOTE**:這只是我學習Cocos2d-x的紀錄順便學學Markdown，大多是自己的理解和筆記，因此正確性不敢保證，網路上有更好的教學網站!如果內容有錯還煩請告知。

資源 : [API文件](http://www.cocos2d-x.org/reference/native-cpp/V3.2alpha0/index.html)、[官網文件](http://www.cocos2d-x.org/docs/README)

目前使用書籍: 《同時成為iOS/Android開發大師：使用Cocos2d-x及C++》

- - -

1. [環境準備](#environment)
2. [專案創建](#creatNew)
3. [Hello World](#helloWorld)
4. [核心概念](#coreClasses)
5. [計時器](#scheduler)
6. [位置](#position)
7. [座標系](#coordinates)
8. [Log](#log)
9. [字串](#string)
10. [標籤](#label)
11. [選單](#menu)
12. [精靈](#sprite)
13. [場景與層](#sceneAndLayer)

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

OpenGL 和 UI 座標也有轉換的 function。

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
   
<h2 id="log">Log</h2>
[vsLog]:resource/logPosition.jpg
可以在debug的時候使用，滿方便的，應該是可以輸出到console上，不過還不曉得怎用。

`log("%s update %d ",__TIME__, num++);`

輸出結果:

![vs-log][vsLog]

<h2 id="string">字串</h2>

除了C中使用的`char *` 和 C++ 的`std::string`，在cocos2d-x裡還有一種`cocos2d-x::__String`。

假設使用`std::string *str`就要搭配new來使用，不使用時須delete(或是利用smart pointer)。

cocos2d-x::__String可以利用`create`和`createWithFormat`，
則不用管理記憶體的釋放，e.g.

	__String *str1(__String::create("HelloWorld!"));
	__String *str2(__String::createWithFormat("%d %s",123,"HelloWorld"));
	
而在這三種字串中也有互相轉換的function，就不再詳細說明了。

<h2 id="label">標籤</h2>

在遊戲中如要顯示文字可以利用標籤，也就是Label，像是[HelloWorld](#helloWorld)中的圖片內，上方一行HelloWorld。

在cocos2d-x 2內有LabelTTF,LabelAtlas,LabelBMFont三種class，這裡介紹cocos2d-x 3.x中新的class Label。

* **系統原生字體**

		Label* Label::createWithSystemFont(
		const std::string& text,                                    //字串內容
		const std::string& font, 									//字體
		float fontSize, 											//字體大小				
		const Size& dimensions /* = Size::ZERO */,  				//Label在螢幕上佔的大小
		TextHAlignment hAlignment /* = TextHAlignment::LEFT */, 	//水平對齊方式
		TextVAlignment vAlignment /* = TextVAlignment::TOP */		//垂直對齊方式
		)
		
* **TTF**

	正常創建:

		Label* Label::createWithTTF(
		const std::string& text, 
		const std::string& fontFile, 								//字體檔案(*.ttf)
		float fontSize, 
		const Size& dimensions /* = Size::ZERO */, 
		TextHAlignment hAlignment /* = TextHAlignment::LEFT */, 
		TextVAlignment vAlignment /* = TextVAlignment::TOP */
		)

	使用TTFConfig:
		
		Label* Label::createWithTTF(
		const TTFConfig& ttfConfig, 								//TTFConfig		
		const std::string& text, 
		TextHAlignment alignment /* = TextHAlignment::CENTER */, 	
		int maxLineWidth /* = 0 */									//最大行寬，可用來設定自動換行
		)

	TTFConfig:
		
		typedef struct _ttfConfig
		{
		std::string fontFilePath;									//字體檔案
		int fontSize;											

		GlyphCollection glyphs;										//字型庫(字符集)
		const char *customGlyphs;									//自訂字型庫
		
		bool distanceFieldEnabled;									//理解為 字體是否緊湊
		int outlineSize;											//字型描邊

		_ttfConfig(const char* filePath = "",
		int size = 12, 
		const GlyphCollection& glyphCollection = GlyphCollection::DYNAMIC,
		const char *customGlyphCollection = nullptr,
		bool useDistanceField = false,
		int outline = 0)
		:
		fontFilePath(filePath),
		fontSize(size),
		glyphs(glyphCollection),
		customGlyphs(customGlyphCollection),
		distanceFieldEnabled(useDistanceField),
		outlineSize(outline)
		
		{
			if(outline > 0)
			{
				distanceFieldEnabled = false;
			}
		}
		}TTFConfig;
				
e.g.
	
	auto label1(Label::createWithTTF("HelloWorld!", "fonts/Marker Felt.ttf", 60));
	
	TTFConfig ttf("fonts/Marker Felt.ttf", 60);
	ttf.outlineSize = 4;
	auto label2(Label::createWithTTF(ttf, "HelloWorld!"));

此外還有 **createWithCharMap** 和 **createWithBMFont**  (詳細可查閱document或看CCLabel.h內的註解)。

createWithCharMap的字型資源來自*.png(還有其他種)，還須設定每個字元的寬高。

而createWithBMFont則是使用\*.fnt，要先添加好字體檔案，包含一個圖片集檔案(\*.png)和字型座標檔案(\*.fnt)，檔案名稱須相同。

接著Label有一些效果:

shadow(陰影)、Glow(螢光)、OutLine(描邊)

p.s. OutLine 與 Glow 貌似會起衝突

e.g.:

	label->enableOutline(Color4B::RED, 4);								//第一個參數為邊的顏色，第二個為大小
	label->enableShadow(Color4B(91, 0, 174, 128), Size(3, -10));       	//第一個參數為顏色，第二個為陰影的相對位置(實際更改一次就可以明白了)
	label->enableGlow(Color3B::GREEN);									//螢光為綠色	

	label->disableEffect();												//取消效果

這裡稍微補充一下常常用到的Color3B,color4B就差在有沒有透明度，RGBA，A:255為完全不透明

Label還有許多的function可使用例如`label->setTextColor(Color4B(255, 0, 0,128))`

可以從[這裡](http://www.cocos2d-x.org/reference/native-cpp/V3.2alpha0/db/de4/classcocos2d_1_1_label.html)得到更多資訊!


<h2 id="menu">選單</h2>
[menuUml]:resource/menuUml.png

![menu][menuUml]

先講一下目前的理解:

「Menu 繼承了 Layer，而選單中又包含了選單項目(Node <- MenuItem)，所以操作上，要把 MenuItem 加進 Menu，Menu 再加進 父節點。

一個場景包含多個層，而層上又有許多東西(e.g. Sprite、Menu、Label...) ，都是Node，然後這裡的關係大概是 MenuItem 加進 Menu，Menu 再加進 Layer。」
		
[官網文件](http://www.cocos2d-x.org/docs/manual/framework/native/v3/menu/zh)也有介紹。

* **文字選單:**

MenuItemFont、MenuItemAtlasFont、MenuItemLabel 裡面的參數可從程式碼註解或[API文件](http://www.cocos2d-x.org/reference/native-cpp/V3.2alpha0/index.html)得知。

舉最簡單的例子:
	
	MenuItemFont* onItem = MenuItemFont::create("on", CC_CALLBACK_1(HelloWorld::menuItemcallBack1,this));
	//Menu *menu(Menu::create(onItem, NULL));//記得NULL!!
	//menu->setPosition(Vec2::ZERO);
	
	menu->addChild(onItem);
	this->addChild(menu,1);

在創建MenuItem時須設定點選後發生的事，也就是參數列中的callback function，在這裡利用macro: `CC_CALLBACK_1` 中產生 ，`HelloWorld::menuItemcallBack1`則是所用到的function，
當然，我們需要在HelloWorld中寫一個menuItemcallBack1的function:
	
	void menuItemcallBack1(cocos2d::Ref* pSender)
	{
	//do something...
	}
	
至於CC\_CALLBACK\_1的詳細用法和其餘(CC\_CALLBACK\_0 1 2 3)看了很多講解還是不太懂，猜測是跟參數數量有關，
後面如果了解了再說明。

p.s 官網文件中使用的方法是Lambda運算式。

* **精靈選單**(Sprite)

精靈選單的選單項目類別是MenuItemSprite，其中還有一個衍生類別為MenuItemImage(圖片選單)。

使用MenuItemSprite時，要先建立欲使用的精靈(e.g. `Sprite *normalSprite = Sprite::create("menu/normal.png")`)

e.g.

	MenuItemSprite * MenuItemSprite::create(
	Node *normalSprite,					//項目正常時的精靈
	Node *selectedSprite,				//項目被選擇時的精靈
	Node *disabledSprite,				//項目禁用時的精靈，可省略
	const ccMenuCallback& callback		//callback 
	)
	
	//MenuItemImage則是用圖片創建
	MenuItemImage * MenuItemImage::create(
	const std::string& normalImage,
	const std::string& selectedImage,
	const std::string& disabledImage
	)
	
* **切換選單**(Toggle)

開關選單的類別是 MenuItemToggle ，可以進行多種狀態的切換。(例如:聲音的開關)

其中一種建立方式:

	MenuItemToggle * MenuItemToggle::createWithCallback(
	const ccMenuCallback &callback				//欲操作的callback 
	, MenuItem* item,							//進行切換的MenuItem
	...
	)

	//官網範例
	auto item6 = MenuItemToggle::createWithCallback(
	[&](Ref*){ log("Toggle touched!");},			//lambda expression ， 我們也可以用CC_CALLBACK_1來做
	MenuItemFont::create("On"),						//MenuItem
    MenuItemFont::create("Off"),
	NULL
	);

還有一些Menu的function介紹和效果可以從官網文件中學習!
	
p.s 關於一些可變參數(`void func(...);`)的用法可以再自行查閱。

<h2 id="sprite">精靈</h2>

前面有用到一些精靈，這裡再稍微記一下他的幾個用法。

圖片:

`static Sprite* create(const std::string & filename)`

`static Sprite* create(const std::string & filename,const Rect & rect )`	

紋理:

`static Sprite* createWithTexture(Texture2D * texture)`

`static Sprite* createWithTexture(Texture2D * texture,const Rect & rect,bool rotated = false )`

紋理的`Rect(float x,float y,float width,float height)` ，x、y為紋理圖片的UI座標，而width、height則是所選區域的寬高了。

關於**紋理圖集**(Texture Atlas):

也稱作精靈表(Sprite Sheet)，大致上是把許多小的精靈圖片組合到一張裡面去，我們可以利用工具(如:Zwoptex、TexturePacker)
產生出紋理圖集檔案(如:png)和紋理圖集座標(plist)。

而除了上面所提到的方法，我們還可以利用**紋理快取**和**精靈影格快取**:

紋理快取(TextureCache):
e.g.

	Texture2D* cache = Director::getInstance()->getTextureCache()->addImage("texture.png");	
	Sprite* car = Sprite::create();
	car->setTexture(cache);
	car->setTextureRect(Rect(10, 15, 150, 150));

精靈影格快取(SpriteFrameCache):
e.g.

	SpriteFrameCache::getInstance()->addSpriteFramesWithFile("SpriteSheet.plist");
	Sprite* car = Sprite::createWithSpriteFrameName("car.png");

其中car.png的詳細資訊則是在SpriteSheet.plist(一種XML檔案)中。
當然還有許多建立快取的function可再自行查閱。

此外當不再使用精靈快取後，需要進行移除的動作!

相關remove的函數: `void removeSpriteFrames()` ...

例如:我們可以override HelloWorld的onExit()
	
	void HelloWorld::onExit()
	{
	Layer::onExit()		//下一章節會提及
	SpriteFrameCache::getInstance()->removeSpriteFrames();
	}

<h2 id="sceneAndLayer">場景與層</h2>
	
場景與層的關係為一對多，一個場景有很多層(至少一個)，而場景不需要重新編寫子類別，一般情況下，一個場景需要一個層，而層是需要子類別化的，也就是編寫一個衍生自Layer的類別。
從這邊也就可以知道，我們能透過 override 層的生命週期function，來處理場景在不同階段的事件，待會會再做說明。

關於切換場景有幾個相關的function:
`void replaceScene(Scene* scene)`、`void pushScene(Scene* scene)`、`void popScene()`、`void popToRootScene()`

e.g.

	//Cover衍生自Layer
	auto cover = Cover::createScene();
	Director::getInstance()->replaceScene(cover);
	//Director::getInstance()->pushScene(cover);
	
	//回到上一個場景
	Director::getInstance()->popScene();
	
我們也可以建立過場動畫:

e.g.
	
	auto cover = Cover::createScene();
	auto transSc = TransitionJumpZoom::create(1.0f,cover);//(持續時間,場景物件)
	Director::getInstance()->pushScene(tranSc);//非使用場景物件，而是傳過場動畫的物件
	
還有許多相關過場動畫，這裡不多作紀錄了。

而上述`void replaceScene(Scene* scene)`、`void pushScene(Scene* scene)`有些不同，使用replace會將目前的場景給釋放，而push會暫停並放入場景的堆疊中。
在這裡說明一下他的生命週期函數的執行順序。

Layer的生命週期函數(繼承了Node):

	bool init();//初始化
	void onEnter();//層進入時呼叫
	void onEnterTransitionDidFinish();//進入層且過場動畫結束時呼叫
	void onExit();//退出層時呼叫
	void onExitTransitionDidStart();//退出層且開始過場動畫時呼叫
	void cleanup();//被清除釋放時呼叫
	
以下"->"表示為順序:

場景被啟動時: init() -> onEnter() -> onEnterTransitionDidFinish()

Now為目前場景，pushScene(After): 

After::init() -> Now::onExitTransitionDidStart() -> After::onEnter() -> Now::onExit() -> After::onEnterTransitionDidFinish()

replaceScene(After):

After::init() -> Now::onExitTransitionDidStart() -> After::onEnter() -> Now::onExit() -> After::onEnterTransitionDidFinish() -> Now::cleanup()

Before為先前場景，pop: 

Now::onExitTransitionDidStart() -> Now::onExit() -> Now::cleanup() -> Before::onEnter() -> Before::onEnterTransitionDidFinish()

可以看出push和replace有差在是否cleanup，而pop沒有在init一次之前的場景。

而在使用上我們可以在編寫層的子類別時，對生命週期函數進行override。

e.g.
	
	void HelloWorld::onExit()
	{
	Layer::onExit()		//記得呼叫父類別的函數
	SpriteFrameCache::getInstance()->removeSpriteFrames();
	}
	