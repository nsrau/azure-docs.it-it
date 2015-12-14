1. Aprire Android SDK Manager facendo clic sull'icona della barra degli strumenti di Android Studio o su **Tools** -> **Android** -> **SDK Manager** nel menu. Individuare la versione di destinazione di Android SDK usata nel progetto, aprirla, quindi scegliere **Google APIs**, se non è già installato.

2. Passare a **File**, **Struttura progetto**. Abilitare le notifiche push in **Notifiche**.

3. Aprire **AndroidManifest.xml** e aggiungere il tag seguente al tag *application*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=AcomDC_1203_2015-->