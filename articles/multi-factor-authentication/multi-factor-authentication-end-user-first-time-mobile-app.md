<properties
	pageTitle="Utilizzare app per dispositivi mobili come metodo di contatto con Azure Multi-Factor Authentication | Microsoft Azure"
	description="Questo articolo illustra come usare un'app mobile come metodo di contatto principale perAzure Multi-Factor Authentication."
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

Se si vuole usare l'app Microsoft Authenticator come metodo di contatto principale, è possibile vedere questo articolo, che illustra come impostare Azure Multi-Factor Authentication per usare l'app per dispositivi mobili come metodo di contatto principale.

L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## Usare Microsoft Authenticator come metodo di contatto


1. Nella schermata **Verifica aggiuntiva di sicurezza** selezionare **App per dispositivi mobili** dall'elenco a discesa.
2. Selezionare **Notifica** o **Password monouso**, quindi fare clic su **Imposta**.

	![Schermata della verifica aggiuntiva di sicurezza](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)  

3. Sul telefono in cui è installata l'app Microsoft Authenticator avviare l'app e fare clic sul **+** per aggiungere un account.
4. Specificare che si desidera aggiungere un account aziendale o dell'istituto di istruzione. Verrà visualizzata il lettore di codici QR. Se la fotocamera non funziona correttamente, è possibile selezionare di inserire manualmente le informazioni della società. Per ulteriori informazioni, vedere l'articolo su come [aggiungere manualmente un account](#add-an-account-manually).

	![Schermata relativa alla selezione di un account](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)  

	![Schermata in cui viene scelto se analizzare un codice QR o immettere un account manualmente](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan4.png)  

5. Eseguire la scansione dell'immagine del codice QR visualizzata nella schermata di configurazione dell'app mobile. Fare clic su **Done** (Fine) per chiudere la schermata del codice QR.

	![Schermata di codice QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)  

	Se non si riesce a ottenere il codice QR per la scansione, è possibile immettere manualmente le informazioni.

	![Schermata di immissione manuale delle informazioni](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)  

6. Una volta completata l'attivazione sul telefono, selezionare **Contact me** (Contattami). Viene inviata una notifica o un codice di verifica al telefono cellulare. Selezionare **Verify** (Verifica).

	![Schermata in cui selezionare la verifica dell'accesso](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)  

7. Se richiesto dall'azienda, immettere il PIN per approvare la verifica di accesso.

	![Casella per l'immissione di un PIN](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)  

8. Una volta immesso il PIN, selezionare **Close** (Chiudi). A questo punto, la verifica avrà esito positivo.
9. Si consiglia di immettere il proprio numero di telefono cellulare, nel caso in cui si perda l'accesso all'app per dispositivi mobili. Specificare il paese dall'elenco a discesa e immettere il numero di telefono cellulare nella casella accanto al paese. Selezionare **Avanti**.
10. Ora il metodo di contatto è impostato. A questo punto è possibile configurare le password delle app non basate su browser, ad esempio Outlook 2010 o versioni precedenti. Se non si utilizzano queste app, selezionare **Done** (Fine). In caso contrario, procedere al prossimo passaggio.

	![Schermata per la creazione di una password dell'app](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)  

11. Se si utilizzano app non basate su browser, copiare la password fornita e incollarla nelle applicazioni. Per i passaggi su singole app, come ad esempio Outlook e Lync, vedere Come modificare la password nell'email con la password dell'app e Come modificare la password dell'applicazione con la password dell'app.
12. Selezionare **Done** (Fine).


## Aggiungere manualmente un account
Per aggiungere manualmente un account:

1. Fare clic sul pulsante **Enter account manually** (Immetti account manualmente).

	![Schermata per l'immissione di codice e URL](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)  

	![Schermata per l'immissione di codice e URL](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)  

2. Se si dispone di un account in cui è già presente Azure Multi-Factor Authentication, immettere il codice e l'URL fornito nella stessa pagina del codice a barre. Queste informazioni devono essere inserite nelle caselle **Code** (Codice) e **URL** dell'app per dispositivi mobili.

	![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)  

3. Al termine dell'attivazione, selezionare **Contact me** (Contattami). Viene inviata una notifica o un codice di verifica al telefono cellulare. Selezionare **Verify** (Verifica).

<!---HONumber=AcomDC_0914_2016-->