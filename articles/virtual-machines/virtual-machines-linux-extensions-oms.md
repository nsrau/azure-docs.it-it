---
title: Estensione macchina virtuale di Azure OMS per Linux | Documentazione Microsoft
description: Distribuire l&quot;agente OMS in una macchina virtuale Linux usando un&quot;estensione macchina virtuale.
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 9f27890e52cb7a9a0d46f2bb84bfe92f7c6fff37
ms.openlocfilehash: 9864314956d79317785c1c2a4bc87621bc6a3e2d


---
# <a name="oms-virtual-machine-extension-for-linux"></a>Estensione macchina virtuale OMS per Linux

## <a name="overview"></a>Panoramica

In Operations Management Suite (OMS) sono disponibili funzionalità di monitoraggio, avviso e correzione tramite avvisi in risorse cloud e locali. L'estensione macchina virtuale Agente OMS per Linux è pubblicata e supportata da Microsoft. L'estensione installa l'agente OMS in macchine virtuali di Azure e registra le macchine virtuali in un'area di lavoro OMS esistente. Questo documento descrive in dettaglio le piattaforme, le configurazioni e le opzioni di distribuzione supportate per l'estensione macchina virtuale OMS per Linux.

Per informazioni generali sulle estensioni macchina virtuale di Azure, vedere la [panoramica sulle estensioni macchina virtuale](./virtual-machines-linux-extensions-features.md).

Per altre informazioni su Operations Management Suite, vedere la [panoramica su Operations Management Suite](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite).

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

L'estensione agente OMS può essere eseguita in queste distribuzioni di Linux.

| Distribuzione | Versione |
|---|---|
| CentOS Linux | 5, 6 e 7 |
| Oracle Linux | 5, 6 e 7 |
| Red Hat Enterprise Linux Server | 5, 6 e 7 |
| Debian GNU/Linux | 6, 7 e 8 |
| Ubuntu | 12.04 LTS, 14.04 LTS, 15.04 |
| SUSE Linux Enterprise Server | 11 e 12 |

### <a name="connectivity"></a>Connettività

Per distribuire l'estensione agente OMS per Linux, è necessario che la macchina virtuale di destinazione sia connessa a Internet. 

## <a name="extension-configuration"></a>Configurazione dell'estensione

Per l'estensione macchina virtuale Agente OMS per Linux sono necessari un ID e una chiave dell'area di lavoro OMS di destinazione. Poiché la chiave dell'area di lavoro deve essere considerata in modo analogo ai dati sensibili, viene archiviata in una configurazione protetta. I dati della configurazione protetta dell'estensione macchina virtuale di Azure vengono crittografati e decrittografati solo nella macchina virtuale di destinazione. Le configurazioni pubbliche e private vengono specificate in fase di distribuzione, operazione descritta in dettaglio nelle sezioni successive di questo documento.

### <a name="public-configuration"></a>Configurazione pubblica

Schema per la configurazione pubblica:

- workspaceId: (necessario, stringa) ID dell'area di lavoro OMS in cui caricare la macchina virtuale.

```json
{
  "workspaceId": "myWorkspaceId"
}
```

### <a name="private-configuration"></a>Configurazione privata

Schema per la configurazione pubblica:

- workspaceKey: (necessario, stringa) chiave condivisa primaria/secondaria dell'area di lavoro.

```json
{
  "workspaceKey": "myWorkSpaceKey"
}
```

## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. I modelli rappresentano la scelta migliore quando si distribuiscono una o più macchine virtuali per cui è necessaria una configurazione post-distribuzione, ad esempio il caricamento in OMS. Un esempio di modello di Resource Manager che include l'estensione macchina virtuale Agente OMS è disponibile nella [raccolta di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

Questo esempio può essere distribuito dal presente documento usando il pulsante:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-oms-extension-ubuntu-vm%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

Il codice JSON usato per distribuire l'estensione macchina virtuale Agente OMS è analogo al seguente esempio JSON:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<extension-deployment-name>",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.0",
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

L'interfaccia della riga di comando di Azure può essere usata per distribuire l'estensione macchina virtuale Agente OMS in una macchina virtuale esistente. Prima di distribuire l'estensione Agente OMS, creare un file public.json e protected.json. Lo schema per questi file è illustrato nei dettagli in precedenza in questo documento.

```azurecli
azure vm extension set <resource-group> <vm-name> \
  OmsAgentForLinux Microsoft.EnterpriseCloud.Monitoring 1.0 \
  --public-config-path public.json  \
  --private-config-path protected.json
```

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risoluzione dei problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite l'interfaccia della riga di comando di Azure. Per visualizzare lo stato di distribuzione delle estensioni per una determinata VM, eseguire il comando seguente nell'interfaccia della riga di comando di Azure.

```azurecli
azure vm extension get myResourceGroup myVM
```

L'output dell'esecuzione dell'estensione viene registrato nel file seguente:

`
/opt/microsoft/omsagent/bin/stdout
`

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/en-us/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/en-us/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/en-us/support/faq/).



<!--HONumber=Dec16_HO1-->


