---
title: Installare e configurare l'estensione di diagnostica di Windows Azure (WAD)Install and configure Windows Azure diagnostics extension (WAD)
description: Informazioni su come raccogliere dati di diagnostica di Azure in un account di Archiviazione di Azure per poterli visualizzare con uno dei numerosi strumenti disponibili.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 929ab4109eb8d0e90b6c561a2135c0b7dd4205bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672260"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Installare e configurare l'estensione di diagnostica di Windows Azure (WAD)Install and configure Windows Azure diagnostics extension (WAD)
L'estensione di diagnostica di Azure è un agente in Monitoraggio di Azure che raccoglie i dati di monitoraggio dal sistema operativo guest e dai carichi di lavoro delle macchine virtuali di Azure e di altre risorse di calcolo. Questo articolo fornisce informazioni dettagliate sull'installazione e la configurazione dell'estensione di diagnostica di Windows e una descrizione della modalità di archiviazione dei dati e dell'account di archiviazione di Azure.This article provides details on installing and configuring the Windows diagnostics extension and a description of how the data is stored in and Azure Storage account.

L'estensione di diagnostica viene implementata come estensione di [macchina virtuale](../../virtual-machines/extensions/overview.md) in Azure, pertanto supporta le stesse opzioni di installazione usando i modelli di Resource Manager, PowerShell e l'interfaccia della riga di comando. Per informazioni dettagliate sull'installazione e la gestione delle estensioni delle macchine virtuali, [vedere Estensioni e funzionalità](../../virtual-machines/extensions/features-windows.md) delle macchine virtuali per Windows.See Virtual machine extensions and features for Windows for details on installing and maintaining virtual machine extensions.

## <a name="install-with-azure-portal"></a>Installare con il portale di AzureInstall with Azure portal
È possibile installare e configurare l'estensione di diagnostica in una singola macchina virtuale nel portale di Azure che offre un'interfaccia anziché utilizzare direttamente la configurazione. Quando si abilita l'estensione di diagnostica, verrà utilizzata automaticamente una configurazione predefinita con i contatori delle prestazioni e gli eventi più comuni. È possibile modificare questa configurazione predefinita in base alle esigenze specifiche.

> [!NOTE]
> Sono disponibili impostazioni di estensione di diagnostica che non è possibile configurare usando il portale di Azure, incluso l'invio di dati agli hub eventi di Azure.There are diagnostics extension settings you cannot configure using the Azure portal including sending data to Azure Event Hubs. È necessario utilizzare uno degli altri metodi di configurazione per queste impostazioni.

1. Aprire il menu per una macchina virtuale nel portale di Azure.Open the menu for a virtual machine in the Azure portal.
2. Fare clic su **Impostazioni di diagnostica** nella sezione **Monitoraggio** del menu VM.
3. Fare clic su **Abilita monitoraggio** a livello di guest se l'estensione di diagnostica non è già stata abilitata.
4. Verrà creato un nuovo account di Archiviazione di Azure per la macchina virtuale con il nome basato sul nome del gruppo di risorse per la macchina virtuale. È possibile collegare la macchina virtuale a un altro account di archiviazione selezionando la scheda **Agente.You** can attach the VM to another storage account by selecting the Agent tab.

![Impostazioni di diagnostica](media/diagnostics-extension-windows-install/diagnostic-settings.png)


È possibile modificare la configurazione predefinita dopo aver abilitato l'estensione di diagnostica. Nella tabella seguente vengono descritte le opzioni che è possibile modificare nelle diverse schede. Alcune opzioni **dispongono** di un comando personalizzato che consente di specificare una configurazione più dettagliata; Per informazioni dettagliate sulle diverse impostazioni, vedere [Schema dell'estensione di diagnostica di Windows.See Windows diagnostics extension schema](diagnostics-extension-schema-windows.md) for details on different settings.

| Scheda | Descrizione |
|:---|:---|
| Panoramica | Visualizza la configurazione corrente con i collegamenti alle altre schede. |
| Contatori delle prestazioni | Selezionare i contatori delle prestazioni da raccogliere e la frequenza di campionamento per ognuno di essi.  |
| Log | Selezionare i dati del log da raccogliere. Sono inclusi i registri eventi di Windows, i registri IIS, i registri applicazioni .NET e gli eventi ETW.  |
| Dump di arresto anomalo | Abilitare il dump di arresto anomalo del sistema per processi diversi. |
| Sink | Abilitare i sink di dati per l'invio di dati alle destinazioni oltre ad Archiviazione di Azure.Enable data sinks to send data to destinations in addition to Azure Storage.<br>Azure Monitor - Sends performance data to Azure Monitor Metrics.<br>Application Insights: inviare dati a un'applicazione Application Insights.Application Insights - Send data to an Application Insights application. |
| Agente | Modificare la seguente configurazione per l'agente:<br>- Modificare l'account di archiviazione.<br>- Specificare il numero massimo di dischi locali utilizzati per l'agente.<br>- Configurare i registri per l'integrità dell'agente stesso.|


> [!NOTE]
> Mentre la configurazione per l'estensione di diagnostica può essere formattata in JSON o XML, qualsiasi configurazione eseguita nel portale di Azure verrà sempre archiviata come JSON. Se si usa XML con un altro metodo di configurazione e quindi si modifica la configurazione con il portale di Azure, le impostazioni verranno modificate in JSON.

