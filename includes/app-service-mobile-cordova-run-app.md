
1. Visitare il [portale di Azure].
2. Fare clic su **Servizi app** e quindi selezionare il back-end che è stato creato.
3. Nelle impostazioni dell'app per dispositivi mobili fare clic su **Quickstart** > **Cordova**.
![Portale di Azure con app per dispositivi mobili del servizio app, avvio rapido evidenziato][quickstart]
4. In **Configure your client application** (Configura applicazione client), selezionare **Create a New App** (Crea nuova app) e quindi fare clic su **Download** (Scarica).
2. Decomprimere il file con estensione zip scaricato in una directory sul disco rigido, passare al file della soluzione con estensione sln e aprirlo con Visual Studio.
3. In Visual Studio, scegliere la piattaforma di soluzioni (Android, iOS o Windows) dall'elenco a discesa accanto alla freccia di inizio. Selezionare un dispositivo di distribuzione specifico o un emulatore facendo clic sul menu a discesa sulla freccia verde. È possibile usare la piattaforma Android e l'emulatore Ripple predefiniti. Per esercitazioni più avanzate (ad esempio, sulle notifiche push) sarà necessario selezionare un dispositivo o un emulatore supportato.
4. Per compilare ed eseguire l'app Cordova, premere F5 o fare clic sulla freccia verde. Se nell'emulatore viene visualizzata una finestra di dialogo di sicurezza che richiede di accedere alla rete, accettare.
5. Dopo l'avvio dell'app nel dispositivo o nell'emulatore, digitare un testo significativo in **Enter new text** (Immettere nuovo testo), ad esempio *Complete the tutorial* (Completare l'esercitazione) e quindi fare clic sul pulsante **Add** (Aggiungi).

Il back-end inserisce i dati della richiesta nella tabella TodoItem del database SQL e restituisce informazioni sugli elementi appena archiviati all'app per dispositivi mobili. L'app per dispositivi mobili consente di visualizzare questi dati nell'elenco.

È possibile ripetere i passaggi da 3 a 5 per altre piattaforme.

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png

<!-- URLs -->
[portale di Azure]: https://portal.azure.com/
