---
title: Estensione macchina virtuale di monitoraggio di Azure per Windows
description: Distribuire l'agente di Log Analytics nella macchina virtuale Windows usando un'estensione macchina virtuale.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2019
ms.author: akjosh
ms.openlocfilehash: c9fd62e57d131fb21e657c53914f9cd5349107ec
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073669"
---
# <a name="azure-monitor-virtual-machine-extension-for-windows"></a>Estensione macchina virtuale di monitoraggio di Azure per Windows

Log di monitoraggio di Azure offre funzionalità di monitoraggio tra risorse cloud e locali. L'estensione macchina virtuale dell'agente di Log Analytics per Windows viene pubblicata e supportata da Microsoft. L'estensione installa l'agente di Log Analytics in macchine virtuali di Azure e registra le macchine virtuali in un'area di lavoro Log Analytics esistente. Questo documento illustra in dettaglio le piattaforme, le configurazioni e le opzioni di distribuzione supportate per l'estensione macchina virtuale di monitoraggio di Azure per Windows.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>prerequisiti

### <a name="operating-system"></a>Sistema operativo

Per informazioni dettagliate sui sistemi operativi Windows supportati, vedere l'articolo [Panoramica dell'agente log Analytics](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Versione dell'estensione della macchina virtuale e dell'agente
Nella tabella seguente viene fornito un mapping della versione dell'estensione della macchina virtuale di monitoraggio di Windows Azure e del bundle di agenti Log Analytics per ogni versione. 

| Log Analytics versione bundle agente Windows | Versione dell'estensione della macchina virtuale Windows di monitoraggio di Azure | Data di rilascio | Note sulla versione |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18011 | 1.0.18011 | Luglio 2019 | <ul><li> Correzioni di bug e miglioramenti della stabilizzazione secondari </li><li> Aumento della MaxExpressionDepth a 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Giugno 2019 | <ul><li> Correzioni di bug e miglioramenti della stabilizzazione secondari </li><li> Aggiunta della possibilità di disabilitare le credenziali predefinite quando si crea una connessione proxy (supporto per WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | Marzo 2019 | <ul><li>Correzioni di stabilizzazione secondarie </li></ul> |
| 10.19.10006 | N/D | 2018 dicembre | <ul><li> Correzioni di stabilizzazione secondarie </li></ul> | 
| 8.0.11136 | N/D | 2018 settembre |  <ul><li> Aggiunta del supporto per il rilevamento della modifica dell'ID risorsa nello spostamento della macchina virtuale </li><li> Aggiunta del supporto per la creazione di report di ID risorsa quando si usa un'installazione non di estensione </li></ul>| 
| 8.0.11103 | N/D |  Aprile 2018 | |
| 8.0.11081 | 1.0.11081 | Nov 2017 | | 
| 8.0.11072 | 1.0.11072 | 2017 settembre | |
| 8.0.11049 | 1.0.11049 | 2017 feb | |

### <a name="azure-security-center"></a>Centro sicurezza di Azure

Il Centro sicurezza di Azure effettua automaticamente il provisioning dell'agente di Log Analytics e lo connette all'area di lavoro predefinita Log Analytics della sottoscrizione di Azure. Se si usa il Centro sicurezza di Azure, non completare i passaggi inclusi in questo documento. In caso contrario, si sovrascriverà l'area di lavoro configurata e si interromperà la connessione al Centro sicurezza di Azure.

### <a name="internet-connectivity"></a>Connettività Internet
L'estensione dell'agente di Log Analytics per Windows richiede che la macchina virtuale di destinazione sia connessa a Internet. 

## <a name="extension-schema"></a>Schema dell'estensione

Il codice JSON seguente mostra lo schema per l'estensione dell'agente di Log Analytics. L'estensione richiede l'ID dell'area di lavoro e la chiave dell'area di lavoro dall'area di lavoro Log Analytics di destinazione Questi valori sono disponibili nelle impostazioni dell'area di lavoro nel portale di Azure. Poiché la chiave dell'area di lavoro deve essere tratta come i dati sensibili, deve essere memorizzata in una configurazione protetta. I dati della configurazione protetta dell'estensione macchina virtuale di Azure sono crittografati e vengono decrittografati solo nella macchina virtuale di destinazione. Tenere presente che **workspaceId** e **workspaceKey** distinguono tra maiuscole e minuscole.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Valori delle proprietà

| Nome | Valore/Esempio |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (esempio)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (esempio) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* Nell'API di Log Analytics, workspaceId è denominato consumerId.

## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. Lo schema JSON presentato nella sezione precedente può essere usato in un modello di Azure Resource Manager per eseguire l'estensione dell'agente di Log Analytics durante la distribuzione di un modello di Azure Resource Manager. Un esempio di modello che include l'estensione macchina virtuale dell'agente di Log Analytics è disponibile nella [raccolta di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>Il modello non supporta la specifica di più di un ID e una chiave dell'area di lavoro quando si desidera configurare l'agente per la segnalazione a più aree di lavoro. Per configurare l'agente per la segnalazione a più aree di lavoro, vedere [aggiunta o rimozione di un'area di lavoro](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

Il codice JSON per un'estensione della macchina virtuale può essere nidificato nella risorsa della macchina virtuale o posizionato nel livello radice o nel livello superiore di un modello JSON di Resource Manager. Il posizionamento di JSON influisce sul valore del nome e tipo di risorsa. Per altre informazioni, vedere [Set name and type for child resources](../../azure-resource-manager/child-resource-name-type.md) (Impostare il nome e il tipo per le risorse figlio). 

Nell'esempio seguente si presuppone che l'estensione di monitoraggio di Azure sia annidata all'interno della risorsa della macchina virtuale. Quando la risorsa di estensione viene nidificata, JSON viene inserito nell'oggetto `"resources": []` della macchina virtuale.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Quando si posiziona l'estensione JSON nella radice del modello, il nome della risorsa include un riferimento alla macchina virtuale padre e il tipo riflette la configurazione annidata. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>Distribuzione PowerShell

È possibile usare il comando `Set-AzVMExtension` per distribuire l'estensione macchina virtuale dell'agente di Log Analytics in una macchina virtuale esistente. Prima di eseguire il comando, le configurazioni pubbliche e private devono essere archiviate in una tabella hash di PowerShell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere i problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite il modulo Azure PowerShell. Per visualizzare lo stato di distribuzione delle estensioni per una determinata VM, eseguire questo comando nel modulo Azure PowerShell.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

L'output dell'esecuzione dell'estensione viene registrato nei file presenti nella directory seguente:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare Ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).
