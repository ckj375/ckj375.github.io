---
title: Android源码解析(一):bringToFront
date: 2017-10-24 16:34:14
tags: bringToFront
categories: Android
---
最近项目中用到bringToFront()这个方法,在自己的三星S4手机(Android 4.2版本,android-17)上调试怎么都不生效,于是查看源码(android-23),发现Android 4.4之前的版本如果使用这个方法,必须得加上requestLayout()和invalidate()才能生效.先看下View.java中bringToFront()的实现,发现它调用的是ViewGroup.java的bringChildToFront(View child)方法.
```
/**
 * Change the view's z order in the tree, so it's on top of other sibling
 * views. This ordering change may affect layout, if the parent container
 * uses an order-dependent layout scheme (e.g., LinearLayout). Prior
 * to {@link android.os.Build.VERSION_CODES#KITKAT} this
 * method should be followed by calls to {@link #requestLayout()} and
 * {@link View#invalidate()} on the view's parent to force the parent to redraw
 * with the new child ordering.
 *
 * @see ViewGroup#bringChildToFront(View)
 */
public void bringToFront() {
    if (mParent != null) {
        mParent.bringChildToFront(this);
    }
}
```
<!-- more -->
在bringChildToFront(View child)方法中主要操作是将child从ArrayList中删除后重新添加,随后调用requestLayout()和invalidate()方法刷新视图.
```
public void bringChildToFront(View child) {
    final int index = indexOfChild(child);
    if (index >= 0) {
        removeFromArray(index);
        addInArray(child, mChildrenCount);
        child.mParent = this;
        requestLayout();
        invalidate();
    }
}
```
再来看下Android 4.2版本(android-18)的源码,发现ViewGroup.java中的bringChildToFront方法中未调用requestLayout()和invalidate(),原来如此.
```
public void bringChildToFront(View child) {
        int index = indexOfChild(child);
        if (index >= 0) {
            removeFromArray(index);
            addInArray(child, mChildrenCount);
            child.mParent = this;
        }
    }
```
至于requestLayout()和invalidate()的具体实现在View.java中,具体作用参见源码注释.
```
/**
    * Call this when something has changed which has invalidated the
    * layout of this view. This will schedule a layout pass of the view
    * tree. This should not be called while the view hierarchy is currently in a layout
    * pass ({@link #isInLayout()}. If layout is happening, the request may be honored at the
    * end of the current layout pass (and then layout will run again) or after the current
    * frame is drawn and the next layout occurs.
    *
    * <p>Subclasses which override this method should call the superclass method to
    * handle possible request-during-layout errors correctly.</p>
    */
   @CallSuper
   public void requestLayout() {
       if (mMeasureCache != null) mMeasureCache.clear();

       if (mAttachInfo != null && mAttachInfo.mViewRequestingLayout == null) {
           // Only trigger request-during-layout logic if this is the view requesting it,
           // not the views in its parent hierarchy
           ViewRootImpl viewRoot = getViewRootImpl();
           if (viewRoot != null && viewRoot.isInLayout()) {
               if (!viewRoot.requestLayoutDuringLayout(this)) {
                   return;
               }
           }
           mAttachInfo.mViewRequestingLayout = this;
       }

       mPrivateFlags |= PFLAG_FORCE_LAYOUT;
       mPrivateFlags |= PFLAG_INVALIDATED;

       if (mParent != null && !mParent.isLayoutRequested()) {
           mParent.requestLayout();
       }
       if (mAttachInfo != null && mAttachInfo.mViewRequestingLayout == this) {
           mAttachInfo.mViewRequestingLayout = null;
       }
   }

   /**
     * Invalidate the whole view. If the view is visible,
     * {@link #onDraw(android.graphics.Canvas)} will be called at some point in
     * the future.
     * <p>
     * This must be called from a UI thread. To call from a non-UI thread, call
     * {@link #postInvalidate()}.
     */
    public void invalidate() {
        invalidate(true);
    }
   ```
