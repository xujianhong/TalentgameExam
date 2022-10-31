package com.daomingedu.talentgameexam;

import android.animation.LayoutTransition;
import android.animation.ObjectAnimator;
import android.annotation.SuppressLint;
import android.annotation.TargetApi;
import android.app.Activity;
import android.app.DownloadManager;
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.content.IntentFilter;
import android.content.SharedPreferences;
import android.content.pm.ActivityInfo;
import android.graphics.Bitmap;
import android.net.Uri;
import android.os.Build;
import android.os.Bundle;
import android.os.Environment;
import android.text.TextUtils;
import android.view.KeyEvent;
import android.view.View;
import android.view.ViewGroup;
import android.view.WindowManager;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.webkit.DownloadListener;
import android.webkit.PermissionRequest;
import android.webkit.URLUtil;
import android.webkit.ValueCallback;
import android.webkit.WebChromeClient;
import android.webkit.WebSettings;
import android.webkit.WebView;
import android.webkit.WebViewClient;
import android.widget.FrameLayout;
import android.widget.ProgressBar;
import android.widget.TextView;

import com.daomingedu.talentgameexam.ui.VideoEnabledWebChromeClient;
import com.daomingedu.talentgameexam.ui.VideoEnabledWebView;

import java.io.File;

import androidx.appcompat.app.AppCompatActivity;
import androidx.appcompat.widget.Toolbar;

import butterknife.BindView;
import butterknife.ButterKnife;
import butterknife.internal.Utils;
import timber.log.Timber;


/**
 * Description
 * Created by jianhongxu on 4/6/21
 */
public class CommonWebActivity extends AppCompatActivity {
    @BindView(R.id.wv_web)
    VideoEnabledWebView wv_web;
    @BindView(R.id.pb_load)
    ProgressBar pb_load;
    @BindView(R.id.rl_web)
    FrameLayout rl_web;
//    @BindView(R.id.toolbar2)
//    Toolbar toolbar;
//    @BindView(R.id.tv_name)
//    TextView tv_name;
//    @BindView(R.id.common_web_record)
//    TextView common_web_record;


    String title;
    int limit;
    int water;
    private String url;




    boolean isback = true;

    private ValueCallback<Uri> uploadMessage;
    private ValueCallback<Uri[]> uploadMessageAboveL;

    private final static int FILE_CHOOSER_RESULT_CODE = 10000;

    VideoEnabledWebChromeClient webChromeClient;
    private DownloadCompleteReceiver receiver;
    private boolean isUploadVideo;//是否是上传视频，默认false不是，用来显示/隐藏选择文件的拍照按钮

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_common_web);
        ButterKnife.bind(this);





       /* limit = getIntent().getIntExtra("limit", 0);
        water = getIntent().getIntExtra("water", 1);
        SharedPreferencesUtil.INSTANCE.setWater(this, water);
        SharedPreferencesUtil.INSTANCE.setLimit(this, limit);*/


//        url = "http://test.4hand.com.cn/talentgame_cq/teacher/video/teacherInfo.do";
//        url = "http://cqtalent.4hand.com.cn/teacher/video/teacherInfo.do"; //重庆才艺评分地址
        url = "http://cqtalent.4hand.com.cn:8080/teacher/video/teacherInfo.do";//山西才艺评分地址

        initViews();

