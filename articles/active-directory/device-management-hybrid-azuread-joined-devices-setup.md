---
title: "Come configurare dispositivi aggiunti all'identità ibrida di Azure Active Directory | Microsoft Docs"
description: "Informazioni su come configurare dispositivi aggiunti all'identità ibrida di Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 203e36b198186db63b7e902db296adeaa9ffb4ee
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="how-to-configure-hybrid-azure-active-directory-joined-devices"></a>Come configurare dispositivi aggiunti all'identità ibrida di Azure Active Directory

Tramite la gestione dei dispositivi in Azure Active Directory (Azure AD) è possibile garantire che gli utenti accedano alle risorse da dispositivi che soddisfano gli standard di sicurezza e conformità. Per altre informazioni, vedere [Introduction to device management in Azure Active Directory](device-management-introduction.md) (Introduzione alla gestione dei dispositivi in Azure Active Directory).

Se in un ambiente Active Directory locale Per aggiungere ad Azure AD i dispositivi aggiunti a un dominio in un ambiente Active Directory locale, è possibile configurare dispositivi aggiunti all'identità ibrida di Azure AD. Questo argomento offre i passaggi correlati. 


## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare a configurare dispositivi aggiunti all'identità ibrida di Azure AD nell'ambiente, è consigliabile acquisire familiarità con gli scenari supportati e i vincoli.  

Se si intende usare l'[Utilità preparazione sistema (Sysprep)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc721940(v=ws.10)), creare immagini da un'installazione di Windows che non è stata ancora registrata con Azure AD.

Tutti i dispositivi aggiunti a un dominio che eseguono la versione Aggiornamento dell'anniversario di Windows 10 e Windows Server 2016 vengono registrati automaticamente in Azure AD al riavvio del dispositivo o all'accesso dell'utente dopo il completamento dei passaggi di configurazione illustrati di seguito. Se non si desidera applicare questo comportamento di registrazione automatica o se si vuole eseguire un'implementazione controllata, seguire le istruzioni nella sezione Controllare la distribuzione e l'implementazione più avanti per abilitare o disabilitare in modo selettivo l'implementazione automatica prima di eseguire gli altri passaggi di configurazione.  

Per una migliore leggibilità delle descrizioni, in questo argomento viene usata la terminologia seguente. 

- **Dispositivo Windows corrente**: questo termine indica i dispositivi aggiunti a un dominio che eseguono Windows 10 o Windows Server 2016.
- **Dispositivi Windows di livello inferiore**: questo termine indica tutti i dispositivi Windows aggiunti a un dominio **supportati** che non eseguono Windows 10 né Windows Server 2016.  

### <a name="windows-current-devices"></a>Dispositivi Windows correnti

