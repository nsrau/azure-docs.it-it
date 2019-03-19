---
title: Distribuire Azure Stack - PowerShell | Microsoft Docs
description: In questo articolo, si installa il ASDK dalla riga di comando tramite PowerShell.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: e8d3653049b0160fac155ebc49329c17cb27224f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58014380"
---
# <a name="deploy-the-asdk-from-the-command-line"></a>Distribuire il ASDK dalla riga di comando
Il ASDK è un ambiente di test e sviluppo che è possibile distribuire per valutare e illustrano i servizi e funzionalità di Azure Stack. Per renderlo attivo e in esecuzione, è necessario preparare l'ambiente hardware ed eseguire alcuni script (questa operazione richiederà diverse ore). Successivamente, è possibile accedere ai portali di amministratore e utente per iniziare a usare Azure Stack.

## <a name="prerequisites"></a>Prerequisiti 
Preparare il computer host kit di sviluppo. Pianificare l'hardware, software e rete. Computer che ospita il kit di sviluppo (host development kit) deve soddisfare i requisiti di rete, hardware e software. È anche necessario scegliere tra l'uso di Azure Active Directory (Azure AD) o Active Directory Federation Services (ADFS). Assicurarsi di rispettare questi prerequisiti prima di avviare la distribuzione in modo che il processo di installazione venga eseguito senza problemi. 

Prima di distribuire il ASDK, assicurarsi che l'hardware del computer di sviluppo pianificato kit host, sistema operativo, account e le configurazioni di rete soddisfino i requisiti minimi per l'installazione di ASDK.

**[Esaminare le considerazioni di pianificazione della distribuzione di ASDK](asdk-deploy-considerations.md)**

> [!TIP]
> È possibile usare la [dello strumento di controllare i requisiti di distribuzione di Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) dopo aver installato il sistema operativo per verificare che l'hardware soddisfi tutti i requisiti.

## <a name="download-and-extract-the-deployment-package"></a>Scaricare ed estrarre il pacchetto di distribuzione
Dopo aver verificato che il computer host kit di sviluppo soddisfi i requisiti di base per l'installazione di ASDK, il passaggio successivo è per scaricare ed estrarre il pacchetto di distribuzione ASDK. Il pacchetto di distribuzione include il file Cloudbuilder.vhdx, ovvero un disco rigido virtuale che include un sistema operativo avviabile e i file di installazione di Azure Stack.

È possibile scaricare il pacchetto di distribuzione per l'host del kit di sviluppo o in un altro computer. I file estratti distribuzione occupano 60 GB di spazio libero su disco, in modo usando un altro computer consente di ridurre i requisiti hardware per l'host del kit di sviluppo.

