
1. Visitare il [portale di Azure]. Fare clic su **Esplora tutto** > **App per dispositivi mobili** > e sul back-end appena creato. Nelle impostazioni dell'app per dispositivi mobili fare clic su **Quickstart** > **Cordova**. In **Configure your client application**, selezionare **Create a New App** e quindi fare clic su **Download**. In questo modo viene scaricato il progetto Cordova completo per un'app preconfigurata da connettere al back-end.

2. Decomprimere il file con estensione zip scaricato in una directory sul disco rigido.

3. Aprire il progetto in **Visual Studio**. Fare clic su **Apri** > **Progetto/Soluzione**.

4. Trovare il file _sitename_.sln e fare clic su **Apri**.

5. L'emulatore predefinito è **Ripple - Nexus (Galaxy)**. Fare clic sulla freccia a discesa accanto all'emulatore e selezionare **Emulatore Android di Google**.

6. Fare clic su **Emulatore Android di Google**. Il progetto verrà compilato e quindi eseguito. L'utente potrebbe visualizzare un avviso di sicurezza dell'emulatore Android di Google che richiede l'accesso alla rete. Alla fine verrà visualizzato l'emulatore Android di Google e l'applicazione verrà eseguita.

7. Nell'app digitare un testo significativo, ad esempio _Complete the tutorial_ quindi fare clic sul pulsante ’Add’. Verrà inviata una richiesta POST al back-end di Azure distribuito in precedenza. Il back-end inserisce i dati della richiesta nella tabella SQL TodoItem e restituisce le informazioni sugli elementi appena archiviati all'app per dispositivi mobili. L'app per dispositivi mobili consente di visualizzare questi dati nell'elenco.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)

[portale di Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0211_2016-->