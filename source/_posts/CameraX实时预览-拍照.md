---
title: CameraX实时预览+拍照
date: 2023-05-26 16:48:58
index_img: /img/8.jpg
category:
- Android
tags:
- Android进阶知识
---

# 预览+图片拍摄

## 实时预览

1.编辑`activity_main` layout 文件：

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/camera_capture_button"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_marginBottom="50dp"
        android:scaleType="fitCenter"
        android:text="Take Photo"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        android:elevation="2dp" />
    
    <androidx.camera.view.PreviewView
        android:id="@+id/viewFinder"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</androidx.constraintlayout.widget.ConstraintLayout>

```

### 1.请求 CameraProvider

```java
  ListenableFuture<ProcessCameraProvider> cameraProviderFuture = ProcessCameraProvider.getInstance(this);
```

### 2.检查 CameraProvider 可用性

```java
cameraProviderFuture.addListener(() -> {
    try {
        // 将你的相机和当前生命周期的所有者绑定所需的对象
        ProcessCameraProvider cameraProvider = cameraProviderFuture.get();
        //绑定相机，具体代码见下面部分
        bindPreview(cameraProvider);
    } catch (ExecutionException | InterruptedException e) {
        // No errors need to be handled for this Future.
        // This should never be reached.
    }
}, ContextCompat.getMainExecutor(this));
```

### 3.选择相机并绑定生命周期和用例

1. 创建 `Preview`。
2. 指定所需的相机 `LensFacing` 选项。
3. 将所选相机和任意用例绑定到生命周期。
4. 将 `Preview` 连接到 `PreviewView`。

```java
void bindPreview(@NonNull ProcessCameraProvider cameraProvider) {
    
    //创建一个Preview 实例，并设置该实例的 surface 提供者（provider）。
    Preview preview = new Preview.Builder().build();
    PreviewView previewView = (PreviewView)findViewById(R.id.viewFinder);
    preview.setSurfaceProvider(previewView.getSurfaceProvider());
    
    //选择默认的摄像头（后置/前置）
    CameraSelector cameraSelector = new CameraSelector.Builder()
            .requireLensFacing(CameraSelector.LENS_FACING_BACK)
            .build();

    // 重新绑定用例前先解绑
	processCameraProvider.unbindAll();
    // 绑定用例至相机
    Camera camera = cameraProvider.bindToLifecycle((LifecycleOwner)this, cameraSelector, preview);
}
```

到此为止：如何实现实时预览已经说明完毕。

## 图片拍摄：

```java
  // 创建拍照所需的实例
            imageCapture = new ImageCapture.Builder().setTargetRotation(mViewFinder.getDisplay().getRotation()).build();


```

- 首先，检查 `imageCapture` 对象是否已经实例化，以避免空指针异常。
- 创建一个带时间戳的输出文件，用于保存照片，确保文件名的唯一性。
- 创建 `OutputFileOptions` 对象，用于指定照片的输出方式。
- 调用 `takePicture()` 方法进行拍照操作，传入输出文件选项和保存照片的回调函数。
- 在保存照片的回调函数中，可以获取保存的照片的 `Uri`，显示成功的提示消息，并将消息打印到日志中。
- 如果在保存照片时发生错误，会在回调函数的 `onError()` 方法中进行处理，打印错误消息到日志中。

```java
.  /**
     * 拍照，并保存照片
     *
     * @author luxi
     * @date 2023/5/23 14:09
     */
    private void takePhoto() {
        // 确保imageCapture 已经被实例化, 否则程序将可能崩溃
        if (imageCapture != null) {
            // 创建带时间戳的输出文件以保存图片，带时间戳是为了保证文件名唯一
            File photoFile = new File(outputDirectory,
                    new SimpleDateFormat(Configuration.FILENAME_FORMAT,
                            Locale.SIMPLIFIED_CHINESE).format(System.currentTimeMillis())
                            + ".jpg");

            // 创建 output option 对象，用以指定照片的输出方式
            ImageCapture.OutputFileOptions outputFileOptions = new ImageCapture.OutputFileOptions
                    .Builder(photoFile)
                    .build();

            // 执行takePicture（拍照）方法
            imageCapture.takePicture(outputFileOptions,
                    ContextCompat.getMainExecutor(this),
                    // 保存照片时的回调
                    new ImageCapture.OnImageSavedCallback() {
                        @Override
                        public void onImageSaved(@NonNull ImageCapture.OutputFileResults outputFileResults) {
                            Uri savedUri = Uri.fromFile(photoFile);
                            String msg = "照片捕获成功! " + savedUri;
                            Toast.makeText(getBaseContext(), msg, Toast.LENGTH_SHORT).show();
                            Log.d(Configuration.TAG, msg);
                        }

                        @Override
                        public void onError(@NonNull ImageCaptureException exception) {
                            Log.e(Configuration.TAG, "Photo capture failed: " + exception.getMessage());
                        }
                    });
        }
    }
