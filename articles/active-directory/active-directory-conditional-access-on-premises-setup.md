
<properties
	pageTitle="Configurazione dell'accesso condizionale locale usando il servizio Registrazione dispositivo di Azure Active Directory | Microsoft Azure"
	description="Guida dettagliata per abilitare l'accesso condizionale alle applicazioni locali tramite Active Directory Federation Services (AD FS) in Windows Server 2012 R2."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2015"
	ms.author="femila"/>

# Configurazione dell'accesso condizionale locale usando il servizio Registrazione dispositivo di Azure Active Directory

I dispositivi di proprietà degli utenti possono essere contrassegnati come noti all'organizzazione richiedendo agli utenti di aggiungerli all'area di lavoro nel servizio Registrazione dispositivo di Azure Active Directory. Di seguito è riportata una guida dettagliata per abilitare l'accesso condizionale alle applicazioni locali tramite Active Directory Federation Services (AD FS) in Windows Server 2012 R2.

> [AZURE.NOTE]Quando si usano dispositivi registrati nei criteri di accesso condizionale del servizio Registrazione dispositivo di Azure Active Directory, è necessaria la licenza di Office 365 o la licenza di Azure AD Premium. Questo include i criteri applicati da Active Directory Federation Services (AD FS) alle risorse locali.

