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
	ms.date="08/30/2016" 
	ms.author="billmath"/>

# Utilizzare app per dispositivi mobili come metodo di contatto con Azure Multi-Factor Authentication

Se si vuole usare l'app Microsoft Authenticator come metodo di contatto principale, è possibile vedere questo articolo. Illustrerà come impostare l’autenticazione a più fattori per utilizzare la app per dispositivi mobili come metodo di contatto principale.

L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## Per usare Microsoft Authenticator come metodo di contatto


- Nella schermata Verifica aggiuntiva di sicurezza selezionare App per dispositivi mobili dall'elenco a discesa.


![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

- Selezionare Notifica o Password monouso e fare clic su Imposta.
- Sul telefono in cui è installata l'app Microsoft Authenticator avviare l'applicazione e fare clic sul "+" per aggiungere un nuovo account. Quindi specificare che si desidera aggiungere un account aziendale o dell'istituto di istruzione che avvierà la scansione del codice QR. Se la fotocamera non funziona correttamente, è possibile selezionare di inserire manualmente le informazioni della società. [Aggiunta manuale di un account](#adding-an-account-manually).

![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan4.png)

![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

- Eseguire la scansione dell'immagine del codice QR visualizzata con la schermata di configurazione dell'app per dispositivi mobili. Fai clic su Done per chiudere la schermata del codice QR.

![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- Se non si riesce a ottenere il codice QR per la scansione, è possibile immettere manualmente le informazioni.

![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

- Sul telefono, inizierà l’attivazione, una volta completata questa operazione fare clic su Contattami. Verrà inviata una notifica o un codice di verifica al telefono cellulare. Fare clic su verifica.

![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

- Alcune aziende potrebbero richiedere un PIN durante la verifica.

![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)


- Al termine è possibile fare clic su Chiudi. A questo punto, la verifica avrà esito positivo.
- Ora si consiglia di immettere il proprio numero di telefono cellulare, nel caso in cui si perda l'accesso all'app per dispositivi mobili.
- Specificare il paese dall'elenco a discesa e immettere il numero di telefono cellulare nella casella accanto al paese. Fare clic su Avanti.
- A questo punto, è stato impostato il metodo di contatto ed è il momento di impostare le password per le app non basate su browser, come Outlook 2010 o versione precedente. Se non si usano queste app, fare clic su **Operazione completata**. In caso contrario, continuare con il passaggio successivo.

![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

- Se si utilizzano queste app, copiare la password dell’app fornita e incollare la password nell'applicazione non basata su browser. Per i passaggi su singole applicazioni, come ad esempio Outlook e Lync, vedere Come modificare la password nell’email con la password dell’app e Come modificare la password dell'applicazione con la password dell’app.
- Fare clic su Done.


## Aggiunta manuale di un account
Per aggiungere un account manualmente, selezionare il pulsante Immetti manualmente.

![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)


![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

Se si ha un account dove Azure MFA è già presente, immettere il codice e l'URL fornito nella stessa pagina dove è visualizzato il codice a barre. Le informazioni devono essere inserite nelle caselle del codice e dell'URL dell'app per dispositivi mobili. Viene avviata l'attivazione.

![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

Al termine, fare clic su Contatti. Verrà inviata una notifica o un codice di verifica al telefono cellulare. Fare clic su verifica.



 

<!---HONumber=AcomDC_0831_2016-->