- Per i dispositivi che eseguono il sistema operativo desktop Windows, è supportata la versione 1607 (Aggiornamento dell'anniversario di Windows 10) o successiva. 
- La registrazione dei dispositivi Windows correnti **è** supportata in ambienti non federati come le configurazioni con sincronizzazione dell'hash delle password.  


### <a name="windows-down-level-devices"></a>Dispositivi Windows di livello inferiore

- Sono supportati i dispositivi Windows di livello inferiore seguenti:
    - Windows 8.1
    - Windows 7
    - Windows Server 2012 R2
    - Windows Server 2012
    - Windows Server 2008 R2
- La registrazione dei dispositivi legacy di Windows **è** supportata in ambienti non federati tramite l'accesso Seamless Single Sign On [Azure Active Directory Seamless Single Sign-On](https://aka.ms/hybrid/sso).
- Le registrazione di dispositivi legacy di Windows **non è** supportata per i dispositivi che utilizzano profili di roaming. In caso di roaming delle impostazioni o dei profili, usare Windows 10.



## <a name="prerequisites"></a>prerequisiti

Prima di iniziare ad abilitare i dispositivi aggiunti all'identità ibrida di Azure AD nell'organizzazione, è necessario assicurarsi di eseguire una versione aggiornata di Azure AD Connect.

Azure AD Connect:

- Mantiene l'associazione tra l'account computer nell'istanza locale di Active Directory (AD) e l'oggetto dispositivo in Azure AD. 
- Abilita altre funzionalità correlate al dispositivo come Windows Hello for Business.

Verificare che gli URL seguenti siano accessibili dall'interno della rete aziendale per la registrazione dei computer ad Azure AD:

- https://enterpriseregistration.windows.net

- https://login.microsoftonline.com

- https://device.login.microsoftonline.com

Se l'organizzazione richiede l'accesso a Internet tramite un proxy in uscita, è necessario implementare Web Proxy Auto-Discovery (WPAD) per consentire ai computer Windows 10 di registrarsi ad Azure AD.


## <a name="configuration-steps"></a>Procedura di configurazione

È possibile configurare dispositivi aggiunti all'identità ibrida di Azure AD per diversi tipi di piattaforme di dispositivi Windows. Questo argomento include i passaggi necessari per tutti gli scenari di configurazione tipici.  

Per una panoramica dei passaggi necessari per il proprio scenario, vedere la tabella seguente:  



| Passaggi                                      | Dispositivi Windows correnti e sincronizzazione dell'hash delle password | Dispositivi Windows correnti e federazione | Dispositivi Windows di livello inferiore |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Passaggio 1: Configurare il punto di connessione del servizio | ![Controllo][1]                            | ![Controllo][1]                    | ![Controllo][1]        |
| Passaggio 2: Configurare il rilascio delle attestazioni           |                                        | ![Controllo][1]                    | ![Controllo][1]        |
| Passaggio 3: Abilitare dispositivi non Windows 10      |                                        |                                | ![Controllo][1]        |
| Passaggio 4: Controllare la distribuzione e l'implementazione     | ![Controllo][1]                            | ![Controllo][1]                    | ![Controllo][1]        |
| Passaggio 5: Verificare i dispositivi aggiunti          | ![Controllo][1]                            | ![Controllo][1]                    | ![Controllo][1]        |



## <a name="step-1-configure-service-connection-point"></a>Passaggio 1: Configurare il punto di connessione del servizio

L'oggetto punto di connessione del servizio (SCP, Service Connection Point) viene usato dai dispositivi durante la registrazione per individuare le informazioni del tenant di Azure AD. Nell'istanza locale di Active Directory l'oggetto SCP per i dispositivi aggiunti all'identità ibrida di Azure AD deve essere incluso nella partizione del contesto dei nomi di configurazione della foresta del computer. Esiste un solo contesto dei nomi di configurazione per foresta. In una configurazione di Active Directory a più foreste, il punto di connessione del servizio deve essere presente in tutte le foreste contenenti computer aggiunti a un dominio.

Per recuperare il contesto dei nomi di configurazione della foresta è possibile usare il cmdlet [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx).  

Per una foresta con nome di dominio di Active Directory *fabrikam.com*, il contesto dei nomi di configurazione è il seguente:

`CN=Configuration,DC=fabrikam,DC=com`

Nella foresta, l'oggetto SCP per la registrazione automatica dei dispositivi aggiunti a un dominio si trova in:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

A seconda di come è stato distribuito Azure AD Connect, l'oggetto SCP potrebbe essere già stato configurato.
È possibile verificare l'esistenza dell'oggetto e recuperare i valori per l'individuazione con lo script di Windows PowerShell seguente: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

L'output di **$scp.Keywords** mostra le informazioni sul tenant di Azure AD, per esempio:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Se il punto di connessione del servizio non esiste, è possibile crearlo eseguendo il cmdlet `Initialize-ADSyncDomainJoinedComputerSync` nel server Azure AD Connect. Per eseguire questo cmdlet sono necessarie le credenziali di amministratore dell'organizzazione.  
Il cmdlet esegue queste operazioni:

- Crea il punto di connessione del servizio nella foresta di Active Directory a cui è connesso Azure AD Connect. 
- Richiede che si specifichi il parametro `AdConnectorAccount`, che è l'account configurato come account connettore di Active Directory in Azure AD Connect. 


Lo script seguente mostra un esempio dell'uso del cmdlet. In questo script, `$aadAdminCred = Get-Credential` richiede la digitazione di un nome utente. È necessario specificare il nome utente in formato UPN (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

Il `Initialize-ADSyncDomainJoinedComputerSync` cmdlet esegue queste operazioni:

- usa il modulo Active Directory PowerShell e gli strumenti Active Directory Domain Services, che si basano sull'esecuzione di Servizi Web Active Directory in un controller di dominio. Il servizio Servizi Web Active Directory è supportato nei controller di dominio che eseguono Windows Server 2008 R2 e versioni successive.
- È supportato solo per il **modulo MSOnline PowerShell versione 1.1.166.0**. Per scaricare questo modulo, utilizzare il [collegamento](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185).   
- Se gli strumenti Active Directory Domain Services non sono installati `Initialize-ADSyncDomainJoinedComputerSync` avrà esito negativo.  Gli strumenti Active Directory Domain Services possono essere installati tramite Server Manager in Funzionalità - Strumenti di amministrazione remota del server - Strumenti di amministrazione ruoli.

Per i controller di dominio che eseguono Windows Server 2008 o versioni precedenti, usare lo script seguente per creare il punto di connessione del servizio.

In una configurazione a più foreste, è consigliabile usare lo script seguente per creare il punto di connessione del servizio in ogni foresta in cui sono presenti computer:
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your AD configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC

    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()


## <a name="step-2-setup-issuance-of-claims"></a>Passaggio 2: Configurare il rilascio delle attestazioni

In una configurazione federata di Azure AD, per eseguire l'autenticazione ad Azure AD i dispositivi si basano su Active Directory Federation Services (AD FS) o su un servizio federativo locale di terze parti. I dispositivi eseguono l'autenticazione per ottenere un token di accesso per la registrazione nel servizio Registrazione dispositivo Azure Active Directory.

Per l'autenticazione, i dispositivi Windows correnti usano l'autenticazione integrata di Windows a un endpoint WS-Trust attivo (versione 1.3 o 2005) ospitato dal servizio federativo locale.

> [!NOTE]
> Quando si usa AD FS, è necessario abilitare **adfs/services/trust/13/windowstransport** o **adfs/services/trust/2005/windowstransport**. Se si sta usando il proxy per l'autenticazione Web, assicurarsi anche che l'endpoint venga pubblicato tramite il proxy. È possibile verificare gli endpoint abilitati nella console di gestione di AD FS, in **Servizio > Endpoint**.
>
>Se non si usa AD FS come servizio federativo locale, seguire le istruzioni del fornitore per verificare che siano supportati endpoint WS-Trust 1.3 o 2005 e che questi vengano pubblicati tramite file Metadata Exchange (MEX).

Per completare la registrazione dei dispositivi, nel token ricevuto dal servizio Registrazione dispositivo Azure Active Directory devono essere presenti le attestazioni seguenti. Il servizio Registrazione dispositivo Azure Active Directory creerà un oggetto dispositivo in Azure AD con alcune di queste informazioni, che verranno quindi usate da Azure AD Connect per associare l'oggetto dispositivo appena creato all'account computer in locale.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Se si hanno più nomi di dominio verificati, è necessario specificare l'attestazione seguente per i computer:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Se viene già rilasciata un'attestazione ImmutableID (ad esempio, un ID di accesso alternativo), è necessario specificare un'attestazione corrispondente per i computer:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

Nelle sezioni seguenti sono disponibili informazioni su:
 
- I valori necessari in ogni attestazione
- Come si presenta una definizione in AD FS

La definizione consente di verificare se i valori sono presenti o devono essere creati.

> [!NOTE]
> Se non si usa AD FS per il server federativo locale, seguire le istruzioni del fornitore per creare la configurazione appropriata per il rilascio di queste attestazioni.

### <a name="issue-account-type-claim"></a>Rilasciare l'attestazione del tipo di account

**`http://schemas.microsoft.com/ws/2012/01/accounttype`**: questa attestazione deve contenere un valore **DJ** che identifica il dispositivo come un computer aggiunto a un dominio. In AD FS, è possibile aggiungere una regola di trasformazione rilascio simile alla seguente:

    @RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Rilasciare l'attestazione objectGUID dell'account computer locale

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`**: questa attestazione deve contenere il valore di **objectGUID** dell'account computer locale. In AD FS, è possibile aggiungere una regola di trasformazione rilascio simile alla seguente:

    @RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Rilasciare l'attestazione objectSID dell'account computer locale

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`**: questa attestazione deve contenere il valore di **objectSID** dell'account computer locale. In AD FS, è possibile aggiungere una regola di trasformazione rilascio simile alla seguente:

    @RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>Rilasciare l'attestazione issuerID per il computer in presenza di più nomi di dominio verificati in Azure AD

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`**: questa attestazione deve contenere l'URI (Uniform Resource Identifier) di tutti i nomi di dominio verificati che si connettono al servizio federativo locale (AD FS o servizio di terze parti) che rilascia il token. In AD FS, è possibile aggiungere regole di trasformazione rilascio simili alle seguenti, nell'ordine specificato, dopo quelle riportate sopra. Si noti che è necessaria una regola per rilasciare in modo esplicito la regola per gli utenti. Nelle regole seguenti è stata aggiunta una prima regola che identifica l'autenticazione utente in alternativa all'autenticazione computer.

    @RuleName = "Issue account type with the value User when its not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://<verified-domain-name>/adfs/services/trust/"
    );


Nell'attestazione precedente,

- `<verified-domain-name>` è un segnaposto che è necessario sostituire con uno dei nomi di dominio verificati in Azure AD, ad esempio Value = "http://contoso.com/adfs/services/trust/"



Per altre informazioni sui nomi dominio verificati, vedere [Aggiungere un nome di dominio personalizzato ad Azure Active Directory](active-directory-domains-add-azure-portal.md).  
Per ottenere un elenco dei domini aziendali verificati, è possibile usare il cmdlet [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0). 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>Rilasciare l'attestazione ImmutableID per il computer quando ne esiste una per gli utenti (ad esempio, è impostato un ID di accesso alternativo)

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`**: questa attestazione deve contenere un valore valido per i computer. In AD FS, è possibile creare una regola di trasformazione rilascio come segue:

    @RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Script helper per creare le regole di trasformazione rilascio di AD FS

Lo script seguente semplifica la creazione delle regole di trasformazione rilascio descritte sopra.

    $multipleVerifiedDomainNames = $false
    $immutableIDAlreadyIssuedforUsers = $false
    $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains
    
    $rule1 = '@RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );'

    $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'

    $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);'

    $rule4 = ''
    if ($multipleVerifiedDomainNames -eq $true) {
    $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
    );'
    }

    $rule5 = ''
    if ($immutableIDAlreadyIssuedforUsers -eq $true) {
    $rule5 = '@RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'
    }

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

