1. Aprire Android SDK Manager facendo clic sull'icona all'estremità destra della barra degli strumenti di Android Studio (in caso di dubbio, passare il puntatore del mouse sull'icona). Individuare la versione di destinazione di Android SDK usata nel progetto, aprirla, quindi scegliere **Google APIs**, se non è già installato.

2. Scorrere verso il basso fino all'opzione **Extras**, espanderla, quindi scegliere **Google Play Services** come illustrato di seguito. Fare clic su **Install Packages**. Prendere nota del percorso dell'SDK per l'uso nel prossimo passaggio. 

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. Aprire il file **build.gradle** nella directory dell'applicazione.

	![](./media/mobile-services-android-get-started-push/android-studio-push-build-gradle.png)

4. Aggiungere questa riga sotto *dependencies*: 

   		compile 'com.google.android.gms:play-services-base:6.5.87'

5. In *defaultConfig* impostare *minSdkVersion* su 9.
 
6. Fare clic sul pulsante **Sync Project with Gradle Files** nella barra degli strumenti.

7. Aprire **AndroidManifest.xml** e aggiungere il seguente tag per il tag *application*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 




<!--HONumber=49-->