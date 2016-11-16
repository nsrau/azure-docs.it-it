
1. In Mac visitare il [portale di Azure]. Fare clic su **Esplora tutto** > **App per dispositivi mobili** > back-end appena creato. Nelle impostazioni dell'app per dispositivi mobili fare clic su **Avvio rapido** > **iOS (Objective-C)**. Se si preferisce Swift, fare clic su **Avvio rapido** > **iOS (Swift)**. In **Download and run your iOS project** (Scarica ed esegui il progetto iOS) fare clic su **Scarica**. In questo modo, viene scaricato il progetto Xcode per un'app preconfigurata da connettere al back-end. Aprire il progetto con Xcode.
2. Fare clic su **Esegui** per creare il progetto e avviare l'app nel simulatore iOS.
3. Nell'app digitare un testo significativo, ad esempio *Complete the tutorial* , quindi fare clic sull'icona con il segno PIÙ (**+**). Verrà inviata una richiesta POST al back-end di Azure distribuito in precedenza. Il back-end inserisce i dati della richiesta nella tabella SQL TodoItem e restituisce le informazioni sugli elementi appena archiviati all'app per dispositivi mobili. L'app per dispositivi mobili consente di visualizzare questi dati nell'elenco. 
   
       ![](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[portale di Azure]: https://portal.azure.com/


<!--HONumber=Nov16_HO2-->