### <a name="remarks"></a>Osservazioni 

- Questo script aggiunge le regole a quelle esistenti. Non eseguire lo script due volte, perché il set di regole verrebbe aggiunto due volte. Prima di eseguire di nuovo lo script, verificare che non esistano regole corrispondenti per le attestazioni (in condizioni corrispondenti).

- Se si hanno più nomi di dominio verificati (in base a quanto visualizzato nel portale di Azure AD o tramite il cmdlet Get-MsolDomains), impostare il valore di **$multipleVerifiedDomainNames** nello script su **$true**. Assicurarsi anche di rimuovere qualsiasi attestazione issuerid esistente che potrebbe essere stata creata da Azure AD Connect o con altri mezzi. Di seguito è riportato un esempio di questa regola:


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Se è già stata rilasciata un'attestazione **ImmutableID** per gli account utente, impostare il valore di **$immutableIDAlreadyIssuedforUsers** nello script su **$true**.

## <a name="step-3-enable-windows-down-level-devices"></a>Passaggio 3: Abilitare dispositivi Windows di livello inferiore

Se alcuni dei dispositivi aggiunti a un dominio sono dispositivi Windows di livello inferiore, è necessario eseguire queste operazioni:

- Impostare un criterio in Azure AD per consentire agli utenti di registrare i dispositivi.
 
