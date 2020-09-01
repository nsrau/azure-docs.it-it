---
title: Installare e configurare l'estensione Diagnostica di Azure per Windows
description: Informazioni su come raccogliere i dati di diagnostica di Azure in un account di Archiviazione di Azure per visualizzarli con uno dei diversi strumenti disponibili.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: ac087a7ba241534c08c4e5737973861727ab01ca
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069579"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Installare e configurare l'estensione Diagnostica di Azure per Windows
L'[estensione Diagnostica di Azure](diagnostics-extension-overview.md) è un agente di Monitoraggio di Azure che raccoglie i dati di monitoraggio dal sistema operativo guest e i carichi di lavoro delle macchine virtuali di Azure e di altre risorse di calcolo. Questo articolo include informazioni dettagliate sull'installazione e la configurazione dell'estensione Diagnostica Windows e una descrizione della modalità di archiviazione dei dati in un account di Archiviazione di Azure.

L'estensione Diagnostica viene implementata come [estensione macchina virtuale](../../virtual-machines/extensions/overview.md) in Azure, pertanto supporta le stesse opzioni di installazione usando modelli di Gestione risorse, PowerShell e l'interfaccia della riga di comando. Per informazioni dettagliate sull'installazione e la gestione delle estensioni macchina virtuale, vedere [Estensioni e funzionalità della macchina virtuale per Windows](../../virtual-machines/extensions/features-windows.md).

## <a name="overview"></a>Panoramica
Quando si configura l'estensione Diagnostica di Azure è necessario specificare un account di archiviazione in cui verranno inviati tutti i dati specificati. Facoltativamente, è possibile aggiungere uno o più *sink di dati* per inviare i dati a percorsi diversi.

- Sink Monitoraggio di Azure: inviare i dati delle prestazioni guest alle metriche di Monitoraggio di Azure.
- Sink di Hub eventi: inviare i dati di log e prestazioni guest agli hub eventi di Azure per l'inoltro all'esterno di Azure. Non è possibile configurare questo sink nel portale di Azure.


## <a name="install-with-azure-portal"></a>Eseguire l'installazione con il portale di Azure
È possibile installare e configurare l'estensione Diagnostica in una singola macchina virtuale nel portale di Azure, che offre un'interfaccia ed evita di dover lavorare direttamente con la configurazione. Quando si abilita l'estensione Diagnostica, viene usata automaticamente una configurazione predefinita con i contatori delle prestazioni e gli eventi più comuni. È possibile modificare questa configurazione predefinita in base ai requisiti specifici.

> [!NOTE]
> Di seguito vengono descritte le impostazioni più comuni per l'estensione Diagnostica. Per informazioni dettagliate su tutte le opzioni di configurazione, vedere [Schema dell'estensione Diagnostica per Windows](diagnostics-extension-schema-windows.md).

1. Aprire il menu di una macchina virtuale nel portale di Azure.

2. Fare clic su **Impostazioni di diagnostica** nella sezione **Monitoraggio** del menu della macchina virtuale.

3. Fare clic su **Abilita monitoraggio a livello di guest** se l'estensione di diagnostica non è già stata abilitata.

   ![Abilitare il monitoraggio](media/diagnostics-extension-windows-install/enable-monitoring.png)

4. Verrà creato un nuovo account di Archiviazione di Azure per la macchina virtuale, con un nome basato sul nome del gruppo di risorse della macchina virtuale, e verrà selezionato un set predefinito di log e contatori delle prestazioni guest.

   ![Impostazioni di diagnostica](media/diagnostics-extension-windows-install/diagnostic-settings.png)

5. Nella scheda **Contatori prestazioni** selezionare le metriche guest che si vuole raccogliere da questa macchina virtuale. Usare l'impostazione **Personalizzate** per una selezione più dettagliata.

   ![Contatori delle prestazioni](media/diagnostics-extension-windows-install/performance-counters.png)

6. Nella scheda **Log** selezionare i log da raccogliere dalla macchina virtuale. I log possono essere inviati a risorse di archiviazione o hub eventi, ma non a Monitoraggio di Azure. Usare l'[agente Log Analytics](log-analytics-agent.md) per raccogliere i log guest in Monitoraggio di Azure.

   ![Log](media/diagnostics-extension-windows-install/logs.png)

7. Nella scheda **Dump di arresto anomalo** specificare i processi per la raccolta di dump di memoria dopo un arresto anomalo del sistema. I dati verranno scritti nell'account di archiviazione definito dall'impostazione di diagnostica, ed è possibile specificare facoltativamente un contenitore BLOB.

   ![Dump di arresto anomalo](media/diagnostics-extension-windows-install/crash-dumps.png)

8. Nella scheda **Sink** specificare se inviare i dati a percorsi diversi dall'archiviazione di Azure. Se si seleziona **Monitoraggio di Azure**, i dati sulle prestazioni guest verranno inviati alle metriche di Monitoraggio di Azure. Non è possibile configurare il sink degli hub eventi usando il portale di Azure.

   ![Sink](media/diagnostics-extension-windows-install/sinks.png)
   
   Se non è stata abilitata un'identità assegnata dal sistema per la macchina virtuale, è possibile che venga visualizzato l'avviso seguente quando si salva una configurazione con il sink di Monitoraggio di Azure. Fare clic sul banner per abilitare l'identità assegnata dal sistema.
   
   ![Entità gestita](media/diagnostics-extension-windows-install/managed-entity.png)

