1. Aprire Android SDK Manager facendo clic sull'icona della barra degli strumenti di Android Studio o su **Tools** -> **Android** -> **SDK Manager** nel menu. Individuare la versione di destinazione di Android SDK usata nel progetto, aprirla, quindi scegliere **Google APIs**, se non è già installato.

2. Scorrere verso il basso fino all'opzione **Extras**, espanderla e quindi scegliere **Google Play Services**, come illustrato di seguito. Fare clic su **Install Packages**. Prendere nota del percorso dell'SDK per l'uso nel prossimo passaggio.

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. Aprire il file **build.gradle** nella directory dell'app.

	![](./media/mobile-services-android-get-started-push/android-studio-push-build-gradle.png)

4. In *dependencies* aggiungere questa riga:

   		compile 'com.google.android.gms:play-services-gcm:8.4.0'

5. In *defaultConfig* impostare il valore di *minSdkVersion* su 9.
 
6. Fare clic sul pulsante **Sync Project with Gradle Files** sulla barra degli strumenti.

7. Aprire **AndroidManifest.xml** e aggiungere il tag seguente al tag *application*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=AcomDC_0204_2016-->