- Configurare il servizio federativo locale per il rilascio di attestazioni in modo da supportare l'**autenticazione integrata di Windows** per la registrazione dei dispositivi.
 
- Aggiungere l'endpoint di autenticazione dei dispositivi di Azure AD alle aree Intranet locali per evitare che vengano visualizzate richieste di certificati durante l'autenticazione del dispositivo.

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Impostare un criterio in Azure AD per consentire agli utenti di registrare i dispositivi

Per registrare i dispositivi Windows di livello inferiore, è necessario verificare che l'impostazione per consentire agli utenti di registrare i dispositivi in Azure AD sia configurata. Nel portale di Azure, questa impostazione è disponibile in:

`Azure Active Directory > Users and groups > Device settings`
    
Il criterio **Gli utenti possono registrare i propri dispositivi in Azure AD** deve essere impostato su **Tutti**.

![Registrare i dispositivi](./media/active-directory-conditional-access-automatic-device-registration-setup/23.png)


### <a name="configure-on-premises-federation-service"></a>Configurare il servizio federativo locale 

Il servizio federativo locale deve supportare il rilascio delle attestazioni **authenticationmehod** e **wiaormultiauthn** alla ricezione di una richiesta di autenticazione per la relying party di Azure AD contenente un parametro resouce_params con un valore codificato come il seguente:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

