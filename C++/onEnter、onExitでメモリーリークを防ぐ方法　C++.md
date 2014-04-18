onEnter、onExitでメモリーリークを防ぐ方法　C++

    //上記コードの以下のメソッドをかえる
    bool MyLayer::init(){
       if(!CCLayer::init()) return false; //CCLayerの初期化
       return true;
    }
 
    void MyLayer::onEnter(){
       if(m_mySpriteAry != NULL) return;
       m_mySpriteAry = CCArray::create();
       m_mySpriteAry->retain(); //解放されないようにretain()しとかなきゃね！
       for(int i = 0; i < 10; i++){
          MySprite* spr = MySprite::create(this);
          m_mySpriteAry->addObject(spr);
       }
     }
 
    void MyLayer::onExit(){
       CC_SAFE_RELEASE(m_mySpriteAry);
       m_mySpriteAry = NULL;
       removeAllChildren();
    }
 
    //デストラクタ
    MyLayer::~MyLayer(){
    }