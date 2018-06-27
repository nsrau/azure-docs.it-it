---
title: Prima di distribuire il servizio App nello Stack di Azure | Documenti Microsoft
description: Passaggi per completare prima di distribuire il servizio App di Azure stack
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
ms.date: 06/04/2018
ms.author: anwestg
ms.openlocfilehash: ae21a7cc5c38fefd40a2676e15308b027c6f95d5
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796734"
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Prima di iniziare a con il servizio App nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

> [!IMPORTANT]
> Applicare l'aggiornamento 1804 al sistema Azure Stack integrato o distribuire il kit di sviluppo dello Stack di Azure più recente prima di distribuire Azure App Service 1.2.
>
>

Prima di distribuire Azure App Service nello Stack di Azure, è necessario completare i prerequisiti descritti in questo articolo.

## <a name="download-the-installer-and-helper-scripts"></a>Scaricare gli script di installazione e supporto

1. Scaricare il [servizio App di script di supporto di distribuzione Azure Stack](https://aka.ms/appsvconmashelpers).
2. Scaricare il [servizio di App nel programma di installazione di Azure Stack](https://aka.ms/appsvconmasinstaller).
3. Estrarre i file dal file di supporto degli script con estensione zip. Vengono visualizzati la struttura di cartelle e i file seguenti:
   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - Moduli
     - GraphAPI.psm1

## <a name="high-availability"></a>Disponibilità elevata

A causa della versione 1802 di Stack di Azure, che è stato aggiunto il supporto per domini di errore, nuove distribuzioni di Azure App Service nello Stack di Azure verranno distribuite nei domini di errore e forniscono tolleranza di errore.  Per le distribuzioni esistenti del servizio App di Azure nello Stack di Azure, che sono state distribuite prima del rilascio dell'aggiornamento 1802, vedere la [documentazione](azure-stack-app-service-fault-domain-update.md) sul ribilanciare la distribuzione.

Servizio App di Azure nello Stack di Azure per la disponibilità elevata, inoltre distribuire necessari al file server e dell'istanza di SQL Server in una configurazione a disponibilità elevata.

## <a name="get-certificates"></a>Ottenere i certificati

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Azure certificato di gestione risorse per lo Stack di Azure

In una sessione di PowerShell in esecuzione come azurestack\CloudAdmin in un computer, che può raggiungere l'endpoint con privilegi nel sistema integrata dello Stack di Azure o Host Kit di sviluppo dello Stack di Azure, eseguire lo script Get-AzureStackRootCert.ps1 dalla cartella in cui è stato estratto gli script di supporto. Lo script crea un certificato radice nella stessa cartella in cui lo script di servizio App è necessario per la creazione di certificati.

```PowerShell
    Get-AzureStackRootCert.ps1
```

| Parametro | Obbligatoria o facoltativa | Valore predefinito | DESCRIZIONE |
| --- | --- | --- | --- |
| PrivilegedEndpoint | Obbligatoria | AzS-ERCS01 | Endpoint con privilegi |
| CloudAdminCredential | Obbligatoria | AzureStack\CloudAdmin | Credenziale dell'account di dominio per gli amministratori cloud di Azure Stack |

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Certificati necessari per il Kit di sviluppo dello Stack di Azure

Il primo script funziona con l'autorità di certificazione dello Stack di Azure per creare quattro certificati servizio App:

| Nome file | Uso |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Certificato SSL predefinito del servizio app |
| api.appservice.local.azurestack.external.pfx | Certificato SSL di API del servizio App |
| ftp.appservice.local.azurestack.external.pfx | Certificato SSL del servizio App publisher |
| sso.appservice.local.azurestack.external.pfx | Certificato di servizio App identità dell'applicazione |

Eseguire lo script nell'host di Azure Stack Development Kit e verificare che sia in esecuzione PowerShell come azurestack\CloudAdmin:

1. In una sessione di PowerShell in esecuzione come azurestack\AzureStackAdmin, eseguire lo script crea AppServiceCerts.ps1 dalla cartella in cui sono stati estratti gli script di supporto. Lo script crea quattro certificati nella stessa cartella in cui lo script di servizio App è necessario per la creazione di certificati.
2. Immettere una password per proteggere i file con estensione pfx e prendere nota di esso. È necessario immetterlo nel servizio App sul programma di installazione dello Stack di Azure.

#### <a name="create-appservicecertsps1-parameters"></a>AppServiceCerts.ps1 creare parametri

```PowerShell
    Create-AppServiceCerts.ps1
```

| Parametro | Obbligatoria o facoltativa | Valore predefinito | DESCRIZIONE |
| --- | --- | --- | --- |
| pfxPassword | Obbligatoria | Null | Password che consente di proteggere la chiave privata del certificato |
| DomainName | Obbligatoria | local.azurestack.external | Suffisso area e di dominio di Azure Stack |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Certificati necessari per una distribuzione di produzione del servizio App di Azure nello Stack di Azure

Per utilizzare il provider di risorse nell'ambiente di produzione, è necessario fornire i certificati a quattro seguenti:

#### <a name="default-domain-certificate"></a>Certificato di dominio predefinito

Il certificato di dominio predefinito viene applicato il ruolo Front-End. Le applicazioni utente per le richieste di dominio con caratteri jolly o predefinito al servizio App di Azure usare questo certificato. Il certificato viene utilizzato inoltre per le operazioni di controllo di origine (Kudu).

Il certificato deve essere nel formato PFX e deve essere un certificato a carattere jolly con tre soggetti. Questo requisito consente a un certificato coprire il dominio predefinito sia l'endpoint di Gestione controllo servizi per le operazioni di controllo di origine.

| Format | Esempio |
| --- | --- |
| \*.appservice. \<regione\>.\< DomainName\>.\< estensione\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.scm.appservice.redmond.azurestack.external |
| \*.sso.appservice.<region>.<DomainName>.<extension> | \*.sso.appservice.redmond.azurestack.external |

#### <a name="api-certificate"></a>Certificato API

Il certificato API viene applicato al ruolo di gestione. Il provider di risorse utilizza per le chiamate API protetta. Il certificato per la pubblicazione deve contenere un soggetto corrispondente alla voce DNS di API.

| Format | Esempio |
| --- | --- |
| API.appservice. \<regione\>.\< DomainName\>.\< estensione\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Certificato di distribuzione

Il certificato per il ruolo del server di pubblicazione consente di proteggere il traffico FTPS per i proprietari delle applicazioni durante il caricamento del contenuto. Il certificato per la pubblicazione deve contenere un soggetto corrispondente alla voce DNS FTP.

| Format | Esempio |
| --- | --- |
| FTP.appservice. \<regione\>.\< DomainName\>.\< estensione\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Certificato di identità

Il certificato per l'applicazione di identità consente:

- Integrazione tra Azure Active Directory (Azure AD) o Active Directory Federation Services (ADFS) directory, Stack di Azure e servizio App per supportare l'integrazione con il provider di risorse di calcolo.
- Scenari Single sign-on per strumenti di sviluppo avanzate in Azure App Service nello Stack di Azure.

Il certificato per l'identità deve contenere un soggetto corrispondente al formato seguente:

| Format | Esempio |
| --- | --- |
| SSO.appservice. \<regione\>.\< DomainName\>.\< estensione\> | sso.appservice.redmond.azurestack.external |

## <a name="virtual-network"></a>Rete virtuale

Servizio App di Azure nello Stack di Azure consente di distribuire il provider di risorse in uno una rete virtuale esistente o creato uno come parte della distribuzione del servizio App.  L'utilizzo di una rete virtuale esistente consente l'uso di IP interni per la connessione al file server e SQL server necessarie dal servizio App di Azure nello Stack di Azure.  La rete virtuale deve essere configurata con l'intervallo di indirizzi e subnet seguenti prima di installare Azure App Service nello Stack di Azure:

Rete virtuale - /16

Subnet

- ControllersSubnet /24
- ManagementServersSubnet /24
- FrontEndsSubnet /24
- PublishersSubnet /24
- WorkersSubnet /21

## <a name="prepare-the-file-server"></a>Preparare il file server

Servizio App di Azure richiede l'utilizzo di un file server. Per le distribuzioni di produzione, il file server deve essere configurato per essere in grado di gestire gli errori e a disponibilità elevata.

Per distribuzioni solo Azure Stack Development Kit, è possibile usare il [modello di esempio di distribuzione Azure Resource Manager](https://aka.ms/appsvconmasdkfstemplate) per distribuire un server di configurazione del file a nodo singolo. Il server a nodo singolo file sarà un gruppo di lavoro.

>[!IMPORTANT]
> Se si sceglie di distribuire il servizio App in una rete virtuale esistente nel File Server deve essere distribuito in una Subnet separata dal servizio App.
>

### <a name="provision-groups-and-accounts-in-active-directory"></a>Eseguire il provisioning di gruppi e account in Active Directory

1. Creare i seguenti gruppi di sicurezza globali di Active Directory:
   - FileShareOwners
   - FileShareUsers
2. Creare i seguenti account di Active Directory come account del servizio:
   - FileShareOwner
   - FileShareUser

   Come procedura di sicurezza consigliata, gli utenti per questi account e di tutti i ruoli web, deve essere distinti tra loro e disporre di sicuri nomi utente e password. Impostare le password con le condizioni seguenti:
   - Abilitare **Nessuna scadenza Password**.
   - Abilitare **cambiamento password non**.
   - Disabilitare **cambiamento obbligatorio password all'accesso successivo**.
3. Aggiungere gli account alle appartenenze come indicato di seguito:
   - Aggiungere **FileShareOwner** per il **FileShareOwners** gruppo.
   - Aggiungere **FileShareUser** per il **FileShareUsers** gruppo.

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Eseguire il provisioning di gruppi e account in un gruppo di lavoro

>[!NOTE]
> Quando si configurano un file server, eseguire tutti i comandi seguenti in una finestra del prompt dei comandi amministrativa. *Non utilizzare PowerShell.*

Quando si usa il modello di gestione risorse di Azure, gli utenti sono già stati creati.

1. Eseguire i comandi seguenti per creare gli account FileShareOwner e FileShareUser. Sostituire `<password>` con valori personalizzati.
    ``` DOS
    net user FileShareOwner <password> /add /expires:never /passwordchg:no
    net user FileShareUser <password> /add /expires:never /passwordchg:no
    ```
2. Impostare le password per gli account a non scadano mai eseguendo i comandi WMIC riportati:
    ``` DOS
    WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
    WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
    ```
3. Creare i gruppi locali FileShareUsers e FileShareOwners e aggiungervi gli account nel primo passaggio:
    ``` DOS
    net localgroup FileShareUsers /add
    net localgroup FileShareUsers FileShareUser /add
    net localgroup FileShareOwners /add
    net localgroup FileShareOwners FileShareOwner /add
    ```

### <a name="provision-the-content-share"></a>Eseguire il provisioning della condivisione contenuto

La condivisione del contenuto contiene contenuto del sito Web tenant. La procedura per effettuare il provisioning della condivisione contenuto in un singolo file server è lo stesso per ambienti di Active Directory e di gruppo di lavoro. Ma è diversa per un cluster di failover in Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Eseguire il provisioning della condivisione contenuto in un singolo file server (Active Directory o gruppo di lavoro)

In un singolo file server, eseguire i comandi seguenti a un prompt dei comandi con privilegi elevati. Sostituire il valore per `C:\WebSites` con i percorsi corrispondenti nell'ambiente in uso.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="add-the-fileshareowners-group-to-the-local-administrators-group"></a>Aggiungere il gruppo FileShareOwners al gruppo Administrators locale

Per la gestione remota Windows per il corretto funzionamento, è necessario aggiungere il gruppo fileshareowners al gruppo Administrators locale.

#### <a name="active-directory"></a>Active Directory

Eseguire i comandi seguenti a un prompt dei comandi con privilegi elevati nel file server o in ogni file server che funge da un nodo del cluster di failover. Sostituire il valore per `<DOMAIN>` con il nome di dominio che si desidera utilizzare.

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>Gruppo di lavoro

Eseguire il comando seguente a un prompt dei comandi con privilegi elevati nel file server:

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>Configurare il controllo di accesso alle condivisioni

Eseguire i comandi seguenti a un prompt dei comandi con privilegi elevati nel file server o nel nodo del cluster di failover, ovvero il proprietario corrente della risorsa cluster. Sostituire i valori in corsivo con valori che sono specifici dell'ambiente.

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

Per il servizio App di Azure in hosting dello Stack di Azure e nei database di controllo, è necessario preparare un'istanza di SQL Server per contenere i database di servizio App.

Per le distribuzioni del Kit di sviluppo dello Stack di Azure, è possibile utilizzare SQL Server Express 2014 SP2 o versione successiva.

Per scopi di disponibilità elevata e di produzione, è necessario utilizzare una versione completa di SQL Server 2014 SP2 o versioni successive, abilitare l'autenticazione modalità mista e distribuire un [configurazione a disponibilità elevata](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

L'istanza di SQL Server per il servizio App di Azure nello Stack di Azure deve essere accessibile da tutti i ruoli del servizio App. È possibile distribuire SQL Server all'interno della sottoscrizione di Provider predefinito nello Stack di Azure. È possibile impostare o utilizzare dell'infrastruttura esistente all'interno dell'organizzazione (purché non vi è connettività allo Stack di Azure). Se si usa un'immagine di Azure Marketplace, è necessario configurare il firewall in modo appropriato.

>[!NOTE]
> Un numero di immagini di macchina virtuale SQL IaaS è disponibile tramite la funzionalità di gestione di Marketplace. Assicurarsi sempre di scaricare la versione più recente dell'estensione SQL IaaS prima di distribuire una macchina virtuale usando un elemento del Marketplace. Le immagini SQL sono le stesse macchine virtuali di SQL disponibili in Azure. Per le macchine virtuali SQL create da queste immagini, l'estensione di IaaS e corrispondente miglioramenti del portale di fornire funzionalità quali l'applicazione automatica di patch e funzionalità di backup.
>
Per uno dei ruoli di SQL Server, è possibile utilizzare un'istanza predefinita o un'istanza denominata. Se si usa un'istanza denominata, assicurarsi di avviare il servizio SQL Server Browser e aprire la porta 1434 manualmente.

>[!IMPORTANT]
> Se si sceglie di distribuire il servizio App in una rete virtuale esistente di SQL Server deve essere distribuito in una Subnet separata dal servizio App e il File Server.
>

## <a name="create-an-azure-active-directory-application"></a>Creare un'applicazione Azure Active Directory

Configurare un'entità servizio di Azure AD per supportare le operazioni seguenti:

- Scalabilità della macchina virtuale impostare integrazione per i piani di lavoro.
- SSO per gli strumenti di sviluppo del portale e avanzate di funzioni di Azure.

Questi passaggi si applicano solo negli ambienti Azure protetto AD Azure Stack.

Gli amministratori devono configurare SSO per:

- Abilitare gli strumenti di sviluppo avanzati all'interno di servizio App (Kudu).
- Abilitare l'utilizzo dell'esperienza del portale Azure funzioni.

A tale scopo, seguire questa procedura:

1. Aprire un'istanza di PowerShell come azurestack\AzureStackAdmin.
2. Passare al percorso di script che è stato scaricato ed estratto il [prerequisito](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md).
4. Eseguire la **AADIdentityApp.ps1 crea** script. Quando richiesto, immettere l'ID tenant di Azure AD che sta usando per la distribuzione dello Stack di Azure. Ad esempio, immettere **myazurestack.onmicrosoft.com**.
5. Nel **credenziale** finestra, immettere la password e account di amministratore del servizio di Azure AD. Selezionare **OK**.
6. Immettere il percorso del file di certificato e la password del certificato per il [certificato creato in precedenza](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Il certificato creato per questo passaggio per impostazione predefinita è **sso.appservice.local.azurestack.external.pfx**.
7. Lo script crea una nuova applicazione nell'istanza del tenant Azure AD. Prendere nota dell'ID applicazione che viene restituito nell'output di PowerShell. È necessario queste informazioni durante l'installazione.
8. Aprire una nuova finestra del browser e accedere ai [portale di Azure](https://portal.azure.com) come amministratore del servizio Azure Active Directory.
9. Aprire il provider di risorse di Azure AD.
10. Selezionare **registrazioni di App**.
11. Cercare l'ID dell'applicazione restituito come parte del passaggio 7. Un'applicazione di servizio App è elencata.
12. Selezionare **applicazione** nell'elenco.
13. Fare clic su **Impostazioni**.
14. Selezionare **le autorizzazioni necessarie** > **concedere le autorizzazioni** > **Sì**.

```PowerShell
    Create-AADIdentityApp.ps1
```

| Parametro | Obbligatoria o facoltativa | Valore predefinito | DESCRIZIONE |
| --- | --- | --- | --- |
| DirectoryTenantName | Obbligatoria | Null | ID tenant di Azure AD. Specificare il GUID o una stringa. Un esempio è myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Obbligatoria | Null | Endpoint amministratore Azure Resource Manager. Un esempio è adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Obbligatoria | Null | Endpoint di Azure Resource Manager tenant. Un esempio è management.local.azurestack.external. |
| AzureStackAdminCredential | Obbligatoria | Null | Credenziali di amministratore del servizio Azure Active Directory. |
| CertificateFilePath | Obbligatoria | Null | **Percorso completo** nel file di certificato dell'applicazione di identità generato in precedenza. |
| CertificatePassword | Obbligatoria | Null | Password che consente di proteggere la chiave privata del certificato. |

## <a name="create-an-active-directory-federation-services-application"></a>Creare un'applicazione Active Directory Federation Services

Per gli ambienti Azure Stack protetti da AD FS, è necessario configurare un'entità servizio di ADFS per supportare le operazioni seguenti:

- Scalabilità della macchina virtuale impostare integrazione per i piani di lavoro.
- SSO per gli strumenti di sviluppo del portale e avanzate di funzioni di Azure.

Gli amministratori devono configurare SSO per:

- Configurare un'entità servizio per l'integrazione con set di scalabilità macchina virtuale in piani di lavoro.
- Abilitare gli strumenti di sviluppo avanzati all'interno di servizio App (Kudu).
- Abilitare l'utilizzo dell'esperienza del portale Azure funzioni.

A tale scopo, seguire questa procedura:

1. Aprire un'istanza di PowerShell come azurestack\AzureStackAdmin.
2. Passare al percorso di script che è stato scaricato ed estratto il [prerequisito](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md).
4. Eseguire la **ADFSIdentityApp.ps1 crea** script.
5. Nel **credenziale** finestra, immettere la password e un account amministratore cloud AD FS. Selezionare **OK**.
6. Fornire il percorso del file di certificato e la password del certificato per il [certificato creato in precedenza](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Il certificato creato per questo passaggio per impostazione predefinita è **sso.appservice.local.azurestack.external.pfx**.

```PowerShell
    Create-ADFSIdentityApp.ps1
```

| Parametro | Obbligatoria o facoltativa | Valore predefinito | DESCRIZIONE |
| --- | --- | --- | --- |
| AdminArmEndpoint | Obbligatoria | Null | Endpoint amministratore Azure Resource Manager. Un esempio è adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Obbligatoria | Null | Endpoint con privilegi. Un esempio è AzS ERCS01. |
| CloudAdminCredential | Obbligatoria | Null | Credenziale dell'account di dominio per gli amministratori cloud di Azure Stack. Un esempio è Azurestack\CloudAdmin. |
| CertificateFilePath | Obbligatoria | Null | **Percorso completo** al file PFX del certificato dell'applicazione di identità. |
| CertificatePassword | Obbligatoria | Null | Password che consente di proteggere la chiave privata del certificato. |

## <a name="next-steps"></a>Passaggi successivi

[Installare il provider di risorse di servizio App](azure-stack-app-service-deploy.md)
