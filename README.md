# NodeContent
开发的一些总结代码
===========================
* [Glide获取视频的某一帧](#Glide获取视频的某一帧)
* [双击back返回键退出app](#双击back返回键退出app)
* [通过反射的方式拿到Application上下文](#通过反射的方式拿到Application上下文)


Glide获取视频的某一帧
---------------------
```java
/**
   有个0- 到1秒的延迟
   * @param context 上下文
   * @param uri 视频地址
   * @param imageView 设置image
   * @param frameTimeMicros 获取某一时间帧
   */
  public static void loadVideoScreenshot(final Context context, String uri, ImageView imageView, long frameTimeMicros) {
      RequestOptions requestOptions = RequestOptions.frameOf(frameTimeMicros);
      requestOptions.set(FRAME_OPTION, MediaMetadataRetriever.OPTION_CLOSEST);
      requestOptions.transform(new BitmapTransformation() {
          @Override
          protected Bitmap transform(@NonNull BitmapPool pool, @NonNull Bitmap toTransform, int outWidth, int outHeight) {
              return toTransform;
          }
          @Override
          public void updateDiskCacheKey(MessageDigest messageDigest) {
              try {
                  messageDigest.update((context.getPackageName() + "RotateTransform").getBytes("utf-8"));
              } catch (Exception e) {
                  e.printStackTrace();
              }
          }
      });
      Glide.with(context).load(uri).apply(requestOptions).into(imageView);
  }
```

双击back返回键退出app
---------------------
```java
public class MainActivity extends Activity {

	private Toast toast;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		toast = Toast.makeText(getApplicationContext(), "确定退出？", 0);
	}
	public void onBackPressed() {
		quitToast();
	}
	private void quitToast() {
		if(null == toast.getView().getParent()) {
			toast.show();
		}else{
			System.exit(0);
		}
	}
}
```



通过反射的方式拿到Application上下文
-------------------------------------------
```java
private Application getApplicationByReflect() {
	try {
	    @SuppressLint("PrivateApi")
	    Class<?> activityThread = Class.forName("android.app.ActivityThread");
	    Object thread = activityThread.getMethod("currentActivityThread").invoke(null);
	    Object app = activityThread.getMethod("getApplication").invoke(thread);
	    if (app == null) {
		throw new NullPointerException("u should init first");
	    }
	    return (Application) app;
	} catch (NoSuchMethodException e) {
	    e.printStackTrace();
	} catch (IllegalAccessException e) {
	    e.printStackTrace();
	} catch (InvocationTargetException e) {
	    e.printStackTrace();
	} catch (ClassNotFoundException e) {
	    e.printStackTrace();
	}
	throw new NullPointerException("u should init first");
}
```

