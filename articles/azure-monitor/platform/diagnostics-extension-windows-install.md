---
title: Installare e configurare l'estensione diagnostica di Microsoft Azure (WAD)
description: Informazioni su come raccogliere i dati di diagnostica di Azure in un account di archiviazione di Azure in modo che sia possibile visualizzarli con uno dei diversi strumenti disponibili.
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
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Installare e configurare l'estensione diagnostica di Microsoft Azure (WAD)
L'estensione diagnostica di Azure è un agente di monitoraggio di Azure che raccoglie i dati di monitoraggio dal sistema operativo guest e i carichi di lavoro delle macchine virtuali di Azure e di altre risorse di calcolo. Questo articolo fornisce informazioni dettagliate sull'installazione e la configurazione dell'estensione di diagnostica Windows e una descrizione della modalità di archiviazione dei dati in un account di archiviazione di Azure.

L'estensione di diagnostica viene implementata come [estensione della macchina virtuale](../../virtual-machines/extensions/overview.md) in Azure, pertanto supporta le stesse opzioni di installazione usando modelli di gestione risorse, PowerShell e l'interfaccia della riga di comando. Per informazioni dettagliate sull'installazione e la gestione delle estensioni delle macchine virtuali, vedere [estensioni e funzionalità delle macchine virtuali per Windows](../../virtual-machines/extensions/features-windows.md) .

## <a name="install-with-azure-portal"></a>Installare con portale di Azure
È possibile installare e configurare l'estensione di diagnostica in una singola macchina virtuale nell'portale di Azure che fornisce un'interfaccia anziché lavorare direttamente con la configurazione. Quando si Abilita l'estensione di diagnostica, viene automaticamente utilizzata una configurazione predefinita con i contatori delle prestazioni e gli eventi più comuni. È possibile modificare questa configurazione predefinita in base ai requisiti specifici.

> [!NOTE]
> Sono disponibili impostazioni dell'estensione di diagnostica che non è possibile configurare usando il portale di Azure incluso l'invio di dati a hub eventi di Azure. Per queste impostazioni è necessario usare uno degli altri metodi di configurazione.

1. Aprire il menu per una macchina virtuale nell'portale di Azure.
2. Fare clic su **impostazioni di diagnostica** nella sezione **monitoraggio** del menu VM.
3. Fare clic su **Abilita monitoraggio a livello di Guest** se l'estensione diagnostica non è già stata abilitata.
4. Verrà creato un nuovo account di archiviazione di Azure per la macchina virtuale con il nome in base al nome del gruppo di risorse per la macchina virtuale. È possibile allegare la macchina virtuale a un altro account di archiviazione selezionando la scheda **agente** .

![Impostazioni di diagnostica](media/diagnostics-extension-windows-install/diagnostic-settings.png)


È possibile modificare la configurazione predefinita dopo che l'estensione di diagnostica è stata abilitata. Nella tabella seguente vengono descritte le opzioni che è possibile modificare nelle diverse schede. Alcune opzioni hanno un comando **personalizzato** che consente di specificare una configurazione più dettagliata; per informazioni dettagliate sulle diverse impostazioni, vedere lo [schema dell'estensione diagnostica di Windows](diagnostics-extension-schema-windows.md) .

| Scheda | Descrizione |
|:---|:---|
| Panoramica | Visualizza la configurazione corrente con i collegamenti alle altre schede. |
| Contatori delle prestazioni | Selezionare i contatori delle prestazioni da raccogliere e la frequenza di campionamento per ogni.  |
| Log | Selezionare i dati di log da raccogliere. Sono inclusi i registri eventi di Windows, i log di IIS, i registri applicazioni .NET e gli eventi ETW.  |
| Dump di arresto anomalo | Abilitare il dump di arresto anomalo per processi diversi. |
| Sink | Consentire ai sink di dati di inviare dati a destinazioni oltre ad archiviazione di Azure.<br>Monitoraggio di Azure: Invia i dati sulle prestazioni alle metriche di monitoraggio di Azure.<br>Application Insights inviare i dati a un'applicazione Application Insights. |
| Agente | Modificare la configurazione seguente per l'agente:<br>-Modificare l'account di archiviazione.<br>-Specificare il disco locale massimo usato per l'agente.<br>-Configurare i log per l'integrità dell'agente stesso.|


> [!NOTE]
> Sebbene sia possibile formattare la configurazione per l'estensione di diagnostica in JSON o XML, qualsiasi configurazione eseguita nel portale di Azure verrà sempre archiviata come JSON. Se si usa XML con un altro metodo di configurazione e quindi si modifica la configurazione con il portale di Azure, le impostazioni verranno modificate in JSON.

