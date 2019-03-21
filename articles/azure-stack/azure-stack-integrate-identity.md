---
title: Integrazione di Data Center Azure Stack - identità
description: Descrive come integrare Azure Stack AD FS con il tuo Data Center AD FS
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 03/04/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 03/04/2019
ms.openlocfilehash: 5f34991dca4dbb4275033c764981c44492b9920e
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58257809"
---
# <a name="azure-stack-datacenter-integration---identity"></a>Integrazione di Data Center Azure Stack - identità
È possibile distribuire Azure Stack tramite Azure Active Directory (Azure AD) o Active Directory Federation Services (ADFS) come provider di identità. È necessario effettuare la scelta prima di distribuire Azure Stack. In uno scenario connesso, è possibile scegliere di Azure AD o AD FS. Per uno scenario disconnesso, è supportato solo da AD FS.

> [!IMPORTANT]
> È possibile passare al provider di identità senza ridistribuire l'intera soluzione di Azure Stack.

## <a name="active-directory-federation-services-and-graph"></a>Grafico e active Directory Federation Services

La distribuzione con AD FS consente le identità in una foresta di Active Directory esistente per l'autenticazione con le risorse in Azure Stack. Questa foresta di Active Directory esistente richiede una distribuzione di AD FS per consentire la creazione di un trust federativo AD FS.

L'autenticazione è una parte dell'identità. Per gestire basato su accesso controllo ruoli (RBAC) in Azure Stack, il componente grafico deve essere configurato. Quando l'accesso a una risorsa viene delegata, il componente grafico Cerca l'account utente nella foresta Active Directory esistente tramite il protocollo LDAP.

