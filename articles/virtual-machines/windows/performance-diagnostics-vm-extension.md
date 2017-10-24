---
title: Estensione per macchine virtuali Diagnostica prestazioni di Azure per Windows | Microsoft Docs
description: Presenta l'estensione per macchine virtuali Diagnostica prestazioni di Azure per Windows.
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/29/2017
ms.author: genli
ms.openlocfilehash: b521b4cae29578798247921331d98fc5077ad266
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Estensione per macchine virtuali Diagnostica prestazioni di Azure per Windows

## <a name="summary"></a>Riepilogo
L'estensione per macchine virtuali Diagnostica prestazioni di Azure raccoglie i dati di diagnostica delle prestazioni di macchine virtuali Windows, esegue l'analisi dei dati e genera un report con i risultati, aggiungendo consigli per l'identificazione e la risoluzione di eventuali problemi di prestazioni delle macchine virtuali esaminate. Questa estensione installa uno strumento di risoluzione dei problemi denominato [PerfInsights](http://aka.ms/perfinsights).

## <a name="prerequisites"></a>Prerequisiti
### <a name="operating-systems"></a>Sistemi operativi
Questa estensione può essere installata in Windows Server 2008 R2, 2012, 2012 R2, 2016, nonché nei sistemi operativi Windows 8.1 e Windows 10.

