title: android  ormlite使用
date: 2014-11-10 18:42:19
tags: Android
description: ormlite步骤
---

## 导入jar
[ormlite-android-4.48.jar](/jar/ormlite-android-4.48.jar)
[ormlite-core-4.48.jar](/jar/ormlite-core-4.48.jar)

## 新建DatabaseHelper 继承OrmLiteSqliteOpenHelper
```java
//构造函数
public DatabaseHelper(Context context) {
    super(context, Constants.Config.DB_NAME, null, Constants.Config.DB_VERSION);
}

//建表
@Override
public void onCreate(SQLiteDatabase db, ConnectionSource connectionSource) {
    createTables(db, connectionSource);
}
//更新数据库，会自动运行，参数会自己获取
@Override
public void onUpgrade(SQLiteDatabase db, ConnectionSource connectionSource, int oldVersion, int newVersion) {
	if (newVersion <= oldVersion) {
		return;
	}

	try {//删除表
		TableUtils.dropTable(connectionSource,GoldAdvBean.class, true);
	} catch (SQLException e) {
		e.printStackTrace();
		db.delete("GOLD_ADV", null, null);//删除表内容
	}
	createTables(db, connectionSource); 
}

private void createTables(SQLiteDatabase db, ConnectionSource connectionSource) {
        try {
            TableUtils.createTableIfNotExists(connectionSource, GoldAdvBean.class);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
```

## 建立操作数据库help单利对象
在Myapplication中建立
``` java 
public DatabaseHelper mDatabaseHelper;
public DatabaseHelper getDatabaseHelper() {
	if (mDatabaseHelper == null) {
		mDatabaseHelper = new DatabaseHelper(MyApplication.this);
	}
	return mDatabaseHelper;
}
```

## 建立bean的映射类
``` java 
@DatabaseTable(tableName = "GOLD_ADV", daoClass = GoldAdvBeanDao.class)
public class GoldAdvBean implements Parcelable, Serializable{

	private static final long serialVersionUID = 1L;

	@DatabaseField(generatedId = true)//id 作为主键  自动增长
	private long Id; 
	
	@DatabaseField
	private String imgUrl="";
	
	
	@Override
	public void writeToParcel(Parcel dest, int flags) {
		dest.writeLong(Id);
		dest.writeString(imgUrl);
	}
	
	@Override
	public int describeContents() {
		return 0;
	}

	 /**
     * @return the creator
     */
    public static Parcelable.Creator<GoldAdvBean> getCreator() {
        return CREATOR;
    }

    public static final Parcelable.Creator<GoldAdvBean> CREATOR = new Creator<GoldAdvBean>() {

        @Override
        public GoldAdvBean[] newArray(int size) {
            return new GoldAdvBean[size];
        }

        @Override
        public GoldAdvBean createFromParcel(Parcel source) {
        	GoldAdvBean data = new GoldAdvBean();
            data.Id = source.readLong();
            data.imgUrl = source.readString();
            return data;
        }
    };
	/***************get - set**********************/
}

```

##  使用
``` java 
try {
	GoldAdvBeanDao dao = new GoldAdvBeanDao(myApplication.getDatabaseHelper());
	for (int i = 0; i < 20; i++) {
		GoldAdvBean goldAdvBean = new GoldAdvBean();
		dao.createOrUpdate(goldAdvBean);
	}
	List<GoldAdvBean> b=dao.queryForAll();
	Log.e("b---->", b.size()+"");
} catch (SQLException e) {
	e.printStackTrace();
}
```

<!--more-->