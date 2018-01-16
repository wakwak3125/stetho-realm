# Stetho-Realm

Stetho-Realm は、[Stetho](https://facebook.github.io/stetho)  で [Realm](https://realm.io/)のデータベースの内容を表示するようにするための Stetho モジュールです。

Stetho がもともと持っている SQLite データベースの内容を表示する機能を置き換える形で Realm データベースの内容を表示します。

## Set-up

### Download
```groovy
repositories {
    maven { url "https://jitpack.io" }
}

dependencies {
    implementation 'com.facebook.stetho:stetho:1.5.0'
    implementation 'com.github.wakwak3125:stetho-realm:2.1.3'
}
```

Stetho-Realm 2.0 は、 Stetho 1.1以降、Realm 4.0.0 以降に対応しています。

### アプリケーションへの組み込み
`Application` クラスで以下のように Stetho の初期化を行ってください。

`RealmInspectorModulesProvider.ProviderBuilder` を用いて `InspectorModulesProvider` を作成します。
`RealmInspectorModulesProvider.ProviderBuilder` はデフォルトのモジュールリストにRealm 用のモジュールを追加します。
`RealmInspectorModulesProvider.ProviderBuilder#baseProvider(InspectorModulesProvider)`を用いて
デフォルト以外の InspectorModulesProvider を使用させることもできます。

以下はデフォルトの設定で有効になっている SQLite モジュールに Realm モジュールを追加して
使用する例です。

```java
public class MyApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();

        Realm.init(this);

        Stetho.initialize(
                Stetho.newInitializerBuilder(this)
                        .enableDumpapp(Stetho.defaultDumperPluginsProvider(this))
                        .enableWebKitInspector(RealmInspectorModulesProvider.builder(this).build())
                        .build());
    }
}
```

`RealmInspectorModulesProvider.ProviderBuilder` の各種メソッドを呼び出すことで、データベースファイルを
探すフォルダの指定、表示する件数の上限、表示をidの昇順にする加か降順にするか、メタデータのテーブル
(pk と metadataテーブル)の情報を表示するかどうか、復号に使用するキー、
データベースファイル名のパターンを指定することができます。

```java
    RealmInspectorModulesProvider.builder(this)
            .withFolder(getCacheDir())
            .withEncryptionKey("encrypted.realm", key)
            .withMetaTables()
            .withDescendingOrder()
            .withLimit(1000)
            .databaseNamePattern(Pattern.compile(".+\\.realm"))
            .build()
```

## デバッグビルドのみに Stetho を組み込む方法

英語ですが以下のページを参考にしてください。

http://littlerobots.nl/blog/stetho-for-android-debug-builds-only/

## License

Stetho-Realm is BSD-licensed.

## TODO

* SQLite モジュールとの共存
* 読み込み以外の実装

## deployメモ

1. バージョン番号を変更
2. ./gradlew clean assemble :stetho_realm:publishMavenPublicationToMavenRepository
3. git に add して commit して push (masterブランチで！)