## <a name="extension-schema"></a>Schema dell'estensione
Il codice JSON seguente illustra lo schema dell'estensione Diagnostica prestazioni di Azure. Per salvare l'output e il report di diagnostica, l'estensione richiede il nome e la chiave di un account di archiviazione. Questi valori sono riservati e devono essere archiviati all'interno di una configurazione con impostazioni protette. I dati della configurazione protetta dell'estensione macchina virtuale di Azure vengono crittografati, per essere poi decrittografati solo nella macchina virtuale di destinazione. Si noti che per storageAccountName e storageAccountKey sussiste la distinzione tra maiuscole e minuscole. Gli altri parametri obbligatori sono elencati nella sezione seguente.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
```

### <a name="property-values"></a>Valori delle proprietà

|   **Nome**   |**Valore/Esempio**|       **Descrizione**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Versione dell'API
|publisher|Microsoft.Azure.Performance.Diagnostics|Spazio dei nomi del server di pubblicazione per l'estensione
|type|AzurePerformanceDiagnostics|Tipo dell'estensione per macchine virtuali
|typeHandlerVersion|1.0|Versione del gestore dell'estensione
|performanceScenario|basic|Scenario di prestazioni per il quale acquisire i dati. I valori validi sono: **basic**, **vmslow**, **azurefiles** e **custom**.
|traceDurationInSeconds|300|Durata delle tracce se è selezionata una delle opzioni di traccia.
|DiagnosticsTrace|d|Opzione che abilita la traccia di diagnostica. I valori validi sono **d** o un valore vuoto. Se non si vuole acquisire la traccia, lasciare vuoto il valore.
|perfCounterTrace|p|Opzione che abilita la traccia del contatore delle prestazioni. I valori validi sono **p** o un valore vuoto. Se non si vuole acquisire la traccia, lasciare vuoto il valore.
|networkTrace|n|Opzione che abilita la traccia Netmon. I valori validi sono **n** o un valore vuoto. Se non si vuole acquisire la traccia, lasciare vuoto il valore.
|xperfTrace|x|Opzione che abilita la traccia XPerf. I valori validi sono **x** o un valore vuoto. Se non si vuole acquisire la traccia, lasciare vuoto il valore.
|storPortTrace|s|Opzione che abilita la traccia StorPort. I valori validi sono s o un valore vuoto. Se non si vuole acquisire la traccia, lasciare vuoto il valore.
|srNumber|123452016365929|Numero del ticket di supporto, se disponibile. Se non è disponibile, lasciare vuoto il valore.
|requestTimeUtc|9/2/2017 11:06:00 PM|Data e ora correnti in formato UTC. Questo valore non è necessario se si installa l'estensione tramite il portale.
|storageAccountName|mystorageaccount|Nome dell'account di archiviazione con cui archiviare i log di diagnostica e i risultati.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|Chiave per l'account di archiviazione.

## <a name="install-the-extension"></a>Installare l'estensione

Per installare l'estensione all'interno di macchine virtuali Windows, eseguire la procedura seguente:

1. Accedere al [portale di Azure](http://portal.azure.com).
2. Selezionare la macchina virtuale in cui installare l'estensione.

    ![Selezionare la macchina virtuale](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Selezionare il pannello **Estensioni** e fare clic sul pulsante **Aggiungi**.

    ![Selezionare Estensioni](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Selezionare l'estensione Diagnostica prestazioni di Azure, rivedere i termini e le condizioni e fare clic sul pulsante **Crea**.

    ![Creare l'estensione per macchine virtuali Diagnostica prestazioni di Azure](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Specificare i valori per i parametri di installazione e fare clic su **OK** per installare l'estensione. Altre informazioni sugli scenari di risoluzione dei problemi supportati sono disponibili [qui](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Installare l'estensione](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Dopo il completamento dell'installazione viene visualizzato un messaggio che indica l'esito positivo del provisioning.

    ![Messaggio di esito positivo del provisioning](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > Dopo il completamento del provisioning l'estensione viene avviata. L'esecuzione per lo scenario di base richiede qualche minuto. Per altri scenari, l'esecuzione ha la durata specificata durante l'installazione.

## <a name="remove-the-extension"></a>Rimuovere l'estensione
Per rimuovere l'estensione da una macchina virtuale, eseguire questa procedura:

1. Accedere al [portale di Azure](http://portal.azure.com), selezionare la macchina virtuale da cui si vuole rimuovere l'estensione e quindi selezionare il pannello Estensioni. 
2. Fare clic su (**…**) per la voce corrispondente all'estensione Diagnostica prestazioni e selezionare Disinstalla.

    ![Disinstallare l'estensione](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > È anche possibile selezionare la voce dell'estensione e selezionare l'opzione Disinstalla.

## <a name="template-deployment"></a>Distribuzione di modelli
Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. Lo schema JSON illustrato nella sezione precedente può essere usato in un modello di Azure Resource Manager per eseguire l'estensione Diagnostica prestazioni di Azure durante la distribuzione di un modello di Azure Resource Manager. Ecco un modello di esempio utilizzabile nella distribuzione modelli:

````
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "diagnosticsTrace": {
      "type": "string",
      "defaultValue": "d"
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
  ]
}
````

## <a name="powershell-deployment"></a>Distribuzione con PowerShell
Il comando `Set-AzureRmVMExtension` consente di distribuire l'estensione per macchine virtuali Diagnostica prestazioni di Azure in una macchina virtuale esistente. Prima di eseguire il comando, le configurazioni pubbliche e private devono essere archiviate in una tabella hash di PowerShell.

PowerShell

````
$PublicSettings = @{ "performanceScenario":"basic","traceDurationInSeconds":300,"diagnosticsTrace":"d","perfCounterTrace":"p","networkTrace":"","xperfTrace":"","storPortTrace":"","srNumber":"","requestTimeUtc":"2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountName":"mystorageaccount","storageAccountKey":"mystoragekey"}

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
````

## <a name="information-on-the-data-captured"></a>Informazioni sui dati acquisiti
Lo strumento PerfInsights raccoglie i diversi dati di log, configurazione, diagnostica e così via, a seconda dello scenario selezionato. Per altre informazioni sui dati raccolti in base allo scenario, visitare la [documentazione di PerfInsights](http://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Visualizzare e condividere i risultati

Per impostazione predefinita, l'output dell'estensione viene archiviato in una cartella denominata log_collection nell'unità Temp (in genere D:\log_collection). In questa cartella è possibile visualizzare uno o più file con estensione zip contenenti i log di diagnostica e un report contenente risultati e consigli.

Il file con estensione zip creato viene caricato anche nell'account di archiviazione specificato durante l'installazione e viene condiviso per 30 giorni tramite [firme di accesso condiviso (SAS, Shared Access Signature)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). Viene anche creato un file di testo denominato *nomefilezip*_saslink.txt nella cartella log_collection. Questo file contiene il collegamento SAS creato per scaricare il file con estensione zip. Con questo collegamento qualsiasi utente sarà in grado di scaricare il file con estensione zip.

Microsoft può usare il collegamento SAS per scaricare i dati di diagnostica e consentire al tecnico del supporto responsabile di un ticket di eseguire un'analisi più approfondita.

Per visualizzare il report, è sufficiente estrarre il file con estensione zip e aprire il file **PerfInsights Report.html**.

È anche possibile scaricare il file con estensione zip direttamente dal portale selezionando l'estensione.

![Visualizzare lo stato dettagliato](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> Il collegamento SAS visualizzato nel portale in alcuni casi non funziona a causa dell'inserimento di caratteri speciali all'interno dell'URL (che quindi non è valido) durante l'operazione di codifica e decodifica. La soluzione alternativa consiste nell'ottenere il collegamento direttamente dal file *_saslink.txt dalla macchina virtuale.

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto
### <a name="troubleshoot"></a>Risoluzione dei problemi

- Lo stato della distribuzione dell'estensione (nell'area di notifica) può visualizzare "Distribuzione in corso" anche se il provisioning, dell'estensione è stato completato.

    Questo problema può essere ignorato in tutta sicurezza, a condizione che lo stato dell'estensione indichi che il provisioning di quest'ultima è stato eseguito correttamente.
- Alcuni problemi di installazione possono essere risolti tramite i log dell'estensione. L'output dell'esecuzione dell'estensione viene registrato nei file presenti nella directory seguente:

        C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).