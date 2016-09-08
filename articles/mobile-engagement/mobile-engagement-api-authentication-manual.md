<properties 
	pageTitle="Eseguire l'autenticazione con le API REST di Mobile Engagement - installazione manuale"
	description="Descrive come configurare manualmente l'autenticazione per le API REST di Mobile Engagement" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="08/19/2016"
	ms.author="piyushjo"/>

# Eseguire l'autenticazione con le API REST di Mobile Engagement - installazione manuale

Questo è un documento in appendice per [eseguire l'autenticazione con le API REST di Mobile Engagement](mobile-engagement-api-authentication.md). Leggerlo prima per capire il contesto. Il documento descrive un modo alternativo per eseguire l'installazione singola al fine di configurare l'autenticazione per le API REST di Mobile Engagement tramite il portale di Azure.

>[AZURE.NOTE] Le istruzioni che seguono si basano sulla [guida di Active Directory](../resource-group-create-service-principal-portal.md) e sono state adattate ai requisiti per l'autenticazione per le API di Mobile Engagement. Consultare la guida per comprendere bene la seguente procedura.

1. Accedere all'account di Azure tramite il [portale classico](https://manage.windowsazure.com/).

2. Selezionare **Active Directory** dal riquadro di sinistra.

     ![selezionare Active Directory][1]

3. Scegliere la **directory predefinita di Active Directory** nel portale di Azure.

     ![scegliere la directory][2]

	>[AZURE.IMPORTANT] Questo approccio funziona solo se si utilizza la directory predefinita di Active Directory del proprio account e non funzionerà se si esegue il processo da una directory di Active Directory creata nell'account.

4. Per visualizzare le applicazioni nella directory, fare clic su **Applicazioni**.

     ![visualizzare le applicazioni][3]

5. Fare clic su **AGGIUNGI**.

     ![aggiungere un'applicazione][4]

6. Fare clic su **Aggiungi un'applicazione che l'organizzazione sta sviluppando**

     ![nuova applicazione][5]

6. Inserire il nome dell'applicazione e selezionare il tipo di applicazione come **APPLICAZIONE WEB E/O API WEB**, quindi fare clic su Avanti.

     ![assegnare un nome all'applicazione][6]

7. È possibile fornire un URL fittizio come **URL ACCESSO** e **URI ID APP**. Questi valori non vengono usati per questo scenario e gli URL non vengono convalidati.

     ![proprietà dell'applicazione][7]

8. Alla fine della procedura si otterrà un'app AAD con il nome specificato in precedenza simile al seguente. Si tratta di **AD\_APP\_NAME** e si deve prenderne nota.

     ![Nome dell'applicazione][8]

9. Fare clic sul nome dell'applicazione, poi fare clic su **Configura**.

     ![configurare l'applicazione][9]

10. Prendere nota dell'ID CLIENT che verrà utilizzato come **CLIENT\_ID** per le chiamate API.

     ![configurare l'applicazione][10]

11. Scorrere fino alla sezione **Chiavi** e aggiungere una chiave preferibilmente con 2 anni di durata (scadenza), quindi fare clic su **Salva**.

     ![configurare l'applicazione][11]


12. Copiare immediatamente il valore visualizzato per la chiave perché viene visualizzato soltanto ora e non verrà archiviato, quindi non verrà mai più visualizzato. In caso di smarrimento sarà necessario generare una nuova chiave. Questo sarà il **CLIENT\_SECRET** per le chiamate API.

     ![configurare l'applicazione][12]

	>[AZURE.IMPORTANT] Questa chiave scadrà alla fine della durata specificata. Ricordarsi di rinnovarla al momento opportuno, altrimenti l'autenticazione API non funzionerà più. È possibile anche eliminare e ricreare la chiave se si ritiene che sia stata compromessa.
 
13. Fare clic su **VISUALIZZA ENDPOINT** per aprire la finestra di dialogo **Endpoint dell'app**.

	![][13]

14. Nella finestra di dialogo Endpoint dell'app, copiare l'**ENDPOINT TOKEN OAUTH 2.0**.

	![][14]

15. L'endpoint avrà il formato seguente, in cui il GUID nell'URL è il **TENANT\_ID** e si deve prenderne nota:

		https://login.microsoftonline.com/<GUID>/oauth2/token

16. A questo punto si potranno configurare le autorizzazioni dell'applicazione. A tal fine, aprire il [portale di Azure](https://portal.azure.com).

17. Fare clic su **Gruppi di risorse** e individuare il gruppo di risorse **Mobile Engagement**.

	![][15]

18. Fare clic sul gruppo di risorse **Mobile Engagement** e passare al pannello **Impostazioni**.

	![][16]

19. Fare clic su **Utenti** nel pannello Impostazioni e fare clic su **Aggiungi** per aggiungere un utente.

	![][17]

20. Fare clic su **Selezionare un ruolo**.

	![][18]

21. Fare clic su **Proprietario**

	![][19]

22. Cercare il nome dell'applicazione **AD\_APP\_NAM** nella casella di ricerca. Qui non verrà visualizzato per impostazione predefinita. Dopo aver trovato il nome, selezionarlo e fare clic su **Seleziona** nella parte inferiore del pannello.

	![][20]

23. Nel pannello **Aggiungi accesso** viene visualizzato come **1 utente, 0 gruppi**. Fare clic su **OK** nel pannello per confermare la modifica.

	![][21]

Ora la configurazione di AAD necessaria è terminata e tutto è pronto per le chiamate API.

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png

<!---HONumber=AcomDC_0824_2016-->