//        IntentFilter filter = new IntentFilter();
//        filter.addAction(Action.FINISH_WEBSIGNUP);
//        msgReceiver = new MsgReceiver();
//        LocalBroadcastManager.getInstance(Utils.app).registerReceiver(msgReceiver, filter);
    }

    @SuppressLint({"SetJavaScriptEnabled", "JavascriptInterface"})
    private void initViews() {
        LayoutTransition transition = new LayoutTransition();
        transition.setAnimator(LayoutTransition.APPEARING, ObjectAnimator.ofFloat(rl_web, "alpha", 0, 1f));

        transition.setAnimator(LayoutTransition.DISAPPEARING, ObjectAnimator.ofFloat(rl_web, "alpha", 1f, 0f));
        transition.setDuration(300);
        rl_web.setLayoutTransition(transition);


        View nonVideoLayout = findViewById(R.id.nonVideoLayout); // Your own view, read class comments
        ViewGroup videoLayout = (ViewGroup) findViewById(R.id.videoLayout); // Your own view, read class comments
//        View loadingView = getLayoutInflater().inflate(R.layout.view_loading_video, null); // Your own view, read class comments
        webChromeClient = new VideoEnabledWebChromeClient(nonVideoLayout, videoLayout, null, wv_web) {
            @Override
            public void onProgressChanged(WebView view, int newProgress) {
                Timber.d("newProgress:" + newProgress);

                pb_load.setProgress(newProgress);


            }

            @Override
            public void onPermissionRequest(PermissionRequest request) {
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
                    request.grant(request.getResources());
                }
            }

            // For Android < 3.0
            public void openFileChooser(ValueCallback<Uri> valueCallback) {
                uploadMessage = valueCallback;
                openImageChooserActivity(null);
            }

            // For Android  >= 3.0
            public void openFileChooser(ValueCallback valueCallback, String acceptType) {
                uploadMessage = valueCallback;
                openImageChooserActivity(acceptType);
            }


            //For Android  >= 4.1
            public void openFileChooser(ValueCallback<Uri> valueCallback, String acceptType, String capture) {
                uploadMessage = valueCallback;
                openImageChooserActivity(acceptType);
            }


            @Override
            public boolean onShowFileChooser(WebView webView, ValueCallback<Uri[]> filePathCallback, WebChromeClient.FileChooserParams fileChooserParams) {
                String acceptType = null;
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
                    String[] acceptTypes = fileChooserParams.getAcceptTypes();
                    if (acceptTypes != null && acceptTypes.length > 0) {
                        acceptType = acceptTypes[0];
                    }
                }
                uploadMessageAboveL = filePathCallback;
                openImageChooserActivity(acceptType);
                return true;
            }
        };
        webChromeClient.setOnToggledFullscreen(new VideoEnabledWebChromeClient.ToggledFullscreenCallback() {
            @Override
            public void toggledFullscreen(boolean fullscreen) {

                // Your code to handle the full-screen change, for example showing and hiding the title bar. Example:
                if (fullscreen) {
                    if (getRequestedOrientation() != ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE) {
                        setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE);
                    }

                    WindowManager.LayoutParams attrs = getWindow().getAttributes();
                    attrs.flags |= WindowManager.LayoutParams.FLAG_FULLSCREEN;
                    attrs.flags |= WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON;
                    getWindow().setAttributes(attrs);
                    getWindow().getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_LOW_PROFILE);

//                    toolbar.setVisibility(View.GONE);
                } else {
                    if (getRequestedOrientation() != ActivityInfo.SCREEN_ORIENTATION_PORTRAIT) {
                        setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_PORTRAIT);
                    }
                    WindowManager.LayoutParams attrs = getWindow().getAttributes();
                    attrs.flags &= ~WindowManager.LayoutParams.FLAG_FULLSCREEN;
                    attrs.flags &= ~WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON;
                    getWindow().setAttributes(attrs);
                    getWindow().getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_VISIBLE);

//                    toolbar.setVisibility(View.VISIBLE);

                }
            }
        });
        wv_web.setWebChromeClient(webChromeClient);
        WebSettings setting = wv_web.getSettings();
        setting.setUseWideViewPort(true); //将图片调整至适合Webview大小
        setting.setSupportZoom(true);//支持缩放

        setting.setLoadWithOverviewMode(true);//缩放至屏幕大小
        setting.setLoadsImagesAutomatically(true);//支持自动加载图片

        // 加载JS
        setting.setJavaScriptEnabled(true);//支持js
//进行缓存

        setting.setLayoutAlgorithm(WebSettings.LayoutAlgorithm.TEXT_AUTOSIZING);
        if ("音乐游戏".equals(title)) {
            setting.setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK);//设置缓存模式
            setting.setDomStorageEnabled(true); //开启dom storage AOI功能
            setting.setAppCacheEnabled(true);//加载缓存
            String path = this.getFilesDir().getAbsolutePath() + "/gamecache";
//            Timber.d("GameActivity" + path);
            setting.setAppCachePath(path);//设计缓存路径
        } else
            setting.setCacheMode(WebSettings.LOAD_NO_CACHE);//设置缓存模式


        //https当中不能加载http资源，需要设置开启。
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
            setting.setMixedContentMode(WebSettings.MIXED_CONTENT_ALWAYS_ALLOW);
        }


