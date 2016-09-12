<properties 
	pageTitle="App Microsoft Authenticator per telefoni cellulari" 
	description="Informazioni su come effettuare l'aggiornamento alla versione più recente di Azure Authenticator." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="femila" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/22/2016" 
	ms.author="billmath"/>  

# Microsoft Authenticator

L'app Microsoft Authenticator fornisce un livello di sicurezza aggiuntivo che può essere usato con l'account Azure (ad es. bsimon@contoso.onmicrosoft.com), l'account di lavoro locale (ad es. bsimon@contoso.com) o l'account Microsoft (ad es. bsimon@outlook.com).

## Scaricare l'app Microsoft Authenticator

L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## Funzionamento dell'app Microsoft Authenticator
L'app funziona inviando una notifica all'app Microsoft Authenticator sul tablet o sullo smartphone dell'utente. L'utente deve solo toccare "Verifica" all'interno dell'applicazione.

In alternativa l'app può essere usata anche con un codice di verifica. È sufficiente inserire il codice fornito dall'app nella schermata di accesso quando richiesto.

Queste due modalità sono le seguenti:

**Notifica** - In questa modalità l'app Microsoft Authenticator impedisce l'accesso agli account non autorizzati e arresta le transazioni illecite. L’avvertenza è una notifica push inviata sul telefono o sul dispositivo registrato. Visualizzare la notifica e, se del caso, selezionare Autentica. In caso contrario, è possibile scegliere Nega oppure Nega e segnala la notifica illecita. Per informazioni sulla segnalazione di notifiche illecite, vedere l'argomento relativo a come usare le funzionalità Nega e Segnala illecito per Multi-Factor Authentication.

**One-Time Password** - In questa modalità, l'app Microsoft Authenticator può essere usata come token software per generare un codice di verifica OATH. Questo codice di verifica quindi può pertanto essere immesso con il nome utente e con la password come seconda modalità di autenticazione.

## Aggiungere un account all'app Microsoft Authenticator mediante lo scanner di codici QR

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


## Aggiungere un account all'app Microsoft Authenticator manualmente
Per aggiungere un account manualmente, selezionare il pulsante Immetti manualmente.

![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)  


![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)  

Se si ha un account dove Azure MFA è già presente, immettere il codice e l'URL fornito nella stessa pagina dove è visualizzato il codice a barre. Le informazioni devono essere inserite nelle caselle del codice e dell'URL dell'app per dispositivi mobili. Viene avviata l'attivazione.

![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

Al termine, fare clic su Contatti. Verrà inviata una notifica o un codice di verifica al telefono cellulare. Fare clic su verifica.

## Passaggio alla nuova app Microsoft Authenticator

Con il rilascio dell'app Microsoft Authenticator, viene sostituita l'app Azure Authenticator precedente. L'app Azure Authenticator continuerà a funzionare, ma questo articolo offre informazioni utili per chi decide di passare alla nuova app Microsoft Authenticator.


### Informazioni su come passare alla nuova app Microsoft Authenticator 

**Passaggio 1:** Installare Microsoft Authenticator.


**Passaggio 2:** Attivare gli account con la nuova app.

Assicurarsi prima di tutto di avere a disposizione il codice QR o il codice e l'URL per l'immissione manuale per l'account da aggiungere all'app.

> [AZURE.NOTE] In caso di dubbi su come ottenere il codice QR, Contattare l'help desk per richiedere assistenza.
> 
> Se non si riesce ad attivare l'account con la nuova app, Contattare l'help desk.
>


Quando il codice QR è disponibile, avviare l'app. Fare clic su +.

Specificare quindi che si desidera aggiungere un account aziendale o dell'istituto di istruzione. Verrà avviata la fotocamera per la scansione del codice QR. Se non è possibile eseguire la scansione del codice QR, è sempre disponibile l'opzione per l'immissione manuale.

![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)  

Per confermare che l'account è stato attivato correttamente, assicurarsi che venga visualizzato nelle pagine relative agli account.


Eseguire questo passaggio per tutti gli account di cui si vuole eseguire la migrazione alla nuova app.



**Passaggio 3:** Disinstallare l'app Multi-Factor Authentication precedente dal telefono.

Dopo avere aggiunto tutti gli account alla nuova app, disinstallare l'app precedente dal telefono.



## Come aggiungere un account utilizzando lo scanner di codice a barre



- In primo luogo, accedere alla pagina delle impostazioni di verifica della sicurezza. Per informazioni su come accedere a questa pagina, vedere [Modifica delle impostazioni di sicurezza](multi-factor-authentication-end-user-manage-settings.md).

- Fare clic sul pulsante Configura.
 
![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)  

- Verrà visualizzata una schermata contenente un codice a barre.
  
![Scansione di un codice a barre](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- Aprire ora l'app Microsoft Authenticator per essere reindirizzati alla pagina degli account. contenente l'elenco di tutti gli account configurati. Se si desidera aggiungere un nuovo account fare clic sul segno +, quindi specificare che si desidera aggiungere un account aziendale o dell'istituto di istruzione. Verrà aperto lo scanner.

- Eseguire la scansione del codice a barre.

![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)  

- Attendere che l'account venga attivato.

- L'operazione è terminata. Nella pagina degli account verrà visualizzato anche il nuovo account.

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)  

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/accounts.png)  

