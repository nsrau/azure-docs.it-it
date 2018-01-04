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
ms.openlocfilehash: 5a7dc313f1d6453562e4d5a11ceca03e4459b043
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Estensione per macchine virtuali Diagnostica prestazioni di Azure per Windows

Estensione della macchina virtuale Azure diagnostica delle prestazioni consente di raccogliere dati di diagnostica delle prestazioni da macchine virtuali di Windows. L'estensione esegue l'analisi e fornisce un report dei risultati e raccomandazioni per identificare e risolvere i problemi di prestazioni nella macchina virtuale. Questa estensione installa uno strumento di risoluzione dei problemi denominato [PerfInsights](http://aka.ms/perfinsights).

## <a name="prerequisites"></a>Prerequisiti

Questa estensione può essere installata in Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Può anche essere installato in Windows 8.1 e Windows 10.

## <a name="extension-schema"></a>Schema dell'estensione
Il codice JSON seguente mostra lo schema per l'estensione VM diagnostica delle prestazioni di Azure. Questa estensione richiede il nome e la chiave per un account di archiviazione archiviare l'output di diagnostica e i report. Questi valori sono riservati e devono essere archiviati all'interno di una configurazione protetta. Dati impostazione protette dell'estensione della macchina virtuale Azure viene crittografati e viene decrittografato solo nella macchina virtuale di destinazione. Si noti che **storageAccountName** e **storageAccountKey** tra maiuscole e minuscole. Altri parametri necessari sono elencati nella sezione seguente.

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
|apiVersion|2015-06-15|La versione dell'API.
|publisher|Microsoft.Azure.Performance.Diagnostics|Lo spazio dei nomi di server di pubblicazione per l'estensione.
|type|AzurePerformanceDiagnostics|Il tipo dell'estensione di macchina virtuale.
|typeHandlerVersion|1.0|La versione del gestore dell'estensione.
|performanceScenario|basic|Lo scenario di prestazioni per il quale acquisire i dati. I valori validi sono: **basic**, **vmslow**, **azurefiles** e **custom**.
|traceDurationInSeconds|300|La durata delle tracce, se una delle opzioni di traccia sono selezionata.
|perfCounterTrace|p|Opzione che abilita la traccia del contatore delle prestazioni. I valori validi sono **p** o un valore vuoto. Se non si desidera acquisire la traccia, lasciare il valore vuoto.
|networkTrace|n|Opzione per abilitare la traccia di rete. I valori validi sono **n** o un valore vuoto. Se non si desidera acquisire la traccia, lasciare il valore vuoto.
|xperfTrace|x|Opzione che abilita la traccia XPerf. I valori validi sono **x** o un valore vuoto. Se non si desidera acquisire la traccia, lasciare il valore vuoto.
|storPortTrace|s|Opzione che abilita la traccia StorPort. I valori validi sono **s** o valore vuoto. Se non si desidera acquisire la traccia, lasciare il valore vuoto.
|srNumber|123452016365929|Il numero di ticket di supporto, se disponibile. Lasciare vuoto se non è il valore.
|storageAccountName|mystorageaccount|Il nome dell'account di archiviazione per archiviare i log di diagnostica e i risultati.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|La chiave per l'account di archiviazione.

## <a name="install-the-extension"></a>Installare l'estensione

Seguire questi passaggi per installare l'estensione nelle macchine virtuali Windows:

1. Accedere al [portale di Azure](http://portal.azure.com).
2. Selezionare la macchina virtuale in cui installare l'estensione.

    ![Schermata del portale, con le macchine virtuali evidenziato](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Selezionare il **estensioni** pannello e selezionare **Aggiungi**.

    ![Pannello schermata di estensioni con componenti evidenziati](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Selezionare **diagnostica delle prestazioni di Azure**, esaminare i termini e condizioni e selezionare **crea**.

    ![Schermata della nuova schermata di risorse, con diagnostica di Azure prestazioni evidenziati](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Specificare i valori dei parametri per l'installazione e selezionare **OK** per installare l'estensione. Per ulteriori informazioni sugli scenari supportati, vedere [illustrato come utilizzare PerfInsights](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Schermata di installare la finestra di dialogo di estensione](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Quando l'installazione ha esito positivo, viene visualizzato un messaggio che indica questo stato.

    ![Schermata di Provisioning ha avuto esito positivo messaggio](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > L'estensione viene eseguito quando il provisioning ha avuto esito positivo. Sono necessari due minuti o meno da completare per lo scenario di base. Per altri scenari, l'esecuzione ha la durata specificata durante l'installazione.

## <a name="remove-the-extension"></a>Rimuovere l'estensione
Per rimuovere l'estensione da una macchina virtuale, eseguire questa procedura:

1. Accedi al [portale di Azure](http://portal.azure.com), selezionare la macchina virtuale da cui si desidera rimuovere questa estensione, quindi selezionare il **estensioni** blade. 
2. Selezionare il (**...** ) per la voce di estensione di diagnostica delle prestazioni nell'elenco e scegliere **Disinstalla**.

    ![Pannello schermata di estensioni con la disinstallazione evidenziata](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > È anche possibile selezionare la voce di estensione e selezionare il **Disinstalla** opzione.

## <a name="template-deployment"></a>Distribuzione del modello
Estensioni di macchina virtuale di Azure possono essere distribuite con modelli di gestione risorse di Azure. Lo schema JSON dettagliato nella sezione precedente può essere utilizzato in un modello di gestione risorse di Azure. L'estensione VM diagnostica delle prestazioni di Azure viene eseguita durante la distribuzione di un modello di gestione risorse di Azure. Di seguito è riportato un modello di esempio:

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

## <a name="powershell-deployment"></a>Distribuzione PowerShell
Il `Set-AzureRmVMExtension` comando può essere utilizzato per distribuire l'estensione VM diagnostica delle prestazioni di Azure in una macchina virtuale esistente. Prima di eseguire il comando, archiviare le configurazioni pubbliche e private in una tabella hash di PowerShell.

PowerShell

````
$PublicSettings = @{ "performanceScenario":"basic","traceDurationInSeconds":300,"perfCounterTrace":"p","networkTrace":"","xperfTrace":"","storPortTrace":"","srNumber":"","requestTimeUtc":"2017-09-28T22:08:53.736Z" }
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
Lo strumento PerfInsights raccoglie vari log, configurazione e dati di diagnostica, a seconda dello scenario selezionato. Per ulteriori informazioni, vedere il [PerfInsights documentazione](http://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Consente di visualizzare e condividere i risultati

Output dell'estensione viene archiviato in una cartella. La cartella denominata log_collection e può trovarsi all'interno dell'unità Temp (in genere D:\log_collection) per impostazione predefinita. In questa cartella, è possibile visualizzare i file zip contenente i log di diagnostica e di un report con i risultati e raccomandazioni.

È anche possibile trovare il file zip nell'account di archiviazione fornita durante l'installazione. È condiviso per 30 giorni tramite [firme di accesso condiviso (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). Viene anche creato un file di testo denominato *nomefilezip*_saslink.txt nella cartella log_collection. Questo file contiene il collegamento SAS creato per scaricare il file con estensione zip. Con questo collegamento qualsiasi utente è in grado di scaricare il file con estensione zip.

Per semplificare il tecnico del supporto lavorando il ticket di supporto, Microsoft potrebbe utilizzare questo collegamento SAS per scaricare i dati di diagnostica.

Per visualizzare il report, estrarre il file zip e aprire il **report. HTML PerfInsights** file.

È anche possibile scaricare il file zip direttamente dal portale selezionando l'estensione.

![Stato dettagliato di schermata di diagnostica delle prestazioni](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> Il collegamento SAS visualizzato nel portale potrebbe non funzionare. Durante le operazioni di codifica e decodifica, questo può dipendere da un URL non valido. In alternativa, è possibile ottenere il collegamento direttamente dal file *_saslink.txt dalla macchina virtuale.

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

- Anche se l'estensione viene correttamente eseguito il provisioning, lo stato dell'estensione distribuzione (nell'area di notifica) potrebbe essere "Distribuzione in corso".

    Questo problema può essere tranquillamente ignorato, come lo stato dell'estensione indica che l'estensione venga eseguito correttamente.
- È possibile risolvere alcuni problemi durante l'installazione utilizzando i log di estensione. L'output dell'esecuzione dell'estensione viene registrato nei file presenti nella directory seguente:

        C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/)e selezionare **ottenere supporto**. Per informazioni sull'utilizzo di supporto tecnico di Azure, leggere la [supporto tecnico di Microsoft Azure domande frequenti su](https://azure.microsoft.com/support/faq/).