9. In **Agente** è possibile modificare l'account di archiviazione, impostare la quota del disco e specificare se verranno raccolti i log dell'infrastruttura di diagnostica.  

   ![Agente](media/diagnostics-extension-windows-install/agent.png)

10. Fare clic su **Salva** per salvare la configurazione. 

> [!NOTE]
> La configurazione per l'estensione di diagnostica può essere salvata con il formato JSON o XML, ma qualsiasi configurazione eseguita nel portale di Azure verrà sempre salvata con il formato JSON. Se si usa XML con un altro metodo di configurazione e quindi si modifica la configurazione con il portale di Azure, le impostazioni verranno convertite in JSON.

## <a name="resource-manager-template"></a>Modello di Resource Manager
Vedere [Usare monitoraggio e diagnostica con una macchina virtuale Windows e modelli di Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md) per informazioni sulla distribuzione dell'estensione di diagnostica con i modelli di Azure Resource Manager. 

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure
L'interfaccia della riga di comando di Azure può essere usata per distribuire l'estensione Diagnostica di Azure a una macchina virtuale esistente usando [az vm extension set](/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set), come nell'esempio seguente. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

Le impostazioni protette sono definite nell'[elemento PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element) dello schema di configurazione. Di seguito è riportato un esempio minimo di un file di impostazioni protette che definisce l'account di archiviazione. Per informazioni complete sulle impostazioni private, vedere [Configurazione di esempio](diagnostics-extension-schema-windows.md#privateconfig-element).

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```

Le impostazioni pubbliche sono definite nell'[elemento PublicConfig](diagnostics-extension-schema-windows.md#publicconfig-element) dello schema di configurazione. Di seguito è riportato un esempio minimale di file di impostazioni pubbliche che abilita la raccolta di log dell'infrastruttura di diagnostica, un singolo contatore delle prestazioni e un singolo registro eventi. Per informazioni complete sulle impostazioni pubbliche, vedere [Configurazione di esempio](diagnostics-extension-schema-windows.md#publicconfig-element).

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
PowerShell può essere usato per distribuire l'estensione Diagnostica di Azure a una macchina virtuale esistente usando [Set-AzVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension), come nell'esempio seguente. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

Le impostazioni private sono definite nell'[elemento PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element), mentre le impostazioni pubbliche sono definite nell'[elemento PublicConfig](diagnostics-extension-schema-windows.md#publicconfig-element) dello schema di configurazione. È anche possibile scegliere di specificare i dettagli dell'account di archiviazione come parametri del cmdlet Set-AzVMDiagnosticsExtension, anziché includerli nelle impostazioni private.

Di seguito è riportato un esempio minimale di file di configurazione che abilita la raccolta di log dell'infrastruttura di diagnostica, un singolo contatore delle prestazioni e un singolo registro eventi. Per informazioni complete sulle impostazioni pubbliche e private, vedere [Configurazione di esempio](diagnostics-extension-schema-windows.md#publicconfig-element). 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000,
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
La tabella seguente elenca i diversi tipi di dati raccolti dall'estensione di diagnostica e indica se vengono archiviati come una tabella o un BLOB. I dati archiviati nelle tabelle possono anche essere archiviati in BLOB a seconda dell'impostazione [StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) nella configurazione pubblica.


| Data | Tipo di archiviazione | Descrizione |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabella | Monitor di diagnostica e modifiche alla configurazione. |
| WADDirectoriesTable | Tabella | Directory monitorate dal monitor di diagnostica.  Sono inclusi i log di IIS, i log delle richieste non riuscite di IIS e le directory personalizzate.  La posizione del file di log dei BLOB è specificata nel campo Container e il nome del BLOB si trova nel campo RelativePath.  Il campo AbsolutePath indica la posizione e il nome del file esistente nella macchina virtuale di Azure. |
| WadLogsTable | Tabella | Log scritti nel codice con il listener di traccia. |
| WADPerformanceCountersTable | Tabella | Contatori delle prestazioni. |
| WADWindowsEventLogsTable | Tabella | Log eventi di Windows. |
| wad-iis-failedreqlogfiles | BLOB | Contiene informazioni dei log delle richieste non riuscite di IIS. |
| wad-iis-logfiles | BLOB | Contiene le informazioni sui log di IIS. |
| "custom" | BLOB | Contenitore personalizzato basato sulle directory di configurazione monitorate dal monitor di diagnostica.  Il nome di questo contenitore BLOB verrà specificato in WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Strumenti per visualizzare i dati di diagnostica
Sono disponibili diversi strumenti per visualizzare i dati una volta trasferiti nell'account di archiviazione. Ad esempio:

* Esplora server in Visual Studio: se sono stati installati gli Strumenti di Azure per Microsoft Visual Studio, è possibile usare il nodo Archiviazione di Azure in Esplora server per visualizzare i dati di tabelle e BLOB di sola lettura dagli account di archiviazione di Azure. È possibile visualizzare i dati dall'account dell'emulatore di archiviazione locale e anche dagli account di archiviazione creati per Azure. Per altre informazioni, vedere [Esplorazione e gestione delle risorse di archiviazione con Esplora server](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma che consente di usare facilmente dati di Archiviazione di Azure in Windows, OSX e Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) include Azure Diagnostics Manager che consente di visualizzare, scaricare e gestire i dati di diagnostica raccolti dalle applicazioni in esecuzione in Azure.

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni dettagliate su come inoltrare i dati di monitoraggio a hub eventi di Azure, vedere [Inviare dati dall'estensione diagnostica di Microsoft Azure a hub eventi di Azure](diagnostics-extension-stream-event-hubs.md).
