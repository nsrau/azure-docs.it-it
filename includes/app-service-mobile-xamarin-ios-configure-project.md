###In Xamarin Studio

1. In Xamarin Studio aprire **Info.plist** e aggiornare il valore in **Bundle Identifier** con l'ID creato in precedenza.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)

2. Scorrere verso il basso fino a **Background Modes** e selezionare le caselle **Enable Background Modes** e **Remote notifications**.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)

3. Fare doppio clic sul progetto nel riquadro delle soluzioni per aprire **Project Options**.

4.  Scegliere **iOS Bundle Signing** in **Build** e selezionare i valori corrispondenti per **Identity** e **Provisioning profile** appena configurati per questo progetto.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

    Per effetto di questa operazione, il progetto userà il nuovo profilo per la firma del codice. Per la documentazione ufficiale, vedere la pagina relativa al [provisioning del dispositivo Xamarin].

### In Visual Studio

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto e quindi scegliere **Proprietà**.

3. Nelle pagine delle proprietà fare clic sulla scheda **iOS Application** e aggiornare il campo **Identifier** con l'ID creato in precedenza.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)

4. Nella scheda **iOS Bundle Signing** selezionare i valori corrispondenti per **Identity** e **Provisioning profile** appena configurati per questo progetto.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Per effetto di questa operazione, il progetto userà il nuovo profilo per la firma del codice. Per la documentazione ufficiale, vedere la pagina relativa al [provisioning del dispositivo Xamarin].


[provisioning del dispositivo Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/

<!---HONumber=Oct15_HO3-->