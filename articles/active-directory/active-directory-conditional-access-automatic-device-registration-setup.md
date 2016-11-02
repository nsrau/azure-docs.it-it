<properties
    pageTitle="Come configurare la registrazione automatica dei dispositivi Windows con Azure Active Directory aggiunti a un dominio | Microsoft Azure"
    description="L'amministratore IT può scegliere di registrare con Azure Active Directory (Azure AD) i dispositivi Windows aggiunti a un dominio in modo automatico e invisibile all'utente."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="markvi"/>




# <a name="how-to-setup-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Come configurare la registrazione automatica dei dispositivi Windows con Azure Active Directory aggiunti a un dominio 

Per abilitare l' [accesso condizionale basato su dispositivo di Azure Active Directory](active-directory-conditional-access.md), è necessario registrare i computer Windows con Azure AD aggiunti a un dominio.

L'aggiornamento è finalizzato, ad esempio, a consentire l'utilizzo dell'account aziendale o dell'istituto di istruzione per migliorare l'esperienza SSO per app di Azure AD, il roaming delle impostazioni tra vari dispositivi a livello aziendale, l'utilizzo di Windows Store per le aziende, un'autenticazione più avanzata e un accesso più pratico con Windows Hello. 

> [AZURE.NOTE] Benché l'aggiornamento per Windows 10 di novembre 2015 supporti alcune delle esperienze utente avanzate, è l'aggiornamento dell'anniversario che offre il supporto completo per l'accesso condizionale basato su dispositivo.  
Per ulteriori informazioni sull'accesso condizionale, vedere l'articolo sull' [accesso condizionale basato su dispositivo di Azure Active Directory](active-directory-conditional-access.md).  
Per altre informazioni sui dispositivi Windows 10 nell'area di lavoro e l'esperienza restituita agli utenti registrati con Azure AD, vedere [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md). 


La registrazione è supportata nelle versioni di Windows precedenti, tra cui: 

- Windows 8.1 

- Windows 7 

Per il caso d'uso dei computer Windows Server utilizzati come desktop (come nel caso di uno sviluppatore che utilizzi un sistema Windows Server come computer primario) è possibile registrare le piattaforme seguenti: 

- Windows Server 2016 

- Windows Server 2012 R2 

- Windows Server 2012 

- Windows Server 2008 R2 

Di seguito sono indicate le operazioni da eseguire nel proprio ambiente per abilitare la registrazione dei dispositivi Windows aziendali con Azure AD aggiunti a un dominio. Le informazioni indicate saranno le seguenti: 

1. Prerequisiti 

2. Distribuzione e implementazione 

3. Risoluzione dei problemi 

4. Domande frequenti 

 

## <a name="prerequisites"></a>Prerequisiti 

Il requisito principale per abilitare la registrazione automatica dei dispositivi con Azure AD aggiunti a un dominio è una versione aggiornata di Azure AD Connect. 

A seconda di come è stato distribuito Azure AD Connect, ovvero con l'installazione rapida o personalizzata oppure con un aggiornamento sul posto, potrebbero essere stati configurati automaticamente i seguenti prerequisiti: 

1. Il punto di connessione del servizio Active Directory locale per l'individuazione delle informazioni del tenant Azure AD da parte dei computer che si registrano ad Azure AD. 

2. Le regole di trasformazione del rilascio di AD FS per l'autenticazione dei computer al momento della registrazione (per le configurazioni federate). 

Se nell'organizzazione si dispone di computer aggiunti a un dominio non Windows 10, è necessario quanto segue: 

1. Verificare che in Azure AD sia abilitato il criterio per consentire agli utenti di registrare i dispositivi. 

2. In AD FS verificare l'autenticazione integrata di Windows sia impostata come possibile alternativa all'autenticazione a più fattori. 

 

## <a name="service-connection-point-for-discovery-of-azure-ad-tenant"></a>Punto di connessione del servizio per l'individuazione del tenant di Azure AD 

Nella partizione del contesto dei nomi per la configurazione della struttura del dominio al quale vengono aggiunti i computer deve essere presente un punto di connessione del servizio contenente informazioni sul rilevamento del tenant di Azure AD in cui verranno registrati i computer. In una configurazione di Active Directory a più foreste deve essere presente un punto di connessione del servizio in tutte le foreste alle quali si sono aggiunti i computer. 

Il punto di connessione del servizio deve trovarsi nel percorso seguente di Active Directory: 

    CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Configuration Naming Context] 

