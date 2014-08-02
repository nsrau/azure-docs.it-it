

1.  Aprire Android SDK Manager facendo clic su **Window** sulla barra degli strumenti nella parte superiore della schermata di Eclipse. Individuare la versione di destinazione di Android SDK specificata nelle proprietà del progetto, aprirla, quindi scegliere **Google APIs**.

2.  Scorrere verso il basso fino all'opzione **Extras**, espanderla, quindi scegliere **Google Play Services**, come illustrato di seguito. Fare clic su **Install Packages**. Prendere nota del percorso dell'SDK per l'utilizzo nel prossimo passaggio. Riavviare Eclipse.

	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)

3.  Installare Google Play Services SDK nel progetto. In Eclipse fare clic su **File**, quindi su **Import**. Selezionare **Android**, quindi **Existing Android Code into Workspace** e fare clic su **Next**. Fare clic su **Browse**, passare al percorso di Android SDK (di solito in una cartella denominata `adt-bundle-windows-x86_64`), quindi passare alla sottocartella `\extras\google\google_play_services\libproject` e selezionare la cartella google-play-services-lib; infine, scegliere **OK**. Selezionare la casella di controllo **Copy projects into workspace**, quindi fare clic su **Finish**.

    ![](./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png)

4.  È quindi necessario fare riferimento dal progetto alla libreria Google Play Services SDK importata in precedenza.

5.  In **Package Explorer** fare clic con il pulsante destro del mouse sul progetto e scegliere *Properties*.

6.  Nella parte sinistra della finestra Proprietà scegliere Android.

    ![](./media/mobile-services-android-get-started-push/mobile-google-set-project-properties.png)

7.  Nella sezione **Library** scegliere **Add**, quindi selezionare il progetto Google Play Services (*google-play-services-lib*) e fare clic su **OK**.

8.  Fare clic su **Apply** e quindi su **OK**.


