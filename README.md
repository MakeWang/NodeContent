# NodeContent
开发的一些总结代码
===========================
* [Glide获取视频的某一帧](#Glide获取视频的某一帧)


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