> [AZURE.NOTE] Per una foresta con dominio di Active Directory denominato example.com, il contesto dei nomi di configurazione è CN=Configuration,DC=example,DC=com 

Per verificare l'esistenza dell'oggetto e i valori per l'individuazione di tenant di Azure AD, è possibile usare lo script di PowerShell seguente, sostituendo il contesto dei nomi di configurazione dell'esempio con quello in uso: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry; 

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com"; 

    $scp.Keywords; 

Nell'output di $scp.Keywords compaiono le seguenti informazioni sul tenant di Azure AD: 

    azureADName:microsoft.com 

    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47 

Se Il punto di connessione del servizio non esiste, è possibile crearne uno eseguendo il seguente script PowerShell nel server di Azure AD Connect: 

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1"; 

    $aadAdminCred = Get-Credential; 

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred; 


> [AZURE.NOTE]Quando si esegue il cmdlet $aadAdminCred = Get-Credential, usare il formato user@example.com per il nome utente delle credenziali immesse quando viene visualizzato il popup Get-Credential.  
> Quando si esegue il cmdlet Initialize-ADSyncDomainJoinedComputerSync, sostituire [connector account name] con l'account di dominio usato come account di Active Directory Connector.  
> Il cmdlet precedente usa il modulo di Active Directory PowerShell basato su Servizi Web Active Directory nei controller di dominio. I Servizi Web Active Directory sono supportati nei controller di dominio Windows Server 2008 R2 o versione successiva. Se si dispone solo di controller di dominio Windows Server 2008 o versione precedente, è possibile utilizzare System.DirectoryServices API tramite PowerShell per creare il punto di connessione del servizio e assegnare i valori di parole chiave appropriati. 

## <a name="ad-fs-rules-for-instant-computer-registration-(federated-orgs)"></a>Regole di AD FS per la registrazione immediata dei computer (organizzazioni federate) 

In una configurazione federata di Azure AD i computer si basano su AD FS o sul server federativo locale per eseguire l'autenticazione ad Azure AD e la registrazione con il servizio Registrazione dispositivo Azure. 

> [AZURE.NOTE] In una configurazione non federata (ovvero in cui viene eseguito l'hashing di una password utente sincronizzato con Azure AD) i computer aggiunti a un dominio di Windows 10 e Windows Server 2016 vengono autenticati nel servizio Registrazione dispositivo Azure tramite una credenziale che scrivono negli account del computer locale trasmesso ad Azure tramite Azure AD Connect. Per i computer non Windows 10/Windows Server 2016 in una configurazione non federata, vedere la sezione sul pacchetto di Windows Installer per la registrazione di computer aggiunti a un dominio non Windows 10/Windows Server 2016, al paragrafo Distribuzione e implementazione di questo documento, per conoscere le opzioni di certificazione in base ai dispositivi da abilitare nell'organizzazione. 

Per i computer Windows 10 e Windows Server 2016, Azure AD Connect associa l'oggetto dispositivo in Azure AD all'oggetto dell'account computer locale. A tale scopo, per completare anzitutto la registrazione e creare l'oggetto dispositivo, durante l'autenticazione al servizio Registrazione dispositivo Azure sono necessarie le informazioni seguenti: 

- `http://schemas.microsoft.com/ws/2012/01/accounttype` , ovvero il valore "DJ" che indica l'entità che esegue l'autenticazione come computer aggiunto a un dominio. 
- `http://schemas.microsoft.com/identity/claims/onpremobjectguid` , ovvero il valore dell'attributo objectGUID dell'account computer in locale. 
- `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` , ovvero il SID primario del computer, corrispondente al valore dell'attributo objectSid dell'account computer in locale. 
- `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` , ovvero il valore che consente ad Azure AD di considerare attendibile il token emesso da AD FS o dal servizio token di sicurezza locale. Ciò è importante in una configurazione di Active Directory a più foreste in cui i computer possono essere aggiunti a una foresta diversa da quella che si connette ad Azure AD dove si trova AD FS o il servizio token di sicurezza locale. Nel caso di AD FS il valore deve essere `http://<domain-name>/adfs/services/trust/`, dove "\<nome-dominio\>" è il nome di dominio convalidato in Azure AD. 

Per creare queste regole manualmente, in AD FS, è possibile usare il seguente script PowerShell nella sessione connessa al server. Si noti che la prima riga deve essere sostituita con il nome di dominio dell'organizzazione convalidato in Azure AD. 

> [AZURE.NOTE] Se AD FS non è disponibile come server federativo locale, attenersi alle istruzioni del fornitore per creare le regole appropriate per l'emissione dell'attestazione. 

    $validatedDomain = "example.com"      # Replace example.com with your validated domain name in Azure AD 

    $rule1 = '@RuleName = "Issue object GUID" 

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

    $rule2 = '@RuleName = "Issue account type for domain joined computers" 

      c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

    $rule3 = '@RuleName = "Pass through primary SID" 

      c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

      c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

      => issue(claim = c2);' 

    $rule4 = '@RuleName = "Issue AccountType with the value User when it’s not a computer account" 

      NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]) 

      => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");' 

    $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID" 

      c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] && 

      c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));' 

    $rule6 = '@RuleName = "Update issuer for DJ computer auth" 

      c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$validatedDomain+'/adfs/services/trust/");' 

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5 + $rule6 

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 
 
    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

