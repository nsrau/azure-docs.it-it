---
title: Monitoraggio di una macchina virtuale Linux con un&quot;estensione VM | Documentazione Microsoft
description: Informazioni su come usare l&quot;estensione di diagnostica Linux per monitorare le prestazioni e i dati di diagnostica di una VM Linux in Azure.
services: virtual-machines-linux
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: f54a11c5-5a0e-40ff-af6c-e60bd464058b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: Ning
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 5bc983ef1ffaac5d6de7bbc2cdf51838d8051aac
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017

---
# <a name="use-the-linux-diagnostic-extension-to-monitor-the-performance-and-diagnostic-data-of-a-linux-vm"></a>Uso dell'estensione di diagnostica Linux per monitorare le prestazioni e i dati di diagnostica di una macchina virtuale Linux

Questo documento descrive la versione 2.3 dell'estensione Diagnostica per Linux.

> [!IMPORTANT]
> Questa versione è deprecata e la sua pubblicazione potrebbe essere annullata a partire dal 30 giugno 2018. È stata sostituita dalla versione 3.0. Per altre informazioni, vedere la [documentazione per la versione 3.0 dell'estensione Diagnostica per Linux](../diagnostic-extension.md).

## <a name="introduction"></a>Introduzione

**Nota**: l'estensione della diagnostica Linux è open source in [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic), dove vengono pubblicate per prime le informazioni più aggiornate sull'estensione. È possibile vedere la [pagina GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic).

L'estensione di diagnostica Linux consente all'utente di monitorare le VM Linux eseguite in Microsoft Azure. Questo servizio offre le funzionalità seguenti:

* Raccoglie e carica informazioni sulle prestazioni del sistema dalla VM Linux alla tabella di archiviazione dell'utente, incluse informazioni di diagnostica e di syslog.
* Consente agli utenti di personalizzare la metrica dei dati che verrà raccolta e caricata.
* Consente agli utenti di caricare in una tabella di archiviazione designata i file di log specificati.

Nella versione 2.3 i dati includono:

* Tutti i log Rsyslog Linux, inclusi i log di sistema, sicurezza e applicazioni.
* Tutti i dati di sistema specificati nel [sito System Center Cross Platform Solutions](https://scx.codeplex.com/wikipage?title=xplatproviders).
* I file di log specificati dall'utente.

Questa estensione funziona sia con i modelli classici che con i modelli di distribuzione Resource Manager.

### <a name="current-version-of-the-extension-and-deprecation-of-old-versions"></a>Versione corrente dell'estensione e versioni precedenti deprecate

La versione più recente dell'estensione è la **2.3**, mentre **tutte le versioni precedenti, ovvero le versioni 2.0, 2.1 e 2.2, verranno deprecate, annullandone la pubblicazione entro la fine di quest'anno (2017)**. Se è installata l'estensione di diagnostica Linux con disabilitato l'aggiornamento automatico della versione secondaria, è consigliabile disinstallare l'estensione e reinstallarla abilitando l'aggiornamento automatico della versione secondaria. Nelle macchine virtuali classiche (ASM), è possibile ottenere questo risultato specificando '2.*' come versione, se si installa l'estensione tramite Powershell o l'interfaccia della riga di comando XPLAT di Azure. Nelle macchine virtuali ARM, è possibile ottenere questo risultato includendo '"autoUpgradeMinorVersion": true' nel modello di distribuzione della macchina virtuale. Inoltre, in qualsiasi nuova installazione dell'estensione dovrebbe essere attivata l'opzione di aggiornamento automatico alla versione secondaria.

## <a name="enable-the-extension"></a>Abilitare l'estensione

È possibile abilitare questa estensione usando il [portale di Azure](https://portal.azure.com/#), Azure PowerShell o gli script dell'interfaccia della riga di comando di Azure.

Per visualizzare e configurare i dati di sistema e le prestazioni direttamente dal portale di Azure, seguire questa [procedura nel blog di Azure](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/).

Questo articolo illustra come abilitare e configurare l'estensione usando i comandi dell'interfaccia della riga di comando di Azure. In questo modo è possibile leggere e visualizzare i dati direttamente dalla tabella di archiviazione.

Si noti che i metodi di configurazione descritti qui non funzioneranno per il portale di Azure. Per visualizzare e configurare i dati di sistema e prestazioni direttamente dal portale di Azure, l'estensione deve essere abilitata con il portale.

## <a name="prerequisites"></a>Prerequisiti

* **Agente Linux di Azure 2.0.6 o versione successiva**.

  Si noti che la maggior parte delle immagini della raccolta Linux di macchine virtuali di Azure include la versione 2.0.6 o successive. È possibile eseguire **WAAgent -version** per verificare la versione installata nella macchina virtuale. Se la macchina virtuale esegue una versione precedente alla 2.0.6, è possibile seguire queste [istruzioni in GitHub](https://github.com/Azure/WALinuxAgent "istruzioni") per aggiornarla.
* **Interfaccia della riga di comando di Azure**. Seguire le linee guida in [Installare l'interfaccia della riga di comando di Azure](../../../cli-install-nodejs.md) per configurare l'ambiente dell'interfaccia della riga di comando di Azure nella macchina virtuale. Dopo l'installazione dell'interfaccia della riga di comando di Azure, sarà possibile usare il comando **azure** nell'interfaccia della riga di comando (Bash, terminale o prompt dei comandi) per accedere ai relativi comandi. Ad esempio:

  * Eseguire **azure vm extension set --help** per informazioni della Guida dettagliate.
  * Eseguire **azure login** per accedere ad Azure.
  * Eseguire **azure vm list** per elencare tutte le macchine virtuali disponibili in Azure.
* Un account di archiviazione per archiviare i dati. Saranno necessari un nome di account di archiviazione creato in precedenza e una chiave di accesso per caricare i dati nella risorsa di archiviazione.

## <a name="use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension"></a>Usare l'interfaccia della riga di comando di Azure per abilitare l'estensione della diagnostica Linux

### <a name="scenario-1-enable-the-extension-with-the-default-data-set"></a>Scenario 1. Abilitare l'estensione con il set di dati predefinito

Nella versione 2.3 e successive i dati predefiniti che verranno raccolti includono:

* Tutte le informazioni Rsyslog (inclusi i log di sistema, sicurezza e applicazioni).  
* Un set principale di dati di sistema di base. Si noti che il set di dati completo è descritto nel [sito System Center Cross Platform Solutions](https://scx.codeplex.com/wikipage?title=xplatproviders).
  Per abilitare dati aggiuntivi, proseguire con i passaggi degli scenari 2 e 3.

Passaggio 1. Creare un file denominato PrivateConfig.json con il contenuto seguente:

    {
        "storageAccountName" : "the storage account to receive data",
        "storageAccountKey" : "the key of the account"
    }

Passaggio 2. Eseguire **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.

### <a name="scenario-2-customize-the-performance-monitor-metrics"></a>Scenario 2. Personalizzare le metriche di monitoraggio delle prestazioni

In questa sezione viene descritto come personalizzare la tabella dati delle prestazioni e della diagnostica.

Passaggio 1. Creare un file denominato PrivateConfig.json con il contenuto descritto nello scenario 1. Creare anche un file denominato PublicConfig.json. Specificare i dati specifici che si desidera raccogliere.

Per tutti i provider e le variabili supportati, vedere il [sito System Center Cross Platform Solutions](https://scx.codeplex.com/wikipage?title=xplatproviders). È possibile disporre di più query e archiviarle in più tabelle aggiungendo altre query nello script.

Per impostazione predefinita i dati Rsyslog verranno sempre raccolti.

    {
          "perfCfg":
          [
              {
                  "query" : "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
                  "table" : "LinuxMemory"
              }
          ]
    }


Passaggio 2. Eseguire **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**.

### <a name="scenario-3-upload-your-own-log-files"></a>Scenario 3. Caricamento dei propri file di log

Questa sezione descrive come raccogliere e caricare file di log specifici nell'account di archiviazione. È necessario specificare sia il percorso del file di log che il nome della tabella in cui si vuole archiviare il log. È possibile creare più file di log aggiungendo più voci di file/tabella nello script.

Passaggio 1. Creare un file denominato PrivateConfig.json con il contenuto descritto nello scenario 1. Creare quindi un altro file denominato PublicConfig.json con il contenuto seguente:

```json
{
    "fileCfg" :
    [
        {
            "file" : "/var/log/mysql.err",
            "table" : "mysqlerr"
            }
    ]
}
```

Passaggio 2. Eseguire `azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json`.

Si noti che con questa impostazione nelle versioni dell'estensione precedenti la 2.3, tutti i log scritti in `/var/log/mysql.err` potrebbero essere duplicati anche in `/var/log/syslog` o `/var/log/messages` a seconda del distributore di Linux. Se si vuole evitare la registrazione duplicata, è possibile escludere la registrazione dei log `local6` della struttura nella configurazione rsyslog. Dipende dalla distribuzione di Linux, ma in un sistema di Ubuntu 14.04, il file da modificare è `/etc/rsyslog.d/50-default.conf` ed è possibile sostituire la riga `*.*;auth,authpriv.none -/var/log/syslog` con `*.*;auth,authpriv,local6.none -/var/log/syslog`. Il problema viene risolto nell'ultimo aggiornamento rapido 2.3 (2.3.9007), pertanto se si dispone della versione dell'estensione 2.3 il problema non dovrebbe verificarsi. Se invece il problema persiste anche dopo aver riavviato la VM, è possibile contattarci e aiutarci a rintracciare il motivo per cui l'ultima versione dell'aggiornamento rapido non viene installata automaticamente.

### <a name="scenario-4-stop-the-extension-from-collecting-any-logs"></a>Scenario 4. Arrestare la raccolta di log dell'estensione

Questa sezione descrive come arrestare la raccolta di log da parte dell'estensione. Si noti che il processo dell'agente di monitoraggio sarà operativo anche con questa riconfigurazione. Per arrestare completamente il processo dell'agente di monitoraggio, è possibile disabilitare l'estensione. Il comando per disabilitare l'estensione è `azure vm extension set --disable <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions '2.*'`.

Passaggio 1. Creare un file denominato PrivateConfig.json con il contenuto descritto nello scenario 1. Creare un altro file denominato PublicConfig.json con il contenuto seguente:

    {
        "perfCfg" : [],
        "enableSyslog" : "false"
    }


Passaggio 2. Eseguire **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**.

## <a name="review-your-data"></a>Esaminare i dati

I dati delle prestazioni e della diagnostica vengono archiviati in una tabella di archiviazione di Azure. Per informazioni su come accedere ai dati nella tabella di archiviazione usando gli script dell'interfaccia della riga di comando di Azure, vedere [Come usare l'archivio tabelle di Azure da Ruby](../../../storage/storage-ruby-how-to-use-table-storage.md) .

È anche possibile usare gli strumenti dell'interfaccia utente seguenti per accedere ai dati:

1. Esplora server di Visual Studio. Passare all'account di archiviazione. Dopo circa cinque minuti dall'esecuzione della VM, verranno visualizzate le quattro tabelle predefinite: "LinuxCpu", "LinuxDisk", "LinuxMemory" e "Linuxsyslog". Fare doppio clic sui nomi delle tabelle per visualizzare i dati.
1. [Esplora archivi di Azure](https://azurestorageexplorer.codeplex.com/ "Esplora archivi di Azure").

![immagine](./media/diagnostic-extension/no1.png)

Se è stato abilitato fileCfg o perfCfg (come illustrato negli scenari 2 e 3), è possibile usare Esplora server di Visual Studio e Azure Storage Explorer per visualizzare i dati non predefiniti.

## <a name="known-issues"></a>Problemi noti

* Le informazioni Rsyslog e il file di log specificato dal cliente sono accessibili solo tramite scripting.

