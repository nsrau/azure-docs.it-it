---
title: Prima di distribuire il servizio App in Azure Stack | Microsoft Docs
description: Passaggi da completare prima di distribuire il servizio App in Azure Stack
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: anwestg
ms.openlocfilehash: ec34b99f48fbe7b634d7d143e8e108e8f2868f67
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55182728"
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Prima di iniziare con il servizio App in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Prima di distribuire il servizio App di Azure in Azure Stack, è necessario completare i passaggi dei prerequisiti in questo articolo.

> [!IMPORTANT]
> Applicare l'aggiornamento 1809 al sistema integrato Azure Stack o distribuire più recente Azure Stack Development Kit (ASDK) prima di distribuire Azure App Service 1.4.

## <a name="download-the-installer-and-helper-scripts"></a>Scaricare il programma di installazione e script helper

1. Scaricare il [servizio App in script di supporto di Azure Stack deployment](https://aka.ms/appsvconmashelpers).
2. Scaricare il [servizio App nel programma di installazione di Azure Stack](https://aka.ms/appsvconmasinstaller).
3. Estrarre i file dal file ZIP script helper. Vengono estratti i file e cartelle seguenti:

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - Cartella di moduli
     - GraphAPI.psm1

## <a name="syndicate-the-custom-script-extension-from-the-marketplace"></a>Pubblicare l'estensione Script personalizzato dal Marketplace

Servizio App di Azure in Azure Stack richiede v1.9.0 estensione Script personalizzato.  L'estensione deve essere [sfruttati da Marketplace](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) prima di avviare la distribuzione o aggiornamento del servizio App di Azure in Azure Stack

## <a name="high-availability"></a>Disponibilità elevata

L'aggiornamento 1802 dello Stack di Azure aggiunto il supporto per domini di errore. Le nuove distribuzioni del servizio App di Azure in Azure Stack verranno distribuite tra domini di errore e garantire tolleranza di errore.

Per le distribuzioni esistenti di servizio App di Azure in Azure Stack, che sono state distribuite prima dell'aggiornamento 1802, vedere la [ribilanciare un provider di risorse del servizio App in domini di errore](azure-stack-app-service-fault-domain-update.md) articolo.

Inoltre, distribuire il server di file richiesto e istanze di SQL Server in una configurazione a disponibilità elevata.

## <a name="get-certificates"></a>Ottenere i certificati

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Certificato di radice di Azure Resource Manager per Azure Stack

Aprire una sessione di PowerShell con privilegi elevata in un computer in grado di raggiungere l'endpoint con privilegi nel sistema integrato di Azure Stack o Azure Stack Development Kit Host.

Eseguire la *Get-AzureStackRootCert.ps1* script dalla cartella in cui sono stati estratti gli script helper. Lo script crea un certificato radice nella stessa cartella dello script necessarie per il servizio App per la creazione di certificati.

Quando si esegue il comando PowerShell seguente, è possibile specificare l'endpoint con privilegi e le credenziali per il AzureStack\CloudAdmin.

```PowerShell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Get-AzureStackRootCert.ps1 script parameters

| Parametro | Obbligatoria o facoltativa | Valore predefinito | DESCRIZIONE |
| --- | --- | --- | --- |
| PrivilegedEndpoint | Obbligatorio | AzS-ERCS01 | Endpoint con privilegi |
| CloudAdminCredential | Obbligatorio | AzureStack\CloudAdmin | Credenziale dell'account di dominio per gli amministratori cloud di Azure Stack |

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Certificati necessari per la distribuzione ASDK del servizio App di Azure

Il *Create AppServiceCerts.ps1* script funziona con l'autorità di certificazione di Azure Stack per creare i quattro certificati necessarie per il servizio App.

| Nome file | Uso |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Certificato SSL predefinito del servizio app |
| api.appservice.local.azurestack.external.pfx | Certificato SSL dell'API del servizio App |
| ftp.appservice.local.azurestack.external.pfx | Certificato SSL del servizio App server di pubblicazione |
| sso.appservice.local.azurestack.external.pfx | Certificato applicazione identità del servizio App |

Per creare i certificati, seguire questa procedura:

1. Accedere all'host Azure Stack Development Kit utilizzando l'account AzureStack\AzureStackAdmin.
2. Aprire una sessione di PowerShell con privilegi elevata.
3. Eseguire la *Create AppServiceCerts.ps1* script dalla cartella in cui sono stati estratti gli script helper. Questo script crea quattro certificati nella stessa cartella dello script necessarie per il servizio App per la creazione di certificati.
4. Immettere una password per proteggere i file con estensione pfx e prendere nota di esso. È possibile immetterla nel servizio App nel programma di installazione di Azure Stack.

#### <a name="create-appservicecertsps1-script-parameters"></a>Create-AppServiceCerts.ps1 script parameters

| Parametro | Obbligatoria o facoltativa | Valore predefinito | DESCRIZIONE |
| --- | --- | --- | --- |
| pfxPassword | Obbligatorio | Null | Password che consente di proteggere la chiave privata del certificato |
| DomainName | Obbligatorio | local.azurestack.external | Suffisso area e il dominio di Azure Stack |

### <a name="certificates-required-for-azure-stack-production-deployment-of-azure-app-service"></a>Certificati necessari per la distribuzione di produzione di Azure Stack del servizio App di Azure

Per eseguire il provider di risorse nell'ambiente di produzione, è necessario fornire i certificati seguenti:

- Certificato di dominio predefinito
- Certificato dell'API
- Certificato di pubblicazione
- Certificato di identità

#### <a name="default-domain-certificate"></a>Certificato di dominio predefinito

Il certificato di dominio predefinito viene inserito il ruolo Front-End. Le applicazioni utente per la richiesta di dominio con caratteri jolly o predefinito nel servizio App di Azure di usano questo certificato. Il certificato viene usato anche per le operazioni di controllo di origine (Kudu).

Il certificato deve essere in formato PFX e deve essere un certificato con caratteri jolly tre soggetti. Questo requisito consente a uno certificato coprire sia il dominio predefinito e l'endpoint SCM per operazioni del controllo codice sorgente.

| Format | Esempio |
| --- | --- |
| \*.appservice. \<regione\>.\< DomainName\>.\< estensione\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.scm.appservice.redmond.azurestack.external |
| \*.sso.appservice.<region>.<DomainName>.<extension> | \*.sso.appservice.redmond.azurestack.external |

#### <a name="api-certificate"></a>Certificato dell'API

Il certificato API viene inserito il ruolo di gestione. Il provider di risorse utilizza per consentire le chiamate API protette. Il certificato per la pubblicazione deve contenere un soggetto corrispondente alla voce DNS di API.

| Format | Esempio |
| --- | --- |
| API.appservice. \<regione\>.\< DomainName\>.\< estensione\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Certificato di pubblicazione

Il certificato per il ruolo del server di pubblicazione consente di proteggere il traffico FTPS per i proprietari dell'applicazione durante il caricamento del contenuto. Il certificato per la pubblicazione deve contenere un soggetto corrispondente alla voce DNS FTPS.

| Format | Esempio |
| --- | --- |
| FTP.appservice. \<regione\>.\< DomainName\>.\< estensione\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Certificato di identità

Il certificato per l'applicazione di identità consente:

- Integrazione tra Azure Active Directory (Azure AD) o Active Directory Federation Services (ADFS) directory, Azure Stack e servizio App per supportare l'integrazione con il provider di risorse di calcolo.
- Scenari Single sign-on per gli strumenti di sviluppo avanzate in servizio App di Azure in Azure Stack.

Il certificato per l'identità deve contenere un oggetto che corrisponde al formato seguente.

| Format | Esempio |
| --- | --- |
| SSO.appservice. \<regione\>.\< DomainName\>.\< estensione\> | sso.appservice.redmond.azurestack.external |


### <a name="validate-certificates"></a>La convalida dei certificati
Prima di distribuire il provider di risorse del servizio app, dovrebbe [convalidare i certificati da utilizzare](azure-stack-validate-pki-certs.md#perform-platform-as-a-service-certificate-validation) utilizzando lo strumento di controllo di conformità di Azure Stack disponibile il [PowerShell Gallery](https://aka.ms/AzsReadinessChecker). Lo strumento di controllo dello stato di preparazione dello Stack di Azure verifica che i certificati PKI generati siano idonei per la distribuzione di servizi app. 

Come procedura consigliata, quando si lavora con uno qualsiasi dei necessari [i certificati di infrastruttura a chiave pubblica di Azure Stack](azure-stack-pki-certs.md), è consigliabile pianificare il tempo necessario per testare ed eseguire nuovamente i certificati se necessario. 

## <a name="virtual-network"></a>Rete virtuale

> [!NOTE]
> La pre-creazione di una rete virtuale personalizzata è facoltativa perché il servizio App di Azure in Azure Stack può creare la rete virtuale necessaria, ma sarà quindi necessario comunicare con SQL e File Server tramite gli indirizzi IP pubblici.

Servizio App di Azure in Azure Stack ti permette di distribuire il provider di risorse a una rete virtuale esistente oppure è possibile creare una rete virtuale come parte della distribuzione. Usa una rete virtuale esistente consente l'uso di indirizzi IP interni per la connessione al file server e SQL server richiesto dal servizio App di Azure in Azure Stack. La rete virtuale deve essere configurata con l'intervallo di indirizzi e subnet seguenti prima di installare il servizio App di Azure in Azure Stack:

Rete virtuale - /16

Subnet

- ControllersSubnet /24
- ManagementServersSubnet /24
- FrontEndsSubnet da/24
- PublishersSubnet /24
- WorkersSubnet /21

## <a name="prepare-the-file-server"></a>Preparare il file server

Servizio App di Azure richiede l'uso di un file server. Per le distribuzioni di produzione, il file server deve essere configurato per essere in grado di gestire gli errori e a disponibilità elevata.

### <a name="quickstart-template-for-file-server-for-deployments-of-azure-app-service-on-asdk"></a>Modello di avvio rapido per il File Server per le distribuzioni del servizio App di Azure in ASDK.

Azure Stack Development Kit solo per le distribuzioni, è possibile usare la [modello di distribuzione Azure Resource Manager di esempio](https://aka.ms/appsvconmasdkfstemplate) per distribuire un server di file a nodo singolo configurato. Il server a nodo singolo file sarà un gruppo di lavoro.

### <a name="quickstart-template-for-highly-available-file-server-and-sql-server"></a>Modello di avvio rapido per File Server a disponibilità elevata e SQL Server

Oggetto [modello di avvio rapido di architettura di riferimento](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/appservice-fileserver-sqlserver-ha) è ora disponibile, che distribuirà i File Server, SQL Server, Active Directory di supporto dell'infrastruttura in una rete virtuale configurata per supportare una distribuzione a disponibilità elevata di Servizio App di Azure in Azure Stack.  

### <a name="steps-to-deploy-a-custom-file-server"></a>Passaggi per distribuire un File Server personalizzato

>[!IMPORTANT]
> Se si sceglie di distribuire il servizio App in una rete virtuale esistente, il File Server deve essere distribuito in una Subnet separata dal servizio App.

#### <a name="provision-groups-and-accounts-in-active-directory"></a>Effettuare il provisioning di gruppi e account in Active Directory

1. Creare i gruppi di sicurezza globali di Active Directory seguenti:

   - FileShareOwners
   - FileShareUsers

2. Creare i seguenti account di Active Directory come account del servizio:

   - FileShareOwner
   - FileShareUser

   Come procedura di sicurezza consigliata, gli utenti per questi account e, per tutti i ruoli web, deve essere univoco e sono sicuri nomi utente e password. Impostare le password con le condizioni seguenti:

   - Abilitare **Nessuna scadenza Password**.
   - Abilitare **cambiamento password non**.
   - Disabilitare **cambiamento obbligatorio password all'accesso successivo**.

3. Aggiungere gli account per l'appartenenza ai gruppi come indicato di seguito:

   - Aggiungere **FileShareOwner** per il **FileShareOwners** gruppo.
   - Aggiungere **FileShareUser** per il **FileShareUsers** gruppo.

#### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Effettuare il provisioning di gruppi e account in un gruppo di lavoro

>[!NOTE]
> Quando si sta configurando un file server, eseguire i comandi seguenti da un **prompt dei comandi amministratore**. <br>***Non usare PowerShell.***

Quando si usa il modello di Azure Resource Manager, gli utenti sono già stati creati.

1. Eseguire i comandi seguenti per creare gli account FileShareOwner e FileShareUser. Sostituire `<password>` con i propri valori.

   ``` DOS
   net user FileShareOwner <password> /add /expires:never /passwordchg:no
   net user FileShareUser <password> /add /expires:never /passwordchg:no
   ```

2. Impostare le password per l'account per non scadere mai, eseguire i comandi WMIC seguenti:

   ``` DOS
   WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
   WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
   ```

3. Creare i gruppi locali FileShareUsers e FileShareOwners e aggiungervi l'account nel primo passaggio:

   ``` DOS
   net localgroup FileShareUsers /add
   net localgroup FileShareUsers FileShareUser /add
   net localgroup FileShareOwners /add
   net localgroup FileShareOwners FileShareOwner /add
   ```

#### <a name="provision-the-content-share"></a>Effettuare il provisioning della condivisione contenuto

La condivisione del contenuto contiene contenuto del sito Web tenant. La procedura per effettuare il provisioning della condivisione contenuto in un singolo file server è lo stesso per gli ambienti Active Directory e del gruppo di lavoro. Ma è diversa per un cluster di failover in Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Effettuare il provisioning della condivisione contenuto in un singolo file server (Active Directory o gruppo di lavoro)

In un singolo file server, eseguire i comandi seguenti in un prompt dei comandi con privilegi elevati. Sostituire il valore di `C:\WebSites` con i percorsi corrispondenti nell'ambiente in uso.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="configure-access-control-to-the-shares"></a>Configurare il controllo di accesso alle condivisioni

Eseguire i comandi seguenti in un prompt dei comandi con privilegi elevati nel file server o nel nodo del cluster di failover, che è il proprietario della risorsa cluster corrente. Sostituire i valori in corsivo con i valori che sono specifici dell'ambiente.

#### <a name="active-directory"></a>Active Directory

```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Gruppo di lavoro

```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server-instance"></a>Preparare l'istanza di SQL Server

Per il servizio App di Azure in hosting di Azure Stack e nei database di controllo, è necessario preparare un'istanza di SQL Server per contenere i database di servizio App.

Per le distribuzioni di Azure Stack Development Kit, è possibile usare SQL Server Express 2014 SP2 o versione successiva.

Per scopi di disponibilità elevata e di produzione, è necessario utilizzare una versione completa di SQL Server 2014 SP2 o versioni successive, abilitare l'autenticazione modalità mista e distribuire un [configurazione a disponibilità elevata](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

L'istanza di SQL Server per il servizio App di Azure in Azure Stack deve essere accessibile da tutti i ruoli del servizio App. È possibile distribuire SQL Server all'interno della sottoscrizione di Provider predefinito in Azure Stack. È possibile impostare o usare l'infrastruttura esistente all'interno dell'organizzazione (purché sia presente la connettività ad Azure Stack). Se si usa un'immagine di Marketplace di Azure, ricordarsi di configurare il firewall di conseguenza.

>[!NOTE]
> Un numero di immagini di macchine virtuali IaaS di SQL è disponibile tramite la funzionalità di gestione di Marketplace. Assicurarsi sempre di scaricare la versione più recente dell'estensione SQL IaaS prima di distribuire una macchina virtuale usando un elemento del Marketplace. Le immagini SQL sono le stesse macchine virtuali di SQL disponibili in Azure. Per le macchine virtuali SQL creati da queste immagini, l'estensione IaaS e miglioramenti del portale corrispondenti offrono funzionalità quali l'applicazione automatica delle patch e funzionalità di backup.
>
Per uno dei ruoli di SQL Server, è possibile usare un'istanza predefinita o un'istanza denominata. Se si usa un'istanza denominata, assicurarsi di avviare il servizio SQL Server Browser e aprire la porta 1434 manualmente.

>[!IMPORTANT]
> Se si sceglie di distribuire il servizio App in una rete virtuale esistente di SQL Server deve essere distribuito in una Subnet separata dal servizio App e il File Server.
>

## <a name="create-an-azure-active-directory-application"></a>Creare un'applicazione Azure Active Directory

Configurare un'entità servizio di Azure AD per supportare le operazioni seguenti:

- Integrazione di set di scalabilità di macchine virtuali sui livelli del ruolo di lavoro.
- SSO per gli strumenti di sviluppo del portale e avanzate di funzioni di Azure.

Questa procedura riguarda solo per gli ambienti Azure protetta AD Azure Stack.

Gli amministratori devono configurare l'accesso SSO per:

- Abilita gli strumenti di sviluppo avanzati all'interno del servizio App (Kudu).
- Abilitare l'uso dell'esperienza del portale di funzioni di Azure.

A tale scopo, seguire questa procedura:

1. Aprire un'istanza di PowerShell come azurestack\AzureStackAdmin.
2. Passare al percorso degli script che è stato scaricato ed estratto il [passaggio dei prerequisiti](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started).
3. [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md).
4. Eseguire la **Create AADIdentityApp.ps1** script. Quando richiesto, immettere l'ID tenant di Azure AD che sta usando per la distribuzione di Azure Stack. Ad esempio, immettere **myazurestack.onmicrosoft.com**.
5. Nel **credenziale** finestra, immettere la password e account di amministratore del servizio di Azure AD. Selezionare **OK**.
6. Immettere il percorso del file di certificato e la password del certificato per il [certificato creato in precedenza](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started). Il certificato creato per questo passaggio per impostazione predefinita è **sso.appservice.local.azurestack.external.pfx**.
7. Lo script crea una nuova applicazione nell'istanza di Azure AD tenant. Prendere nota dell'ID applicazione restituito nell'output di PowerShell. Queste informazioni durante l'installazione sono necessarie.
8. Aprire una nuova finestra del browser e Accedi per il [portale di Azure](https://portal.azure.com) come amministratore del servizio Azure Active Directory.
9. Aprire il provider di risorse di Azure AD.
10. Selezionare **registrazioni per l'App**.
11. Cercare l'ID applicazione restituito come parte del passaggio 7. Un'applicazione di servizio App è elencata.
12. Selezionare **applicazione** nell'elenco.
13. Selezionare **Impostazioni**.
14. Selezionare **autorizzazioni necessarie** > **concedere le autorizzazioni** > **Sì**.

```PowerShell
    Create-AADIdentityApp.ps1
```

| Parametro | Obbligatoria o facoltativa | Valore predefinito | DESCRIZIONE |
| --- | --- | --- | --- |
| DirectoryTenantName | Obbligatorio | Null | ID tenant di Azure AD. Specificare il GUID o una stringa. Un esempio è myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Obbligatorio | Null | Endpoint amministratore Azure Resource Manager. Un esempio è adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Obbligatorio | Null | Endpoint tenant di Azure Resource Manager. Un esempio è management.local.azurestack.external. |
| AzureStackAdminCredential | Obbligatorio | Null | Credenziali di amministratore del servizio Azure AD. |
| CertificateFilePath | Obbligatorio | Null | **Percorso completo** nel file di certificato dell'applicazione di identità generato in precedenza. |
| CertificatePassword | Obbligatorio | Null | Password che consente di proteggere la chiave privata del certificato. |
| Environment | Facoltativo | AzureCloud | Il nome dell'ambiente Cloud supportati in cui è disponibile il servizio di Azure Active Directory Graph di destinazione.  Valori consentiti: 'AzureCloud', 'AzureChinaCloud', 'AzureUSGovernment', 'AzureGermanCloud'.|

## <a name="create-an-active-directory-federation-services-application"></a>Creare un'applicazione di Active Directory Federation Services

Per gli ambienti Azure Stack protetti da AD FS, è necessario configurare un'entità servizio AD FS per supportare le operazioni seguenti:

- Integrazione di set di scalabilità di macchine virtuali sui livelli del ruolo di lavoro.
- SSO per gli strumenti di sviluppo del portale e avanzate di funzioni di Azure.

Gli amministratori devono configurare l'accesso SSO per:

- Configurare un'entità servizio per l'integrazione di set di scalabilità di macchina virtuale sui livelli del ruolo di lavoro.
- Abilita gli strumenti di sviluppo avanzati all'interno del servizio App (Kudu).
- Abilitare l'uso dell'esperienza del portale di funzioni di Azure.

A tale scopo, seguire questa procedura:

1. Aprire un'istanza di PowerShell come azurestack\AzureStackAdmin.
2. Passare al percorso degli script che è stato scaricato ed estratto il [passaggio dei prerequisiti](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started).
3. [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md).
4. Eseguire la **Create ADFSIdentityApp.ps1** script.
5. Nel **credenziale** finestra, immettere la password e un account amministratore del cloud AD FS. Selezionare **OK**.
6. Fornire il percorso file di certificato e la password del certificato per il [certificato creato in precedenza](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started). Il certificato creato per questo passaggio per impostazione predefinita è **sso.appservice.local.azurestack.external.pfx**.

```PowerShell
    Create-ADFSIdentityApp.ps1
```

| Parametro | Obbligatoria o facoltativa | Valore predefinito | DESCRIZIONE |
| --- | --- | --- | --- |
| AdminArmEndpoint | Obbligatorio | Null | Endpoint amministratore Azure Resource Manager. Un esempio è adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Obbligatorio | Null | Endpoint con privilegi. Un esempio è AzS-ERCS01. |
| CloudAdminCredential | Obbligatorio | Null | Credenziale dell'account di dominio per gli amministratori cloud di Azure Stack. Un esempio è Azurestack\CloudAdmin. |
| CertificateFilePath | Obbligatorio | Null | **Percorso completo** al file PFX del certificato dell'applicazione di identità. |
| CertificatePassword | Obbligatorio | Null | Password che consente di proteggere la chiave privata del certificato. |

## <a name="next-steps"></a>Passaggi successivi

[Installare il provider di risorse del servizio App](azure-stack-app-service-deploy.md)
