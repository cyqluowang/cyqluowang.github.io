title: android图片飘动
date: 2014-11-08 20:16:13
tags: Android
description: 图片浮动效果，感觉在飘
---

## 思路
1. 首先定义图片的宽度和高度
如果父类的宽度和高度是固定的，那么图片的宽度和高度要比父类大
2. 放图片的marginLeft设置成负数，不然移动的图片是从屏幕左边开始绘制
这样的话往右移动的时候图片是移动了，但是效果不对。
3. 用handler跟新UI，用timer循环

## 代码
``` java 
private ImageView img;
	private int xOffset = 50;//往左偏移量，同时也是marginLeft的值
	private int yOffset = 10;//往下偏移量
	private int continueTime = 4000;//每隔多久运行一次
	private Timer timer;
	private static Handler handler;

@Override
@SuppressLint("HandlerLeak") 
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    img= (ImageView) findViewById(R.id.imageView1);
  

    xOffset = DensityUtil.dp2px(this, xOffset);//接收的参数是px，所以要转换
    yOffset = DensityUtil.dp2px(this, yOffset);
    LinearLayout.LayoutParams lp = new LinearLayout.LayoutParams(
    		LinearLayout.LayoutParams.MATCH_PARENT, 
    		LinearLayout.LayoutParams.MATCH_PARENT);
    lp.setMargins(-xOffset, -yOffset, 0, 0);//设置margin left top
    img.setLayoutParams(lp);
    
    handler = new Handler(){
    	 @Override  
         public void handleMessage(Message msg) {  
    		 TranslateAnimation animation = null;
             switch (msg.what) {  
             //根据不同的消息运行不同的移动
             case 1:  
            	 animation = new TranslateAnimation(0,xOffset,0, 0); 
                 break; 
             case 2:  
            	 animation = new TranslateAnimation(0,0,0, yOffset);  
            	 break;  
             }  
            animation.setDuration(continueTime);//设置动画持续时间 
            animation.setRepeatCount(1);//设置重复次数 
            animation.setFillAfter(true);
            animation.setRepeatMode(Animation.REVERSE);//设置反方向执行 
            img.startAnimation(animation);
            super.handleMessage(msg);  
         }  
   };
   
    timer= new Timer();
    timer.schedule(new TimerTask() {
    	int count =1;
		@Override
		public void run() {
			 if(count % 2 != 0){
				 handler.sendEmptyMessage(1);
			 }else{
				 handler.sendEmptyMessage(2);
			 }
			 count++;
		}
	},0,continueTime*2);
	//设置时间间隔，*2是因为上文中设置了 
	//animation.setRepeatMode(Animation.REVERSE);设置反方向执行 
	//所以时间加倍了
}
```
## 效果
![img](/img/img_activity.gif)
