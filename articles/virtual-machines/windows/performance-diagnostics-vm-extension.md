---
title: Estensione per macchine virtuali Diagnostica prestazioni di Azure per Windows | Microsoft Docs
description: Presenta l'estensione per macchine virtuali Diagnostica prestazioni di Azure per Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/29/2017
ms.author: genli
ms.openlocfilehash: 8f6f3fc8325fb2587dc09b982efa52fbe663e2a9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Estensione per macchine virtuali Diagnostica prestazioni di Azure per Windows

L'estensione per macchine virtuali Azure Performance Diagnostics raccoglie i dati di diagnostica delle prestazioni di macchine virtuali Windows, esegue l'analisi dei dati e genera un report con i risultati, aggiungendo consigli per l'identificazione e la risoluzione di eventuali problemi di prestazioni delle macchine virtuali esaminate. Questa estensione installa uno strumento di risoluzione dei problemi denominato [PerfInsights](http://aka.ms/perfinsights).

## <a name="prerequisites"></a>Prerequisiti

Questa estensione può essere installata in Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016, nonché in Windows 8.1 e Windows 10.

## <a name="extension-schema"></a>Schema dell'estensione
Il codice JSON seguente illustra lo schema dell'estensione per macchine virtuali Azure Performance Diagnostics. Per archiviare l'output e il report di diagnostica, l'estensione richiede il nome e la chiave di un account di archiviazione. Questi valori sono riservati. La chiave dell'account di archiviazione deve essere archiviata all'interno di una configurazione con impostazioni protette. I dati della configurazione protetta dell'estensione per macchine virtuali di Azure vengono crittografati, per essere poi decrittografati solo nella macchina virtuale di destinazione. Per **storageAccountName** and **storageAccountKey** viene fatta distinzione tra maiuscole e minuscole. Gli altri parametri obbligatori sono elencati nella sezione seguente.

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
            "storageAccountName": "[parameters('storageAccountName')]",
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
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>Valori delle proprietà

|   **Nome**   |**Valore/Esempio**|       **Descrizione**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Versione dell'API.
|publisher|Microsoft.Azure.Performance.Diagnostics|Spazio dei nomi del server di pubblicazione per l'estensione.
|type|AzurePerformanceDiagnostics|Tipo dell'estensione per macchine virtuali.
|typeHandlerVersion|1.0|Versione del gestore dell'estensione.
|performanceScenario|basic|Scenario di prestazioni per il quale acquisire i dati. I valori validi sono: **basic**, **vmslow**, **azurefiles** e **custom**.
|traceDurationInSeconds|300|Durata delle tracce se è selezionata una delle opzioni di traccia.
|perfCounterTrace|p|Opzione che abilita la traccia del contatore delle prestazioni. I valori validi sono **p** o un valore vuoto. Se non si vuole acquisire la traccia, lasciare vuoto il valore.
|networkTrace|n|Opzione che abilita la traccia di rete. I valori validi sono **n** o un valore vuoto. Se non si vuole acquisire la traccia, lasciare vuoto il valore.
|xperfTrace|x|Opzione che abilita la traccia XPerf. I valori validi sono **x** o un valore vuoto. Se non si vuole acquisire la traccia, lasciare vuoto il valore.
|storPortTrace|s|Opzione che abilita la traccia StorPort. I valori validi sono **s** o un valore vuoto. Se non si vuole acquisire la traccia, lasciare vuoto il valore.
|srNumber|123452016365929|Numero del ticket di supporto, se disponibile. Se non è disponibile, lasciare vuoto il valore.
|requestTimeUtc|2017-09-28T22:08:53.736Z|Data e ora correnti in formato UTC. Se l'estensione viene installata tramite il portale, non è necessario specificare questo valore.
|storageAccountName|mystorageaccount|Nome dell'account di archiviazione con cui archiviare i log di diagnostica e i risultati.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|Chiave per l'account di archiviazione.

## <a name="install-the-extension"></a>Installare l'estensione

Per installare l'estensione in macchine virtuali Windows, seguire queste istruzioni:

1. Accedere al [portale di Azure](http://portal.azure.com).
2. Selezionare la macchina virtuale in cui installare l'estensione.

    ![Screenshot del portale di Azure con "Macchine virtuali" evidenziato](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Selezionare il pannello **Estensioni** e scegliere **Aggiungi**.

    ![Screenshot del pannello Estensioni con Aggiungi evidenziato](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Selezionare **Azure Performance Diagnostics** (Diagnostica prestazioni di Azure), rivedere i termini e le condizioni e selezionare **Crea**.

    ![Screenshot della schermata Nuova risorsa, con Azure Performance Diagnostics evidenziato](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Specificare i valori per i parametri di installazione e fare clic su **OK** per installare l'estensione. Per altre informazioni sugli scenari supportati, vedere [Come usare PerfInsights](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Screenshot della finestra di dialogo Installa estensione](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Al termine dell'installazione viene visualizzato un messaggio che ne indica lo stato.

    ![Screenshot del messaggio di esito positivo del provisioning](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > L'estensione viene eseguita solo se il provisioning ha avuto esito positivo. Per il completamento dello scenario di base sono necessari al massimo due minuti. Per altri scenari, l'esecuzione ha la durata specificata durante l'installazione.

## <a name="remove-the-extension"></a>Rimuovere l'estensione
Per rimuovere l'estensione da una macchina virtuale, eseguire questa procedura:

1. Accedere al [portale di Azure](http://portal.azure.com), selezionare la macchina virtuale da cui si vuole rimuovere l'estensione e quindi selezionare il pannello **Estensioni**. 
2. Selezionare (**…**) in corrispondenza della voce Performance Diagnostics Extension dell'elenco e scegliere **Disinstalla**.

    ![Screenshot del pannello Estensioni con Disinstalla evidenziato](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > È anche possibile selezionare la voce dell'estensione e scegliere l'opzione **Disinstalla**.

## <a name="template-deployment"></a>Distribuzione del modello
Le estensioni per macchine virtuali di Azure possono essere distribuite con i modelli di Azure Resource Manager. Lo schema JSON dettagliato illustrato nella sezione precedente può essere usato in un modello di Azure Resource Manager per eseguire l'estensione per macchine virtuali Azure Performance Diagnostics durante la distribuzione di un modello di Azure Resource Manager. Di seguito è riportato un modello di esempio:

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
            "storageAccountName": "[parameters('storageAccountName')]",
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
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
  ]
}
````

## <a name="powershell-deployment"></a>Distribuzione PowerShell
Il comando `Set-AzureRmVMExtension` consente di distribuire l'estensione per macchine virtuali Azure Performance Diagnostics in una macchina virtuale esistente.

PowerShell

````
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
````

## <a name="information-on-the-data-captured"></a>Informazioni sui dati acquisiti
Lo strumento PerfInsights raccoglie vari dati di log, configurazione e diagnostica, in base allo scenario selezionato. Per altre informazioni, vedere la [documentazione di PerfInsights](http://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Visualizzare e condividere i risultati

L'output dell'estensione viene archiviato in una cartella denominata log_collection e disponibile per impostazione predefinita nell'unità Temp (in genere D:\log_collection). In questa cartella è possibile visualizzare alcuni file con estensione zip contenenti i log di diagnostica e un report contenente risultati e consigli.

Il file con estensione zip è disponibile anche nell'account di archiviazione specificato durante l'installazione e viene condiviso per 30 giorni tramite [firme di accesso condiviso](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS, Shared Access Signature). Viene anche creato un file di testo denominato *nomefilezip*_saslink.txt nella cartella log_collection. Questo file contiene il collegamento SAS creato per scaricare il file con estensione zip. Con questo collegamento qualsiasi utente è in grado di scaricare il file con estensione zip.

Per facilitare il lavoro del tecnico del supporto responsabile del ticket, Microsoft può usare il collegamento SAS per scaricare i dati di diagnostica.

Per visualizzare il report, estrarre il file con estensione zip e aprire il file **PerfInsights Report.html**.

È anche possibile scaricare il file con estensione zip direttamente dal portale selezionando l'estensione.

![Screenshot dello stato dettagliato di Performance Diagnostics](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> Il collegamento SAS visualizzato nel portale in alcuni casi non funziona perché durante le operazioni di codifica e decodifica è stato specificato un URL non corretto. In questi casi, è possibile ottenere il collegamento direttamente dal file *_saslink.txt della macchina virtuale.

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

- Lo stato della distribuzione dell'estensione (nell'area di notifica) può visualizzare "Distribuzione in corso" anche se il provisioning dell'estensione è stato completato.

    Questo problema può essere ignorato in tutta sicurezza, a condizione che lo stato dell'estensione indichi che il provisioning di quest'ultima è stato eseguito correttamente.
- Alcuni problemi di installazione possono essere risolti usando i log dell'estensione. L'output dell'esecuzione dell'estensione viene registrato nei file presenti nella directory seguente:

        C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Richiesta di supporto**. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).