## Come aggiungere manualmente un account Azure

Se si desidera aggiungere manualmente un account, seguire questa procedura.

- In primo luogo, accedere alla pagina delle impostazioni di verifica della sicurezza. Per informazioni su come accedere a questa pagina, vedere [Modifica delle impostazioni di sicurezza](multi-factor-authentication-end-user-manage-settings.md).

- Fare clic sul pulsante Configura.
 
![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)  

- Verrà visualizzata una schermata contenente un codice a barre. Annotare il codice e l'URL riportati sotto il codice a barre.
  
![Scansione di un codice a barre](./media/multi-factor-authentication-azure-authenticator/barcode2.png)  

- Aprire ora l'app Microsoft Authenticator per essere reindirizzati alla pagina degli account. contenente l'elenco di tutti gli account configurati. Se si desidera aggiungere un nuovo account fare clic sul segno +, quindi specificare che si desidera aggiungere un account aziendale o dell'istituto di istruzione. Verrà aperto lo scanner. Verrà aperto lo scanner.

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)  

- Fare clic sull'opzione per inserire il codice manualmente nella parte inferiore.

![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)  

- Immettere il codice e l'URL riportati nella stessa pagina in cui era contenuto il codice a barre. Le informazioni devono essere inserite nelle caselle del codice e dell'URL dell'app per dispositivi mobili. Viene avviata l'attivazione.

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/manual.png)  

![Configurazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

- Attendere che l'account venga attivato.

- L'operazione è terminata. Nella pagina degli account verrà visualizzato anche il nuovo account.

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)  

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/accounts.png)  


## Come aggiungere un account mediante Touch ID
In iOS, l'app per dispositivi mobili Microsoft Authenticator supporta ID Touch. Azure Multi-Factor Authentication consente alle organizzazioni di richiedere un PIN per i propri dispositivi registrati. Con questa nuova funzionalità, gli utenti di iOS con dispositivi abilitati per Touch ID non dovranno più immettere il PIN. Una volta configurata, gli utenti dovranno semplicemente eseguire la scansione della propria impronta digitale anziché immettere il PIN e toccare Approva.

La procedura di configurazione di ID Touch con Microsoft Authenticator è molto semplice. È sufficiente completare una normale richiesta di verifica con il PIN e, se il dispositivo supporta ID Touch, questo verrà configurato automaticamente.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)  

In questo modo, ogni volta che viene richiesto di verificare i propri dettagli di accesso, sarà sufficiente toccare la notifica push ricevuta ed eseguire la scansione della propria impronta digitale anziché immettere il PIN.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## Come eliminare un account

Per eliminare account singoli dall'app Microsoft Authenticator è sufficiente selezionare l'account. Verrà visualizzata l'opzione per l'eliminazione.

![Rimozione di un account](./media/multi-factor-authentication-azure-authenticator/remove.png)  

<!---HONumber=AcomDC_0831_2016-->