```



## 特殊功能实现：

#### 前后摄像头的切换

我们之前使用`cameraProvider.bindToLifecycle()`的时候，有一个参数是`CameraSelector`。
`CameraX`默认给我们提供了前置摄像头和后置摄像头的`CameraSelector`。

```java
   //选择默认的摄像头（后置/前置）
    CameraSelector cameraSelector = new CameraSelector.Builder()
            .requireLensFacing(CameraSelector.LENS_FACING_BACK)
            .build();
```

切换摄像头的时候，就是重新调用一下`bindPreview`方法，传入新的`cameraSelector`值就好了

```java
    //解除所有绑定，防止CameraProvider重复绑定到Lifecycle发生异常
	processCameraProvider.unbindAll();
    // 绑定用例至相机
    Camera camera = cameraProvider.bindToLifecycle((LifecycleOwner)this, cameraSelector, preview);
```

#### 手势放大缩小

这里使用`ScaleGestureDetector`类来实现手势缩放功能。

`ScaleGestureDetector`用于处理缩放的工具类，用法与GestureDetector类似，都是通过onTouchEvent()关联相应的MotionEvent的。使用该类时，用户需要传入一个完整的连续不断地motion事件（包含ACTION_DOWN,ACTION_MOVE和ACTION_UP事件）

```java
        scaleGestureDetector = new ScaleGestureDetector(this, new ScaleGestureDetector. OnScaleGestureListener() {
            @Override
            public boolean onScale(ScaleGestureDetector detector) {
                //你可以根据缩放手势的因子来执行适当的操作。当前的代码中，我们返回了 true，表示我们处理了                     缩放操作，并消费了该事件。
                return true;
            }

            @Override
            public boolean onScaleBegin(ScaleGestureDetector detector) {
            //返回true，你可以执行一些准备工作，或者返回 false 来指示不处理缩放操作。一般都设为true
                return true;
            }

            @Override
            public void onScaleEnd(ScaleGestureDetector detector) {
            //这里是缩放结束后做的操作，具体的使用效果可以参考微信，你把图片缩小之后（80%），你松手，图片会回               弹到原来的大小。 

            }
        });
```

1. 在该方法中，我们首先获取缩放因子 `scaleFactor`，它表示缩放手势的影响因子。
2. 我们获取当前的缩放状态，然后获取当前的缩放比例
3. 我们获取新的缩放级别。`Math.max()` 和 `Math.min()` 方法来限制缩放级别在最小值和最大值之间
4. 将新的缩放级别应用到相机控制器中，并打印出当前的缩放比例。

```java
     @Override
     public boolean onScale(ScaleGestureDetector detector) {
         //缩放级别的影响因子
         float scaleFactor = detector.getScaleFactor();
         LiveData<ZoomState> zoomState = mCameraInfo.getZoomState();
         //返回当前缩放比例
         float zoomRatio = zoomState.getValue().getZoomRatio();
         float minZoomRatio = zoomState.getValue().getMinZoomRatio();
         float maxZoomRatio = zoomState.getValue().getMaxZoomRatio();
         //根据缩放手势的因子调整缩放级别
         float newZoomLevel = zoomRatio * scaleFactor;
         //限制缩放级别在最小值和最大值之间
         newZoomLevel = Math.max(1.0f, Math.min(newZoomLevel, 10));

          mCameraControl.setZoomRatio(newZoomLevel);
          Log.d(TAG, "当前缩放比例: " + newZoomLevel);
          return true;
      }
