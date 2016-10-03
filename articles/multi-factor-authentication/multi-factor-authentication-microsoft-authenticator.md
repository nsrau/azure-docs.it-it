<properties
	pageTitle="App Microsoft Authenticator per telefoni cellulari | Microsoft Azure"
	description="Informazioni su come effettuare l'aggiornamento alla versione più recente di Azure Authenticator."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>  

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="kgremban"/>  

# Microsoft Authenticator

L'app Microsoft Authenticator offre un livello di sicurezza aggiuntivo in un account Azure (ad esempio, bsimon@contoso.onmicrosoft.com), un account aziendale locale (ad esempio, bsimon@contoso.com) o un account Microsoft (ad esempio, bsimon@outlook.com).

L'app può essere usata in uno dei due modi seguenti:

- **Notifica**. L'app consente di impedire l'accesso non autorizzato agli account e arrestare le transazioni illecite effettuando il push di una notifica allo smartphone o al tablet dell'utente. È sufficiente visualizzare la notifica e, se legittima, selezionare **Verifica**. In caso contrario, è possibile selezionare **Nega**. Per informazioni su come negare le notifiche, vedere l'argomento relativo a come usare le funzionalità Nega e segnala illecito per Multi-Factor Authentication.

- **Password con codice di verifica**. L'app può essere usata come token software per generare un codice di verifica OAuth. Quando richiesto, si immette il codice fornito dall'app nella schermata di accesso, con il nome utente e la password. Il codice di verifica offre una seconda forma di autenticazione.

Con il rilascio dell'app Microsoft Authenticator, viene sostituita l'app Azure Authenticator precedente. L'app Azure Authenticator continuerà a funzionare, ma questo articolo offre informazioni utili nel caso in cui si decida di passare alla nuova app Microsoft Authenticator.

## Installare l'app

L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## Aggiungere account all'app

Per ogni account che si vuole aggiungere all'app Microsoft Authenticator, seguire una delle procedure seguenti.

### Aggiungere un account all'app con lo scanner di codici a matrice

1. Accedere alla schermata delle impostazioni di verifica della sicurezza. Per informazioni su come accedere a questa schermata, vedere la sezione relativa alla [modifica delle impostazioni di sicurezza](multi-factor-authentication-end-user-manage-settings.md).

2. Selezionare **Configura**.

	![Pulsante Configura nella schermata delle impostazioni di verifica della sicurezza](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)  

	Verrà visualizzata una schermata contenente un codice a matrice.

	![Schermata contenente il codice a matrice](./media/multi-factor-authentication-azure-authenticator/barcode2.png)  

