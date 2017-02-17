# FirstMaBeeeAndroid

200~# FirstMaBeeeAndroid

## 概要

- とりあえず簡単にMaBeeeを使ってみることを目的としたサンプルプロジェクトです。
- ここではこのサンプルプロジェクトを最初からつくる手順をご紹介します。


## ライブラリのインポート

AndroidStudioで[build.gradle(Module: app)](https://github.com/novars-jp/FirstMaBeeeAndroid/blob/master/app/build.gradle)に以下を追加します。

```gradle
repositories {
    maven { url 'http://raw.github.com/novars-jp/MaBeeeAndroidSDK/master/repository/' }
}
dependencies {
    compile 'jp.novars.mabeee.sdk:sdk:1.1'
}
```

## ソースの編集

### 1. activity_main.xmlのレイアウト編集

Scanボタン1つと、SeekBarをもつ画面を作ります。

- [activity_main.xml](https://github.com/novars-jp/FirstMaBeeeAndroid/blob/master/app/src/main/res/layout/activity_main.xml)

### 2. ライブラリの初期化

- [MainActivity.java](https://github.com/novars-jp/FirstMaBeeeAndroid/blob/master/app/src/main/java/jp/novars/firstmabeee/MainActivity.java)のonCreateに以下のソース追加します。

```java
App.getInstance().initializeApp(getApplicationContext());
```

- [Appクラス](http://developer.novars.jp/mabeee/android/javadoc/jp/novars/mabeee/sdk/App.html)はこのSDKの中心となるクラスで、Android端末のBluetoothや接続済みのMaBeeeデバイスを管理します。
- App.getInstance()でAppクラスのSingletonインスタンスを取得します。
- AppクラスのinitializeApp(Context context)関数で、Appクラス、ライブラリの初期化を行ないます。
 - これは一般的にはApplicationクラスで最初に呼び出すか、複数回呼び出しても問題ないので、最初のActivityのonCreateなどで呼び出してください。


### 3. Scanボタンのイベント編集

- ScanボタンにOnClickListenerを設定し、ScanAcitivtyを呼び出す処理を追加します。

```java
Button scanButton = (Button)findViewById(R.id.scanButton);
scanButton.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
       Intent intent = new Intent(MainActivity.this, ScanActivity.class);
       startActivity(intent);
    }
 });
```

- ScanActivityはMaBeeeデバイスのスキャンと接続を簡単に実装できるUIを提供します。


### 4. SeekBarのイベント編集

- SeekBarにOnSeeekBarChangeListenerを設定し、MaBeeeデバイスの出力を変更する処理を追加します。

```java
SeekBar seekBar = (SeekBar)findViewById(R.id.seekBar);
seekBar.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
    @Override
    public void onProgressChanged(SeekBar seekBar, int i, boolean b) {
        //Log.d("", "" + seekBar.getProgress());
        Device[] devices = App.getInstance().getDevices();
        for (Device device : devices) {
            device.setPwmDuty(seekBar.getProgress());
        }
    }

    @Override
    public void onStartTrackingTouch(SeekBar seekBar) {
    }

    @Override
    public void onStopTrackingTouch(SeekBar seekBar) {
    }
});
```

- App.getInstance().getDevices()で接続済みのMaBeeeデバイスの配列が取得できます。
- DeviceクラスのsetPwmDuty関数で、MaBeeeデバイスの出力を0から100の範囲で調整できます。


### スキャン実行

- ビルドして実行します。
- AndroidのBluetoothがONになっているかを確認してください。
- MaBeeeをおもちゃなどにセットして、おもちゃなどの電源をONにしてください。
- スキャンボタンを押すと、ScanActivityが表示されます。
- セルには、MaBeeeの名前、RSSI、接続しているかどうかのアイコンが表示されます。

### 接続

- セルをタップすると接続します。
- 接続すると赤いアイコンになります。

### 出力の調整

- SeekBarを調整すると、MaBeeeデバイスの出力が変化します。
