---
title: Integrazione di Azure Data Center di Stack - identità
description: Apprendere come integrare Azure Stack AD FS con AD FS Data Center
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 05/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: ee1c48c4a33d699dcb3da24b2e9a3d6e001b16c5
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801474"
---
# <a name="azure-stack-datacenter-integration---identity"></a>Integrazione di Azure Data Center di Stack - identità
È possibile distribuire Azure Stack usando Azure Active Directory (Azure AD) o Active Directory Federation Services (ADFS), i provider di identità. È necessario adottare la scelta prima di distribuire Azure Stack. Distribuzione tramite ADFS viene detta anche la distribuzione di Azure Stack in modalità disconnessa.

Nella tabella seguente vengono illustrate le differenze tra le scelte di due identità:

||Disconnesso da internet|Connesso a internet|
|---------|---------|---------|
|Fatturazione|Deve essere capacità<br> Enterprise Agreement (EA)|Capacità o retribuzione come-si-utilizzo<br>EA o Provider di soluzioni Cloud (CSP)|
|Identità|È necessario AD FS|Azure Active Directory o AD FS|
|Diffusione di Marketplace|Supportato<br>BYOL licenze|Supportato<br>BYOL licenze|
|Registrazione|Consigliata, è necessario un supporto rimovibile<br> e un dispositivo connesso distinto.|Automatico|
|Patch e aggiornamenti|Obbligatorio, è necessario un supporto rimovibile<br> e un dispositivo connesso distinto.|Pacchetto di aggiornamento può essere scaricato direttamente<br> da Internet allo Stack di Azure.|

> [!IMPORTANT]
> È possibile passare il provider di identità senza ridistribuire l'intera soluzione Azure Stack.

## <a name="active-directory-federation-services-and-graph"></a>Grafico e active Directory Federation Services

Distribuzione con AD FS consente alle identità in una foresta di Active Directory esistente per l'autenticazione con le risorse nello Stack di Azure. Questa foresta di Active Directory esistente è necessaria una distribuzione di ADFS per consentire la creazione di un trust federativo AD FS.

L'autenticazione è una parte dell'identità. Per gestire basato sui ruoli accesso controllo (RBAC) nello Stack di Azure, il componente grafico deve essere configurato. Quando l'accesso a una risorsa viene delegata, il componente grafico Cerca l'account utente nella foresta Active Directory esistente tramite il protocollo LDAP.

