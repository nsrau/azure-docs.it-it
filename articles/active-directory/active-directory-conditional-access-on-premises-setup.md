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

Per altre informazioni sugli scenari di accesso condizionale per risorse locali, vedere [Aggiunta all'area di lavoro da qualsiasi dispositivo per SSO e autenticazione a due fattori trasparente per tutte le applicazioni aziendali](https://technet.microsoft.com/library/dn280945.aspx). Queste funzionalità sono disponibili per i clienti che acquistano una licenza di Azure Active Directory Premium.

Dispositivi supportati
-------------------------------------------------------------------------
* Dispositivi Windows 7 aggiunti a un dominio.
* Dispositivi Windows 8.1 personali e aggiunti a un dominio.
* iOS 6 e versioni successive.
* Android 4.0 o versioni successive, telefoni Samsung GS3 o più recenti, tablet Samsung Note2 o più recenti.

Prerequisiti dello scenario
------------------------------------------------------------------------
* Windows Server Active Directory (Windows Server 2008 o versioni successive)
* Schema aggiornato in Windows Server 2012 R2
* Sottoscrizione di Azure Active Directory Premium
* Windows Server 2012 R2 Federation Services configurato per l'accesso SSO ad Azure AD
* Proxy applicazione Web di Windows Server 2012 R2.
* Strumento di sincronizzazione della directory (DirSync) con writeback degli oggetti dispositivo. [Scaricare la build più recente di dirsync.exe.](http://go.microsoft.com/fwlink/?LinkID=278924)

Problemi noti di questa versione
-------------------------------------------------------------------------------
* I criteri di accesso condizionale basati su dispositivo richiedono la funzionalità di writeback degli oggetti dispositivo in Active Directory da Azure Active Directory. L'esecuzione del writeback degli oggetti dispositivo in Active Directory può richiedere fino a 3 ore.
* I dispositivi iOS 7 richiedono sempre di selezionare un certificato durante l'autenticazione dei certificati client. 
* I dispositivi iOS 8 beta al momento non sono supportati.


## Distribuire il servizio Registrazione dispositivo di Azure Active Directory
Usare questa guida per distribuire e configurare il servizio Registrazione dispositivo di Azure Active Directory per l'organizzazione.

Nella guida si presuppone che Windows Server Active Directory sia stato configurato e che sia stata effettuata la sottoscrizione a Microsoft Azure Active Directory. Vedere i prerequisiti riportati sopra.

Per distribuire il servizio Registrazione dispositivo di Azure Active Directory con il tenant di Azure Active Directory, completare nell'ordine indicato le attività riportate nell'elenco di controllo seguente. Quando un collegamento a un riferimento porta a un argomento concettuale, leggere l'argomento e quindi tornare a questo elenco di controllo, per poter procedere con le attività restanti. Alcune attività includeranno un passaggio per la convalida dello scenario, per verificare di aver completato il passaggio correttamente.

**Elenco di controllo: Abilitare Registrazione dispositivo di Azure Active Directory**

Seguire questo elenco di controllo per abilitare e configurare il servizio Registrazione dispositivo di Azure Active Directory.

| Attività | Riferimento |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Abilitare Registrazione dispositivo nel tenant di Azure Active Directory per consentire l'aggiunta dei dispositivi all'area di lavoro. Per impostazione predefinita, l'autenticazione a più fattori non è abilitata per il servizio. L'autenticazione a più fattori è tuttavia consigliata quando si registra un dispositivo. Prima di abilitare l'autenticazione a più fattori in ADRS, assicurarsi che AD FS sia configurato per un provider di autenticazione a più fattori. | [Abilitare Registrazione dispositivo di Azure Active Directory](active-directory-conditional-access-device-registration-overview.md) |
| I dispositivi individueranno il servizio Registrazione dispositivo di Azure Active Directory cercando record DNS noti. È necessario configurare il DNS della società in modo che i dispositivi possano trovare il servizio Registrazione dispositivo di Azure Active Directory. | [Configurare l'individuazione di Registrazione dispositivo di Azure Active Directory](active-directory-conditional-access-device-registration-overview.md). |

**Elenco di controllo: Distribuire e configurare Windows Server 2012 R2 Active Directory Federation Services e impostare una relazione federativa con Azure Active Directory** Il set di attività seguente consente di integrare Windows Server 2012 R2 Federation Services con Azure Active Directory. In questo modo si potranno usare i dispositivi registrati nel servizio Registrazione dispositivo di Azure Active Directory per i criteri di accesso condizionale in AD FS.


| Attività | Riferimento |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| Distribuire un dominio di Servizi di dominio Active Directory con le estensioni dello schema di Windows Server 2012 R2. Non è necessario aggiornare alcun controller di dominio a Windows Server 2012 R2. L'unico requisito è l'aggiornamento dello schema. | Aggiornare lo schema di Servizi di dominio Active Directory. |
| Distribuire Windows Server 2012 R2 Federation Services con il Proxy applicazione Web. | Distribuire Windows Server 2012 R2 Federation Services con il Proxy applicazione Web. |
| La foresta di Servizi di dominio Active Directory deve essere configurata con gli oggetti e i contenitori necessari per supportare gli oggetti dispositivo. Si dovrà anche abilitare l'autenticazione dispositivo in AD FS. | Preparare la foresta Active Directory per supportare i dispositivi. |
| Configurare una relazione federativa con l'organizzazione e Azure Active Directory. Questo passaggio guiderà l'utente nella configurazione del tenant di Azure Active Directory con Windows Server 2012 R2 Federation Services. | Configurare una relazione federativa con Azure Active Directory e lo strumento di sincronizzazione della directory |
| Configurare Sincronizzazione directory (DirSync) per abilitare il writeback degli oggetti dispositivo. I dispositivi creati in Azure Active Directory verranno scritti in Active Directory. | Configurare DirSync per abilitare il writeback degli oggetti dispositivo |
| È consigliabile configurare un provider di autenticazione a più fattori con Windows Server 2012 R2 Federation Services. In questo modo gli utenti possono registrare in modo sicuro i propri dispositivi con l'autenticazione a più fattori. | Configurare i servizi federativi per fornire l'autenticazione a più fattori. |


### Aggiornare lo schema di Servizi di dominio Active Directory
> [AZURE.NOTE]L'aggiornamento dello schema di Active Directory non può essere annullato. È quindi consigliabile eseguirlo in un ambiente di test.

1. Accedere al controller di dominio con un account che abbia sia i diritti di amministratore dell'organizzazione sia di amministratore dello schema.
2. Copiare la directory [media]\\support\\adprep e le sottodirectory in uno dei controller di dominio Active Directory. 
3. Dove [media] è il percorso del supporto di installazione di Windows Server 2012 R2.
4. Al prompt dei comandi passare alla directory adprep ed eseguire: adprep.exe /forestprep. Seguire le istruzioni visualizzate per completare l'aggiornamento dello schema.

### Distribuire Windows Server 2012 R2 Federation Services con il Proxy applicazione Web
Seguire queste due guide per distribuire Windows Server 2012 R2 Federation Services con il Proxy applicazione Web, quindi tornare a questa guida.. Seguire le istruzioni per distribuire una server farm federativa di Windows Server 2012 R2.

> [AZURE.NOTE]Quando si distribuisce AD FS, è necessario ignorare il Passaggio facoltativo: Configurare un server federativo con, Device Registration Service (DRS). Questo passaggio non è necessario quando si usa Registrazione dispositivo di Azure Active Directory. Seguire le istruzioni per distribuire un proxy server federativo di Windows Server 2012 R2.

Installare l'aggiornamento più recente per Windows Server 2012 R2 in tutti i server federativi e server proxy di Windows Server 2012 R2. Questa operazione può essere eseguita prima o dopo aver configurato la farm federativa. L'aggiornamento è disponibile qui: Windows Server 2012 R2 Update.


### Preparare la foresta Active Directory per supportare i dispositivi

> [AZURE.NOTE]Questa è un'operazione da eseguire una sola volta per preparare la foresta Active Directory per supportare i dispositivi. Per completare questa procedura è necessario accedere con autorizzazioni di amministratore dell'organizzazione e la foresta Active Directory deve avere lo schema di Windows Server 2012 R2..

1. Nel server federativo aprire una finestra di comando di Windows PowerShell e digitare: *Initialize-ADDeviceRegistration*

2. Quando viene richiesto di specificare ServiceAccountName, immettere il nome dell'account del servizio selezionato come account del servizio per AD FS. Se si tratta di un account del servizio gestito del gruppo, immetterlo nel formato dominio\\nomeaccount$. Per un account di dominio, usare il formato dominio\\nomeaccount.


### Abilitare l'autenticazione dispositivo in AD FS

1. Nel server federativo aprire la console di gestione di AD FS e passare ad **AD FS** > **Criteri di autenticazione**.
2. Selezionare **Modifica autenticazione primaria globale* nel riquadro **Azioni**.
3. Selezionare **Abilita autenticazione dispositivo** e quindi scegliere **OK**.
4. Per impostazione predefinita, AD FS rimuove periodicamente da Active Directory i dispositivi inutilizzati. È necessario disabilitare questa attività quando si usa Registrazione dispositivo di Azure Active Directory, in modo che i dispositivi possano essere gestiti in Azure.


### Disabilitare la pulizia dei dispositivi inutilizzati
1. Nel server federativo aprire una finestra di comando di Windows PowerShell e digitare: `*Set-AdfsDeviceRegistration -MaximumInactiveDays 0*`


###Configurare una relazione federativa con Azure Active Directory e lo strumento di sincronizzazione della directory
La sezione seguente è utile per impostare una relazione di trust federativa tra Azure Active Directory e AD FS. Per alcuni passaggi potrebbe essere necessario accedere a un'altra pagina. Seguire le istruzioni fornite, quindi tornare a questa guida.

Integrare Azure Active Directory con Active Directory locale e configurare l'accesso Single Sign-On
------------------------------------------------------------------------------------------------------

1. Accedere al Portale di Azure come **Amministratore**.
2. Nel riquadro sinistro selezionare **Active Directory**.
3. Selezionare la propria directory nella scheda **Directory**.
4. Selezionare la scheda **Integrazione directory**.
5. Nella sezione relativa all'integrazione con Active Directory locale trovare l'opzione Sincronizzazione directory e selezionare Attivata.
6. Eseguire i passaggi da 1 a 4 per integrare Azure Active Directory con la directory locale.
  1. **Aggiungere i domini**.
  2. **Configurare Single Sign-On e preparare la sincronizzazione delle directory**. Se AD FS è già stato distribuito, è possibile seguire le istruzioni di queste sottosezioni per configurare e convalidare un trust tra AD FS e Azure Active Directory. 
      *  [Installare Windows PowerShell per l'accesso Single Sign-On con AD FS](https://msdn.microsoft.com/library/azure/jj151814.aspx).
      *  [Configurare una relazione di trust tra AD FS e Azure AD](https://msdn.microsoft.com/library/azure/jj205461.aspx).
      *  [Verificare e gestire l'accesso Single Sign-On con AD FS](https://msdn.microsoft.com/library/azure/jj151809.aspx).
  	  *  [Altre informazioni di riferimento su AD FS](https://msdn.microsoft.com/library/azure/dn151321.aspx)

  3. Installare ed eseguire lo strumento Sincronizzazione directory. Se DirSync è già stato installato, è necessario effettuare l'aggiornamento alla versione più recente. La minima versione richiesta è la 6862.0000.
  4. Verificare e gestire la sincronizzazione della directory. 
  5. Configurare alcune regole attestazioni aggiuntive per l'oggetto trust della relying party per Azure Active Directory. Tale oggetto in genere è denominato Piattaforma delle identità di Microsoft Office 365.

###Configurare le regole attestazioni del trust della relying party di Azure Active Directory

1. Aprire la console di gestione di AD FS e passare ad AD FS > Relazioni di attendibilità > **Trust relying party**. Fare clic con il pulsante destro del mouse sull'**oggetto trust della relying party della Piattaforma delle identità di Microsoft Office 365** e scegliere **Modifica regole attestazione.
2. Nella scheda **Regole di trasformazione rilascio** selezionare Aggiungi regola**.
3. Nella casella di riepilogo a discesa **Modello di regola attestazione** selezionare **Inviare attestazioni mediante una regola personalizzata**. Selezionare **Avanti**.
4. Digitare Regola attestazioni Metodo di autenticazione nella casella di testo **Nome regola attestazione**.
5. Digitare la regola attestazioni seguente nella casella di testo Regola attestazioni.

    ` c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]` `=> issue(claim = c);`

6. Fare clic su **OK** due volte per completare la finestra di dialogo.
7. Configurare alcuni riferimenti aggiuntivi alla classe di autenticazione per l'oggetto trust della relying party per Azure Active Directory. Tale oggetto in genere è denominato Piattaforma delle identità di Microsoft Office 365.


###Configurare il riferimento alla classe di autenticazione del trust della relying party di Azure Active Directory

Nel server federativo aprire una finestra di comando di Windows PowerShell e digitare:

*Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn*


Dove <RPObjectName> è il nome dell'oggetto relying party per l'oggetto trust della relying party di Azure Active Directory. Tale oggetto in genere è denominato Piattaforma delle identità di Microsoft Office 365.

### Configurare DirSync per abilitare il writeback degli oggetti dispositivo
È necessario configurare Sincronizzazione directory (DirSync) per abilitare il writeback degli oggetti dispositivo. I dispositivi creati in Azure Active Directory verranno scritti in Active Directory. L'operazione di writeback in Azure Active Directory dei dispositivi creati in Azure Active Directory può richiedere fino a 3 ore.
>[Azure.Note]Per eseguire questa procedura è necessario essere connessi con autorizzazioni di amministratore dell'organizzazione. Scaricare la build più recente di dirsync.exe qui.

> Per consentire il writeback del dispositivo in Active Directory è necessaria una licenza di Office 365 o di Azure AD Premium.

> Se l'installazione guidata di DirSync è stata completata, disconnettersi ed eseguire di nuovo l'accesso prima di procedere. Questa operazione garantisce l'aggiornamento del token di accesso.

>L'esecuzione del writeback degli oggetti dispositivo in Servizi di dominio Active Directory può richiedere fino a 3 ore.

1. Nel server che esegue la sincronizzazione della directory aprire una finestra di comando di Windows PowerShell e digitare i comandi seguenti:

    `Import-Module DirSync`
    
    `$AADCreds = Get-Credential`
    
    `$ADCreds = Get-Credential`

2. Quando viene richiesto di specificare le credenziali, digitare le credenziali dell'account amministratore del servizio cloud e dell'amministratore di Active Directory.

*Enable-MSOnlineObjectManagement –ObjectTypes Device –TargetCredential $AADCreds -Credential $ADCreds*

### Configurare i servizi federativi per fornire l'autenticazione a più fattori
I dispositivi registrati nell'organizzazione possono essere usati senza problemi come secondo fattore di autenticazione. È quindi consigliabile richiedere l'autenticazione avanzata quando si registra un dispositivo. È possibile attenersi alla guida seguente per configurare AD FS con Azure Multi-Factor Authentication e quindi tornare a questa guida. Seguire le istruzioni per la distribuzione di Microsoft Azure Multi-Factor Authentication.

## Aggiungere dispositivi all'area di lavoro con Registrazione dispositivo di Azure Active Directory

### Aggiungere un dispositivo iOS con Registrazione dispositivo di Azure Active Directory

Registrazione dispositivo di Azure Active Directory usa il processo di registrazione del profilo OTA (Over-the-Air) per i dispositivi iOS. Questo processo inizia quando l'utente si connette all'URL di registrazione del profilo con il Web browser Safari. Il formato dell'URL è il seguente:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Dove <yourdomainname> è il nome di dominio configurato con Azure Active Directory. Ad esempio, se il nome di dominio è contoso.com, l'URL è:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

È possibile comunicare questo URL agli utenti in diversi modi. Quello consigliato consiste nel pubblicare l'URL in un messaggio personalizzato di accesso all'applicazione negato in AD FS. Questa attività viene descritta nella sezione seguente: Creare un criterio di accesso alle applicazioni e un messaggio di accesso negato personalizzato.

###Aggiungere un dispositivo Windows 8.1 con Registrazione dispositivo di Azure Active Directory

1. Nel dispositivo Windows 8.1 passare a **Impostazioni PC** > **Rete** > **Area di lavoro**.
2. Immettere il proprio nome utente in formato UPN, ad esempio dan@contoso.com..
3. Selezionare **Aggiungi**.
4. Quando richiesto, accedere con le proprie credenziali. Il dispositivo ora è aggiunto.

### Aggiungere un dispositivo Android con Registrazione dispositivo di Azure Active Directory

L'argomento Azure Authenticator for Android include istruzioni su come installare l'app Azure Authenticator in un dispositivo Android e aggiungere un account aziendale. Quando viene creato un account aziendale in un dispositivo Android, tale dispositivo viene aggiunto all'area di lavoro nell'organizzazione.

### Verificare che venga eseguito il writeback dei dispositivi registrati in Active Directory
È possibile verificare che sia stato eseguito il writeback degli oggetti dispositivo in Active Directory con LDP.exe o ADSI Edit. Entrambi sono disponibili con gli strumenti dell'amministratore di Active Directory.

Per impostazione predefinita, gli oggetti dispositivo di cui viene eseguito il writeback da Azure Active Directory vengono inseriti nello stesso dominio della farm AD FS.

`CN=RegisteredDevices,defaultNamingContext`

###Creare un criterio di accesso alle applicazioni e un messaggio di accesso negato personalizzato
Considerare lo scenario seguente: si crea un trust della relying party per l'applicazione in AD FS e si configura una regola di autorizzazione rilascio che consente solo dispositivi registrati. Ora solo i dispositivi registrati possono accedere all'applicazione. Per semplificare l'accesso degli utenti all'applicazione, si configura un messaggio di accesso negato personalizzato che include istruzioni su come aggiungere il dispositivo dell'utente. A questo punto gli utenti hanno un modo per registrare i propri dispositivi per accedere a un'applicazione.

Nei passaggi seguenti viene mostrato come implementare questo scenario.
>[AZURE.NOTE]Questa sezione presuppone che sia già stato configurato un trust della relying party per l'applicazione in AD FS.

1. Aprire lo strumento AD FS di MMC e passare ad AD FS > Relazioni di trust > Trust relying party.
2. Trovare l'applicazione a cui si applicherà questa nuova regola di accesso. Fare clic con il pulsante destro del mouse sull'applicazione e scegliere Modifica regole attestazione.
3. Selezionare la scheda **Regole di autorizzazione rilascio** e quindi selezionare **Aggiungi regola**.
4. Nell'elenco a discesa **Modello di regola attestazione** selezionare **Consentire o negare l'accesso agli utenti in base a un'attestazione in ingresso**. Selezionare **Avanti**.
5. Nel campo Nome regola attestazione digitare **Consenti accesso dai dispositivi registrati**
6. Nell'elenco a discesa Tipo di attestazione in ingresso selezionare **È un utente registrato**.
7. Nel campo Valore attestazione in ingresso digitare: **true**
9. Selezionare **Fine** e quindi **Applica**.
10. Rimuovere le eventuali regole più permissive di quella appena creata. Ad esempio, rimuovere la regola predefinita 
8. Selezionare il pulsante di opzione **Consenti accesso agli utenti con questa attestazione in ingresso**. Consenti accesso a tutti gli utenti.

L'applicazione ora è configurata per consentire l'accesso solo quando l'utente opera da un dispositivo registrato e aggiunto all'area di lavoro. Per criteri di accesso più avanzati, vedere Gestire i rischi con il controllo di accesso a più fattori.

A questo punto è necessario configurare un messaggio di errore personalizzato per l'applicazione. Tale messaggio indicherà agli utenti che devono aggiungere il dispositivo all'area di lavoro prima di poter accedere all'applicazione.. È possibile creare un messaggio personalizzato di accesso all'applicazione negato usando codice HTML personalizzato e Windows PowerShell.

Nel server federativo aprire una finestra di comando di Windows PowerShell e digitare il comando seguente. Sostituire alcune parti del comando con gli elementi specifici del proprio sistema:

`Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage ` Prima di poter accedere all'applicazione, è necessario registrare il dispositivo.

**Se si usa un dispositivo iOS, selezionare questo collegamento per accedere al dispositivo**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Aggiungere questo dispositivo iOS alla propria area di lavoro.


**Se si usa un dispositivo Windows 8.1**, è possibile aggiungere il dispositivo selezionando **Impostazioni PC **> **Rete** > **Area di lavoro**.


Dove "**relying party trust name**" è il nome dell'oggetto trust della relying party dell'applicazione in AD FS. Dove yourdomain.com è il nome di dominio configurato con Azure Active Directory, ad esempio, contoso.com. Ricordarsi di rimuovere le eventuali interruzioni di riga presenti nel contenuto html passato al cmdlet **Set-AdfsRelyingPartyWebContent**.

<!---HONumber=August15_HO9-->