循環参照によるメモリリーク対策　C++

    //レイヤー
    class MyLayer : public CCLayer{
       CCArray* m_mySpriteAry;
    public:
       CREATE_FUNC(MyLayer);
       virtual bool init();
       virtual ~MyLayer();
    };
 
    //スプライト
    class MySprite : public CCSprite{
    MyLayer* m_myLayer;

    public:
       static MySprite* create(MyLayer* $myLayer);
       virtual bool init(MyLayer* $myLayer);
       virtual ~MySprite();
    };
 
    /**
     *  ここからソースファイル
     *  まずはMySpriteから実装(順番に意味はないですよ)
     */
 
    MySprite* MySprite::create(MyLayer* $myLayer){
        MySprite* pRet = new MySprite();
        if(pRet && pRet->init($myLayer)){
           pRet->autorelease(); //これ忘れるとメモリリークするよ
           return pRet;
        }
         CC_SAFE_DELETE(pRet);
         pRet = NULL;
         return NULL;
    }
 
    bool MySprite::init(MyLayer* $myLayer){
      if(!CCSprite::init()) return false;  //CCSpriteの初期化
        m_myLayer = $myLayer;
        m_myLayer->retain();  //解放しないようにretain()しとかなきゃね！
       return true;
    }
 
    //デストラクタ
    MySprite::~MySprite(){
       CC_SAFE_RELEASE(m_myLayer); //m_myLayerをrelease()
    }
    /**
     * ここからMyLayerの実装
     */
    bool MyLayer::init(){
       if(!CCLayer::init()) return false; //CCLayerの初期化
       m_mySpriteAry = CCArray::create();
       m_mySpriteAry->retain(); //解放されないようにretain()しとかなきゃね！
       for(int i = 0; i < 10; i++){
           MySprite* spr = MySprite::create(this);
           m_mySpriteAry->addObject(spr);
       }
        return true;
      }
 
    //デストラクタ
    MyLayer::~MyLayer(){
      CC_SAFE_RELEASE(m_mySpriteAry); //m_mySpriteAryをrelease()
    }