Quando arriva una richiesta di questo tipo, il servizio federativo locale deve autenticare l'utente con l'autenticazione integrata di Windows e al termine rilasciare le due attestazioni seguenti:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

In AD FS, è necessario avere una regola di trasformazione rilascio che trasmetta il metodo di autenticazione.  

**Per aggiungere questa regola:**

1. Nella console di gestione di AD FS passare a `AD FS > Trust Relationships > Relying Party Trusts`.
2. Fare clic con il pulsante destro del mouse sull'oggetto trust della relying party della Piattaforma delle identità di Microsoft Office 365 e quindi scegliere **Modifica regole attestazione**.
3. Nella scheda **Regole di trasformazione rilascio** selezionare **Aggiungi regola**.
4. Nell'elenco dei modelli delle **regole di attestazione** selezionare **Inviare attestazioni mediante una regola personalizzata**.
5. Selezionare **Avanti**.
6. Nella casella di testo **Nome regola attestazione** digitare **Auth Method Claim Rule (Regola attestazioni Metodo di autenticazione)**.
7. Nella casella **Claim rule** (Regola attestazioni) digitare la regola seguente:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. Nel server federativo digitare il comando di PowerShell seguente dopo aver sostituito **\<RPObjectName\>** con il nome dell'oggetto relying party per l'oggetto trust della relying party di Azure AD. Tale oggetto è solitamente denominato **Piattaforma delle identità di Microsoft Office 365**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Aggiungere l'endpoint di autenticazione dei dispositivi di Azure AD alle aree Intranet locali

Per evitare che vengano visualizzate richieste di certificati quando gli utenti dei dispositivi registrati eseguono l'autenticazione ad Azure AD, è possibile effettuare il push di un criterio ai dispositivi aggiunti a un dominio per aggiungere l'URL seguente all'area Intranet locale in Internet Explorer:

`https://device.login.microsoftonline.com`

## <a name="step-4-control-deployment-and-rollout"></a>Passaggio 4: Controllare la distribuzione e l'implementazione

Al termine dei passaggi necessari, i dispositivi aggiunti a un dominio sono pronti per essere automaticamente aggiunti ad Azure AD:

- Tutti i dispositivi aggiunti a un dominio che eseguono la versione Aggiornamento dell'anniversario di Windows 10 e Windows Server 2016 vengono registrati automaticamente in Azure AD al riavvio del dispositivo o all'accesso dell'utente. 

- I nuovi dispositivi vengono registrati in Azure AD al riavvio del dispositivo al termine dell'operazione di aggiunta a un dominio.

- I dispositivi registrati in precedenza in Azure AD, ad esempio per Intune, passano a "*Aggiunto a un dominio, Registrato con AAD*", ma il completamento del processo per tutti i dispositivi richiede tempo a causa del normale flusso di attività del dominio e dell'utente.

### <a name="remarks"></a>Osservazioni

- Per controllare l'implementazione della registrazione automatica dei computer Windows 10 e Windows Server 2016 aggiunti al dominio, è possibile usare un oggetto Criteri di gruppo.

- Con l'aggiornamento di novembre 2015 di Windows 10, l'aggiunta automatica ad Azure AD viene eseguita **solo** se è impostato l'oggetto Criteri di gruppo di implementazione.