![Architettura di Azure Stack AD FS](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

L'istanza di ADFS esistente è l'account servizio token di sicurezza (STS) che invia le attestazioni per AD FS Azure Stack (la risorsa servizio token di sicurezza). In Azure Stack, automazione crea la relazione di trust di provider di attestazioni con l'endpoint dei metadati per l'istanza di ADFS esistente.

In AD FS esistente, è necessario configurare un trust della relying party. Questo passaggio non viene eseguito per l'automazione e deve essere configurato dall'operatore. L'endpoint di Azure Stack VIP per AD FS, è possibile crearle usando il modello `https://adfs.<Region>.<ExternalFQDN>/`.

La configurazione del trust della relying party richiede anche di configurare le regole di trasformazione di attestazioni che sono fornite da Microsoft.

Per la configurazione per i grafi, deve essere un account di servizio purché disponga dell'autorizzazione in Active Directory esistente lettura. Questo account è necessario come input per l'automazione abilitare scenari di RBAC.

Per l'ultimo passaggio, un nuovo proprietario è configurato per la sottoscrizione del provider predefinito. Questo account ha accesso completo a tutte le risorse quando l'accesso al portale di amministrazione di Azure Stack.

Requirements:

|Componente|Requisito|
|---------|---------|
|Grafico|Microsoft Active Directory 2012 o 2012 R2 o 2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Configurazione di integrazione di Graph

Graph supporta solo l'integrazione con una singola foresta di Active Directory. Se sono presenti più foreste, solo la foresta specificata nella configurazione da utilizzare per recuperare gli utenti e gruppi.

Le informazioni seguenti sono necessari come input per i parametri di automazione:

|Parametro|DESCRIZIONE|Esempio|
|---------|---------|---------|
|CustomADGlobalCatalog|Nome di dominio completo della destinazione di foresta di Active Directory<br>che si vuole integrare con|Contoso.com|
|CustomADAdminCredentials|Un utente con autorizzazioni di lettura LDAP|YOURDOMAIN\graphservice|

### <a name="configure-active-directory-sites"></a>Configurare siti di Active Directory

Per le distribuzioni di Active Directory con più siti, configurare il sito Active Directory più vicino alla distribuzione di Azure Stack. La configurazione consente di evitare che il servizio Graph di Azure Stack di risolvere le query che utilizzano un Server di catalogo globale da un sito remoto.

Aggiungere Azure Stack [rete VIP pubblici](azure-stack-network.md#public-vip-network) subnet per il sito di Active Directory più vicino per Azure Stack. Ad esempio, se il servizio Active Directory dispone di due siti Seattle e Redmond con Azure Stack distribuito nel sito di Seattle, aggiungere la subnet di rete di indirizzo VIP pubblico di Azure Stack per il sito di Active Directory per Seattle.

Per altre informazioni sui siti di Active Directory, vedere [la progettazione della topologia dei siti](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology).

> [!Note]  
> Se il servizio Active Directory è costituito da un singolo sito è possibile ignorare questo passaggio. Nel caso in cui presente una subnet di catch-all configurata convalidare che la subnet di rete di indirizzo VIP pubblico di Azure Stack non fa parte di esso.

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Creare account utente in Active Directory esistente (facoltativo)

Facoltativamente, è possibile creare un account per il servizio Graph in Active Directory esistente. Eseguire questo passaggio se si ha già un account che si desidera utilizzare.

1. In Active Directory esistente, creare l'account utente seguenti (consigliato):
   - **Nome utente**: graphservice
   - **Password**: usare una password complessa<br>Configurare la password senza scadenza.

   È richiesto alcun autorizzazioni speciali o l'appartenenza.

#### <a name="trigger-automation-to-configure-graph"></a>Attivare l'automazione per configurare graph

Per questa procedura, utilizzare un computer nella rete datacenter in grado di comunicare con l'endpoint con privilegi in Azure Stack.

1. Aprire una sessione di Windows PowerShell con privilegi elevata (Esegui come amministratore) e connettersi all'indirizzo IP dell'endpoint con privilegi. Usare le credenziali per **CloudAdmin** per eseguire l'autenticazione.

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Ora che ci si connette all'endpoint con privilegi, eseguire il comando seguente: 

   ```PowerShell  
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   Quando richiesto, specificare le credenziali per l'account utente che si desidera utilizzare per il servizio Graph (ad esempio graphservice). L'input per il cmdlet Register-DirectoryService deve essere il nome della foresta / root dominio nella foresta anziché qualsiasi altro dominio nella foresta.

   > [!IMPORTANT]
   > Attendere che le credenziali a comparsa (Get-Credential non è supportato nell'endpoint con privilegi) e immettere le credenziali dell'Account di servizio Graph.

#### <a name="graph-protocols-and-ports"></a>Porte e protocolli di Graph

Servizio Graph nello Stack di Azure Usa i seguenti protocolli e porte per comunicare con un scrivibile globali Server di catalogo e il centro distribuzione chiavi (KDC) in grado di elaborare le richieste di accesso nella foresta di Active Directory di destinazione.

Servizio Graph nello Stack di Azure Usa i seguenti protocolli e porte per comunicare con Active Directory di destinazione:

|Type|Porta|Protocollo|
|---------|---------|---------|
|LDAP|389|TCP E UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Impostazione dell'integrazione di AD FS, scaricare i metadati della federazione

Le informazioni seguenti sono necessarie come input per i parametri di automazione:

|Parametro|DESCRIZIONE|Esempio|
|---------|---------|---------|
|CustomAdfsName|Nome del provider di attestazioni.<br>Viene visualizzato in questo modo nella pagina di destinazione AD FS.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Collegamento di metadati di federazione| https:\//ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml |


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Attivare l'automazione per configurare i trust del provider di attestazioni in Azure Stack

Per questa procedura, utilizzare un computer in grado di comunicare con l'endpoint con privilegi in Azure Stack. È previsto che il certificato usato dall'account **servizio token di sicurezza AD FS** è considerato attendibile da Azure Stack.

1. Aprire una sessione di Windows PowerShell con privilegi elevata e connettersi all'endpoint con privilegi.

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Ora che si è connessi all'endpoint con privilegi, eseguire il comando seguente usando i parametri appropriati per l'ambiente:

   ```PowerShell  
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Eseguire il comando seguente per aggiornare il proprietario della sottoscrizione del provider predefinito, utilizzando i parametri appropriati per l'ambiente:

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Impostazione dell'integrazione di AD FS, fornendo il file di metadati di federazione

A partire dalla versione 1807, usare questo metodo se una delle condizioni seguenti è true:

- La catena di certificati è diversa per AD FS rispetto a tutti gli altri endpoint in Azure Stack.
- Non è disponibile connettività di rete nel server AD FS esistente dall'istanza di AD FS di Azure Stack.

Le informazioni seguenti sono necessarie come input per i parametri di automazione:


|Parametro|DESCRIZIONE|Esempio|
|---------|---------|---------|
|CustomAdfsName|Nome del provider di attestazioni. Viene visualizzato in questo modo nella pagina di destinazione AD FS.|Contoso|
|CustomADFSFederationMetadataFileContent|Contenuto dei metadati|$using:federationMetadataFileContent|

### <a name="create-federation-metadata-file"></a>Creare il file di metadati di federazione

Per la procedura seguente, è necessario usare un computer con connettività di rete per la distribuzione di AD FS esistente, che diventa l'account del servizio token di sicurezza. Inoltre, i certificati necessari devono essere installati.

1. Aprire una sessione di Windows PowerShell con privilegi elevata ed eseguire il comando seguente, usando i parametri appropriati per l'ambiente:

   ```PowerShell  
    $url = "https://win-SQOOJN70SGL.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml"
    $webclient = New-Object System.Net.WebClient
    $webclient.Encoding = [System.Text.Encoding]::UTF8
    $metadataAsString = $webclient.DownloadString($url)
    Set-Content -Path c:\metadata.xml -Encoding UTF8 -Value $metadataAsString
   ```

2. Copiare il file di metadati in un computer in grado di comunicare con l'endpoint con privilegi.

### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Attivare l'automazione per configurare i trust del provider di attestazioni in Azure Stack

Per questa procedura, usare un computer in cui può comunicare con l'endpoint con privilegi in Azure Stack e ha accesso al file dei metadati che è stato creato nel passaggio precedente.

1. Aprire una sessione di Windows PowerShell con privilegi elevata e connettersi all'endpoint con privilegi.

   ```PowerShell  
   $federationMetadataFileContent = get-content c:\metadata.xml
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Ora che si è connessi all'endpoint con privilegi, eseguire il comando seguente usando i parametri appropriati per l'ambiente:

    ```PowerShell
    Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataFileContent $using:federationMetadataFileContent
    ```

3. Eseguire il comando seguente per aggiornare il proprietario della sottoscrizione del provider predefinito, utilizzando i parametri appropriati per l'ambiente:

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

   > [!Note]  
   > Quando si ruota il certificato in ADFS esistente (account del servizio token di sicurezza) è necessario impostare nuovamente l'integrazione di AD FS. Anche se l'endpoint dei metadati è raggiungibile o che è stato configurato, fornendo il file di metadati, è necessario configurare l'integrazione.

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Configurare una relying party nella distribuzione di AD FS esistente (account del servizio token di sicurezza)

Microsoft fornisce uno script che configura il trust della relying party, comprese le regole di trasformazione di attestazioni. Usando lo script è facoltativo in quanto è possibile eseguire i comandi manualmente.

È possibile scaricare lo script helper [strumenti di Azure Stack](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) su GitHub.

Se si decide di eseguire manualmente i comandi, seguire questa procedura:

1. Copiare il contenuto seguente in un file con estensione txt (ad esempio, salvato come c:\ClaimRules.txt) nel membro di istanza o una farm AD FS del tuo Data Center:

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
   => issue(claim = c);
   ```

2. Verificare che l'autenticazione basata su moduli di Windows per la rete extranet e intranet è abilitata. Per prima cosa verificare se il già abilitata eseguendo il cmdlet seguente:

   ```PowerShell  
   Get-AdfsAuthenticationProvider | where-object { $_.name -eq "FormsAuthentication" } | select Name, AllowedForPrimaryExtranet, AllowedForPrimaryIntranet
   ```

    > [!Note]  
    > Il Windows Integrated Authentication (WIA) agente utente supportate stringhe potrebbero obsoleta per la distribuzione di AD FS possono richiedere di essere aggiornato per supportare i client più recenti. È possibile leggere informazioni sull'aggiornamento di WIA stringhe agente utente è supportato nell'articolo [Configuring intranet basata su form l'autenticazione per i dispositivi che non supportano WIA](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-intranet-forms-based-authentication-for-devices-that-do-not-support-wia).<br>I passaggi per abilitare i criteri di autenticazione basata su Form sono documentati nell'articolo [configurare i criteri di autenticazione](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-authentication-policies).

3. Per aggiungere il trust della relying party, eseguire il comando Windows PowerShell seguente istanza di AD FS o un membro di farm. Assicurarsi di aggiornare l'endpoint di AD FS e puntare al file creato nel passaggio 1.

   **Per AD FS 2016**

   ```PowerShell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone" -TokenLifeTime 1440
   ```

   **Per AD FS 2012 o 2012 R2**

   ```PowerShell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -TokenLifeTime 1440
   ```

   > [!IMPORTANT]  
   > Configurare le regole di autorizzazione rilascio quando si usa Windows Server 2012 o 2012 R2 AD ADFS, è necessario utilizzare lo snap-in MMC di AD FS.

4. Quando si usa il browser Microsoft Edge o Internet Explorer per accedere a Azure Stack, è necessario ignorare associazioni dei token. In caso contrario, i tentativi di accesso esito negativo. L'istanza di AD FS o un membro di farm, eseguire il comando seguente:

   > [!note]  
   > Questo passaggio non è applicabile quando si usa Windows Server 2012 o 2012 R2 AD ADFS. È possibile ignorare questo comando e continuare con l'integrazione.

   ```PowerShell  
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

## <a name="spn-creation"></a>Creazione di SPN

Esistono molti scenari che richiedono l'uso di un nome dell'entità servizio (SPN) per l'autenticazione. Di seguito sono riportati alcuni esempi:

- Utilizzo dell'interfaccia della riga con la distribuzione di ADFS di Azure Stack
- System Center Management Pack per Azure Stack quando distribuito con AD FS
- Provider di risorse in Azure Stack quando distribuito con AD FS
- Diverse applicazioni
- È necessario un accesso interattivo

> [!Important]  
> ADFS supporta solo le sessioni di accesso interattivo. Se è necessario un account di accesso interattivo per uno scenario automatizzato, è necessario usare un nome SPN.

Per altre informazioni sulla creazione di un nome SPN, vedere [creare un'entità servizio per AD FS](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).


## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="configuration-rollback"></a>Rollback di configurazione

Se si verifica un errore che lascia l'ambiente in uno stato in cui è non è più possibile l'autenticazione, è disponibile un'opzione di rollback.

1. Aprire una sessione di Windows PowerShell con privilegi elevata ed eseguire i comandi seguenti:

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Eseguire quindi il cmdlet seguente:

   ```PowerShell  
   Reset-DatacenterIntegrationConfiguration
   ```

   Dopo aver eseguito l'azione di rollback, tutte le modifiche di configurazione il rollback. Solo l'autenticazione con le funzionalità integrate **CloudAdmin** utente è possibile.

   > [!IMPORTANT]
   > È necessario configurare il proprietario della sottoscrizione del provider predefinito originale

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>La raccolta dei log aggiuntivi

Se uno dei cmdlet non riesce, è possibile raccogliere log aggiuntivi usando il `Get-Azurestacklogs` cmdlet.

1. Aprire una sessione di Windows PowerShell con privilegi elevata ed eseguire i comandi seguenti:

   ```PowerShell  
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Quindi, eseguire il cmdlet seguente:

   ```PowerShell  
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>Passaggi successivi

[Integrare soluzioni di monitoraggio esterne](azure-stack-integrate-monitor.md)