3. Aprire l'app Microsoft Authenticator. Nella schermata **account** selezionare **+** e quindi specificare che si vuole aggiungere un account aziendale o dell'istituto di istruzione.

	![Schermata account con segno più](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)  

	![Schermata per specificare un account aziendale o dell'istituto di istruzione](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)  

4. Usare la fotocamera per effettuare la scansione del codice a matrice e quindi selezionare **Operazione completata** per chiudere la relativa schermata.

	![Schermata per la scansione di un codice a matrice](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)  

	Se la fotocamera non funziona correttamente, è possibile immettere manualmente il codice a matrice e l'URL. Per altre informazioni, vedere [Aggiungere manualmente un account all'app](#add-an-account-to-the-app-manually).

5. Attendere che l'account venga attivato. Al termine dell'attivazione, selezionare **Contatti**. Verrà inviata una notifica o un codice di verifica al telefono dell'utente. Selezionare **Verifica**.

	![Schermata in cui si seleziona Verifica per l'accesso](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)  

6. Se richiesto dall'azienda, immettere il PIN per approvare la verifica di accesso.

	![Casella per l'immissione di un PIN](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)  

7. Al termine dell'immissione del PIN, selezionare **Chiudi**. A questo punto, la verifica avrà esito positivo.
8. È consigliabile immettere il proprio numero di telefono cellulare, in caso si perda l'accesso all'app. Specificare il paese nell'elenco a discesa e immettere il numero di telefono cellulare nella casella accanto al nome del paese. Selezionare **Avanti**.
9. A questo punto, il metodo di contatto è configurato. È ora possibile configurare le password per le app non basate su browser, ad esempio Outlook 2010 o versioni precedenti. Se non si usano app di questo tipo, selezionare **Operazione completata**. In caso contrario, continuare con il passaggio successivo.

	![Schermata per la creazione di una password dell'app](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)  

10. Se si usano app non basate su browser, copiare la relativa password fornita e incollarla nelle app. Per i passaggi nelle singole app, ad esempio Outlook e Lync, vedere la procedura per modificare la password della posta elettronica con la password dell'app e la procedura per modificare la password dell'applicazione con la password dell'app.
11. Selezionare **Operazione completata**.

Nella schermata **Account** verrà ora visualizzato il nuovo account.

![Schermata Account](./media/multi-factor-authentication-azure-authenticator/accounts.png)  

### Aggiungere manualmente un account all'app

1. Accedere alla schermata delle impostazioni di verifica della sicurezza. Per informazioni su come accedere a questa schermata, vedere la sezione relativa alla [modifica delle impostazioni di sicurezza](multi-factor-authentication-end-user-manage-settings.md).

2. Selezionare **Configura**.

	![Pulsante Configura nella schermata delle impostazioni di verifica della sicurezza](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)  

	Verrà visualizzata una schermata contenente un codice a matrice. Prendere nota del codice e dell'URL.

	![Schermata contenente il codice a matrice e l'URL](./media/multi-factor-authentication-azure-authenticator/barcode2.png)  

3. Aprire l'app Microsoft Authenticator. Nella schermata **account** selezionare **+** e quindi specificare che si vuole aggiungere un account aziendale o dell'istituto di istruzione.

	![Schermata account con segno più](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)  

	![Schermata per specificare un account aziendale o dell'istituto di istruzione](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)  

4. Nello scanner selezionare **In alternativa, immettere il codice manualmente**.

	![Schermata per la scansione di un codice a matrice](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)  

5. Immettere il codice e l'URL nelle caselle appropriate nell'app.

	![Schermata per l'immissione del codice e dell'URL](./media/multi-factor-authentication-azure-authenticator/manual.png)  

	![Schermata per l'immissione del codice e dell'URL](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

6. Attendere che l'account venga attivato. Al termine dell'attivazione, selezionare **Contatti**. Verrà inviata una notifica o un codice di verifica al telefono dell'utente. Selezionare **Verifica**.

Nella schermata **Account** verrà ora visualizzato il nuovo account.

![Schermata Account](./media/multi-factor-authentication-azure-authenticator/accounts.png)  

### Aggiungere un account all'app usando Touch ID

In iOS, l'app Microsoft Authenticator supporta Touch ID. Azure Multi-Factor Authentication consente alle organizzazioni di richiedere un PIN per i dispositivi. Con Touch ID, non è necessario che gli utenti iOS immettano un PIN. Possono invece effettuare la scansione della propria impronta digitale e selezionare **Approva**.

Configurare Touch ID con Microsoft Authenticator è semplice. Si completa una normale richiesta di verifica con un PIN. Se il dispositivo supporta Touch ID, verrà automaticamente configurato da Microsoft Authenticator per l'account.

![Verifica della configurazione di Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)  

Successivamente, ogni volta che verrà richiesto di verificare le informazioni di accesso sarà sufficiente selezionare la notifica push ricevuta ed effettuare la scansione della propria impronta digitale anziché immettere il PIN.

![Notifica push](./media/multi-factor-authentication-azure-authenticator/touchid2.png)  

## Disinstallare l'app di autenticazione di Azure precedente

Dopo aver aggiunto tutti gli account alla nuova app, è possibile disinstallare l'app precedente dal telefono.

## Eliminare un account

Per rimuovere un account dall'app Microsoft Authenticator, selezionare un account e quindi **Elimina**.

![Pulsante Elimina](./media/multi-factor-authentication-azure-authenticator/remove.png)  

<!---HONumber=AcomDC_0921_2016-->