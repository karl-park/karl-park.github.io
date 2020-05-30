---
layout: post
title:  "[Android] Caching Bitmap"
date:   2020-04-12 07:00:00
tags: Android Kotlin Bitmap Memory Caching OOM
categories: routine
---

# Caching Bitmap

There are two ways to cache the bitmaps. First one is to save the bitmaps into the meemory, and the other one is to use the disk cache.


## 1. Memory Cache
We can use the LRU algorithm(Least Recently Used Caching) for caching the Bitmap. There is the [LruCache](https://developer.android.com/reference/android/util/LruCache) class in the android.util pacakage.(also we can find this class in the [support library LruCache](https://developer.android.com/reference/androidx/collection/LruCache))

> A pupular caching bitmap algorithm was a [SoftReference](https://developer.android.com/reference/java/lang/ref/SoftReference) and [WeakReference](https://developer.android.com/reference/java/lang/ref/WeakReference) bitmap cache in the past, but now, Using the LruCache class for caching the Bitmap is strongly recommended.

### How to determine a suitable size for a LruCache?
- How memory intensive is the rest of your activity and/or application?
- How many images will be on-screen at once? How many need to be available ready to come on-screen?
- What is the screen size and density of the device? An extra high density screen (xhdpi) device like Galaxy Nexus will need a larger cache to hold the same number of images in memory compared to a device like Nexus S (hdpi).
- What dimensions and configuration are the bitmaps and therefore how much memory will each take up?
- How frequently will the images be accessed? Will some be accessed more frequently than others? If so, perhaps you may want to keep certain items always in memory or even have multiple LruCache objects for different groups of bitmaps.
- Can you balance quality against quantity? Sometimes it can be more useful to store a larger number of lower quality bitmaps, potentially loading a higher quality version in another background task.

> There dosn't exist a right size or fomula that can be applied to all applications.
> A cahce that is too small causes additional overhead, and a cache that is too large can occur OOM exception.

### LruCache Example
```kotlin

class BitmapCacheActivity : Activity() {
  private lateinit var memoryCache: LruCache<String, Bitmap>

  override fun onCreate(savedInstanceState: Bundle?) {
      ...
      // Get max available VM memory, exceeding this amount will throw an
      // OutOfMemory exception. Stored in kilobytes as LruCache takes an
      // int in its constructor.
      val maxMemory = (Runtime.getRuntime().maxMemory() / 1024).toInt()

      // Use 1/8th of the available memory for this memory cache.
      val cacheSize = maxMemory / 8

      memoryCache = object : LruCache<String, Bitmap>(cacheSize) {

          override fun sizeOf(key: String, bitmap: Bitmap): Int {
              // The cache size will be measured in kilobytes rather than
              // number of items.
              return bitmap.byteCount / 1024
          }
      }
      ...
  }

  fun loadBitmap(resId: Int, imageView: ImageView) {
    val imageKey: String = resId.toString()

    val bitmap: Bitmap? = getBitmapFromMemCache(imageKey)?.also {
        mImageView.setImageBitmap(it)
    } ?: run {
        mImageView.setImageResource(R.drawable.image_placeholder)
        val task = BitmapWorkerTask()
        task.execute(resId)
        null
    }
  }

  private inner class BitmapWorkerTask : AsyncTask<Int, Unit, Bitmap>() {
    ...
    // Decode image in background.
    override fun doInBackground(vararg params: Int?): Bitmap? {
        return params[0]?.let { imageId ->
            decodeSampledBitmapFromResource(resources, imageId, 100, 100)?.also { bitmap ->
                addBitmapToMemoryCache(imageId.toString(), bitmap)
            }
        }
    }
    ...
  }

  fun getBitmapFromMemCache(String key) : Bitmap = mMemoryCache.get(key)

  fun decodeSampledBitmapFromResource(context: Context, resId: Int, reqWidth: Int, reqHeight: Int) : Bitmap {
    
    // First decode with inJustDecodeBounds=true to check dimensions
    val options = BitmapFactory.Options().apply {
      inJustDecodeBounds = true
    }
    
    BitmapFactory.decodeResource(res, resId, options)

    
    options.apply {
      // Calculate inSampleSize
      inSampleSize = calculateInSampleSize(options, reqWidth, reqHeight)
  
      // Decode bitmap with inSampleSize set
      inJustDecodeBounds = false
    }

    return BitmapFactory.decodeResource(res, resId, options)
  }
}
```

> Note: In this example, one eighth of the application memory is allocated for our cache. On a normal/hdpi device this is a minimum of around 4MB (32/8). A full screen GridView filled with images on a device with 800x480 resolution would use around 1.5MB (800*480*4 bytes), so this would cache a minimum of around 2.5 pages of images in memory.




## 2. Disk Cache
If we use the memory cache for the bitmaps, sometimes it can cause some problems. For example, if we use a grid layout that has many bitmap images, the memory can be filled up easily. Another example is, let's suppose that when a user use a gallary application which use a memory cache, and the use get a phone call(or another tasks), all memory cache can be cleared.

The disk cache is much slower than the memory cache, but it is much reliable.

> Note: A ContentProvider might be a more appropriate place to store cached images if they are accessed more frequently, for example in an image gallery application.


### Disk Cache Example



```kotlin
// DiskLruCache : https://android.googlesource.com/platform/libcore/+/jb-mr2-release/luni/src/main/java/libcore/io/DiskLruCache.java

class DiskCacheActivity: Activity() {
  private const val DISK_CACHE_SIZE = 1024 * 1024 * 10 // 10MB
  private const val DISK_CACHE_SUBDIR = "thumbnails"
  ...
  private var diskLruCache: DiskLruCache? = null
  private val diskCacheLock = ReentrantLock()
  private val diskCacheLockCondition: Condition = diskCacheLock.newCondition()
  private var diskCacheStarting = true

  override fun onCreate(savedInstanceState: Bundle?) {
    ...
    // Initialize memory cache
    ...
    // Initialize disk cache on background thread
    val cacheDir = getDiskCacheDir(this, DISK_CACHE_SUBDIR)
    InitDiskCacheTask().execute(cacheDir)
    ...
  }

  internal inner class InitDiskCacheTask : AsyncTask<File, Void, Void>() {
    override fun doInBackground(vararg params: File): Void? {
        diskCacheLock.withLock {
            val cacheDir = params[0]
            diskLruCache = DiskLruCache.open(cacheDir, DISK_CACHE_SIZE)
            diskCacheStarting = false // Finished initialization
            diskCacheLockCondition.signalAll() // Wake any waiting threads
        }
        return null
    }
  }

  internal inner class  BitmapWorkerTask : AsyncTask<Int, Unit, Bitmap>() {
    ...

    // Decode image in background.
    override fun doInBackground(vararg params: Int?): Bitmap? {
        val imageKey = params[0].toString()

        // Check disk cache in background thread
        return getBitmapFromDiskCache(imageKey) ?:
                // Not found in disk cache
                decodeSampledBitmapFromResource(resources, params[0], 100, 100)
                        ?.also {
                            // Add final bitmap to caches
                            addBitmapToCache(imageKey, it)
                        }
    }
  }

  fun addBitmapToCache(key: String, bitmap: Bitmap) {
    // Add to memory cache as before
    if (getBitmapFromMemCache(key) == null) {
        memoryCache.put(key, bitmap)
    }

    // Also add to disk cache
    synchronized(diskCacheLock) {
        diskLruCache?.apply {
            if (!containsKey(key)) {
                put(key, bitmap)
            }
        }
    }
  }

  fun getBitmapFromDiskCache(key: String): Bitmap? =
        diskCacheLock.withLock {
            // Wait while disk cache is started from background thread
            while (diskCacheStarting) {
                try {
                    diskCacheLockCondition.await()
                } catch (e: InterruptedException) {
                }

            }
            return diskLruCache?.get(key)
        }

  // Creates a unique subdirectory of the designated app cache directory. Tries to use external
  // but if not mounted, falls back on internal storage.
  fun getDiskCacheDir(context: Context, uniqueName: String): File {
    // Check if media is mounted or storage is built-in, if so, try and use external cache dir
    // otherwise use internal cache dir
    val cachePath =
            if (Environment.MEDIA_MOUNTED == Environment.getExternalStorageState()
                    || !isExternalStorageRemovable()) {
                context.externalCacheDir.path
            } else {
                context.cacheDir.path
            }

    return File(cachePath + File.separator + uniqueName)
  }
}








# Reference
- [Cache Bitmap](https://developer.android.com/topic/performance/graphics/cache-bitmap)
- [Loading Large Bitmaps Efficiently](https://developer.android.com/topic/performance/graphics/load-bitmap)