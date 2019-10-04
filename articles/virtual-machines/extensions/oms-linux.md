---
title: Estensione macchina virtuale di monitoraggio di Azure per Linux | Microsoft Docs
description: Distribuire l'agente di Log Analytics nella macchina virtuale Linux usando un'estensione macchina virtuale.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: akjosh
ms.openlocfilehash: 95b630342ac2b4bc9cf51f3aa3d8563c4962ce11
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168946"
---
# <a name="azure-monitor-virtual-machine-extension-for-linux"></a>Estensione macchina virtuale di monitoraggio di Azure per Linux

## <a name="overview"></a>Panoramica

Log di monitoraggio di Azure offre funzionalità di monitoraggio, avviso e correzione degli avvisi in risorse cloud e locali. L'estensione macchina virtuale agente di Log Analytics per Linux è pubblicata e supportata da Microsoft. L'estensione installa l'agente di Log Analytics in macchine virtuali di Azure e registra le macchine virtuali in un'area di lavoro Log Analytics esistente. Questo documento illustra in dettaglio le piattaforme, le configurazioni e le opzioni di distribuzione supportate per l'estensione macchina virtuale di monitoraggio di Azure per Linux.

>[!NOTE]
>Nel quadro della transizione in corso da Microsoft Operations Management Suite (OMS) a Monitoraggio di Azure, l'Agente OMS per Windows o Linux verrà indicato come agente di Log Analytics per Windows e agente di Log Analytics per Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

Per informazioni dettagliate sulle distribuzioni Linux supportate, vedere l'articolo [Panoramica dell'agente log Analytics](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Versione dell'estensione della macchina virtuale e dell'agente
La tabella seguente fornisce un mapping della versione dell'estensione della macchina virtuale di monitoraggio di Azure e del bundle degli agenti Log Analytics per ogni versione. È incluso un collegamento alle note sulla versione per la versione bundle agenti di Log Analytics. Le note sulla versione includono i dettagli sulle correzioni di bug e sulle nuove funzionalità disponibili per una determinata versione degli agenti.  

| Versione dell'estensione della macchina virtuale Linux di monitoraggio di Azure | Versione bundle agenti Log Analytics | 
|--------------------------------|--------------------------|
| 1.11.15 | [1.11.0-9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Centro sicurezza di Azure

Il Centro sicurezza di Azure effettua automaticamente il provisioning dell'agente di Log Analytics e lo connette a un'area di lavoro Log Analytics predefinita creata dal Centro sicurezza di Azure nella sottoscrizione di Azure. Se si usa il Centro sicurezza di Azure, non completare i passaggi inclusi in questo documento. In caso contrario, si sovrascriverà l'area di lavoro configurata e si interromperà la connessione al Centro sicurezza di Azure.

### <a name="internet-connectivity"></a>Connettività Internet

Per distribuire l'estensione agente di Log Analytics per Linux, è necessario che la macchina virtuale di destinazione sia connessa a Internet. 

## <a name="extension-schema"></a>Schema dell'estensione

Il codice JSON riportato di seguito mostra lo schema dell'estensione agente di Log Analytics. L'estensione richiede che siano indicati l'ID e la chiave dell'area di lavoro presenti nell'area di lavoro Log Analytics di destinazione. Questi valori sono [disponibili nell'area di lavoro Log Analytics](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) nel portale di Azure. Poiché la chiave dell'area di lavoro deve essere tratta come i dati sensibili, deve essere memorizzata in una configurazione protetta. I dati della configurazione protetta dell'estensione macchina virtuale di Azure vengono crittografati, per essere poi decrittografati solo nella macchina virtuale di destinazione. Tenere presente che **workspaceId** e **workspaceKey** distinguono tra maiuscole e minuscole.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>Lo schema indicato sopra presuppone che deve essere inserito a livello radice del modello. Se è stato inserito all'interno della risorsa macchina virtuale nel modello, le proprietà `type` e `name` devono essere modificate, come descritto [più avanti](#template-deployment).
>

### <a name="property-values"></a>Valori delle proprietà

| Attività | Valore/Esempio |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| workspaceId (esempio) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (esempio) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Distribuzione modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. I modelli sono ideali quando si distribuiscono una o più macchine virtuali che richiedono la configurazione post-distribuzione, ad esempio l'onboarding nei log di monitoraggio di Azure. Un modello di Gestione risorse di esempio che include l'estensione della macchina virtuale Log Analytics Agent è reperibile nella [raccolta di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

La configurazione JSON per un'estensione macchina virtuale può essere annidata nella risorsa della macchina virtuale o posizionata nel livello radice o nel livello superiore di un modello JSON di Gestione risorse. Il posizionamento della configurazione JSON influisce sul valore del nome e del tipo di risorsa. Per altre informazioni, vedere [Set name and type for child resources](../../azure-resource-manager/child-resource-name-type.md) (Impostare il nome e il tipo per le risorse figlio). 

L'esempio seguente presuppone che l'estensione macchina virtuale sia annidata all'interno della risorsa della macchina virtuale. Quando la risorsa di estensione viene nidificata, JSON viene inserito nell'oggetto `"resources": []` della macchina virtuale.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Quando si posiziona l'estensione JSON nella radice del modello, il nome della risorsa include un riferimento alla macchina virtuale padre e il tipo riflette la configurazione annidata.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure può essere usata per distribuire l'estensione macchina virtuale agente di Log Analytics in una macchina virtuale esistente. Sostituire *workspaceId* e *workspaceKey* con quelli dall'area di lavoro Log Analytics. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.10.1 --protected-settings '{"workspaceKey":"omskey"}' \
  --settings '{"workspaceId":"omsid"}'
```

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite l'interfaccia della riga di comando di Azure. Per visualizzare lo stato di distribuzione delle estensioni per una determinata VM, eseguire il comando seguente nell'interfaccia della riga di comando di Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

L'output dell'esecuzione dell'estensione viene registrato nel file seguente:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Codici di errore e relativi significati

| Codice errore | Significato | Azione possibile |
| :---: | --- | --- |
| 9 | Chiamata Enable anomala | [Aggiornare l'agente Linux di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) all'ultima versione disponibile. |
| 10 | La macchina virtuale è già connessa a un'area di lavoro Log Analytics | Per connettere la macchina virtuale all'area di lavoro specificata nello schema dell'estensione, impostare stopOnMultipleConnections su false nelle impostazioni pubbliche o rimuovere questa proprietà. Questa macchina virtuale viene fatturata una volta per ogni area di lavoro a cui è connessa. |
| 11 | Configurazione non valida generata per l'estensione | Seguire l'esempio precedente per impostare tutti i valori della proprietà necessari alla distribuzione. |
| 17 | Errore di installazione del pacchetto di Log Analytics | 
| 19 | Errore di installazione del pacchetto OMI | 
| 20 | Errore di installazione del pacchetto SCX |
| 51 | Questa estensione non è supportata sul sistema operativo della macchina virtuale | |
| 55 | Non è possibile connettersi al servizio di monitoraggio di Azure o ai pacchetti richiesti mancanti o la gestione pacchetti dpkg è bloccata| Verificare che il sistema disponga dell'accesso a Internet o che sia stato fornito un proxy HTTP valido. Inoltre, verificare la correttezza dell'ID dell'area di lavoro e verificare che le utilità curl e tar siano installate. |

Altre informazioni sulla risoluzione dei problemi sono disponibili nella [Guida alla risoluzione dei problemi relativi all'agente di Log Analytics per Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).
