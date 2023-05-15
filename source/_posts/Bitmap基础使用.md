---
title: Bitmap基础使用
date: 2023-05-15 22:06:01
index_img: /img/23.png
category:
- Android
tags:
- Android进阶知识
---

## 1. 使用Bitmap时防止OOM的有效方法

------

### 高效压缩图片

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

2.