## <a name="resource-manager-template"></a>Modello di Resource Manager
Vedere [usare il monitoraggio e la diagnostica con una macchina virtuale Windows e modelli di Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md) sulla distribuzione dell'estensione di diagnostica con i modelli Azure Resource Manager. 

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure
L'interfaccia della riga di comando di Azure può essere usata per distribuire l'estensione Diagnostica di Azure a una macchina virtuale esistente usando [AZ VM Extension set](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) come nell'esempio seguente. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

Le impostazioni protette sono definite nell' [elemento PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element) dello schema di configurazione. Di seguito è riportato un esempio minimo di un file di impostazioni protette che definisce l'account di archiviazione. Vedere [configurazione di esempio](diagnostics-extension-schema-windows.md#privateconfig-element) per i dettagli completi delle impostazioni private.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
Le impostazioni pubbliche sono definite nell' [elemento Public](diagnostics-extension-schema-windows.md#publicconfig-element) dello schema di configurazione. Di seguito è riportato un esempio di file di impostazioni pubbliche che consente di raccogliere i log dell'infrastruttura di diagnostica, un singolo contatore delle prestazioni e un singolo registro eventi. Vedere [configurazione di esempio](diagnostics-extension-schema-windows.md#publicconfig-element) per informazioni complete sulle impostazioni pubbliche.

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
PowerShell può essere usato per distribuire l'estensione Diagnostica di Azure a una macchina virtuale esistente usando [set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) , come nell'esempio seguente. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

Le impostazioni private sono definite nell' [elemento PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element), mentre le impostazioni pubbliche sono definite nell' [elemento Public](diagnostics-extension-schema-windows.md#publicconfig-element) dello schema di configurazione. È anche possibile scegliere di specificare i dettagli dell'account di archiviazione come parametri del cmdlet Set-AzVMDiagnosticsExtension anziché includerli nelle impostazioni private.

Di seguito è riportato un esempio minimo di un file di configurazione che Abilita la raccolta di log dell'infrastruttura di diagnostica, un singolo contatore delle prestazioni e un singolo registro eventi. Vedere [configurazione di esempio](diagnostics-extension-schema-windows.md#publicconfig-element) per i dettagli completi delle impostazioni private e pubbliche. 

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
La tabella seguente elenca i diversi tipi di dati raccolti dall'estensione di diagnostica e se vengono archiviati come una tabella o un BLOB. I dati archiviati nelle tabelle possono anche essere archiviati in BLOB a seconda dell' [impostazione di StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) nella configurazione pubblica.


| Data | Tipo di archiviazione | Descrizione |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabella | Monitoraggio diagnostica e modifiche di configurazione. |
| WADDirectoriesTable | Tabella | Directory monitorate dal monitor di diagnostica.  Sono inclusi i log di IIS, i log delle richieste non riuscite di IIS e le directory personalizzate.  La posizione del file di log dei BLOB è specificata nel campo Container e il nome del BLOB si trova nel campo RelativePath.  Il campo AbsolutePath indica la posizione e il nome del file esistente nella macchina virtuale di Azure. |
| WadLogsTable | Tabella | Log scritti nel codice utilizzando il listener di traccia. |
| WADPerformanceCountersTable | Tabella | Contatori delle prestazioni. |
| WADWindowsEventLogsTable | Tabella | Registri eventi di Windows. |
| wad-IIS-failedreqlogfiles | BLOB | Contiene le informazioni dei log delle richieste non riuscite di IIS. |
| wad-iis-logfiles | BLOB | Contiene informazioni sui log di IIS. |
| personalizzato | BLOB | Un contenitore personalizzato basato sulle directory di configurazione monitorate dal monitor di diagnostica.  Il nome di questo contenitore BLOB verrà specificato in WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Strumenti per visualizzare i dati di diagnostica
Sono disponibili diversi strumenti per visualizzare i dati una volta trasferiti nell'account di archiviazione. Ad esempio:

* Esplora server in Visual Studio: se sono stati installati gli Strumenti di Azure per Microsoft Visual Studio, è possibile usare il nodo Archiviazione di Azure in Esplora server per visualizzare i dati di tabelle e BLOB di sola lettura dagli account di archiviazione di Azure. È possibile visualizzare i dati dall'account dell'emulatore di archiviazione locale e anche dagli account di archiviazione creati per Azure. Per altre informazioni, vedere [Esplorazione e gestione delle risorse di archiviazione con Esplora server](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma che consente di usare facilmente dati di Archiviazione di Azure in Windows, OSX e Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) include Azure Diagnostics Manager che consente di visualizzare, scaricare e gestire i dati di diagnostica raccolti dalle applicazioni in esecuzione in Azure.

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni dettagliate su come inoltrare i dati di monitoraggio a hub eventi di Azure, vedere [inviare dati da Windows Azure Diagnostics Extension a hub](diagnostics-extension-stream-event-hubs.md) eventi.