//        updateCookies(url);


        //WebView加载web资源
        wv_web.loadUrl(url);
        //覆盖WebView默认使用第三方或系统默认浏览器打开网页的行为，使网页用WebView打开
        wv_web.setWebViewClient(new WebViewClient() {
            @Override
            public boolean shouldOverrideUrlLoading(WebView view, String url) {
                //返回值是true的时候控制去WebView打开，为false调用系统浏览器或第三方浏览器
                // 如下方案可在非微信内部WebView的H5页面中调出微信支付
                if (url.startsWith("weixin://wap/pay?")) {
                    Intent intent = new Intent();
                    intent.setAction(Intent.ACTION_VIEW);
                    intent.setData(Uri.parse(url));
                    startActivity(intent);
                    return true;
                } else if (url.startsWith("alipays://platformapi/startApp")) {
                    startAlipayActivity(url);
                    return true;
                    // android  6.0 两种方式获取intent都可以跳转支付宝成功,7.1测试不成功
                } else if ((Build.VERSION.SDK_INT > Build.VERSION_CODES.M)
                        && (url.contains("platformapi") && url.contains("startapp"))) {
                    startAlipayActivity(url);
                    return true;
                }
                else if (url.startsWith("tel:")) {
                    //Handle telephony Urls
                    startActivity(new Intent(Intent.ACTION_DIAL, Uri.parse(url)));
                    return true;
                }
                return super.shouldOverrideUrlLoading(view, url);
            }

            @Override
            public void onPageFinished(WebView view, String url) {
                pb_load.setVisibility(View.GONE);

            }

            @Override
            public void onPageStarted(WebView view, String url, Bitmap favicon) {
                Timber.d("onPageStarted: %s", url);

                CookieManager cookieManager = CookieManager.getInstance();
                String s = cookieManager.getCookie(url);
                Timber.d("Cookies==" + s);
                pb_load.setVisibility(View.VISIBLE);
            }
        });

        // 使用R
        receiver = new DownloadCompleteReceiver();
        IntentFilter intentFilter = new IntentFilter();
        intentFilter.addAction(DownloadManager.ACTION_DOWNLOAD_COMPLETE);
        registerReceiver(receiver, intentFilter);


        wv_web.setDownloadListener(new DownloadListener() {
            @Override
            public void onDownloadStart(String url, String userAgent, String contentDisposition, String mimetype, long contentLength) {
// 指定下载地址
                DownloadManager.Request request = new DownloadManager.Request(Uri.parse(url));
                // 允许媒体扫描，根据下载的文件类型被加入相册、音乐等媒体库
                request.allowScanningByMediaScanner();
                // 设置通知的显示类型，下载进行时和完成后显示通知
                request.setNotificationVisibility(DownloadManager.Request.VISIBILITY_VISIBLE_NOTIFY_COMPLETED);
                // 设置通知栏的标题，如果不设置，默认使用文件名
//        request.setTitle("This is title");
                // 设置通知栏的描述
//        request.setDescription("This is description");
                // 允许在计费流量下下载
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.JELLY_BEAN) {
                    request.setAllowedOverMetered(false);
                }
                // 允许该记录在下载管理界面可见
                request.setVisibleInDownloadsUi(false);
                // 允许漫游时下载
                request.setAllowedOverRoaming(true);
                // 允许下载的网路类型
                request.setAllowedNetworkTypes(DownloadManager.Request.NETWORK_WIFI);
                // 设置下载文件保存的路径和文件名
                String fileName = URLUtil.guessFileName(url, contentDisposition, mimetype);
                Timber.d("fileName:{}" + fileName);
                request.setDestinationInExternalPublicDir(Environment.DIRECTORY_DOWNLOADS, fileName);
//        另外可选一下方法，自定义下载路径
//        request.setDestinationUri()
//        request.setDestinationInExternalFilesDir()
                final DownloadManager downloadManager = (DownloadManager) getSystemService(DOWNLOAD_SERVICE);
                // 添加一个下载任务
                long downloadId = downloadManager.enqueue(request);
                Timber.d("downloadId:{}" + downloadId);


            }
        });

    }


    private class DownloadCompleteReceiver extends BroadcastReceiver {

        @Override
        public void onReceive(Context context, Intent intent) {
            Timber.d("onReceive. intent:{}" + intent.toUri(0));

            if (DownloadManager.ACTION_DOWNLOAD_COMPLETE.equals(intent.getAction())) {
                long downloadId = intent.getLongExtra(DownloadManager.EXTRA_DOWNLOAD_ID, -1);
                Timber.d("downloadId:{}" + downloadId);
                DownloadManager downloadManager = (DownloadManager) context.getSystemService(DOWNLOAD_SERVICE);
                String type = null;
                if (downloadManager != null) {
                    type = downloadManager.getMimeTypeForDownloadedFile(downloadId);
                    Timber.d("getMimeTypeForDownloadedFile:{}" + type);
                    if (TextUtils.isEmpty(type)) {
                        type = "*/*";
                    }
                    Uri uri = downloadManager.getUriForDownloadedFile(downloadId);
                    Timber.d("UriForDownloadedFile:{}" + uri);
                    if (uri != null) {
                        Intent handlerIntent = new Intent(Intent.ACTION_VIEW);
                        handlerIntent.setDataAndType(uri, type);
                        context.startActivity(handlerIntent);
                    }
                }

            }

        }
    }


    // 调起支付宝并跳转到指定页面
    private void startAlipayActivity(String url) {
        Intent intent;
        try {
            intent = Intent.parseUri(url,
                    Intent.URI_INTENT_SCHEME);
            intent.addCategory(Intent.CATEGORY_BROWSABLE);
            intent.setComponent(null);
            startActivity(intent);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }


    private void openImageChooserActivity(String acceptType) {
        if ("video/*".equals(acceptType)) {
            isUploadVideo = true;
        } else {
            isUploadVideo = false;
        }
//        Intent intent = new Intent(this, SelectPicActivity.class);
//        intent.putExtra("isUploadVideo", isUploadVideo);
////        intent.putExtra("NoCamera", true);
////        intent.putExtra("isSelectHead", true);
////                bd.jumpResult(intent,2);
//        startActivityForResult(intent, FILE_CHOOSER_RESULT_CODE);

//        Intent intent = new Intent();
//        intent.setType("image/*");
//        intent.setAction(Intent.ACTION_PICK);
//       // intent.setData(android.provider.MediaStore.Images.Media.EXTERNAL_CONTENT_URI);
//		startActivityForResult(intent, FILE_CHOOSER_RESULT_CODE);


//        Intent i = new Intent(Intent.ACTION_GET_CONTENT);
//        i.addCategory(Intent.CATEGORY_OPENABLE);
//        i.setType("image/*");
//        startActivityForResult(Intent.createChooser(i, "Image Chooser"), FILE_CHOOSER_RESULT_CODE);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == FILE_CHOOSER_RESULT_CODE) {
            if (null == uploadMessage && null == uploadMessageAboveL) return;
            Uri result = data == null || resultCode != RESULT_OK ? null : Uri.fromFile(new File(data.getStringExtra("picPath")));
            if (uploadMessageAboveL != null) {
                onActivityResultAboveL(requestCode, resultCode, data);
            } else if (uploadMessage != null) {
                uploadMessage.onReceiveValue(result);
                uploadMessage = null;
            }
        } else if (requestCode == 0) {
            if (resultCode != RESULT_OK) return;
            int result = data.getIntExtra("result", 0);
            Timber.e("返回 result： %s",result);
            wv_web.loadUrl("javascript:appGoBackH5(\"" + result + "\")");
        }
    }

    @TargetApi(Build.VERSION_CODES.LOLLIPOP)
    private void onActivityResultAboveL(int requestCode, int resultCode, Intent intent) {
        if (requestCode != FILE_CHOOSER_RESULT_CODE || uploadMessageAboveL == null)
            return;
        Uri[] results = null;
        if (resultCode == Activity.RESULT_OK) {
            if (intent != null) {
                Uri uri = Uri.fromFile(new File(intent.getStringExtra("picPath")));
                String dataString = uri.toString();
//                ClipData clipData = intent.getClipData();
//                if (clipData != null) {
//                    results = new Uri[clipData.getItemCount()];
//                    for (int i = 0; i < clipData.getItemCount(); i++) {
//                        ClipData.Item item = clipData.getItemAt(i);
//                        results[i] = item.getUri();
//                    }
//                }
                if (dataString != null)
                    results = new Uri[]{Uri.parse(dataString)};
            }
        }
        uploadMessageAboveL.onReceiveValue(results);
        uploadMessageAboveL = null;
    }


    //设置回退
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        if ((keyCode == KeyEvent.KEYCODE_BACK)) {

            if (isback && wv_web.canGoBack()) {
                wv_web.goBack();
                return true;
            }


        }

        return super.onKeyDown(keyCode, event);
    }


    @Override
    public void onBackPressed() {
        // Notify the VideoEnabledWebChromeClient, and handle it ourselves if it doesn't handle it
        if (!webChromeClient.onBackPressed()) {
            if (wv_web.canGoBack()) {
                wv_web.goBack();
            } else {
                // Close app (presumably)
                super.onBackPressed();
            }
        }
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (wv_web != null) {
            wv_web.clearCache(true);
            wv_web = null;

        }

        System.exit(0);
    }








}