**[Scaricare ed estrarre Azure Stack Development Kit (ASDK)](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Preparare il computer host kit di sviluppo
Prima di installare il ASDK nel computer host, è necessario preparare l'ambiente e il sistema è configurato per l'avvio dal disco rigido virtuale. Dopo questo passaggio, verrà avviato l'host del kit di sviluppo Cloudbuilder.vhdx (un disco rigido virtuale che include un sistema operativo avviabile e i file di installazione di Azure Stack).

Usare PowerShell per configurare il computer host ASDK per l'avvio da CloudBuilder.vhdx. Questi comandi configurano il computer host ASDK per l'avvio dallo scaricato ed estratto Azure Stack virtuale disco rigido (CloudBuilder.vhdx). Dopo aver completato questi passaggi, riavviare il computer host ASDK.

Per configurare il computer host per l'avvio da CloudBuilder.vhdx ASDK:

  1. Avviare un prompt dei comandi come amministratore.
  2. Eseguire `bcdedit /copy {current} /d "Azure Stack"`
  3. Copia (CTRL + C), il valore CLSID restituito, tra cui richiesti {}' s. Questo valore è detta {CLSID} e dovrà essere incollato in (CTRL + V oppure pulsante destro del mouse) nei passaggi successivi.
  4. Eseguire `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 
  5. Eseguire `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
  6. Eseguire `bcdedit /set {CLSID} detecthal on` 
  7. Eseguire `bcdedit /default {CLSID}`
  8. Per verificare le impostazioni di avvio, eseguire `bcdedit`. 
  9. Verificare che il CloudBuilder.vhdx file è stato spostato nella radice dell'unità C:\ (C:\CloudBuilder.vhdx) e riavviare il computer host kit di sviluppo. Quando viene riavviato il computer host ASDK, deve avvio dall'unità disco rigido CloudBuilder.vhdx macchina virtuale per iniziare la distribuzione ASDK. 

> [!IMPORTANT]
> Assicurarsi di avere accesso KVM al computer host kit di sviluppo o fisica diretta prima del riavvio. Al primo avvio della macchina virtuale, viene richiesto di completare l'installazione di Windows Server. Fornire le stesse credenziali di amministratore usato per accedere al computer host kit di sviluppo. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Preparare l'host del kit di sviluppo usando PowerShell 
Dopo il kit di sviluppo computer host si avvia correttamente nell'immagine CloudBuilder.vhdx, accedere con le stesse credenziali di amministratore locale usato per accedere al computer host kit di sviluppo (e specificato come parte della finalizzazione del Server di Windows Programma di installazione quando il computer host avviato dal disco rigido virtuale). 

> [!NOTE]
> Facoltativamente, è anche possibile configurare [le impostazioni di telemetria di Azure Stack](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *prima di* installando il ASDK.

Aprire una console di PowerShell con privilegi elevata ed eseguire i comandi in questa sezione per distribuire il ASDK nell'host di kit di sviluppo.

> [!IMPORTANT] 
> Installazione ASDK supporta esattamente una scheda di interfaccia di rete (NIC) per la rete. Se si dispone di più schede di rete, assicurarsi che solo uno è attivato (e tutti gli altri sono disabilitati) prima di eseguire lo script di distribuzione.

È possibile distribuire Azure Stack con Azure AD o Windows Server AD FS come provider di identità. Azure Stack, i provider di risorse e altre applicazioni funzionano allo stesso modo con entrambe.

> [!TIP]
> Se si non specificano i parametri di installazione (vedere esempi riportati di seguito e i parametri facoltativi InstallAzureStackPOC.ps1), richiesto per i parametri obbligatori.

### <a name="deploy-azure-stack-using-azure-ad"></a>Distribuire Azure Stack tramite Azure AD 
Per distribuire Azure Stack **usando Azure AD come provider di identità**, è necessario disporre della connettività internet, direttamente o tramite un proxy trasparente. 

Eseguire i comandi di PowerShell seguenti per distribuire il kit di sviluppo tramite Azure AD:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

Alcuni minuti in installazione ASDK che verrà richiesto per le credenziali di Azure AD. È necessario fornire le credenziali di amministratore globale per il tenant di Azure AD. 

Dopo la distribuzione, l'autorizzazione di amministratore globale di Azure Active Directory non è necessaria. Tuttavia, alcune operazioni potrebbero richiedere le credenziali di amministratore globale. Ad esempio, uno script di programma di installazione di provider di risorse o una nuova funzionalità che richiedono un'autorizzazione da concedere. È possibile temporaneamente riattivare le autorizzazioni di amministratore globale dell'account o utilizzare un account di amministratore globale separata che è un proprietario del *predefinita sottoscrizione provider*.

### <a name="deploy-azure-stack-using-ad-fs"></a>Distribuire Azure Stack tramite AD FS 
Per distribuire il kit di sviluppo **tramite AD FS come provider di identità**, eseguire i comandi PowerShell seguenti (è sufficiente aggiungere il parametro - UseADFS): 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

Nelle distribuzioni di AD FS, l'indicatore predefinita del servizio di Directory viene usato come provider di identità. L'account predefinito per l'accesso è azurestackadmin@azurestack.local, e la password verrà impostata su ciò che è fornito come parte dei comandi di installazione di PowerShell.

Il processo di distribuzione può richiedere alcune ore, durante i quali il sistema si riavvia automaticamente una volta. Quando la distribuzione ha esito positivo, verrà visualizza la console di PowerShell: **COMPLETAMENTO: Azione 'Deployment'**. Se la distribuzione non riesce, è possibile provare a eseguire di nuovo lo script usando eseguire nuovamente il parametro-. In alternativa, è possibile [ridistribuire ASDK](asdk-redeploy.md) da zero.

> [!IMPORTANT]
> Se si desidera monitorare l'avanzamento della distribuzione dopo il riavvio dell'host ASDK, è necessario accedere come AzureStack\AzureStackAdmin. Se si accede come amministratore locale dopo che il computer host viene riavviato (e aggiunti al dominio con azurestack. Local), non verrà visualizzata l'avanzamento della distribuzione. Non rieseguire la distribuzione, invece accedere come azurestack per convalidare che venga eseguito.


#### <a name="azure-ad-deployment-script-examples"></a>Esempi di script di distribuzione AD Azure
È possibile creare script l'intera distribuzione di Azure AD. Ecco alcuni esempi con commenti che includono alcuni parametri facoltativi.

Se l'identità di Azure AD è associata solo con **uno** directory di Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Se l'identità di Azure AD è associata **maggiore di uno** directory di Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Nel caso dell'ambiente non DHCP abilitato, è necessario includere i seguenti parametri aggiuntivi per una delle opzioni sopra (utilizzo di esempio fornito): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>Parametri facoltativi ASDK InstallAzureStackPOC.ps1

|Parametro|Obbligatorio/Facoltativo|DESCRIZIONE|
|-----|-----|-----|
|AdminPassword|Obbligatorio|Imposta l'account amministratore locale e tutti gli altri account utente in tutte le macchine virtuali create come parte della distribuzione kit di sviluppo. Questa password deve corrispondere alla password di amministratore locale corrente nell'host.|
|InfraAzureDirectoryTenantName|Obbligatorio|Imposta la directory del tenant. Usare questo parametro per specificare una directory specifica in cui l'account AAD ha le autorizzazioni per gestire più directory. Il nome di un Tenant di Directory di AAD nel formato completo. onmicrosoft.com oppure ad Azure AD verificato il nome del dominio personalizzato.|
|TimeServer|Obbligatorio|Usare questo parametro per specificare un server specifico. Questo parametro deve essere fornito come un indirizzo IP del server ora valido. I nomi dei server non sono supportati.|
|InfraAzureDirectoryTenantAdminCredential|Facoltativo|Imposta il nome utente di Azure Active Directory e la password. Queste credenziali di Azure devono essere un ID. Org|
|InfraAzureEnvironment|Facoltativo|Selezionare l'ambiente di Azure con cui si vuole registrare la distribuzione di Azure Stack. Le opzioni includono Azure pubblico, Azure - Cina, Azure - governo degli Stati Uniti.|
|DNSForwarder|Facoltativo|Un server DNS viene creato come parte della distribuzione di Azure Stack. Per consentire ai computer all'interno della soluzione per risolvere i nomi all'esterno di timbro, forniscono server di infrastruttura DNS esistente. Il server DNS nel timbro inoltra le richieste di risoluzione nome sconosciuto a questo server.|
|Riesegui|Facoltativo|Usare questo flag per rieseguire la distribuzione. Tutti gli input precedenti viene utilizzato. Dover immettere di nuovo i dati forniti in precedenza non sono supportati perché alcuni valori univoci vengono generati e utilizzati per la distribuzione.|


## <a name="perform-post-deployment-configurations"></a>Eseguire le configurazioni di post-distribuzione
Dopo aver installato il ASDK, esistono alcuni controlli di post-installazione consigliati e modifiche di configurazione che devono essere resi. È possibile convalidare l'installazione è stato installato correttamente usando il cmdlet test-AzureStack e installare gli strumenti PowerShell per Azure Stack e GitHub. 

È anche necessario reimpostare i criteri di scadenza delle password per assicurarsi che la password per l'host del kit di sviluppo non scada prima della scadenza periodo della valutazione.

> [!NOTE]
> Facoltativamente, è anche possibile configurare [le impostazioni di telemetria di Azure Stack](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *dopo* installando il ASDK.

**[Registra le attività di distribuzione ASDK](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registrazione in Azure
È necessario registrare Azure Stack con Azure in modo da poter [download di Azure marketplace elementi](asdk-marketplace-item.md) ad Azure Stack.

**[Registrare Azure Stack con Azure](asdk-register.md)**

## <a name="next-steps"></a>Passaggi successivi
Congratulazioni! Dopo aver completato questi passaggi, si otterrà un ambiente del kit di sviluppo con entrambe [administrator](https://adminportal.local.azurestack.external) e [utente](https://portal.local.azurestack.external) portali. 

[Attività post-installazione ASDK configurazione](asdk-post-deploy.md)

