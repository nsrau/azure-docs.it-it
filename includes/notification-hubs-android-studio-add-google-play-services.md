1. Aprire Android SDK Manager facendo clic sull'icona della barra degli strumenti di Android Studio o su **Tools** -> **Android** -> **SDK Manager** nel menu. Trovare la versione di destinazione di Android SDK usata nel progetto, aprirla facendo clic su **Show Package Details** e quindi scegliere **Google APIs**, se non è già installato.

2. Fare sulla scheda **SDK Tools**. Se Google Play Service non è già installato, fare clic su **Google Play Services** come mostrato di seguito. Fare quindi clic su **Apply** per installarlo.
 
	Prendere nota del percorso dell'SDK per l'uso in un passaggio successivo.

   	![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)


3. Aprire il file **build.gradle** nella directory dell'app.

	![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)

4. In *dependencies* aggiungere questa riga:

   		compile 'com.google.android.gms:play-services-base:6.5.87'

5. In *defaultConfig* impostare il valore di *minSdkVersion* su 9.
 
6. Fare clic sul pulsante **Sync Project with Gradle Files** sulla barra degli strumenti.

7. Aprire **AndroidManifest.xml** e aggiungere il tag seguente al tag *application*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=AcomDC_1217_2015-->