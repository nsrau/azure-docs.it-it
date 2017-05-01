#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Configurazione del progetto iOS in Xamarin Studio
1. In Xamarin Studio aprire **Info.plist** e aggiornare il valore in **Bundle Identifier** (Identificatore bundle) con l'ID creata in precedenza.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Scorrere verso il basso fino a **Background Modes** (Modalità in background). Selezionare le caselle **Enable Background Modes** (Abilita modalità in background) e **Remote notifications** (Notifiche remote).

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Fare doppio clic sul progetto nel riquadro delle soluzioni per aprire **Opzioni progetto**.
4. Scegliere **iOS Bundle Signing** (Firma bundle iOS) in **Genera** e selezionare i valori corrispondenti per l'identità e il profilo di provisioning appena configurati per questo progetto.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Per effetto di questa operazione, il progetto userà il nuovo profilo per la firma del codice. Per la documentazione ufficiale, vedere la pagina relativa al [provisioning del dispositivo Xamarin].

#### <a name="configure-the-ios-project-in-visual-studio"></a>Configurazione del progetto iOS in Visual Studio
1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto e quindi scegliere **Proprietà**.
2. Nelle pagine delle proprietà fare clic sulla scheda **iOS Application** (Applicazione iOS) e aggiornare il campo **Identifier** (Identificatore) con l'ID creato in precedenza.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. Nella scheda **iOS Bundle Signing** (Firma bundle iOS) selezionare i valori corrispondenti per identità e profilo di provisioning appena configurati per questo progetto.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Per effetto di questa operazione, il progetto userà il nuovo profilo per la firma del codice. Per la documentazione ufficiale, vedere la pagina relativa al [provisioning del dispositivo Xamarin].
4. Fare doppio clic su Info.plist per aprirlo e abilitare **RemoteNotifications** in **Background Modes** (Modalità in background).

[provisioning del dispositivo Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