> [AZURE.NOTE] I computer Windows 10 e Windows Server 2016 aggiunti al dominio eseguiranno l'autenticazione integrata di Windows a un endpoint WS-Trust attivo ospitato da AD FS. Assicurarsi che l'endpoint sia abilitato. Se si sta usando il proxy per l'autenticazione Web, assicurarsi anche che l'endpoint venga pubblicato tramite il proxy. L'endpoint è adfs/services/trust/13/windowstransport. A questo punto, dovrebbe risultare abilitato nella console di gestione di AD FS in Servizio > Endpoint. Se AD FS non è disponibile come server federativo locale, attenersi alle istruzioni del fornitore per controllare che l'endpoint corrispondente sia abilitato. 

 

## <a name="ensure-ad-fs-is-set-up-to-support-authentication-of-device-for-registration"></a>Assicurarsi che AD FS sia configurato per supportare l'autenticazione del dispositivo per la registrazione

In AD FS è necessario controllare che l'autenticazione integrata di Windows sia impostata come possibile alternativa all'autenticazione a più fattori per la registrazione dei dispositivi.

Per farlo, è necessario disporre di una regola di trasformazione del rilascio che trasmetta il metodo di autenticazione.

1. Aprire la console di gestione di AD FS e passare ad **AD FS > Relazioni di attendibilità>Trust relying party**. 

2. Fare clic con il pulsante destro del mouse sull'oggetto trust della relying party della Piattaforma delle identità di Microsoft Office 365 e quindi scegliere **Modifica regole attestazione**.

2.  Nella scheda **Regole di trasformazione rilascio** selezionare **Aggiungi regola**.

3.  Selezionare **Inviare attestazioni mediante una regola personalizzata** nell'elenco dei modelli delle **regole di attestazione**. 

4.  Selezionare **Avanti**.

4.  Nella casella di testo **Nome regola attestazione** digitare **Regola attestazioni Metodo di autenticazione**.

5.  Nella casella di testo **Modello di regola attestazione** digitare `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
=> issue(claim = c);`

6. Aprire Windows PowerShell nel server federativo.

7. Digitare il comando seguente:

    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

Dove **\<RPObjectName>\** è il nome dell'oggetto relying party per l'oggetto trust della relying party di Azure Active Directory. Tale oggetto in genere è denominato Piattaforma delle identità di Microsoft Office 365.

 

## <a name="deployment-and-roll-out"></a>Distribuzione e implementazione 

Una volta soddisfatti tutti i prerequisiti, i computer aggiunti a un dominio sono pronti per la registrazione con Azure AD. 

I computer con l'aggiornamento dell'anniversario di Windows 10 e Windows Server 2016 aggiunti a un dominio verranno automaticamente registrati ad Azure AD al successivo riavvio o accesso a Windows da parte dell'utente. I nuovi computer aggiunti al dominio verranno registrati con Azure AD al riavvio successivo all'operazione di aggiunta a un dominio. 

> [AZURE.NOTE] I computer aggiunti al dominio che eseguono Windows 10 verranno automaticamente registrati in Azure AD solo se risulta impostato l'oggetto Criteri di gruppo di implementazione. Per altre informazioni, vedi la sezione seguente. 

Per controllare l'implementazione della registrazione automatica dei computer Windows 10/Windows Server 2016 aggiunti al dominio, è disponibile un oggetto Criteri di gruppo utilizzabile a tale scopo. Per implementare la registrazione automatica dei computer non Windows 10 aggiunti a un dominio, esiste un pacchetto Windows Installer da distribuire nei computer desiderati. 

