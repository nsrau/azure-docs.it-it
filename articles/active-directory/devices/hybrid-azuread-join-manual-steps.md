---
title: Configurare manualmente dispositivi aggiunti all'identità ibrida di Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare manualmente dispositivi aggiunti all'identità ibrida di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 33fc8a3822def68cc0baad4670233f57044d1985
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408408"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Esercitazione: Configurare manualmente dispositivi aggiunti all'identità ibrida di Azure Active Directory 

Tramite la gestione dei dispositivi in Azure Active Directory (Azure AD) è possibile garantire che gli utenti accedano alle risorse da dispositivi che soddisfano gli standard di sicurezza e conformità. Per altre informazioni, vedere [Introduzione alla gestione dei dispositivi in Azure Active Directory](overview.md).


> [!TIP]
> Se Azure AD Connect è un'opzione per l'utente, vedere [Selezionare lo scenario](hybrid-azuread-join-plan.md#select-your-scenario). Con Azure AD Connect, è possibile semplificare la configurazione di aggiunta ad Azure AD ibrido in modo significativo.



Se in un ambiente Active Directory locale Per aggiungere ad Azure AD i dispositivi aggiunti a un dominio in un ambiente Active Directory locale, è possibile configurare dispositivi aggiunti all'identità ibrida di Azure AD. Questa esercitazione illustra come configurare manualmente l'aggiunta di dispositivi ad Azure AD ibrido.

> [!div class="checklist"]
> * Prerequisiti
> * Procedura di configurazione
> * Configurare il punto di connessione del servizio
> * Configurare il rilascio delle attestazioni
> * Abilitare dispositivi Windows di livello inferiore
> * Verificare i dispositivi aggiunti
> * Risolvere i problemi di implementazione
 




## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone che l'utente abbia familiarità con:
    
-  [Introduction to device management in Azure Active Directory](../device-management-introduction.md) (Introduzione alla gestione dei dispositivi in Azure Active Directory)
    
-  [Come pianificare l'implementazione dell'aggiunta all'identità ibrida di Azure Active Directory](hybrid-azuread-join-plan.md)

-  [Come controllare l'aggiunta dei dispositivi all'identità ibrida di Azure AD](hybrid-azuread-join-control.md)


Prima di iniziare ad abilitare i dispositivi aggiunti all'identità ibrida di Azure AD nell'organizzazione, è necessario assicurarsi che:

- Si stia eseguendo una versione aggiornata di Azure AD Connect.

- Azure AD Connect abbia sincronizzato gli oggetti computer dei dispositivi che devono essere aggiunti ad Azure AD ibrido. Se gli oggetti computer appartengono a unità organizzative specifiche, queste unità organizzative devono essere configurate per la sincronizzazione anche in Azure AD Connect.

  

Azure AD Connect:

- Mantiene l'associazione tra l'account computer nell'istanza locale di Active Directory (AD) e l'oggetto dispositivo in Azure AD. 
- Abilita altre funzionalità correlate al dispositivo come Windows Hello for Business.

Verificare che gli URL seguenti siano accessibili dall'interno della rete aziendale per la registrazione dei computer ad Azure AD:

- https://enterpriseregistration.windows.net

- https://login.microsoftonline.com (Consenti)
- https://device.login.microsoftonline.com

- Servizio token di sicurezza dell'organizzazione (domini federati)

Se questa operazione non è già stata eseguita, il servizio token di sicurezza dell'organizzazione (per i domini federati) deve essere incluso nelle impostazioni Intranet locali dell'utente.

Se l'organizzazione prevede di usare l'SSO facile, gli URL seguenti devono essere raggiungibili dai computer all'interno dell'organizzazione e devono anche essere aggiunti all'area Intranet locale dell'utente:

- https://autologon.microsoftazuread-sso.com

- Inoltre, è necessario abilitare l'impostazione seguente nell'area Intranet dell'utente: "Consenti aggiornamenti alla barra di stato tramite script".

Se l'organizzazione usa l'installazione gestita (non federata) con Active Directory locale e non usa il file system distribuito di Azure per la federazione con Azure AD, l'aggiunta ad Azure AD ibrido in Windows 10 si basa su oggetti computer in Active Directory da sincronizzare con Azure AD. Assicurarsi che qualsiasi unità organizzativa (OU) che contiene gli oggetti computer che devono essere aggiunti ad Azure AD ibrido siano abilitati per la sincronizzazione nella configurazione di sincronizzazione di Azure AD Connect.

Per i dispositivi Windows 10 basati sulla versione 1703 o precedente, se l'organizzazione richiede l'accesso a Internet tramite un proxy in uscita, è necessario implementare Web Proxy Auto-Discovery (WPAD) per consentire ai computer Windows 10 di registrarsi ad Azure AD. 

A partire da Windows 10 1803, anche se il tentativo di aggiunta ad Azure AD ibrido di un dispositivo in un dominio federato con AD FS non riesce e Azure AD Connect è configurato per la sincronizzazione degli oggetti del computer o del dispositivo con Azure AD, il dispositivo tenta di completare l'aggiunta ad Azure AD ibrido usando il dispositivo o il computer sincronizzato.

## <a name="configuration-steps"></a>Procedura di configurazione

È possibile configurare dispositivi aggiunti all'identità ibrida di Azure AD per diversi tipi di piattaforme di dispositivi Windows. Questo argomento include i passaggi necessari per tutti gli scenari di configurazione tipici.  

Per una panoramica dei passaggi necessari per il proprio scenario, vedere la tabella seguente:  



| Passaggi                                      | Dispositivi Windows correnti e sincronizzazione dell'hash delle password | Dispositivi Windows correnti e federazione | Dispositivi Windows di livello inferiore |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Configurare il punto di connessione del servizio | ![Controllo][1]                            | ![Controllo][1]                    | ![Controllo][1]        |
| Configurare il rilascio delle attestazioni           |                                        | ![Controllo][1]                    | ![Controllo][1]        |
| Abilitare dispositivi non Windows 10      |                                        |                                | ![Controllo][1]        |
| Verificare i dispositivi aggiunti          | ![Controllo][1]                            | ![Controllo][1]                    | ![Controllo][1]        |



## <a name="configure-service-connection-point"></a>Configurare il punto di connessione del servizio

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
- È supportato solo per il **modulo MSOnline PowerShell versione 1.1.166.0**. Per scaricare questo modulo, utilizzare il [collegamento](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).   
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

Nello script precedente

- `$verifiedDomain = "contoso.com"` è un segnaposto che è necessario sostituire con uno dei nomi di dominio verificati in Azure AD, È necessario essere proprietari del dominio prima che sia possibile usarlo.

Per altre informazioni sui nomi dominio verificati, vedere [Aggiungere un nome di dominio personalizzato ad Azure Active Directory](../active-directory-domains-add-azure-portal.md).  
Per ottenere un elenco dei domini aziendali verificati, è possibile usare il cmdlet [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0). 

![Get-AzureADDomain](./media/hybrid-azuread-join-manual-steps/01.png)

## <a name="setup-issuance-of-claims"></a>Configurare il rilascio delle attestazioni

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

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`** - Questa attestazione deve contenere il valore **objectSid** dell'account del computer locale. In AD FS, è possibile aggiungere una regola di trasformazione rilascio simile alla seguente:

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

- `<verified-domain-name>` è un segnaposto che è necessario sostituire con uno dei nomi di dominio verificati in Azure AD, ad esempio il valore = "http://contoso.com/adfs/services/trust/"



Per altre informazioni sui nomi dominio verificati, vedere [Aggiungere un nome di dominio personalizzato ad Azure Active Directory](../active-directory-domains-add-azure-portal.md).  

Per ottenere un elenco dei domini aziendali verificati, è possibile usare il cmdlet [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0). 

![Get-MsolDomain](./media/hybrid-azuread-join-manual-steps/01.png)

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

## <a name="enable-windows-down-level-devices"></a>Abilitare dispositivi Windows di livello inferiore

Se alcuni dei dispositivi aggiunti a un dominio sono dispositivi di livello inferiore di Windows, è necessario:

- Impostare un criterio in Azure AD per consentire agli utenti di registrare i dispositivi.
 
- Configurare il servizio federativo locale per il rilascio di attestazioni in modo da supportare l'**autenticazione integrata di Windows** per la registrazione dei dispositivi.
 
- Aggiungere l'endpoint di autenticazione dei dispositivi di Azure AD alle aree Intranet locali per evitare che vengano visualizzate richieste di certificati durante l'autenticazione del dispositivo.

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Impostare un criterio in Azure AD per consentire agli utenti di registrare i dispositivi

Per registrare i dispositivi Windows di livello inferiore, è necessario verificare che l'impostazione per consentire agli utenti di registrare i dispositivi in Azure AD sia configurata. Nel portale di Azure, questa impostazione è disponibile in:

`Azure Active Directory > Users and groups > Device settings`
    
Il criterio **Gli utenti possono registrare i propri dispositivi in Azure AD** deve essere impostato su **Tutti**.

![Registrare i dispositivi](./media/hybrid-azuread-join-manual-steps/23.png)


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


## <a name="verify-joined-devices"></a>Verificare i dispositivi aggiunti

Per verificare i dispositivi aggiunti correttamente nell'organizzazione, è possibile usare il cmdlet [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) del [modulo di PowerShell per Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

L'output del cmdlet mostra i dispositivi registrati e aggiunti ad Azure AD. Per ottenere tutti i dispositivi, usare il parametro **-All** e quindi filtrare i dispositivi con la proprietà **deviceTrustType**. I dispositivi aggiunti a un dominio hanno il valore **Domain Joined**.



## <a name="troubleshoot-your-implementation"></a>Risolvere i problemi di implementazione

Se si verificano problemi durante il completamento dell'aggiunta ad Azure AD ibrido per dispositivi Windows aggiunti al dominio, vedere:

- [Risoluzione dei problemi relativi all'aggiunta ad Azure AD ibrido per dispositivi Windows correnti](troubleshoot-hybrid-join-windows-current.md)
- [Risoluzione dei problemi relativi all'aggiunta ad Azure AD ibrido per dispositivi Windows di livello inferiore](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Passaggi successivi

* [Introduction to device management in Azure Active Directory](overview.md) (Introduzione alla gestione dei dispositivi in Azure Active Directory)



<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual-steps/12.png
