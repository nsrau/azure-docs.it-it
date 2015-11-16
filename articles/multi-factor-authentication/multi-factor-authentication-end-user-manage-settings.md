<properties 
	pageTitle="Problemi di Azure Multi-Factor Authentication | Microsoft Azure" 
	description="Questo documento fornisce informazioni sulle operazioni da eseguire se si verifica un problema con Azure multi-Factor Authentication." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/29/2015" 
	ms.author="billmath"/>

# Problemi di Azure Multi-Factor Authentication
Le informazioni seguenti sono utili per risolvere alcuni problemi comuni che si possono verificare.


- [Errori relativi all'ID di correlazione](#correlation-id-errors)
- [Il telefono è stato perso o rubato](#i-have-lost-my-phone-or-it-was-stolen?)
- [Si vuole cambiare il numero di telefono](#i-want-to-change-my-phone-number)
- [Non si riceve alcun codice sul telefono](#i-am-not-receiving-a-code-on-my-phone)
- [Le password dell'app non funzionano](#app-passwords-are-not-working)
- [Come si rimuove Azure Authenticator dal dispositivo precedente per passare a un nuovo dispositivo?](#how-do-i-clean-up-azure-authenticator-from-my-old-device-and-move-to-a-new-one)

##Errori relativi all'ID di correlazione
Se dopo aver eseguito i passaggi seguenti i problemi non vengono risolti, è possibile pubblicare una domanda nei [forum di Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD), [cercare nella Microsoft Knowledge Base (KB)](https://www.microsoft.com/it-IT/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport) o [contattare il personale del supporto tecnico](https://support.microsoft.com/it-IT), che analizzerà il problema nel più breve tempo possibile.

Quando si contatta il personale di supporto, è consigliabile includere le informazioni seguenti:

 - **Descrizione generale dell'errore**: l'esatto messaggio di errore visualizzato. Se non è stato visualizzato alcun messaggio di errore, descrivere dettagliatamente il comportamento imprevisto riscontrato.
 - **Pagina**: la pagina che si stava consultando quando è stato visualizzato l'errore (includere l'URL).
 - **Codice di errore **: l'esatto codice di errore visualizzato.
 - **ID sessione**: l'esatto ID di sessione visualizzato.
 - **ID correlazione**: il codice ID di correlazione generato quando è stato visualizzato l'errore.
 - **Data e ora**: la data e l'ora in cui è stato visualizzato l'errore (includere il fuso orario).
 
![ID correlazione](./media/multi-factor-authentication-end-user-manage/correlation.png)

 - **ID utente**: l'ID dell'utente che ha visualizzato l'errore (ad esempio user@contoso.com)?
 - **Informazioni sull'utente**: specificare se si tratta di un utente federato, con sincronizzazione di hash della password o solo cloud, nonché se l'utente dispone di una licenza Azure AD Premium, Enterprise Mobility o Azure AD Basic e se usa Office 365.

Queste informazioni consentiranno di risolvere il problema nel più breve tempo possibile.

## Il telefono è stato perso o rubato
Se il telefono è stato perso o rubato, è consigliabile richiedere all'amministratore di reimpostare le [password dell'app](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords) e cancellare eventuali [dispositivi memorizzati](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-suspended-devices-for-a-user).

Per accedere di nuovo all'account, sono disponibili due opzioni. Se è necessario configurare un numero di telefono di autenticazione alternativo, la prima opzione consiste nell'usare questo numero per accedere di nuovo all'account e modificare le impostazione di sicurezza.

Se è stato specificato un numero di telefono di autenticazione secondario, sarà possibile accedere mediante tale numero. ![Impostazione](./media/multi-factor-authentication-end-user-manage/altphone.png) Come si può notare nella schermata precedente, sono stati configurati due numeri di telefono, uno che termina con 67 e un altro che termina con 30.
  
Per accedere usando il numero di telefono alternativo, accedere seguendo la procedura normale, quindi scegliere semplicemente **Usa un'opzione di verifica diversa**. ![Verifica diversa](./media/multi-factor-authentication-end-user-manage/differentverification.png)

Selezionare quindi l'altro numero di telefono. In questo caso si seleziona **Chiama al numero +X XXXXXXXX30**

![Telefono alternativo](./media/multi-factor-authentication-end-user-manage/altphone2.png)

>[AZURE.IMPORTANT]È importante configurare un numero di telefono di autenticazione secondario. Poiché il numero di telefono primario e l'app per dispositivi mobili si trovano probabilmente sullo stesso telefono, il numero di telefono secondario è l'unico modo che consente di accedere di nuovo all'account in caso di perdita o furto del telefono.

Se non è stato configurato alcun numero di telefono di autenticazione secondario, sarà necessario contattare l'amministratore e richiedere la cancellazione delle impostazioni. All'accesso successivo verrà quindi richiesto di ripetere la [configurazione dell'autenticazione a più fattori](multi-factor-authentication-manage-users-and-devices.md#require-selected-users-to-provide-contact-methods-again).

## Si vuole cambiare il numero di telefono
In base al modo in cui si usa l'autenticazione a più fattori, è possibile modificare impostazioni come il numero di telefono in diverse posizioni. Usare la tabella seguente per semplificare la scelta ottimale per le proprie esigenze.

Come è possibile utilizzare l’autenticazione a più fattori|Descrizione
:------------- | :------------- | 
[Utilizzo con Office 365](#changing-your-settings-with-office-365)| Ciò significa che si desidera modificare le impostazioni tramite il portale di Office 365.
[Non so](#changing-your-settings-with-the-myapps-portal)|Sarà quindi necessario accedere a [http://myapps.microsoft.com](http://myapps.microsoft.com) e modificare qui le impostazioni.
[Utilizzarlo con Microsoft Azure](#changing-your-settings-with-microsoft-azure)| Ciò significa che si desidera modificare le impostazioni tramite il Portale di Azure.


 
### Modifica delle impostazioni con Office 365


Se si utilizza l'autenticazione a più fattori con Office 365 si potranno gestire le impostazioni di verifica aggiuntiva di sicurezza tramite il portale di Office 365.

#### Per modificare le impostazioni nel portale di Office 365

1. Accedere al [portale di Office 365](https://login.microsoftonline.com/).
2. Nell’angolo superiore destro selezionare il widget e scegliere le impostazioni di Office 365.
3. Fare clic su Verifica aggiuntiva di sicurezza.
4. A destra fare clic sul collegamento **Aggiorna i numeri di telefono usati per la sicurezza dell'account**. ![O365](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Verrà visualizzata una pagina che consente di modificare le impostazioni. ![O365](./media/multi-factor-authentication-end-user-manage/o365b.png)


### Modifica delle impostazioni con il portale Myapps

Se non si è certi di come utilizzare Multi-Factor Authentication, è sempre possibile modificare le impostazioni tramite il portale Myapps.

#### Per modificare le impostazioni nel portale Myapps

1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com)	
2. Nella parte superiore della schermata selezionare il profilo.
3. Selezionare Verifica aggiuntiva di sicurezza. ![Myapps](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Verrà visualizzata una pagina che consente di modificare le impostazioni.

![Verifica](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)

### Modifica delle impostazioni con Microsoft Azure

Se si usa Multi-Factor Authentication con Azure, è consigliabile modificare le impostazioni tramite il portale di Azure.

#### Per accedere alle impostazioni di verifica aggiuntiva di sicurezza nel Portale di Azure


1. Accedere al portale di Azure.
2. Nell'area in alto del portale di Azure fare clic sul proprio nome utente. Viene visualizzata una casella di riepilogo.
3. Dalla casella di riepilogo selezionare Verifica aggiuntiva di sicurezza. ![Azure](./media/multi-factor-authentication-end-user-manage/azure1.png)
4. Verrà visualizzata una pagina che consente di modificare le impostazioni. ![Verifica](./media/multi-factor-authentication-end-user-manage-azure/proofup.png)

##Non si riceve alcun codice sul telefono

È prima di tutto necessario effettuare le verifiche seguenti:

- Se si è scelto di ricevere codici di verifica tramite messaggio di testo nel telefono cellulare, assicurarsi che il piano di servizio e il dispositivo supportino il recapito di messaggi di testo. La velocità e la disponibilità del recapito possono variare in base alla posizione e al provider di servizi. Assicurarsi anche che il segnale cellulare sia adeguato quando si prova a ricevere questi codici.
- Se si è scelto di ricevere una verifica tramite l'app per dispositivi mobili, assicurarsi che il segnale cellulare sia buono. Si noti anche che la velocità e la disponibilità del recapito possono variare in base alla posizione e al provider di servizi. 

Se si usa uno smartphone, è consigliabile usare l'[app Azure Authenticator](multi-factor-authentication-azure-authenticator).

È possibile cambiare la modalità di ricezione dei codici di verifica tramite messaggi di testo e l'app per dispositivi mobili scegliendo **Usa un'opzione di verifica diversa** all'accesso.

![Verifica diversa](./media/multi-factor-authentication-end-user-manage/differentverification.png)


In alcuni casi il recapito di uno di questi servizi è più affidabile rispetto all'altro.

Se sono stati ricevuti più codici di verifica, occorre notare che funzioneranno solo i codici più recenti.

Se si è configurato un telefono di backup, è consigliabile riprovare selezionando tale telefono quando richiesto nella pagina di accesso. Se non sono stati configurati altri metodi, contattare l'amministratore e richiedere la cancellazione delle impostazioni. All'accesso successivo verrà quindi richiesto di ripetere la [configurazione dell'autenticazione a più fattori](multi-factor-authentication-manage-users-and-devices.md#require-selected-users-to-provide-contact-methods-again).

##Le password dell'app non funzionano
Assicurarsi prima di tutto di avere immesso correttamente la password dell'app. Se non funziona ancora, provare ad accedere e [creare una nuova password per l'app](multi-factor-authentication-end-user-app-passwords). Se il problema persiste, contattare l'amministratore e richiedere l'[eliminazione delle password esistenti per l'app](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords), quindi creare una nuova password e usarla.

##Come si rimuove Azure Authenticator dal dispositivo precedente per passare a un nuovo dispositivo?
Quando si disinstalla l'app dal dispositivo o si sovrascrive la memoria del dispositivo, l'attivazione sul back-end non verrà rimossa. È consigliabile eseguire i passaggi illustrati nella sezione relativa allo [spostamento in un nuovo dispositivo](multi-factor-authentication-azure-authenticator.md#how-to-move-to-the-new-azure-authenticator-app).

<!---HONumber=Nov15_HO2-->