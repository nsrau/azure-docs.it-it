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

## Aggiungere un account all'app Microsoft Authenticator

## Passaggio alla nuova app Microsoft Authenticator

Con il rilascio di Microsoft Authenticator, viene sostituita l'app Azure Authenticator precedente. L'app Azure Authenticator continuerà a funzionare, ma questo articolo offre informazioni utili per chi decide di passare alla nuova app Microsoft Authenticator.


## Informazioni su come passare alla nuova app Microsoft Authenticator 

**Passaggio 1:** Installare Microsoft Authenticator.

![Cloud](./media/multi-factor-authentication-azure-authenticator/home.png)

**Passaggio 2:** Attivare gli account con la nuova app.

Assicurarsi prima di tutto di avere a disposizione il codice QR o il codice e l'URL per l'immissione manuale per l'account da aggiungere all'app.

> [AZURE.NOTE] In caso di dubbi su come ottenere il codice QR, Contattare l'help desk per richiedere assistenza.
> 
> Se non si riesce ad attivare l'account con la nuova app, Contattare l'help desk.
>


Quando il codice QR è disponibile, avviare l'app. Fare clic su +.


![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/addaccount.png)

Verrà avviata la fotocamera per la scansione del codice QR. Se non è possibile eseguire la scansione del codice QR, è sempre disponibile l'opzione per l'immissione manuale.

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

- Aprire ora l'app Microsoft Authenticator per essere reindirizzati alla pagina degli account. contenente l'elenco di tutti gli account configurati. Per aggiungere un nuovo account, fare clic sul segno +. Verrà aperto lo scanner.

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Eseguire la scansione del codice a barre.

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/scan.png)

- Attendere che l'account venga attivato.

- L'operazione è terminata. Nella pagina degli account verrà visualizzato anche il nuovo account.

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)


## Come aggiungere manualmente un account Azure

Se si desidera aggiungere manualmente un account, seguire questa procedura.

- In primo luogo, accedere alla pagina delle impostazioni di verifica della sicurezza. Per informazioni su come accedere a questa pagina, vedere [Modifica delle impostazioni di sicurezza](multi-factor-authentication-end-user-manage-settings.md).

- Fare clic sul pulsante Configura.
 
![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- Verrà visualizzata una schermata contenente un codice a barre. Annotare il codice e l'URL riportati sotto il codice a barre.
  
![Scansione di un codice a barre](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- Aprire ora l'app Microsoft Authenticator per essere reindirizzati alla pagina degli account. contenente l'elenco di tutti gli account configurati. Per aggiungere un nuovo account, fare clic sul segno +. Verrà aperto lo scanner.

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Fare clic sul pulsante Immetti manualmente nella parte inferiore della pagina.

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/scan.png)

- Immettere il codice e l'URL riportati nella stessa pagina in cui era contenuto il codice a barre. Le informazioni devono essere inserite nelle caselle del codice e dell'URL dell'app per dispositivi mobili. Viene avviata l'attivazione.

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/manual.png)

- Attendere che l'account venga attivato.

- L'operazione è terminata. Nella pagina degli account verrà visualizzato anche il nuovo account.

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

## Come aggiungere manualmente un account non Azure

Se si desidera aggiungere manualmente un account non Azure, ad esempio il proprio account Microsoft, seguire questa procedura:


- L’aggiunta manuale di un account non di Azure può essere eseguita analizzando il codice QR o immettendo la chiave privata.
- Se si immette manualmente la chiave privata, è possibile ottenere la chiave privata dal sito associato all'account. In Outlook.com, ad esempio, accedere alle impostazioni dell'account, passare alle impostazioni di sicurezza e selezionare l'opzione per la configurazione di un'app di autenticazione. Per ottenere la chiave privata, è necessario selezionare l'opzione in cui si dichiara di non riuscire a eseguire la scansione del codice a barre.
- 

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/secretkey.png)

- Aprire l'app Microsoft Authenticator per essere reindirizzati alla pagina degli account. contenente l'elenco di tutti gli account configurati. Per aggiungere un nuovo account, fare clic sul segno +. Verrà aperto lo scanner.

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Analizzare il codice QR oppure fare clic sul pulsante Immetti manualmente nella parte inferiore della pagina. Se si esegue l'analisi del codice QR ignorare il passaggio successivo, poiché l'attivazione inizierà immediatamente.

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/scan.png)

- Se si inserisce manualmente la chiave privata, inserire il nome dell’account e la chiave privata riportati nella stessa pagina in cui era contenuto il codice a barre. Le informazioni devono essere inserite nelle caselle del codice e dell'URL dell'app per dispositivi mobili. Viene avviata l'attivazione.

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/manual.png)

- Attendere che l'account venga attivato. Dovrebbe essere visualizzato anche il nuovo account.

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/msaccount.png)

- Completare il processo immettendo il codice riportato sotto il nome dell'account (in questo caso 875 756) nell'apposito campo della pagina da cui è stata ricevuta la chiave privata e fare clic su Avanti.

![Aggiunta di un account](./media/multi-factor-authentication-azure-authenticator/verify.png)

## Come aggiungere un account mediante Touch ID
In iOS, l'app per dispositivi mobili Microsoft Authenticator supporta ID Touch. Azure Multi-Factor Authentication consente alle organizzazioni di richiedere un PIN per i propri dispositivi registrati. Con questa nuova funzionalità, gli utenti di iOS con dispositivi abilitati per Touch ID non dovranno più immettere il PIN. Una volta configurata, gli utenti dovranno semplicemente eseguire la scansione della propria impronta digitale anziché immettere il PIN e toccare Approva.

La procedura di configurazione di ID Touch con Microsoft Authenticator è molto semplice. È sufficiente completare una normale richiesta di verifica con il PIN e, se il dispositivo supporta ID Touch, questo verrà configurato automaticamente.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

In questo modo, ogni volta che viene richiesto di verificare i propri dettagli di accesso, sarà sufficiente toccare la notifica push ricevuta ed eseguire la scansione della propria impronta digitale anziché immettere il PIN.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## Come eliminare un account

Per eliminare account singoli dall'app Microsoft Authenticator è sufficiente selezionare l'account. Verrà visualizzata l'opzione per l'eliminazione.

![Rimozione di un account](./media/multi-factor-authentication-azure-authenticator/remove.png)

<!---HONumber=AcomDC_0824_2016-->