![Architettura dello Stack ADFS Azure](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

L'istanza di ADFS esistente è l'account servizio token di sicurezza (STS) che invia attestazioni per AD FS dello Stack di Azure (la risorsa servizio token di sicurezza). Nello Stack di Azure, automazione crea il trust del provider di attestazioni con l'endpoint dei metadati per l'istanza di ADFS esistente.

In ADFS esistente, è necessario configurare un trust della relying party. Questo passaggio non viene eseguito per l'automazione e deve essere configurato dall'operatore. L'endpoint dei metadati di Azure Stack è documentato nel file AzureStackStampDeploymentInfo.JSON o tramite l'endpoint con privilegi eseguendo il comando `Get-AzureStackInfo`.

La configurazione del trust della relying party richiede anche di configurare le regole di trasformazione di attestazioni che sono fornite da Microsoft.

Per la configurazione del grafico, un account del servizio deve essere purché disponga dell'autorizzazione in Active Directory esistente. Questo account è necessario come input per l'automazione abilitare scenari RBAC.

Per l'ultimo passaggio, un nuovo proprietario è configurato per la sottoscrizione di provider predefinito. Questo account disponga dell'accesso completo a tutte le risorse quando accedono al portale di amministrazione di Azure Stack.

Requirements:


|Componente|Requisito|
|---------|---------|
|Grafico|Microsoft Active Directory 2012 o 2012 R2/2016|
|AD FS|Windows Server 2012 o 2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Impostazione dell'integrazione di Graph

Graph supporta solo l'integrazione con una singola foresta di Active Directory. In presenza di più foreste, solo la foresta specificata nella configurazione verrà utilizzata per recuperare gli utenti e gruppi.

Le informazioni seguenti sono necessari come input per i parametri di automazione:


|Parametro|DESCRIZIONE|Esempio|
|---------|---------|---------|
|CustomADGlobalCatalog|Nome di dominio completo della destinazione di foresta di Active Directory<br>che si desidera integrare con|Contoso.com|
|CustomADAdminCredentials|Un utente con autorizzazioni di lettura LDAP|YOURDOMAIN\graphservice|

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Creare account utente in Active Directory esistente (facoltativo)

Facoltativamente, è possibile creare un account per il servizio di grafico in Active Directory esistente. Eseguire questo passaggio se non hai già un account che si desidera utilizzare.

1. In Active Directory esistente, creare l'account utente seguenti (consigliato):
   - **Nome utente**: graphservice
   - **Password**: utilizzare una password complessa<br>Configurare password senza scadenza.

   Non è necessario alcun autorizzazioni speciali o l'appartenenza.

#### <a name="trigger-automation-to-configure-graph"></a>Automazione di trigger per configurare grafico

Per questa procedura, utilizzare un computer della rete di Data Center in grado di comunicare con l'endpoint con privilegi nello Stack di Azure.

2. Aprire una sessione di Windows PowerShell con privilegi elevata (Esegui come amministratore) e connettersi all'indirizzo IP dell'endpoint con privilegi. Utilizzare le credenziali per **CloudAdmin** per l'autenticazione.

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

3. Ora che si è connessi all'endpoint con privilegi, eseguire il comando seguente: 

   ```PowerShell  
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   Quando richiesto, specificare le credenziali per l'account utente che si desidera utilizzare per il servizio di grafico (ad esempio graphservice). L'input per il cmdlet Register-DirectoryService deve essere il nome della foresta / root dominio nella foresta anziché qualsiasi altro dominio nella foresta.

   > [!IMPORTANT]
   > Attendere che le credenziali a comparsa (Get-Credential non è supportato nell'endpoint con privilegi) e immettere le credenziali dell'Account di servizio grafico.

#### <a name="graph-protocols-and-ports"></a>Grafico protocolli e porte

Servizio Graph nello Stack di Azure Usa le seguenti porte e protocolli per comunicare con un scrivibile Server (catalogo globale) e il centro distribuzione chiavi (KDC) in grado di elaborare le richieste di accesso nella foresta Active Directory di destinazione.

Servizio Graph nello Stack di Azure Usa i seguenti protocolli e porte per comunicare con Active Directory di destinazione:

|type|Porta|Protocollo|
|---------|---------|---------|
|LDAP|389|TCP / UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Configurazione dell'integrazione di AD FS di scaricando i metadati della federazione

Sono necessarie le seguenti informazioni come input per i parametri di automazione:

|Parametro|DESCRIZIONE|Esempio|
|---------|---------|---------|
|CustomAdfsName|Nome del provider di attestazioni. <cr>Viene visualizzato in questo modo nella pagina di destinazione AD FS.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Collegamento di metadati di federazione|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Automazione di trigger per configurare l'attendibilità di provider di attestazioni nello Stack di Azure

Per questa procedura, utilizzare un computer in grado di comunicare con l'endpoint con privilegi nello Stack di Azure. È previsto che il certificato utilizzato dall'account **STS AD FS** è considerato attendibile da Stack di Azure.

1. Aprire una sessione di Windows PowerShell con privilegi elevata e connettersi all'endpoint con privilegi.

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Ora che si è connessi all'endpoint con privilegi, eseguire il comando seguente utilizzando i parametri appropriati per l'ambiente:

   ```PowerShell  
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Eseguire il comando seguente per aggiornare il proprietario della sottoscrizione del provider predefinito, utilizzando i parametri appropriati per l'ambiente:

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Configurazione dell'integrazione di AD FS di fornendo il file di metadati di federazione

Utilizzare questo metodo se una delle seguenti condizioni sono vere:

- La catena di certificati è diversa per AD FS rispetto a tutti gli altri endpoint nello Stack di Azure.
- Vi è connettività di rete del server ADFS esistente dall'istanza di AD FS Azure dello Stack.

Sono necessarie le seguenti informazioni come input per i parametri di automazione:


|Parametro|DESCRIZIONE|Esempio|
|---------|---------|---------|
|CustomAdfsName|Nome del provider di attestazioni. Viene visualizzato in questo modo nella pagina di destinazione AD FS.|Contoso|
|CustomADFSFederationMetadataFile|File di metadati di federazione|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|

### <a name="create-federation-metadata-file"></a>Creare il file di metadati di federazione

Per la procedura seguente, è necessario utilizzare un computer con connettività di rete per la distribuzione di ADFS esistente, che diventa l'account del servizio token di sicurezza. Inoltre, i certificati necessari devono essere installati.

1. Aprire una sessione di Windows PowerShell con privilegi elevata ed eseguire il comando seguente, usando i parametri appropriati per l'ambiente:

   ```PowerShell  
   [XML]$Metadata = Invoke-WebRequest -URI https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml -UseBasicParsing

   $Metadata.outerxml|out-file c:\metadata.xml
   ```

2. Copiare il file di metadati in una condivisione accessibile dall'endpoint con privilegi.


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Automazione di trigger per configurare l'attendibilità di provider di attestazioni nello Stack di Azure

Per questa procedura, utilizzare un computer in grado di comunicare con l'endpoint con privilegi nello Stack di Azure.

1. Aprire una sessione di Windows PowerShell con privilegi elevata e connettersi all'endpoint con privilegi.

   ```PowerShell  
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Ora che si è connessi all'endpoint con privilegi, eseguire il comando seguente utilizzando i parametri appropriati per l'ambiente:

   ```PowerShell  
   Register-CustomAdfs -CustomAdfsName Contoso – CustomADFSFederationMetadataFile \\share\metadataexample.xml
   ```

3. Eseguire il comando seguente per aggiornare il proprietario della sottoscrizione del provider predefinito, utilizzando i parametri appropriati per l'ambiente:

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Configurare una relying party nella distribuzione di ADFS esistente (account del servizio token di sicurezza)

Microsoft fornisce uno script che configura il trust della relying party, comprese le regole di trasformazione di attestazioni. È possibile eseguire manualmente i comandi usando lo script è facoltativo.

È possibile scaricare lo script di supporto dal [strumenti di Azure Stack](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) su Github.

Se si decide di eseguire manualmente i comandi, seguire questi passaggi:

1. Copiare il contenuto seguente in un file con estensione txt (ad esempio, salvato come c:\ClaimRules.txt) nel membro di istanza o una farm di ADFS del Data Center:

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
   c:[Type == http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname]
   => issue(claim = c);
   ```

2. Per abilitare l'autenticazione basata su Windows Form, aprire una sessione di Windows PowerShell come un utente con privilegi elevato ed eseguire il comando seguente:

   ```PowerShell  
   Set-AdfsProperties -WIASupportedUserAgents @("MSAuthHost/1.0/In-Domain","MSIPC","Windows Rights Management Client","Kloud")
   ```

3. Per aggiungere il trust della relying party, eseguire il comando seguente di Windows PowerShell all'istanza di AD FS o un membro di farm. Assicurarsi di aggiornare l'endpoint ADFS e puntare al file creato nel passaggio 1.

   **Per AD FS 2016**

   ```PowerShell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone"
   ```

   **Per AD FS 2012 o 2012 R2**

   ```PowerShell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true
   ```

   > [!IMPORTANT]
   > È necessario utilizzare lo snap-in ADFS di MMC per configurare le regole di autorizzazione rilascio quando si utilizza Windows Server 2012 o 2012 R2 AD FS.

4. Quando si utilizza Internet Explorer o il browser Microsoft Edge per accedere allo Stack di Azure, è necessario ignorare associazioni dei token. In caso contrario, i tentativi di accesso esito negativo. L'istanza di AD FS o un membro della farm, eseguire il comando seguente:

   > [!note]  
   > Questo passaggio non è applicabile quando si utilizza Windows Server 2012 o 2012 R2 AD FS. È possibile ignorare questo comando e continuare con l'integrazione.

   ```PowerShell  
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

5. I portali di Stack di Azure e strumenti (Visual Studio) richiedono i token di aggiornamento. Sarà necessario configurarle basandosi sulle attendibilità. Aprire una sessione di Windows PowerShell con privilegi elevata ed eseguire il comando seguente:

   ```PowerShell  
   Set-ADFSRelyingPartyTrust -TargetName AzureStack -TokenLifeTime 1440
   ```

## <a name="spn-creation"></a>Creazione di SPN

Esistono molti scenari che richiedono l'uso di un nome dell'entità servizio (SPN) per l'autenticazione. Di seguito sono riportati alcuni esempi:

- Utilizzo CLI con la distribuzione di ADFS dello Stack di Azure
- System Center Management Pack per Azure Stack quando distribuito con AD FS
- Provider di risorse nello Stack di Azure quando distribuito con AD FS
- Varie applicazioni
- È necessario un account di accesso non interattivo

> [!Important]  
> ADFS supporta solo le sessioni di accesso interattivo. Se è necessario un account di accesso non interattivo per uno scenario automatizzato, è necessario utilizzare un nome SPN.

Per ulteriori informazioni sulla creazione di un nome SPN, vedere [creare l'entità servizio per AD FS](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-ad-fs).


## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="configuration-rollback"></a>Rollback di configurazione

Se si verifica un errore che lascia l'ambiente in uno stato in cui è non possibile non è più eseguire l'autenticazione, è disponibile un'opzione di rollback.

1. Aprire una sessione di Windows PowerShell con privilegi elevata ed eseguire i comandi seguenti:

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Eseguire quindi il cmdlet seguente:

   ```PowerShell  
   Reset-DatacenterIntegationConfiguration
   ```

   Dopo aver eseguito l'azione di rollback, tutte le modifiche di configurazione il rollback. Solo l'autenticazione con l'elemento predefinito **CloudAdmin** utente è possibile.

   > [!IMPORTANT]
   > È necessario configurare il proprietario originale della sottoscrizione del provider predefinito

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>La raccolta dei log aggiuntivi

Se uno dei cmdlet non riesce, è possibile raccogliere i log usando il `Get-Azurestacklogs` cmdlet.

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

[Integrare le soluzioni di monitoraggio esterne](azure-stack-integrate-monitor.md)
