---
title: Bitmap基础使用
date: 2023-05-15 22:06:01
index_img: /img/23.png
category:
- Android
tags:
- Android进阶知识
---



## Bitmap的一些应用场景

------

### 1. 使用Bitmap时防止OOM的有效方法：高效压缩图片

```java
    /**
     * 谷歌推荐使用方法，从资源中加载图像，并高效压缩，有效降低OOM的概率
     * @param res 资源
     * @param resId 图像资源的资源id
     * @param reqWidth 要求图像压缩后的宽度
     * @param reqHeight 要求图像压缩后的高度
     * @return
     */
    public Bitmap decodeSampledBitmapFromResource(Resources res, int resId, int reqWidth, int reqHeight) {
        // 设置inJustDecodeBounds = true ,表示获取图像信息，但是不将图像的像素加入内存
        final BitmapFactory.Options options = new BitmapFactory.Options();
        options.inJustDecodeBounds = true;
        BitmapFactory.decodeResource(res, resId, options);

        // 调用方法计算合适的 inSampleSize
        options.inSampleSize = calculateInSampleSize(options, reqWidth,
                reqHeight);

        // inJustDecodeBounds 置为 false 真正开始加载图片
        options.inJustDecodeBounds = false;
        //将options.inPreferredConfig改成Bitmap.Config.RGB_565，
        // 是默认情况Bitmap.Config.ARGB_8888占用内存的一般
        options.inPreferredConfig= Bitmap.Config.RGB_565;
        return BitmapFactory.decodeResource(res, resId, options);
    }

    // 计算 BitmapFactpry 的 inSimpleSize的值的方法
    public int calculateInSampleSize(BitmapFactory.Options options,
                                     int reqWidth, int reqHeight) {
        if (reqWidth == 0 || reqHeight == 0) {
            return 1;
        }

        // 获取图片原生的宽和高
        final int height = options.outHeight;
        final int width = options.outWidth;
        Log.d(TAG, "origin, w= " + width + " h=" + height);
        int inSampleSize = 1;

        // 如果原生的宽高大于请求的宽高,那么将原生的宽和高都置为原来的一半
        if (height > reqHeight || width > reqWidth) {
            final int halfHeight = height / 2;
            final int halfWidth = width / 2;

            // 主要计算逻辑
            // Calculate the largest inSampleSize value that is a power of 2 and
            // keeps both
            // height and width larger than the requested height and width.
            while ((halfHeight / inSampleSize) >= reqHeight && (halfWidth / inSampleSize) >= reqWidth) {
                inSampleSize *= 2;
            }
        }

        Log.d(TAG, "sampleSize:" + inSampleSize);
        return inSampleSize;
    }  
```

## 2. 绘制圆角矩形Bitmap

```java
   /**
     * @param bitmap 需要修改的图片
     * @param pixels 圆角的弧度
     * @return 圆角图片
     */
    //参考资料:
    //http://blog.csdn.net/c8822882/article/details/6906768
    public Bitmap toRoundCorner(Bitmap bitmap, int pixels) {
        Bitmap roundCornerBitmap = Bitmap.createBitmap(bitmap.getWidth(), bitmap.getHeight(), Bitmap.Config.ARGB_8888);
        Canvas canvas = new Canvas(roundCornerBitmap);//生成画布
        int color = 0xff424242;//int color = 0xff424242;
        Paint paint = new Paint();
        paint.setColor(color);
        //防止锯齿
        paint.setAntiAlias(true);
        //定义一个矩形区域用于绘制bitmap
        Rect rect = new Rect(0, 0, bitmap.getWidth(), bitmap.getHeight());
        //再定义一个矩形用于绘制圆角矩形
        RectF rectF = new RectF(rect);
        //获取圆角的半径
        float roundPx = pixels;
        //相当于清屏
        canvas.drawARGB(0, 0, 0, 0);
        //先画了一个带圆角的矩形
        canvas.drawRoundRect(rectF, roundPx, roundPx, paint);
        //取两层绘制交集。显示上层。
        paint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.SRC_IN));
        //再把原来的bitmap画到现在的bitmap！！！注意这个理解
        canvas.drawBitmap(bitmap, rect, rect, paint);
        return roundCornerBitmap;
    }
```

## 3. 绘制圆形Bitmap

```Java
/**
 * 将图片处理成圆形
 *
 * @param res 
 * @param drawableId 
 * @author luxi
 * @date 2023/5/12 13:21
 */
public static Bitmap createCircleBitmap(Resources res, int drawableId) {
    Bitmap bitmap = BitmapFactory.decodeResource(res, drawableId);
    //获取图片的宽度
    int width = bitmap.getWidth();
    Paint paint = new Paint();
    //设置抗锯齿
    paint.setAntiAlias(true);

    //创建一个与原bitmap一样宽度的正方形bitmap
    Bitmap circleBitmap = Bitmap.createBitmap(width, width, Bitmap.Config.ARGB_8888);
    //以该bitmap为低创建一块画布
    Canvas canvas = new Canvas(circleBitmap);
    //以（width/2, width/2）为圆心，width/2为半径画一个圆
    canvas.drawCircle(width / 2, width / 2, width / 2, paint);

    //设置画笔为取交集模式
    paint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.SRC_IN));
    //裁剪图片
    canvas.drawBitmap(bitmap, 0, 0, paint);

    return circleBitmap;
}
```

## 4.裁剪、缩放、旋转、移动

```java
Matrix matrix = new Matrix();  
// 缩放 
matrix.postScale(0.8f, 0.9f);  
// 左旋，参数为正则向右旋
matrix.postRotate(-45);  
// 平移, 在上一次修改的基础上进行再次修改 set 每次操作都是最新的 会覆盖上次的操作
matrix.postTranslate(100, 80);
// 裁剪并执行以上操作
Bitmap bitmap = Bitmap.createBitmap(source, 0, 0, source.getWidth(), source.getHeight(), matrix, true);

```

1. 图片缩小

   ```java
       /**
        * 缩小图片
        *
        * @param bitmap 需要缩小的图片
        * @return 缩小的图片
        */
       private static Bitmap small(Bitmap bitmap) {
           Matrix matrix = new Matrix();
           matrix.postScale(0.25f, 0.25f);
           Bitmap resizeBmp = Bitmap.createBitmap(bitmap, 0, 0, bitmap.getWidth(), bitmap.getHeight(), matrix, true);
           return resizeBmp;
       }
   ```

2. 图片放大

   ```java
     /**
        * 放大图片
        *
        * @param bitmap 需要放大的图片
        * @return 放大的图片
        */
       private static Bitmap big(Bitmap bitmap) {
           Matrix matrix = new Matrix();
           matrix.postScale(4f, 4f);
           Bitmap resizeBmp = Bitmap.createBitmap(bitmap, 0, 0, bitmap.getWidth(), bitmap.getHeight(), matrix, true);
           return resizeBmp;
       }
   ```

   