## <a name="resource-manager-template"></a>Modello di Resource Manager
Vedere Usare il monitoraggio e la [diagnostica con una macchina virtuale Windows e i modelli](../../virtual-machines/extensions/diagnostics-template.md) di Azure Resource Manager sulla distribuzione dell'estensione di diagnostica con i modelli di Azure Resource Manager.See Use monitoring and diagnostics with a Windows VM and Azure Resource Manager templates on deploying the diagnostics extension with Azure Resource Manager templates. 

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure
L'interfaccia della riga di comando di Azure può essere usata per distribuire l'estensione Diagnostica di Azure in una macchina virtuale esistente usando [l'estensione az vm impostata](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) come nell'esempio seguente. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

Le impostazioni protette sono definite [nell'elemento PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element) dello schema di configurazione. Di seguito è riportato un esempio minimo di un file di impostazioni protette che definisce l'account di archiviazione. Vedere [Configurazione di esempio](diagnostics-extension-schema-windows.md#privateconfig-element) per i dettagli completi delle impostazioni private.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
Le impostazioni pubbliche sono definite [nell'elemento Public](diagnostics-extension-schema-windows.md#publicconfig-element) dello schema di configurazione. Di seguito è riportato un esempio minimo di un file di impostazioni pubbliche che consente la raccolta di registri dell'infrastruttura di diagnostica, un singolo contatore delle prestazioni e un singolo log eventi. Vedere [Configurazione di esempio](diagnostics-extension-schema-windows.md#publicconfig-element) per i dettagli completi delle impostazioni pubbliche.

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>Distribuzione PowerShell
PowerShell può essere usato per distribuire l'estensione Diagnostica di Azure in una macchina virtuale esistente usando [Set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) come nell'esempio seguente. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

Le impostazioni private sono definite [nell'elemento PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element), mentre le impostazioni pubbliche sono definite nell'elemento [Public](diagnostics-extension-schema-windows.md#publicconfig-element) dello schema di configurazione. È anche possibile scegliere di specificare i dettagli dell'account di archiviazione come parametri del cmdlet Set-AzVMDiagnosticsExtension anziché includerli nelle impostazioni private.

Di seguito è riportato un esempio minimo di un file di configurazione che consente la raccolta di log dell'infrastruttura di diagnostica, un singolo contatore delle prestazioni e un singolo log eventi. Vedere [Configurazione di esempio](diagnostics-extension-schema-windows.md#publicconfig-element) per i dettagli completi delle impostazioni private e pubbliche. 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000
            },
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M",
                        "unit": "percent"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

Vedere anche [Usare PowerShell per abilitare la Diagnostica di Azure in una macchina virtuale che esegue Windows](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Archiviazione dei dati
La tabella seguente elenca i diversi tipi di dati raccolti dall'estensione di diagnostica e se vengono archiviati come tabella o BLOB. I dati archiviati nelle tabelle possono anche essere archiviati in BLOB a seconda [dell'impostazione StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) nella configurazione pubblica.


| Dati | Tipo di archiviazione | Descrizione |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabella | Modifiche alla configurazione e al monitoraggio diagnostico. |
| Tabella WADDirectories | Tabella | Directory monitorate dal monitoraggio diagnostico.  Sono inclusi i log di IIS, i log delle richieste non riuscite di IIS e le directory personalizzate.  La posizione del file di log dei BLOB è specificata nel campo Container e il nome del BLOB si trova nel campo RelativePath.  Il campo AbsolutePath indica la posizione e il nome del file esistente nella macchina virtuale di Azure. |
| Tabella WadLogsTabella | Tabella | Registra i log scritti nel codice utilizzando il listener di traccia. |
| WADPerformanceCountersTable | Tabella | Contatori delle prestazioni. |
| WADWindowsEventLogsTable | Tabella | Registri eventi di Windows. |
| wad-iis-failedreqlogfiles | BLOB | Contiene informazioni dai registri delle richieste non riuscite di IIS. |
| wad-iis-logfiles | BLOB | Contiene informazioni sui registri IIS. |
| "personalizzato" | BLOB | Contenitore personalizzato basato sulla configurazione delle directory monitorate dal monitoraggio diagnostico.  Il nome di questo contenitore BLOB verrà specificato in WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Strumenti per visualizzare i dati di diagnostica
Sono disponibili diversi strumenti per visualizzare i dati una volta trasferiti nell'account di archiviazione. Ad esempio:

* Esplora server in Visual Studio: se sono stati installati gli Strumenti di Azure per Microsoft Visual Studio, è possibile usare il nodo Archiviazione di Azure in Esplora server per visualizzare i dati di tabelle e BLOB di sola lettura dagli account di archiviazione di Azure. È possibile visualizzare i dati dall'account dell'emulatore di archiviazione locale e anche dagli account di archiviazione creati per Azure. Per altre informazioni, vedere [Esplorazione e gestione delle risorse di archiviazione con Esplora server](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma che consente di usare facilmente dati di Archiviazione di Azure in Windows, OSX e Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) include Azure Diagnostics Manager che consente di visualizzare, scaricare e gestire i dati di diagnostica raccolti dalle applicazioni in esecuzione in Azure.

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni dettagliate sull'inoltro dei dati di monitoraggio agli hub eventi di Azure, vedere [Inviare dati dall'estensione](diagnostics-extension-stream-event-hubs.md) di diagnostica di Windows Azure agli hub eventi.