```

#### 点击手动对焦

```java
   mGestureDetector = new GestureDetector(this, new GestureDetector. SimpleOnGestureListener() {
            //点击
            @SuppressLint("RestrictedApi")
            @Override
            public boolean onSingleTapUp(MotionEvent e) {
                float x = e.getX();
                float y = e.getY();
                MeteringPointFactory factory = mViewFinder.getMeteringPointFactory();
                MeteringPoint point = factory.createPoint(x, y);
                FocusMeteringAction action = new FocusMeteringAction.Builder(point, FocusMeteringAction.FLAG_AF).build();
                //启动对焦和测光
                ListenableFuture<FocusMeteringResult> focusMeteringResultListenableFuture = mCameraControl.startFocusAndMetering(action);
                focusMeteringResultListenableFuture.addListener(() -> {
                    try {
                        FocusMeteringResult result = focusMeteringResultListenableFuture.get();
                        if (result.isFocusSuccessful()) {
                            Log.d(TAG, "对焦成功");

                            runOnUiThread(new Runnable() {
                                @Override
                                public void run() {
                                    //自定义矩形方框，对焦成功显示
                                    customBoxView.setBoxPosition(x, y);
                                    //这是toast显示对焦成功
                                    showFocusSuccessToast();
                                }
                            });

                        } else {
                            Log.d(TAG, "对焦失败");
                            showFocusFailedToast();
                        }
                    } catch (ExecutionException | InterruptedException ex) {
                        Log.e("YourCameraActivity", "对焦操作失败: " + ex.getMessage());
                    }
                }, CameraXExecutors.directExecutor());


                return true;
            }

        });
```

为 `mViewFinder` 设置了一个触摸监听器，并在触摸事件中调用了 `scaleGestureDetector` 和 `mGestureDetector` 的对应方法。`scaleGestureDetector.onTouchEvent(event)` 是用于处理缩放手势的触摸事件，它将触摸事件传递给 `scaleGestureDetector` 对象来处理缩放操作。`mGestureDetector.onTouchEvent(event)` 是用于处理其他手势的触摸事件，它将触摸事件传递给 `mGestureDetector` 对象来处理其他手势操作，比如单击操作或者双击等。

```java
mViewFinder = (PreviewView) findViewById(R.id.viewFinder); 
mViewFinder.setOnTouchListener(new View.OnTouchListener() {
                @Override
                public boolean onTouch(View v, MotionEvent event) {
                    scaleGestureDetector.onTouchEvent(event);
                    mGestureDetector.onTouchEvent(event);
                    return true;
                }
            });
```

#### 手机紧急晃动拍照

使用加速度传感器（Accelerometer Sensor）来检测手机的晃动，并在达到一定阈值时触发拍照操作。

1. 注册加速度传感器监听

```java
SensorManager sensorManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);
Sensor accelerometerSensor = sensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);

sensorManager.registerListener(sensorEventListener, accelerometerSensor, SensorManager.SENSOR_DELAY_NORMAL);
```

2. 创建一个 SensorEventListener 对象，并实现其 onSensorChanged() 方法

```java
private SensorEventListener sensorEventListener = new SensorEventListener() {
    private static final float SHAKE_THRESHOLD = 2.5f; // 设置晃动的阈值

    private float lastX;
    private float lastY;
    private float lastZ;
    private long lastShakeTime;

    @Override
    public void onSensorChanged(SensorEvent event) {
        float x = event.values[0];
        float y = event.values[1];
        float z = event.values[2];

        // 计算加速度的变化量
        float deltaX = x - lastX;
        float deltaY = y - lastY;
        float deltaZ = z - lastZ;

        // 更新上一次的加速度值
        lastX = x;
        lastY = y;
        lastZ = z;

        // 计算当前加速度的模
        float acceleration = (float) Math.sqrt(deltaX * deltaX + deltaY * deltaY + deltaZ * deltaZ);

        // 判断是否达到晃动阈值
        long currentTime = System.currentTimeMillis();
        if (acceleration > SHAKE_THRESHOLD && currentTime - lastShakeTime > 1000) {
            // 触发拍照操作
            takePhoto();
            lastShakeTime = currentTime;
        }
    }

    @Override
    public void onAccuracyChanged(Sensor sensor, int accuracy) {
        // 当传感器的精度发生变化时触发，不需要处理
    }
};

```

3. 销毁监听

```java
@Override
protected void onDestroy() {
    super.onDestroy();
    sensorManager.unregisterListener(sensorEventListener);
}

```

#### 给拍照图片加水印

在 `public void onImageSaved(@NonNull ImageCapture.OutputFileResults outputFileResults) {}`里调用下面的方法。WaterMarkingUtility是一个添加水印的类。

```java
   private void addWatermarkToPhoto(File photoFile) throws FileNotFoundException {
        Bitmap originalBitmap = BitmapFactory.decodeFile(photoFile.getAbsolutePath());
        FileOutputStream outputStream = new FileOutputStream(photoFile);

        Bitmap bitmap = WaterMarkingUtility.drawTextToCenter(originalBitmap, "水印", 500);
        bitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
    }
```