> [AZURE.NOTE] Tramite i Criteri di gruppo per il controllo dell'implementazione, viene inoltre attivata la registrazione dei computer Windows 8.1 aggiunti al dominio. È possibile scegliere di usare i criteri per la registrazione dei computer Windows 8.1 aggiunti a un dominio o, se si dispone di una combinazione delle versioni di Windows 7 o Windows Server, è possibile abilitare la registrazione di tutti i computer non Windows 10/Windows Server 2016 usando il pacchetto Windows Installer. 

### <a name="group-policy-object-to-control-roll-out-of-automatic-registration"></a>Oggetto Criteri di gruppo per controllare l'implementazione della registrazione automatica 

Per controllare l'implementazione della registrazione automatica dei computer aggiunti a un dominio con Azure AD, è possibile distribuire i computer aggiunti al dominio del registro Criteri di gruppo come dispositivi ai computer che si desidera registrare. Ad esempio, è possibile distribuire i criteri a un'unità organizzativa o in base a un gruppo di sicurezza. 

Per impostare tali criteri, seguire questa procedura: 

1. Aprire Server Manager e passare a **Strumenti > Gestione Criteri di gruppo**. 

2. Da Gestione Criteri di gruppo passare al nodo corrispondente al dominio in cui si vuole abilitare la registrazione automatica dei computer Windows 10 o Windows Server 2016. 

3. Fare clic con il pulsante destro del mouse su **Oggetti Criteri di gruppo** e quindi scegliere **Nuovo**. 

4. Digitare un nome da assegnare all'oggetto Criteri di gruppo, ad esempio *Registrazione automatica ad Azure AD*. Fare clic su OK. 

4. Fare clic con il pulsante destro del mouse sul nuovo oggetto Criteri di gruppo e scegliere **Modifica**. 

5. Passare a **Configurazione computer > Criteri > Modelli amministrativi > componenti di Windows > Registrazione dispositivi**, fare clic con il pulsante destro del mouse su **Registra i computer aggiunti a un dominio come dispositivi** e quindi scegliere **Modifica**. 

    > [AZURE.NOTE] Questo modello di Criteri di gruppo è stato rinominato dalle versioni precedenti della console Gestione Criteri di gruppo. Se si esegue una versione precedente della console, i criteri si troveranno in Configurazione computer > Criteri > Modelli amministrativi > Componenti di Windows > Aggiunta all'area di lavoro con il nome Computer client con aggiunta automatica all'area di lavoro. 

6. Selezionare il pulsante di opzione **Abilitato** e fare clic su **Applica**. 

7. Fare clic su **OK**. 

7. Collegare l'oggetto Criteri di gruppo a una posizione scelta. Ad esempio, una determinata unità organizzativa in cui si trovano i computer o un determinato gruppo di sicurezza che contiene i computer che verranno registrati automaticamente con Azure AD. Per abilitare questi criteri per tutti i computer Windows 10 e Windows Server 2016 aggiunti a un dominio nell'organizzazione, collegare l'oggetto Criteri di gruppo al dominio. 

## <a name="msi-package-for-non-windows-10-computers"></a>Pacchetto MSI per computer non Windows 10  

Per registrare i computer aggiunti a un dominio che eseguono Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 o Windows Server 2012 R2, è possibile scaricare il pacchetto Windows Installer (MSI):

- [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
- [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

Il pacchetto dovrà essere distribuito usando un sistema per la distribuzione di software come System Center Configuration Manager. Il pacchetto supporta le opzioni standard di installazione invisibile all'utente usando il parametro /quiet. L'uso di System Center Configuration Manager 2016 offre ulteriori vantaggi, come la possibilità di tenere traccia delle registrazioni completate. Per altre informazioni, vedere [System Center 2016](https://www.microsoft.com/cloud-platform/system-center-2016). 

Il programma di installazione crea nel sistema un'attività pianificata che viene eseguita nel contesto dell'utente e attivata al momento dell'accesso dell'utente a Windows. L'attività registra automaticamente il dispositivo con Azure AD con le credenziali dell'utente dopo l'autenticazione tramite Autenticazione integrata di Windows. L'attività pianificata è disponibile nella libreria dell'Utilità di pianificazione in **Microsoft > Aggiunta all'area di lavoro**. 



## <a name="additional-topics"></a>Argomenti aggiuntivi

- [Accesso condizionale di Azure Active Directory](active-directory-conditional-access.md)



<!--HONumber=Oct16_HO2-->