Per altre informazioni sugli scenari di accesso condizionale per risorse locali, vedere [Aggiunta all'area di lavoro da qualsiasi dispositivo per SSO e autenticazione a due fattori trasparente per tutte le applicazioni aziendali](https://technet.microsoft.com/library/dn280945.aspx).

Queste funzionalità sono disponibili per i clienti che acquistano una licenza di Azure Active Directory Premium.

Dispositivi supportati
-------------------------------------------------------------------------
* Dispositivi Windows 7 aggiunti a un dominio.
* Dispositivi Windows 8.1 personali e aggiunti a un dominio.
* iOS 6 e versioni successive, per browser Safari
* Android 4.0 o versioni successive, telefoni Samsung GS3 o più recenti, tablet Samsung Note2 o più recenti.


Prerequisiti dello scenario
------------------------------------------------------------------------
* Sottoscrizione di Office 365 o Azure Active Directory Premium
* Tenant di Azure Active Directory
* Windows Server Active Directory (Windows Server 2008 o versioni successive)
* Schema aggiornato in Windows Server 2012 R2
* Sottoscrizione di Azure Active Directory Premium
* Windows Server 2012 R2 Federation Services configurato per l'accesso SSO ad Azure AD
* Microsoft Azure Active Directory Connect (Azure AD Connect) su server proxy applicazione Web di Windows Server 2012 R2. [Scaricare Azure AD Connect da questa pagina](http://www.microsoft.com/it-IT/download/details.aspx?id=47594).
* Dominio verificato. 

Problemi noti di questa versione
-------------------------------------------------------------------------------
* I criteri di accesso condizionale basati su dispositivo richiedono la funzionalità di writeback degli oggetti dispositivo in Active Directory da Azure Active Directory. L'esecuzione del writeback degli oggetti dispositivo in Active Directory può richiedere fino a 3 ore.
* I dispositivi iOS 7 richiedono sempre di selezionare un certificato durante l'autenticazione dei certificati client. 
* Alcune versioni di iOS 8, precedenti iOS 8.3, non funzionano. 

## Presupposti dello scenario
Questo scenario presuppone l'esistenza di un ambiente ibrido, costituito da un tenant di Azure AD e un'istanza di Active Directory locale. Questi tenant devono essere connessi mediante Azure AD Connect, con un dominio verificato e AD FS per SSO. L'elenco di controllo riportato di seguito è utile per configurare l'ambiente per la fase descritta in precedenza.

Elenco di controllo: prerequisiti per lo scenario di accesso condizionale
--------------------------------------------------------------
Connettere il tenant di Azure AD all'istanza di Active Directory locale.

## Configurare il servizio Registrazione dispositivo di Azure Active Directory
Usare questa guida per distribuire e configurare il servizio Registrazione dispositivo di Azure Active Directory per l'organizzazione.

Nella guida si presuppone che Windows Server Active Directory sia stato configurato e che sia stata effettuata la sottoscrizione a Microsoft Azure Active Directory. Vedere i prerequisiti riportati sopra.

Per distribuire il servizio Registrazione dispositivo di Azure Active Directory con il tenant di Azure Active Directory, completare nell'ordine indicato le attività riportate nell'elenco di controllo seguente. Quando un collegamento a un riferimento porta a un argomento concettuale, leggere l'argomento e quindi tornare a questo elenco di controllo, per poter procedere con le attività restanti. Alcune attività includeranno un passaggio per la convalida dello scenario, per verificare di aver completato il passaggio correttamente.

## Parte 1: Abilitare Registrazione dispositivo di Azure Active Directory

Seguire questo elenco di controllo per abilitare e configurare il servizio Registrazione dispositivo di Azure Active Directory.

| Attività | Riferimento |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Abilitare Registrazione dispositivo nel tenant di Azure Active Directory per consentire l'aggiunta dei dispositivi all'area di lavoro. Per impostazione predefinita, l'autenticazione a più fattori non è abilitata per il servizio. L'autenticazione a più fattori è tuttavia consigliata quando si registra un dispositivo. Prima di abilitare l'autenticazione a più fattori in ADRS, assicurarsi che AD FS sia configurato per un provider di autenticazione a più fattori. | [Abilitare Registrazione dispositivo di Azure Active Directory](active-directory-conditional-access-device-registration-overview.md) |
| I dispositivi individueranno il servizio Registrazione dispositivo di Azure Active Directory cercando record DNS noti. È necessario configurare il DNS della società in modo che i dispositivi possano trovare il servizio Registrazione dispositivo di Azure Active Directory. | [Configurare l'individuazione di Registrazione dispositivo di Azure Active Directory](active-directory-conditional-access-device-registration-overview.md). |

##Parte 2: Distribuire e configurare Active Directory Federation Services con Windows Server 2012 R2 e configurare una relazione federativa con Azure Active Directory


| Attività | Riferimento |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Distribuire un dominio di Servizi di dominio Active Directory con le estensioni dello schema di Windows Server 2012 R2. Non è necessario aggiornare alcun controller di dominio a Windows Server 2012 R2. L'unico requisito è l'aggiornamento dello schema. | [Aggiornamento dello schema di Servizi di dominio Active Directory] (#Upgrade your Active Directory Domain Services Schema) |
| I dispositivi individueranno il servizio Registrazione dispositivo di Azure Active Directory cercando record DNS noti. È necessario configurare il DNS della società in modo che i dispositivi possano trovare il servizio Registrazione dispositivo di Azure Active Directory. | [Preparare i dispositivi di supporto di Active Directory](#Prepare your Active Directory to support devices) |


##Parte 3: Abilitare il writeback dei dispositivi in Azure AD

| Attività | Riferimento |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Completare la parte 2 di Abilitazione del writeback dei dispositivi in Azure AD Connect. Al termine, tornare a questa guida. | [Abilitazione del writeback dei dispositivi in Azure AD Connect](#Upgrade your Active Directory Domain Services Schema) |
	 

##[Facoltativo] Parte 4: Abilitazione di Multi-Factor Authentication

È consigliabile configurare una delle diverse opzioni per Multi-Factor Authentication. Se si desidera richiedere MFA, vedere [Scegliere la soluzione di sicurezza Multi-Factor più adatta](multi-factor-authentication-get-started.md). È inclusa una descrizione di ogni soluzione con collegamenti che consentono di configurare la soluzione selezionata.

## Parte 5: Verifica

La distribuzione è completata. È ora possibile provare alcuni scenari. Seguire i collegamenti riportati di seguito per provare il servizio e acquisire familiarità con le funzionalità.


| Attività | Riferimento |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| Aggiungere alcuni dispositivi all'area di lavoro con Registrazione dispositivo di Azure Active Directory. È possibile aggiungere dispositivi iOS, Windows e Android. | [Aggiungere dispositivi all'area di lavoro con Registrazione dispositivo di Azure Active Directory](#Join devices to your workplace using Azure Active Directory Device Registration) |
| È possibile visualizzare e abilitare/disabilitare i dispositivi registrati tramite il portale dell'amministratore. In questa attività si visualizzeranno alcuni dispositivi registrati tramite il portale dell'amministratore. | [Panoramica di Registrazione dispositivo di Azure Active Directory](active-directory-conditional-access-device-registration-overview.md) |
| Verificare che venga eseguito il writeback degli oggetti dispositivo da Azure Active Directory a Windows Server Active Directory. | [Verificare che venga eseguito il writeback dei dispositivi registrati in Active Directory](#Verify registered devices are written-back to Active Director) |
| Ora che gli utenti possono registrare i propri dispositivi, è possibile creare criteri di accesso alle applicazioni in AD FS che consentano l'accesso solo a dispositivi registrati. In questa attività si creerà una regola di accesso alle applicazioni e un messaggio di accesso negato personalizzato. | [Creare un criterio di accesso alle applicazioni e un messaggio di accesso negato personalizzato](#Create an application access policy and custom access denied message) |



## Integrare Azure Active Directory con Active Directory locale
Con questa procedura è possibile integrare il tenant di Azure AD con l'istanza di Active Directory locale usando Azure AD Connect. Anche se i passaggi sono disponibili nel portale di Azure, prendere nota delle istruzioni speciali elencate in questa sezione.

1.	Accedere al portale di Azure come Amministratore.
2.	Nel riquadro sinistro selezionare **Active Directory**.
3.	Selezionare la propria directory nella scheda **Directory**.
4.	Selezionare la scheda **Integrazione directory**.
5.	Nella sezione **distribuisci e gestisci** eseguire i passaggi da 1 a 3 per integrare Azure Active Directory con la directory locale.
  1.	Aggiungere i domini.
  2.	Installare ed eseguire Azure AD Connect. Installare Azure AD Connect seguendo le istruzioni seguenti, [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md).
  3. Verificare e gestire la sincronizzazione della directory. Le istruzioni per l'accesso Single Sign-On sono disponibili in questo passaggio. >[AZURE.NOTE]Configurare la federazione con AD FS come descritto nel documento accessibile dal collegamento precedente. >[AZURE.NOTE]Non è necessario configurare le funzionalità di anteprima.
  
   


## Aggiornare lo schema di Servizi di dominio Active Directory
> [AZURE.NOTE]L'aggiornamento dello schema di Active Directory non può essere annullato. È quindi consigliabile eseguirlo prima in un ambiente di test.

1. Accedere al controller di dominio con un account che abbia sia i diritti di amministratore dell'organizzazione sia di amministratore dello schema.
2. Copiare la directory **[media]\\support\\adprep** e le sottodirectory in uno dei controller di dominio Active Directory. 
3. Dove [media] è il percorso del supporto di installazione di Windows Server 2012 R2.
4. Al prompt dei comandi passare alla directory adprep ed eseguire: **adprep.exe /forestprep**. Seguire le istruzioni visualizzate per completare l'aggiornamento dello schema.

## Preparare Active Directory per supportare i dispositivi
>[AZURE.NOTE]Questa è un'operazione da eseguire una sola volta per preparare la foresta Active Directory per supportare i dispositivi. Per completare questa procedura è necessario accedere con autorizzazioni di amministratore dell'organizzazione e la foresta Active Directory deve avere lo schema di Windows Server 2012 R2..


##Preparare la foresta Active Directory per supportare i dispositivi

> [AZURE.NOTE]Questa è un'operazione da eseguire una sola volta per preparare la foresta Active Directory per supportare i dispositivi. Per completare questa procedura è necessario accedere con autorizzazioni di amministratore dell'organizzazione e la foresta Active Directory deve avere lo schema di Windows Server 2012 R2..

### Preparare la foresta Active Directory

1.	Nel server federativo aprire una finestra di comando di Windows PowerShell e digitare: Initialize-ADDeviceRegistration
2.	Quando viene richiesto di specificare **ServiceAccountName**, immettere il nome dell'account del servizio selezionato come account del servizio per AD FS. Se si tratta di un account del servizio gestito del gruppo, immetterlo nel formato **dominio\\nomeaccount$**. Per un account di dominio, usare il formato **dominio\\nomeaccount**.



### Abilitare l'autenticazione dispositivo in AD FS

1. Nel server federativo aprire la console di gestione di AD FS e passare ad **AD FS** > **Criteri di autenticazione**.
2. Selezionare **Modifica autenticazione primaria globale** nel riquadro **Azioni**.
3. Selezionare **Abilita autenticazione dispositivo** e quindi scegliere **OK**.
4. Per impostazione predefinita, AD FS rimuove periodicamente da Active Directory i dispositivi inutilizzati. È necessario disabilitare questa attività quando si usa Registrazione dispositivo di Azure Active Directory, in modo che i dispositivi possano essere gestiti in Azure.


### Disabilitare la pulizia dei dispositivi inutilizzati
1. Nel server federativo aprire una finestra di comando di Windows PowerShell e digitare: Set-AdfsDeviceRegistration -MaximumInactiveDays 0

### Preparare Azure AD Connect per il writeback dei dispositivi

1.	Completare Parte 1: Preparare AAD Connect. 


## Aggiungere dispositivi all'area di lavoro con Registrazione dispositivo di Azure Active Directory

### Aggiungere un dispositivo iOS con Registrazione dispositivo di Azure Active Directory

Registrazione dispositivo di Azure Active Directory usa il processo di registrazione del profilo OTA (Over-the-Air) per i dispositivi iOS. Questo processo inizia quando l'utente si connette all'URL di registrazione del profilo con il Web browser Safari. Il formato dell'URL è il seguente:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Dove `yourdomainname` è il nome di dominio configurato con Azure Active Directory. Ad esempio, se il nome di dominio è contoso.com, l'URL è:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

È possibile comunicare questo URL agli utenti in diversi modi. Quello consigliato consiste nel pubblicare l'URL in un messaggio personalizzato di accesso all'applicazione negato in AD FS. Questa attività viene descritta nella sezione seguente: [Creare un criterio di accesso alle applicazioni e un messaggio di accesso negato personalizzato](#Create an application access policy and custom access denied message)

###Aggiungere un dispositivo Windows 8.1 con Registrazione dispositivo di Azure Active Directory

1. Nel dispositivo Windows 8.1 passare a **Impostazioni PC** > **Rete** > **Area di lavoro**.
2. Immettere il proprio nome utente in formato UPN, ad esempio dan@contoso.com..
3. Selezionare **Aggiungi**.
4. Quando richiesto, accedere con le proprie credenziali. Il dispositivo ora è aggiunto.

### Aggiungere un dispositivo Android con Registrazione dispositivo di Azure Active Directory

L'[argomento Azure Authenticator per Android](active-directory-conditional-access-azure-authenticator-app.md) include istruzioni su come installare l'app Azure Authenticator in un dispositivo Android e aggiungere un account aziendale. Quando viene creato un account aziendale in un dispositivo Android, tale dispositivo viene aggiunto all'area di lavoro nell'organizzazione.

## Verificare che venga eseguito il writeback dei dispositivi registrati in Active Directory
È possibile verificare che sia stato eseguito il writeback degli oggetti dispositivo in Active Directory con LDP.exe o ADSI Edit. Entrambi sono disponibili con gli strumenti dell'amministratore di Active Directory.

Per impostazione predefinita, gli oggetti dispositivo di cui viene eseguito il writeback da Azure Active Directory vengono inseriti nello stesso dominio della farm AD FS.

    CN=RegisteredDevices,defaultNamingContext

## Creare un criterio di accesso alle applicazioni e un messaggio di accesso negato personalizzato
Considerare lo scenario seguente: si crea un trust della relying party per l'applicazione in AD FS e si configura una regola di autorizzazione rilascio che consente solo dispositivi registrati. Ora solo i dispositivi registrati possono accedere all'applicazione. Per semplificare l'accesso degli utenti all'applicazione, si configura un messaggio di accesso negato personalizzato che include istruzioni su come aggiungere il dispositivo dell'utente. A questo punto gli utenti hanno un modo per registrare i propri dispositivi per accedere a un'applicazione.

Nei passaggi seguenti viene mostrato come implementare questo scenario.
>[AZURE.NOTE]Questa sezione presuppone che sia già stato configurato un trust della relying party per l'applicazione in AD FS.

1. Aprire lo strumento AD FS di MMC e passare ad AD FS > Relazioni di trust > Trust relying party.
2. Trovare l'applicazione a cui si applicherà questa nuova regola di accesso. Fare clic con il pulsante destro del mouse sull'applicazione e scegliere Modifica regole attestazione.
3. Selezionare la scheda **Regole di autorizzazione rilascio** e quindi selezionare **Aggiungi regola**.
4. Nell'elenco a discesa **Modello di regola attestazione** selezionare **Consentire o negare l'accesso agli utenti in base a un'attestazione in ingresso**. Selezionare **Avanti**.
5. Nel campo Nome regola attestazione digitare **Consenti accesso dai dispositivi registrati**.
6. Nell'elenco a discesa Tipo di attestazione in ingresso selezionare **È un utente registrato**.
7. Nel campo Valore attestazione in ingresso digitare: **true**
8. Selezionare il pulsante di opzione **Consenti accesso agli utenti con questa attestazione in ingresso**.
9. Selezionare **Fine** e quindi **Applica**.
10. Rimuovere le eventuali regole più permissive di quella appena creata. Ad esempio, rimuovere la regola predefinita **Consenti accesso a tutti gli utenti**.

L'applicazione ora è configurata per consentire l'accesso solo quando l'utente opera da un dispositivo registrato e aggiunto all'area di lavoro. Per criteri di accesso più avanzati, vedere [Gestire i rischi con il controllo di accesso a più fattori](https://technet.microsoft.com/it-IT/library/dn280949.aspx).

A questo punto è necessario configurare un messaggio di errore personalizzato per l'applicazione. Tale messaggio indicherà agli utenti che devono aggiungere il dispositivo all'area di lavoro prima di poter accedere all'applicazione.. È possibile creare un messaggio personalizzato di accesso all'applicazione negato usando codice HTML personalizzato e Windows PowerShell.

Nel server federativo aprire una finestra di comando di Windows PowerShell e digitare il comando seguente. Sostituire alcune parti del comando con gli elementi specifici del proprio sistema:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Prima di poter accedere all'applicazione, è necessario registrare il dispositivo.

**Se si usa un dispositivo iOS, selezionare questo collegamento per accedere al dispositivo**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Aggiungere questo dispositivo iOS alla propria area di lavoro.


**Se si usa un dispositivo Windows 8.1**, è possibile aggiungere il dispositivo selezionando **Impostazioni PC** > **Rete** > **Area di lavoro**.


Dove "**relying party trust name**" è il nome dell'oggetto trust della relying party dell'applicazione in AD FS. Dove **yourdomain.com** è il nome di dominio configurato con Azure Active Directory. Ad esempio, contoso.com. Ricordarsi di rimuovere le eventuali interruzioni di riga presenti nel contenuto html passato al cmdlet **Set-AdfsRelyingPartyWebContent**.


A questo punto, quando gli utenti accedono all'applicazione da un dispositivo non registrato con il servizio Registrazione dispositivo di Azure Active Directory, riceveranno una pagina simile alla schermata seguente.

![Schermata di un errore visualizzato quando gli utenti non hanno registrato il dispositivo con Azure AD](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)

<!---HONumber=Sept15_HO3-->