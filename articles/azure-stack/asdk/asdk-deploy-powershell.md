---
title: Distribuire Azure Stack - PowerShell | Documenti Microsoft
description: In questa esercitazione, si installa il ASDK dalla riga di comando.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 48ccccaba6b7f5780f1d42dfbe5d9747c5e30292
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-deploy-the-asdk-from-the-command-line"></a>Esercitazione: distribuire ASDK dalla riga di comando
In questa esercitazione, si distribuisce Azure Stack Development Kit (ASDK) dalla riga di comando in un ambiente non di produzione. 

Il ASDK è un ambiente di testing e sviluppo che è possibile distribuire per valutare e illustrano i servizi e le funzionalità dello Stack di Azure. Per ottenerlo attivo e in esecuzione, è necessario preparare l'hardware di ambiente ed eseguire alcuni script (l'operazione richiederà diverse ore). Successivamente, è possibile accedere ai portali amministratore e utente per iniziare a usare Azure Stack.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Scaricare ed estrarre il pacchetto di distribuzione
> * Preparare il computer host kit di sviluppo 
> * Esegui le configurazioni post-distribuzione
> * Registrare con Azure

## <a name="prerequisites"></a>Prerequisiti 
Preparare il computer host kit di sviluppo. Pianificare l'hardware e software e rete. Il computer che ospita il kit di sviluppo (l'host di kit di sviluppo) deve soddisfare requisiti di rete, hardware e software. È inoltre necessario scegliere tra l'utilizzo di Azure Active Directory (Azure AD) o Active Directory Federation Services (ADFS). Assicurarsi di rispettare i prerequisiti prima di avviare la distribuzione in modo che il processo di installazione viene eseguito in modo uniforme. 

Prima di distribuire il ASDK, assicurarsi che l'hardware del computer di sviluppo pianificato kit host, sistema operativo, account e le configurazioni di rete soddisfino i requisiti minimi per l'installazione di ASDK.

**[Esaminare le considerazioni di pianificazione della distribuzione ASDK](asdk-deploy-considerations.md)**

> [!TIP]
> È possibile usare il [dello strumento di verifica dei requisiti di distribuzione di Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) dopo aver installato il sistema operativo per confermare che l'hardware soddisfi tutti i requisiti.

## <a name="download-and-extract-the-deployment-package"></a>Scaricare ed estrarre il pacchetto di distribuzione
Dopo aver verificato che il computer host di kit sviluppo soddisfi i requisiti di base per l'installazione di ASDK, il passaggio successivo consiste a scaricare ed estrarre il pacchetto di distribuzione ASDK. Il pacchetto di distribuzione include il file Cloudbuilder.vhdx, cioè un disco rigido virtuale che include i file di installazione dello Stack di Azure e un sistema operativo avviabile.

È possibile scaricare il pacchetto di distribuzione per l'host del kit di sviluppo o in un altro computer. I file estratti distribuzione richiedere 60 GB di spazio libero su disco, in modo usando un altro computer consente di ridurre i requisiti hardware per l'host del kit di sviluppo.

