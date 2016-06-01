<properties 
	pageTitle="Utilizzare app per dispositivi mobili come metodo di contatto con Azure MFA" 
	description="Questa pagina mostrerà agli utenti l'utilizzo dell'app per dispositivi mobili come metodo di contatto principale per Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenp" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/12/2016" 
	ms.author="billmath"/>

# Utilizzare app per dispositivi mobili come metodo di contatto con Azure Multi-Factor Authentication

Se si desidera utilizzare l'applicazione per dispositivi mobili come metodo di contatto principale, è possibile utilizzare questo articolo. Illustrerà come impostare l’autenticazione a più fattori per utilizzare la app per dispositivi mobili come metodo di contatto principale.

L’app Azure Authenticator è disponibile per [Windows Phone](http://www.windowsphone.com/it-IT/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).

## Utilizzare un’app per dispositivi mobili come metodo di contatto


- Selezionare App per dispositivi mobili dall'elenco a discesa.


![Impostazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

- Selezionare Notifica o Password monouso e fare clic su Imposta.
- Sul telefono in cui è installata l'app Azure Authenticator, avviare l'applicazione e fare clic su analisi del codice a barre. Per aggiungere un account che ha già Azure MFA o un account di terze parti, vedere [Aggiunta manuale di un account](#adding-an-account-manually).

![Impostazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

- Eseguire la scansione del codice a barre visualizzata con la schermata di configurazione dell'app per dispositivi mobili. Fai clic su Done per chiudere la schermata del codice a barre. Se non si riesce a ottenere il codice a barre per l'analisi, è possibile immettere manualmente le informazioni.

![Impostazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

- Sul telefono, inizierà l’attivazione, una volta completata questa operazione fare clic su Contattami. Verrà inviata una notifica o un codice di verifica al telefono cellulare. Fare clic su verifica.

![Impostazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

- Fare clic su Chiudi. A questo punto, la verifica avrà esito positivo.
- Ora si consiglia di immettere il proprio numero di telefono cellulare, nel caso in cui si perda l'accesso all'app per dispositivi mobili.
- Specificare il paese dall'elenco a discesa e immettere il numero di telefono cellulare nella casella accanto al paese. Fare clic su Avanti.
- A questo punto, è stato impostato il metodo di contatto ed è il momento di impostare le password per le app non basate su browser, come Outlook 2010 o versione precedente. Se non si usano queste app, fare clic su **Operazione completata**. In caso contrario, continuare con il passaggio successivo.

![Impostazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

- Se si utilizzano queste app, copiare la password dell’app fornita e incollare la password nell'applicazione non basata su browser. Per i passaggi su singole applicazioni, come ad esempio Outlook e Lync, vedere Come modificare la password nell’email con la password dell’app e Come modificare la password dell'applicazione con la password dell’app.
- Fare clic su Done.


## Aggiunta manuale di un account
Per aggiungere un account manualmente, selezionare il pulsante Immetti manualmente.

![Impostazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)

Se si ha un account dove Azure MFA è già presente, immettere il codice e l'URL fornito nella stessa pagina dove è visualizzato il codice a barre. Le informazioni devono essere inserite nelle caselle del codice e dell'URL dell'app per dispositivi mobili. Viene avviata l'attivazione.

![Impostazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

Al termine, fare clic su Contatti. Verrà inviata una notifica o un codice di verifica al telefono cellulare. Fare clic su verifica. Per terminare, seguire i passaggi precedenti a partire dal numero 6.

Se si usa un account di terze parti con l'app per dispositivi mobili, immettere il nome dell'account e la chiave di sicurezza nelle apposite caselle e quindi attivare l'account. Dopo aver completato la procedura e verificato l'account, seguire i passaggi precedenti a partire dal numero 6.


![Impostazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/add3rdparty.png)

>[AZURE.NOTE]Il messaggio "Aggiungi account lavoro" eventualmente visualizzato riguarda l'aggiunta all'area di lavoro, non la Multi-Factor Authentication. È possibile ignorarlo.
 

<!---HONumber=AcomDC_0518_2016-->