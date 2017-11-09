---
title: Prima di distribuire il servizio App nello Stack di Azure | Documenti Microsoft
description: Passaggi per completare prima di distribuire il servizio App nello Stack di Azure
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: f2e7b5b96b70333ae4ee92d24c354960008c7f00
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2017
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Prima di iniziare con il servizio App nello Stack di Azure

Servizio App di Azure nello Stack di Azure è un numero di passaggi prerequisiti che devono essere completate prima della distribuzione:

- Scaricare il servizio App di Azure in script di supporto di Azure Stack
- Disponibilità elevata
- Certificati necessari per il servizio App di Azure nello Stack di Azure
- Preparare i File Server
- Preparazione di SQL Server
- Creare un'applicazione Azure Active Directory
- Creare un'applicazione Active Directory Federation Services

## <a name="download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts"></a>Scaricare il servizio App di Azure nel programma di installazione di Azure Stack e script di supporto

1. Scaricare il [servizio App di script di supporto di distribuzione Azure Stack](https://aka.ms/appsvconmashelpers).
2. Scaricare il [servizio App nel programma di installazione di Azure Stack](https://aka.ms/appsvconmasinstaller).
3. Estrarre i file dal file zip di script di supporto. Vengono visualizzati i seguenti file e la struttura di cartelle:
  - Common.ps1
  - AADIdentityApp.ps1 creare
  - ADFSIdentityApp.ps1 creare
  - AppServiceCerts.ps1 creare
  - Get-AzureStackRootCert.ps1
  - Remove-AppService.ps1
  - Moduli
    - GraphAPI.psm1
    
## <a name="high-availability"></a>Disponibilità elevata

Servizio App di Azure nello Stack di Azure non è attualmente in grado di offrire disponibilità elevata perché lo Stack di Azure distribuisce solo i carichi di lavoro in un singolo dominio di errore.

Per preparare il servizio App di Azure nello Stack di Azure per la disponibilità elevata, assicurarsi di distribuire un File Server e SQL Server necessarie in una configurazione a disponibilità elevata. Quando lo Stack di Azure supporta più domini di errore, si forniscono istruzioni su come abilitare il servizio App di Azure nello Stack di Azure in una configurazione a disponibilità elevata.


## <a name="certificates-required-for-azure-app-service-on-azure-stack"></a>Certificati necessari per il servizio App di Azure nello Stack di Azure

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Certificati necessari per il Kit di sviluppo di Azure Stack

Il primo script funziona con l'autorità di certificazione dello Stack di Azure per creare quattro i certificati necessari dal servizio App.

| Nome file | Uso |
| --- | --- |
| _.appservice.Local.azurestack.External.pfx | Certificato SSL predefinito di servizio App |
| API.appservice.Local.azurestack.External.pfx | Certificato SSL di API di servizio App |
| FTP.appservice.Local.azurestack.External.pfx | Certificato SSL server di pubblicazione di servizio App |
| SSO.appservice.Local.azurestack.External.pfx | Certificato di servizio App identità dell'applicazione |

Eseguire lo script nell'host di Azure Stack Development Kit e verificare che sia in esecuzione di PowerShell come azurestack\CloudAdmin.

1. In una sessione di PowerShell in esecuzione come azurestack\CloudAdmin, eseguire lo script di creazione AppServiceCerts.ps1 dalla cartella in cui sono stati estratti gli script di supporto. Lo script crea quattro certificati nella stessa cartella lo script di creazione certificati necessari servizio App.
2. Immettere una password per proteggere i file con estensione pfx e prendere nota di esso. È necessario immetterlo nel servizio App sul programma di installazione dello Stack di Azure.

#### <a name="create-appservicecertsps1-parameters"></a>AppServiceCerts.ps1 creare parametri

| Parametro | Obbligatori/opzionali | Valore predefinito | Descrizione |
| --- | --- | --- | --- |
| PfxPassword | Obbligatorio | Null | Password utilizzata per proteggere la chiave privata del certificato |
| DomainName | Obbligatorio | Local.azurestack.External | Suffisso area e di dominio di Azure Stack |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Certificati necessari per una distribuzione di produzione del servizio App di Azure nello Stack di Azure

Per utilizzare il provider di risorse nell'ambiente di produzione, è necessario fornire i seguenti quattro certificati:

#### <a name="default-domain-certificate"></a>Certificato di dominio predefinito

Il certificato di dominio predefinito viene applicato al ruolo Front-End. Viene utilizzato dalle applicazioni utente per le richieste di dominio con caratteri jolly o predefinito per il servizio App di Azure. Il certificato viene utilizzato anche per operazioni di controllo codice sorgente (KUDU).

Il certificato deve essere nel formato PFX e deve essere un certificato a carattere jolly con due soggetti. In questo modo il dominio predefinito sia l'endpoint di Gestione controllo servizi per le operazioni di controllo codice sorgente sono coperti da un certificato.

| Format | Esempio |
| --- | --- |
| \*.appservice. \<area\>.\< DomainName\>.\< estensione\> | \*. appservice.redmond.azurestack.external |
| \*. scm.appservice. <region>. <DomainName>.<extension> | \*. appservice.scm.redmond.azurestack.external |

#### <a name="api-certificate"></a>Certificato API

Il certificato API si trova il ruolo di gestione e viene utilizzato dal provider di risorse per proteggere le chiamate api. Il certificato per la pubblicazione deve contenere un soggetto corrispondente alla voce DNS API:

| Format | Esempio |
| --- | --- |
| API.appservice. \<area\>.\< DomainName\>.\< estensione\> | API.appservice.Redmond.azurestack.External |

#### <a name="publishing-certificate"></a>Certificato di distribuzione

Il certificato per il ruolo server di pubblicazione consente di proteggere il traffico FTPS per i proprietari delle applicazioni durante il caricamento del contenuto.  Il certificato per la pubblicazione deve contenere un soggetto corrispondente alla voce DNS FTP.

| Format | Esempio |
| --- | --- |
| FTP.appservice. \<area\>.\< DomainName\>.\< estensione\> | API.appservice.Redmond.azurestack.External |

#### <a name="identity-certificate"></a>Certificato di identità

Il certificato per l'applicazione di identità consente:
- integrazione tra la directory AAD o ADFS, Stack di Azure e servizio App per supportare l'integrazione con il Provider di risorse di calcolo
- Single Sign-On scenari per gli strumenti di sviluppo avanzati all'interno di servizio App di Azure nello Stack di Azure.
Il certificato di identità deve contenere un oggetto che corrisponde al formato seguente:

| Format | Esempio |
| --- | --- |
| SSO.appservice. \<area\>.\< DomainName\>.\< estensione\> | SSO.appservice.Redmond.azurestack.External |

#### <a name="extract-the-azure-stack-azure-resource-manager-root-certificate"></a>Estrarre il certificato radice di Stack Azure Azure Resource Manager

In una sessione di PowerShell in esecuzione come azurestack\CloudAdmin, eseguire lo script Get-AzureStackRootCert.ps1 dalla cartella in cui sono stati estratti gli script di supporto. Lo script crea quattro certificati nella stessa cartella lo script di creazione certificati necessari servizio App.

| Parametro Get-AzureStackRootCert.ps1 | Obbligatori/opzionali | Valore predefinito | Descrizione |
| --- | --- | --- | --- |
| PrivelegedEndpoint | Obbligatorio | AzS ERCS01 | Endpoint con privilegi. |
| CloudAdminCredential | Obbligatorio | AzureStack\CloudAdmin | Azure credenziale dell'account di dominio amministratore Cloud Stack |


## <a name="prepare-the-file-server"></a>Preparare il File Server

Servizio App di Azure richiede l'utilizzo di un file server. Per le distribuzioni di produzione, il File Server deve essere configurato per essere a disponibilità elevata e in grado di gestire gli errori.

Per l'utilizzo con solo le distribuzioni di Azure Stack Development Kit, è possibile utilizzare questo esempio il modello di distribuzione Azure Resource Manager per distribuire un singolo nodo configurato file server: https://aka.ms/appsvconmasdkfstemplate.

### <a name="provision-groups-and-accounts-in-active-directory"></a>Eseguire il provisioning di gruppi e account in Active Directory

>[!NOTE]
> Eseguire tutti i comandi seguenti, quando si configura il File Server, in una sessione del prompt dei comandi di amministratore.  **Non utilizzare PowerShell.**

1. Creare i seguenti gruppi di sicurezza globali di Active Directory:
    - FileShareOwners
    - FileShareUsers
2. Creare i seguenti account di Active Directory come account del servizio:
    - Password per FileShareOwner
    - FileShareUser

    Per una protezione ottimale, gli utenti per questi account e di tutti i ruoli Web, deve essere distinti tra loro e disporre di nomi sicuri utente e password.
    Impostare le password con le condizioni seguenti:
     - Abilitare **Nessuna scadenza Password**.
     - Abilitare **cambiamento password non**.
     - Disabilitare **cambiamento obbligatorio password all'accesso successivo**.
3. Aggiungere gli account alle appartenenze, come indicato di seguito:
    - Aggiungere **FileShareOwner** per il **FileShareOwners** gruppo.
    - Aggiungere **FileShareUser** per il **FileShareUsers** gruppo.

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Eseguire il provisioning di gruppi e account in un gruppo di lavoro

Un gruppo di lavoro, eseguire net e i comandi WMIC per eseguire il provisioning di gruppi e account.

1. Eseguire i comandi seguenti per creare gli account FileShareOwner e FileShareUser. Sostituire <password> con valori personalizzati.
``` DOS
net user FileShareOwner <password> /add /expires:never /passwordchg:no
net user FileShareUser <password> /add /expires:never /passwordchg:no
```
2. Impostare le password per gli account non scadano mai eseguendo i comandi WMIC riportati:
``` DOS
WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
```
3. Creare i gruppi locali FileShareUsers e FileShareOwners e aggiungervi gli account nel primo passaggio.
``` DOS
net localgroup FileShareUsers /add
net localgroup FileShareUsers FileShareUser /add
net localgroup FileShareOwners /add
net localgroup FileShareOwners FileShareOwner /add
```

### <a name="provision-the-content-share"></a>Eseguire il provisioning della condivisione contenuto

La condivisione del contenuto contiene contenuto del sito web tenant. La procedura per eseguire il provisioning della condivisione contenuto in un singolo file server è lo stesso per gli ambienti Active Directory sia del gruppo di lavoro, ma è diversa per un cluster di Failover in Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-ad-or-workgroup"></a>Eseguire il provisioning della condivisione contenuto in un singolo file server (AD o gruppo di lavoro)

In un singolo file server, eseguire i comandi seguenti a un prompt dei comandi con privilegi elevati. Sostituire il valore per < C:\WebSites > con i percorsi corrispondenti nell'ambiente in uso.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=<C:\WebSites>
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="to-enable-winrm-add-the-fileshareowners-group-to-the-local-administrators-group"></a>Per abilitare WinRM, aggiungere il gruppo fileshareowners al gruppo Administrators locale

Affinché la gestione remota Windows per il corretto funzionamento, è necessario aggiungere il gruppo fileshareowners al gruppo Administrators locale.

#### <a name="active-directory"></a>Active Directory

Eseguire i comandi seguenti a un prompt dei comandi con privilegi elevati nel File Server o in ogni nodo del Cluster di Failover di File Server. Sostituire il valore per <DOMAIN> con il nome di dominio che si desidera utilizzare.

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>Gruppo di lavoro

Eseguire il comando seguente a un prompt dei comandi con privilegi elevati nel File Server.

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>Configurare il controllo di accesso alle condivisioni

Eseguire i comandi seguenti a un prompt dei comandi con privilegi elevati nel File Server o sul nodo del Cluster di Failover di File Server, che è il proprietario corrente della risorsa cluster. Sostituire i valori in corsivo con valori specifici per l'ambiente.

#### <a name="active-directory"></a>Active Directory
```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=<C:\WebSites>
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Gruppo di lavoro
```DOS
set WEBSITES_FOLDER=<C:\WebSites>
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server"></a>Preparazione di SQL Server

Per il servizio App di Azure in hosting dello Stack di Azure e database di controllo, è necessario preparare un Server SQL per contenere i database di servizio App di Azure.

Per l'utilizzo con il Kit di sviluppo dello Stack di Azure, è possibile utilizzare SQL Express 2014 SP2 o versione successiva.

Per la produzione e una disponibilità elevata, è necessario utilizzare una versione completa di SQL 2014 SP2 o versioni successive, abilitare l'autenticazione modalità mista e distribuire un [configurazione a disponibilità elevata](https://docs.microsoft.com/en-us/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

Il servizio App di Azure nel Server SQL di Azure Stack deve essere accessibile da tutti i ruoli del servizio App. SQL Server può essere distribuito nella sottoscrizione di Provider predefinito nello Stack di Azure. È possibile impostare o utilizzare dell'infrastruttura esistente all'interno dell'organizzazione (purché vi sia connettività allo Stack di Azure).

Per uno dei ruoli di SQL Server, è possibile utilizzare un'istanza predefinita o un'istanza denominata. Tuttavia, se si utilizza un'istanza denominata, assicurarsi che si avvia manualmente il servizio SQL Browser e aprire la porta 1434.

## <a name="create-an-azure-active-directory-application"></a>Creare un'applicazione Azure Active Directory

Configurare un'entità servizio di Azure AD per supportare le operazioni seguenti:
- Scalabilità della macchina virtuale impostare integrazione per i piani di lavoro.
- SSO per gli strumenti di sviluppo avanzate e portale di Azure funzioni.

Questa procedura si applica ad Azure AD protetto solo negli ambienti di Stack di Azure.

Gli amministratori devono configurare SSO per:
- Abilitare gli strumenti di sviluppo avanzati all'interno di servizio App (Kudu).
- Abilitare l'utilizzo dell'esperienza del portale di Azure funzioni.

A tale scopo, seguire questa procedura:

1. Aprire un'istanza di PowerShell come azurestack\cloudadmin.
2. Passare al percorso di script scaricati ed estratti nel [prerequisito](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Installare PowerShell Azure Stack](azure-stack-powershell-install.md).
4. Eseguire il **crea AADIdentityApp.ps1** script. Quando viene richiesto per l'ID del tenant di Azure AD, immettere l'ID tenant di Azure AD in uso per la distribuzione di Azure Stack, ad esempio, myazurestack.onmicrosoft.com.
5. Nel **credenziali** finestra, immettere la password e account di amministratore del servizio di Azure AD. Fare clic su **OK**.
6. Immettere il percorso del file di certificato e la password del certificato per il [certificato creato in precedenza](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Il certificato creato per questo passaggio per impostazione predefinita è sso.appservice.local.azurestack.external.pfx.
7. Lo script crea una nuova applicazione nel tenant di Azure AD. Prendere nota dell'ID applicazione che viene restituito nell'output di PowerShell. È necessario queste informazioni durante l'installazione.
8. Aprire una nuova finestra del browser e accedere al portale di Azure (portal.azure.com) come il **amministratore del servizio Azure Active Directory**.
9. Aprire il provider di risorse di Azure AD.
10. Fare clic su **registrazioni di App**.
11. Cercare il **ID applicazione** restituite come parte del passaggio 7. Un'applicazione di servizio App è elencata.
12. Fare clic su **applicazione** nell'elenco
13. Fare clic su **delle autorizzazioni necessarie** > **concedere le autorizzazioni** > **Sì**.

| Parametro AADIdentityApp.ps1 creare | Obbligatori/opzionali | Valore predefinito | Descrizione |
| --- | --- | --- | --- |
| DirectoryTenantName | Obbligatorio | Null | ID tenant di Azure AD. Fornire il GUID o una stringa, ad esempio, myazureaaddirectory.onmicrosoft.com |
| AdminArmEndpoint | Obbligatorio | Null | L'Endpoint di gestione risorse di Azure di amministrazione, ad esempio adminmanagement.local.azurestack.external |
| TenantARMEndpoint | Obbligatorio | Null | Tenant di Endpoint di gestione risorse di Azure, ad esempio: management.local.azurestack.external |
| AzureStackAdminCredential | Obbligatorio | Null | Credenziali di amministratore del servizio di Azure AD |
| CertificateFilePath | Obbligatorio | Null | Percorso per il file di certificato di identità applicazione generato in precedenza. |
| CertificatePassword | Obbligatorio | Null | Password utilizzata per proteggere la chiave privata del certificato. |

## <a name="create-an-active-directory-federation-services-application"></a>Creare un'applicazione Active Directory Federation Services

Per gli ambienti Azure Stack protetti da AD FS, è necessario configurare un'entità servizio ADFS per supportare le operazioni seguenti:
- Scalabilità della macchina virtuale impostare integrazione per i piani di lavoro.
- SSO per gli strumenti di sviluppo avanzate e portale di Azure funzioni.

Gli amministratori devono configurare SSO per:
- Configurare un'entità servizio per l'integrazione di set di scalabilità macchina virtuale in piani di lavoro.
- Abilitare gli strumenti di sviluppo avanzati all'interno di servizio App (Kudu).
- Abilitare l'utilizzo dell'esperienza del portale di Azure funzioni.

A tale scopo, seguire questa procedura:

1. Aprire un'istanza di PowerShell come azurestack\azurestackadmin.
2. Passare al percorso di script scaricati ed estratti nel [prerequisito](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Installare PowerShell Azure Stack](azure-stack-powershell-install.md).
4.  Eseguire il **crea ADFSIdentityApp.ps1** script.
5.  Nel **credenziali** finestra, immettere la password e account di amministratore di AD FS cloud. Fare clic su **OK**.
6.  Fornire il percorso del file di certificato e la password del certificato per il [certificato creato in precedenza](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Il certificato creato per questo passaggio per impostazione predefinita è sso.appservice.local.azurestack.external.pfx.

| Parametro ADFSIdentityApp.ps1 creare | Obbligatori/opzionali | Valore predefinito | Descrizione |
| --- | --- | --- | --- |
| AdminArmEndpoint | Obbligatorio | Null | L'endpoint di gestione risorse di Azure di amministrazione. Ad esempio, adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Obbligatorio | Null | Endpoint con privilegi. Ad esempio, AzS ERCS01. |
| CloudAdminCredential | Obbligatorio | Null | Azure Stack cloudadmin dominio credenziale dell'account. Ad esempio, Azurestack\CloudAdmin. |
| CertificateFilePath | Obbligatorio | Null | Percorso di file PFX del certificato dell'applicazione di identità. |
| CertificatePassword | Obbligatorio | Null | Password utilizzata per proteggere la chiave privata del certificato. |


## <a name="next-steps"></a>Passaggi successivi

[Installare il provider di risorse del servizio App](azure-stack-app-service-deploy.md).