- Per implementare i computer Windows di livello inferiore, è possibile distribuire un [pacchetto di Windows Installer](#windows-installer-packages-for-non-windows-10-computers) nei computer selezionati.

- Se si esegue il push dell'oggetto Criteri di gruppo a dispositivi Windows 8.1 aggiunti a un dominio, viene tentata un'aggiunta. Tuttavia, è consigliabile usare il [pacchetto di Windows Installer](#windows-installer-packages-for-non-windows-10-computers) per aggiungere tutti i dispositivi Windows di livello inferiore. 

### <a name="create-a-group-policy-object"></a>Creare un oggetto Criteri di gruppo 

Per controllare l'implementazione degli attuali computer Windows, è consigliabile distribuire l'oggetto Criteri di gruppo **Registra i computer aggiunti a un dominio come dispositivi** nei dispositivi da registrare. Ad esempio, è possibile distribuire i criteri a un'unità organizzativa o a un gruppo di sicurezza.

**Per impostare i criteri:**

1. Aprire **Server Manager** e quindi passare a `Tools > Group Policy Management`.
2. Passare al nodo corrispondente al dominio in cui si vuole attivare la registrazione automatica dei computer Windows correnti.
3. Fare clic con il pulsante destro del mouse su **Oggetti Criteri di gruppo** e quindi scegliere **Nuovo**.
4. Immettere un nome da assegnare all'oggetto Criteri di gruppo. Ad esempio, *Aggiunta a identità ibrida di Azure AD. 
5. Fare clic su **OK**.
6. Fare clic con il pulsante destro del mouse sul nuovo oggetto Criteri di gruppo e scegliere **Modifica**.
7. Passare a **Configurazione computer** > **Criteri** > **Modelli amministrativi** > **Componenti di Windows** > **Registrazione dispositivo**. 
8. Fare clic con il pulsante destro del mouse su **Registra i computer aggiunti a un dominio come dispositivi** e quindi scegliere **Modifica**.
   
   > [!NOTE]
   > Questo modello di Criteri di gruppo è stato rinominato dalle versioni precedenti della console Gestione Criteri di gruppo. Se si usa una versione precedente della console, passare a `Computer Configuration > Policies > Administrative Templates > Windows Components > Workplace Join > Automatically workplace join client computers`. 

7. Selezionare **Abilitato** e quindi fare clic su **Applica**. È necessario selezionare **Disabilitato** se si vuole che i criteri blocchino la registrazione automatica in Azure AD dei dispositivi controllati da questi criteri di gruppo.

8. Fare clic su **OK**.
9. Collegare l'oggetto Criteri di gruppo a una posizione scelta. Ad esempio, è possibile collegarlo a una determinata unità organizzativa, oppure a un determinato gruppo di sicurezza di computer che vengono automaticamente aggiunti ad Azure AD. Per impostare questi criteri per tutti i computer Windows 10 e Windows Server 2016 aggiunti a un dominio nell'organizzazione, collegare l'oggetto Criteri di gruppo al dominio.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>Pacchetti di Windows Installer per i computer non Windows 10

Per aggiungere computer di livello inferiore Windows aggiunti a un dominio in un ambiente federato, è possibile scaricare e installare il pacchetto di Windows Installer (MSI) dall'Area download in [Microsoft Workplace Join for non-Windows 10 computers](https://www.microsoft.com/en-us/download/details.aspx?id=53554) (Microsoft Workplace Join per computer non Windows 10).

È possibile distribuire il pacchetto usando un sistema di distribuzione software come System Center Configuration Manager. Il pacchetto supporta le opzioni standard di installazione invisibile all'utente con il parametro *quiet*. System Center Configuration Manager Current Branch offre vantaggi aggiuntivi rispetto alle versioni precedenti, come la possibilità di tenere traccia delle registrazioni completate. Per altre informazioni, vedere [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager).

Il programma di installazione crea nel sistema un'attività pianificata che viene eseguita nel contesto dell'utente e attivata nel momento in cui l'utente accede a Windows. L'attività aggiunge automaticamente il dispositivo ad Azure AD con le credenziali utente dopo aver usato l'autenticazione integrata di Windows. Per visualizzare l'attività pianificata, nel dispositivo passare a **Microsoft** > **Workplace Join** e quindi passare alla libreria Utilità di pianificazione.

## <a name="step-5-verify-joined-devices"></a>Passaggio 5: Verificare i dispositivi aggiunti

Per verificare i dispositivi aggiunti correttamente nell'organizzazione, è possibile usare il cmdlet [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) del [modulo di PowerShell per Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

L'output del cmdlet mostra i dispositivi registrati e aggiunti ad Azure AD. Per ottenere tutti i dispositivi, usare il parametro **-All** e quindi filtrare i dispositivi con la proprietà **deviceTrustType**. I dispositivi aggiunti a un dominio hanno il valore **Domain Joined**.

## <a name="next-steps"></a>Passaggi successivi

* [Introduction to device management in Azure Active Directory](device-management-introduction.md) (Introduzione alla gestione dei dispositivi in Azure Active Directory)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