**[Scaricare ed estrarre il Kit di sviluppo dello Stack di Azure (ASDK)](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Preparare il computer host kit di sviluppo
Prima di installare il ASDK nel computer host, è necessario preparare l'ambiente e il sistema è configurato per l'avvio da disco rigido virtuale. Dopo questo passaggio, verrà avviato l'host di kit sviluppo Cloudbuilder.vhdx (un disco rigido virtuale che include un sistema operativo avviabile e i file di installazione dello Stack di Azure).

Utilizzare PowerShell per configurare il computer host ASDK per l'avvio da CloudBuilder.vhdx. Questi comandi configurano il computer host ASDK per l'avvio dallo scaricati ed estratti Stack Azure virtual harddisk (CloudBuilder.vhdx). Dopo aver completato questi passaggi, riavviare il computer host ASDK.

Per configurare il computer host ASDK per l'avvio da CloudBuilder.vhdx:

  1. Avviare un prompt dei comandi come amministratore.
  2. Eseguire `bcdedit /copy {current} /d "Azure Stack"`
  3. Copia (CTRL + C), il valore CLSID restituito, incluse le {obbligatorio} ' s. Questo valore è detto {CLSID} e dovrà essere incollato nel (CTRL + V o destro) negli altri passaggi.
  4. Eseguire `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 
  5. Eseguire `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
  6. Eseguire `bcdedit /set {CLSID} detecthal on` 
  7. Eseguire `bcdedit /default {CLSID}`
  8. Per verificare le impostazioni di avvio, eseguire `bcdedit`. 
  9. Verificare che il CloudBuilder.vhdx file è stato spostato nella radice dell'unità C:\ (C:\CloudBuilder.vhdx) e riavviare il computer host kit di sviluppo. Quando viene riavviato il computer host ASDK, deve avvio dal disco rigido CloudBuilder.vhdx macchina virtuale per iniziare la distribuzione ASDK. 

> [!IMPORTANT]
> Assicurarsi di aver fisica diretta o l'accesso KVM al computer host kit di sviluppo prima di riavviarlo. Al primo avvio della macchina virtuale, viene richiesto di completare l'installazione di Windows Server. Fornire le stesse credenziali di amministratore che è utilizzato per accedere al computer host kit di sviluppo. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Preparare l'host del kit di sviluppo con PowerShell 
Dopo il kit di sviluppo computer host ha avviato correttamente nell'immagine CloudBuilder.vhdx, accedere con le stesse credenziali di amministratore locale utilizzato per accedere al computer host kit di sviluppo (e fornito come parte della finalizzazione del Server di Windows Programma di installazione quando il computer host avviata dal disco rigido virtuale). 

> [!NOTE]
> Facoltativamente, è inoltre possibile configurare [le impostazioni di telemetria di Azure Stack](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *prima di* il ASDK l'installazione.

Aprire una console di PowerShell con privilegi elevata ed eseguire i comandi in questa sezione per distribuire il ASDK nell'host di kit di sviluppo.

> [!IMPORTANT] 
> Installazione ASDK supporta esattamente una scheda di interfaccia di rete (NIC) per la rete. Se si dispone di più schede di rete, assicurarsi che solo uno è attivato (e tutti gli altri sono disabilitati) prima di eseguire lo script di distribuzione.

È possibile distribuire Azure Stack con Azure Active Directory o ADFS di Windows Server come provider di identità. Stack di Azure, i provider di risorse e altre applicazioni funzionano con entrambe.

> [!TIP]
> Se non si specifica alcun parametro di installazione (vedere parametri facoltativi InstallAzureStackPOC.ps1 ed esempi riportati di seguito), viene richiesto per i parametri obbligatori.

### <a name="deploy-azure-stack-using-azure-ad"></a>Distribuire Azure Stack mediante Azure AD 
Per distribuire Azure Stack **mediante Azure AD come provider di identità**, è necessario disporre della connettività internet, direttamente o tramite un proxy trasparente. 

Eseguire i seguenti comandi di PowerShell per distribuire il kit di sviluppo mediante Azure AD:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

Alcuni minuti in installazione ASDK che verrà richiesto per le credenziali di Azure AD. È necessario fornire credenziali di amministratore globale per il tenant di Azure AD. 

### <a name="deploy-azure-stack-using-ad-fs"></a>Distribuire Azure Stack con ADFS 
Per distribuire il kit di sviluppo **con ADFS come provider di identità**, eseguire i comandi di PowerShell seguenti (è sufficiente aggiungere il parametro - UseADFS): 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

Nelle distribuzioni di AD FS, l'indicatore di valore predefinito del servizio di Directory viene usato come provider di identità. L'account predefinito per l'accesso è azurestackadmin@azurestack.local, e la password verrà impostata su fornite come parte dei comandi di installazione di PowerShell.

Il processo di distribuzione può richiedere alcune ore, durante il quale il sistema viene riavviato automaticamente una volta. Quando la distribuzione ha esito positivo, nella console di PowerShell vengono visualizzati: **completa: azione "Distribuzione"**. Se la distribuzione non riesce, è possibile provare a eseguire di nuovo lo script usando eseguire nuovamente il parametro-. In alternativa, è possibile [ridistribuire ASDK](asdk-redeploy.md) da zero.

> [!IMPORTANT]
> Se si desidera monitorare lo stato di distribuzione dopo il riavvio dell'host ASDK, è necessario accedere come AzureStack\AzureStackAdmin. Se si accede come amministratore locale dopo che il computer host viene riavviato (e aggiunti al dominio azurestack.local), non verrà visualizzata l'avanzamento della distribuzione. Non rieseguire la distribuzione, invece Accedi come azurestack verificare che sia in esecuzione.


#### <a name="azure-ad-deployment-script-examples"></a>Esempi di script di distribuzione AD Azure
È possibile generare script l'intera distribuzione di Azure AD. Ecco alcuni esempi di commenti che includono alcuni parametri facoltativi.

Se l'identità di Azure AD è associato solo **uno** directory di Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Se l'identità di Azure AD è associata a **maggiore di uno** directory di Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Se l'ambiente non dispone di DHCP abilitato, è necessario includere i seguenti parametri aggiuntivi per una delle opzioni sopra (utilizzo di esempio forniti): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>Parametri facoltativi ASDK InstallAzureStackPOC.ps1
|Parametro|Obbligatorio/Facoltativo|DESCRIZIONE|
|-----|-----|-----|
|AdminPassword|Obbligatoria|Imposta l'account amministratore locale e tutti gli altri account utente in tutte le macchine virtuali create come parte della distribuzione kit di sviluppo. Questa password deve corrispondere alla password di amministratore locale corrente nell'host.|
|InfraAzureDirectoryTenantName|Obbligatoria|Imposta la directory del tenant. Utilizzare questo parametro per specificare una directory specifica in cui l'account AAD disponga delle autorizzazioni per la gestione di più directory. Nome di un Tenant di Directory nel formato completo. c o m o Azure Active Directory il nome di dominio personalizzato verificato.|
|TimeServer|Obbligatoria|Utilizzare questo parametro per specificare un server di tempo specifico. Questo parametro deve essere fornito come un indirizzo IP del server ora valido. I nomi dei server non sono supportati.|
|InfraAzureDirectoryTenantAdminCredential|Facoltativo|Imposta il nome utente di Azure Active Directory e la password. Le credenziali di Azure devono essere ID. Org|
|InfraAzureEnvironment|Facoltativo|Selezionare l'ambiente di Azure con cui si desidera registrare la distribuzione di Azure Stack. Le opzioni includono pubblica Azure, Azure - Cina, Azure - governo.|
|DNSForwarder|Facoltativo|Un server DNS viene creato come parte della distribuzione di Azure Stack. Per consentire ai computer all'interno della soluzione per la risoluzione dei nomi di fuori l'indicatore, fornire i server di infrastruttura DNS esistente. Il server DNS in timbro inoltra le richieste di risoluzione nome sconosciuto al server.|
|NatIPv4Address|Richiesta per il supporto DHCP NAT|Imposta un indirizzo IP statico per definiti BGPNAT01. Usare questo parametro solo se DHCP non riesce ad assegnare un indirizzo IP valido per accedere a Internet.|
|NatIPv4Subnet|Richiesta per il supporto DHCP NAT|Prefisso di Subnet IP utilizzato per DHCP sul supporto NAT. Usare questo parametro solo se DHCP non riesce ad assegnare un indirizzo IP valido per accedere a Internet.|
|PublicVlanId|Facoltativo|Imposta l'ID VLAN. Utilizzare questo parametro solo se l'host e definiti BGPNAT01 necessario configurare l'ID VLAN per accedere alla rete fisica (e Internet). Ad esempio,.\InstallAzureStackPOC.ps1-Verbose - PublicVLan 305|
|Esegui di nuovo|Facoltativo|Utilizzare questo flag per rieseguire la distribuzione. Tutti gli input precedenti viene utilizzato. Dati reimmettere forniti in precedenza non sono supportati perché più valori univoci vengono generati e utilizzati per la distribuzione.|


## <a name="perform-post-deployment-configurations"></a>Esegui le configurazioni post-distribuzione
Dopo aver installato il ASDK, esistono alcuni controlli consigliati di post-installazione e le modifiche di configurazione che devono essere rese. È possibile convalidare l'installazione è stato installato correttamente usando il cmdlet test-AzureStack e installare gli strumenti di Azure PowerShell dello Stack e GitHub. 

È inoltre necessario reimpostare i criteri di scadenza delle password per assicurarsi che la password per l'host di kit sviluppo senza scadenza prima la fine del periodo di valutazione.

> [!NOTE]
> Facoltativamente, è inoltre possibile configurare [le impostazioni di telemetria di Azure Stack](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *dopo* il ASDK l'installazione.

**[Registra le attività di distribuzione ASDK](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registrare con Azure
È necessario registrare dello Stack di Azure con Azure in modo da poter [scaricare gli elementi di Azure marketplace](asdk-marketplace-item.md) allo Stack di Azure.

**[Registro dello Stack di Azure con Azure](asdk-register.md)**

## <a name="next-steps"></a>Passaggi successivi
Congratulazioni! Dopo aver completato questi passaggi, sarà necessario un ambiente di kit sviluppo con entrambi [amministratore](https://adminportal.local.azurestack.external) e [utente](https://portal.local.azurestack.external) portali. 

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Scaricare ed estrarre il pacchetto di distribuzione
> * Preparare il computer host kit di sviluppo 
> * Esegui le configurazioni post-distribuzione
> * Registrare con Azure

Passare alla prossima esercitazione per informazioni su come aggiungere un elemento del marketplace dello Stack di Azure.

> [!div class="nextstepaction"]
> [Aggiungere un elemento del marketplace Azure Stack](asdk-marketplace-item.md)

