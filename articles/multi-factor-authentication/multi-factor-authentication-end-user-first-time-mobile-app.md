<properties 
	pageTitle="Utilizzare app per dispositivi mobili come metodo di contatto con Azure MFA" 
	description="Questa pagina mostrerà agli utenti l'utilizzo dell'app per dispositivi mobili come metodo di contatto principale per Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="billmath"/>

# Utilizzare app per dispositivi mobili come metodo di contatto con Azure Multi-Factor Authentication

Se si desidera utilizzare l'applicazione per dispositivi mobili come metodo di contatto principale, è possibile utilizzare questo articolo. Illustrerà come impostare l’autenticazione a più fattori per utilizzare la app per dispositivi mobili come metodo di contatto principale.

L’app Azure Authenticator è disponibile per [Windows Phone](http://www.windowsphone.com/it-it/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).

## Utilizzare un’app per dispositivi mobili come metodo di contatto
<ol>
<li>Selezionare App per dispositivi mobili dall'elenco a discesa.</li>

<center>! [Installazione] (. / media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)</center>

<li>Selezionare Notifica o Password monouso e fare clic su Imposta</li>
<li>Sul telefono in cui è installata l'app Azure Authenticator, avviare l'applicazione e fare clic su analisi del codice a barre.</li>


<center>![Installazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)</center>


<li>Eseguire la scansione del codice a barre visualizzata con la schermata di configurazione dell'app per dispositivi mobili. Fai clic su Done per chiudere la schermata del codice a barre. Se non si riesce a ottenere il codice a barre per l'analisi, è possibile immettere manualmente le informazioni.</li>

<center>! [Installazione] (. / media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)</center>

<li>Sul telefono, inizierà l’attivazione, una volta completata questa operazione fare clic su Contattami. Verrà inviata una notifica o un codice di verifica al telefono cellulare. Fare clic su verifica.</li>

<center>! [Installazione] (. / media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)</center>

<li>Fare clic su Chiudi. A questo punto, la verifica avrà esito positivo.</li>
<li>Ora si consiglia di immettere il proprio numero di telefono cellulare, nel caso in cui si perda l'accesso all'app per dispositivi mobili. <li>Specificare il paese dall'elenco a discesa e immettere il numero di telefono cellulare nella casella accanto al paese. Fare clic su Avanti</li> <li>A questo punto, è stato impostato il metodo di contatto e ora è arrivato il momento di impostare le password per le app non basate su browser come Outlook 2010 o versione precedente. Se non si utilizzano queste app fare clic su **Done**. In caso contrario, continuare con il passaggio successivo.</li>

<center>! [Installazione] (. / media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)</center>

<li>Se si utilizzano queste app, copiare la password dell’app fornita e incollare la password nell'applicazione non basata su browser. Per i passaggi su singole applicazioni, come ad esempio Outlook e Lync, vedere Come modificare la password nell’email con la password dell’app e Come modificare la password dell'applicazione con la password dell’app.</li>
<li>Fare clic su Done.</li>

<!---HONumber=July15_HO2-->