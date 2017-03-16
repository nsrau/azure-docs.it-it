---
title: "Disponibilità e scalabilità nei modelli di Azure Resource Manager | Microsoft Docs"
description: Macchine virtuali di Azure - Esercitazione DotNet Core
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8fcfea79-f017-4658-8c51-74242fcfb7f6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 1c8f8a958a6ea6ea096d4a55479879aab7c7e96d
ms.lasthandoff: 03/03/2017


---
# <a name="availability-and-scale-in-azure-resource-manager-templates-for-linux-vms"></a>Disponibilità e scalabilità nei modelli di Azure Resource Manager per macchine virtuali Linux

La disponibilità e la scalabilità sono correlate al tempo di attività e alla possibilità di soddisfare la domanda. Se un'applicazione deve essere attiva durante il 99,9% del tempo, deve avere un'architettura che consente l'esecuzione di più risorse di calcolo simultanee. Ad esempio, anziché avere un singolo sito Web, una configurazione con un livello di disponibilità più elevato include più istanze dello stesso sito, con tecnologia di bilanciamento del carico. In questa configurazione, una singola istanza dell'applicazione può essere arrestata per manutenzione, mentre le altre continuano a funzionare. La scalabilità fa riferimento alla capacità delle applicazioni di soddisfare la domanda. Nel caso di un'applicazione con carico bilanciato, l'aggiunta o la rimozione di istanze dal pool consente a un'applicazione di ridimensionare le risorse in base alla domanda.

Questo documento descrive in che modo è configurata la distribuzione di esempio di Music Store per la disponibilità e la scalabilità. Tutte le dipendenze e le configurazioni univoche sono evidenziate. Per ottenere risultati ottimali, pre-distribuire un'istanza della soluzione alla propria sottoscrizione di Azure ed esercitarsi con il modello di Azure Resource Manager. Il modello completo è disponibile in [Music Store Deployment on Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)(Distribuzione di Music Store in Ubuntu).

## <a name="availability-set"></a>Set di disponibilità
Un set di disponibilità si estende logicamente sulle macchine virtuali di Azure attraverso host fisici e altri componenti dell'infrastruttura, ad esempio alimentatori e hardware di rete fisica. I set di disponibilità assicurano che non tutte le macchine virtuali siano interessate da attività di manutenzione, errori dei dispositivi o altri tempi di inattività. È possibile aggiungere un set di disponibilità a un modello di Azure Resource Manager usando la procedura guidata Aggiungi nuova risorsa di Visual Studio o inserendo una risorsa JSON valida nel modello.

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Set di disponibilità](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L387).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/availabilitySets",
  "name": "[variables('availabilitySetName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "avalibility-set"
  },
  "properties": {
    "platformUpdateDomainCount": 5,
    "platformFaultDomainCount": 3
  }
}
```

Un set di disponibilità viene dichiarato come proprietà di una risorsa di macchina virtuale. 

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Associazione del set di disponibilità con la macchina virtuale](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L313).

```json
"properties": {
  "availabilitySet": {
    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
  }
```
Di seguito è illustrato il set di disponibilità come visualizzato nel portale di Azure. Nell'immagine sono riportate le singole macchine virtuali con i relativi dettagli di configurazione.

![Set di disponibilità](./media/virtual-machines-linux-dotnet-core/aset.png)

Per informazioni approfondite sui set di disponibilità, vedere [Gestione della disponibilità delle macchine virtuali](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

## <a name="network-load-balancer"></a>Servizio di bilanciamento del carico
Se da un lato un set di disponibilità garantisce la tolleranza di errore dell'applicazione, dall'altro un servizio di bilanciamento del carico rende disponibili numerose istanze dell'applicazione su un singolo indirizzo di rete. Più istanze di un'applicazione possono essere ospitate su numerose macchine virtuali, ciascuna connessa a un servizio di bilanciamento del carico. Quando viene inviata una richiesta di accesso all'applicazione, il servizio di bilanciamento del carico instrada la richiesta in ingresso attraverso i membri collegati. È possibile aggiungere un servizio di bilanciamento del carico usando la procedura guidata Aggiungi nuova risorsa di Visual Studio o inserendo una risorsa JSON formattata correttamente nel modello di Azure Resource Manager.

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Servizio di bilanciamento del carico di rete](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L208).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers",
  "name": "[variables('loadBalancerName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer-front"
  },
  ........<truncated>
}
```

Poiché l'applicazione di esempio viene esposta a Internet con un indirizzo IP pubblico, questo indirizzo è associato al servizio di bilanciamento del carico. 

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Associazione del servizio di bilanciamento del carico con l'indirizzo IP pubblico](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L221).

```json
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicipaddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

Nel portale di Azure le informazioni generali sul servizio di bilanciamento del carico di rete mostrano l'associazione con l'indirizzo IP pubblico.

![Servizio di bilanciamento del carico](./media/virtual-machines-linux-dotnet-core/nlb.png)

## <a name="load-balancer-rule"></a>Regola del servizio di bilanciamento del carico
Quando si usa un servizio di bilanciamento del carico, sono configurate delle regole che controllano il modo in cui il traffico viene bilanciato tra le risorse previste. Nel caso dell'applicazione Music Store di esempio, il traffico arriva sulla porta 80 dell'indirizzo IP pubblico e viene distribuito attraverso la porta 80 di tutte le macchine virtuali. 

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Regola del servizio di bilanciamento del carico](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L270).

```json
"loadBalancingRules": [
  {
    "name": "[variables('loadBalencerRule')]",
    "properties": {
      "frontendIPConfiguration": {
        "id": "[concat(resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName')), '/frontendIPConfigurations/LoadBalancerFrontend')]"
      },
      "backendAddressPool": {
        "id": "[variables('lbPoolID')]"
      },
      "protocol": "Tcp",
      "frontendPort": 80,
      "backendPort": 80,
      "enableFloatingIP": false,
      "idleTimeoutInMinutes": 5,
      "probe": {
        "id": "[variables('lbProbeID')]"
      }
    }
  }
]
```

Di seguito è illustrata la regola del servizio di bilanciamento del carico di rete come visualizzata nel portale.

![Regola del servizio di bilanciamento del carico di rete](./media/virtual-machines-linux-dotnet-core/lbrule.png)

## <a name="load-balancer-probe"></a>Probe del servizio di bilanciamento del carico
Il servizio di bilanciamento del carico deve anche monitorare ogni macchina virtuale in modo da soddisfare solo le richieste dei sistemi in esecuzione. Questo monitoraggio avviene tramite l'esecuzione costante del probe su una porta predefinita. La distribuzione di Music Store è configurata in modo da eseguire il probe sulla porta 80 di tutte le macchine virtuali incluse. 

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Probe del servizio di bilanciamento del carico](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L257).

```json
"probes": [
  {
    "properties": {
      "protocol": "Tcp",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 2
    },
    "name": "lbprobe"
  }
]
```

Di seguito è illustrato il probe del servizio di bilanciamento del carico come visualizzato nel portale di Azure.

![Probe del servizio di bilanciamento del carico di rete](./media/virtual-machines-linux-dotnet-core/lbprobe.png)

## <a name="inbound-nat-rules"></a>Regole NAT in ingresso
Quando si usa un servizio di bilanciamento del carico, devono essere configurate delle regole che consentono l'accesso senza carico bilanciato a ogni macchina virtuale. Ad esempio, quando si crea una connessione SSH con ogni macchina virtuale, il carico del traffico non deve essere bilanciato, ma è necessario che sia configurato un percorso predeterminato. I percorsi di questo tipo vengono configurati mediante una risorsa di regola NAT in ingresso. Usando questa risorsa, le comunicazioni in ingresso possono essere mappate alle singole macchine virtuali. 

Nel caso dell'applicazione Music Store, una porta identificata da un numero a partire da 5000 viene mappata alla porta 22 di ogni macchina virtuale per l'accesso SSH. Per incrementare il numero della porta in ingresso viene usata la funzione `copyindex()` , in modo che la seconda macchina virtuale riceva una porta in ingresso 5001, la terza una 5002 e così via. 

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Regole NAT in ingresso](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L270). 

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers/inboundNatRules",
  "name": "[concat(variables('loadBalancerName'), '/', 'SSH-VM', copyIndex())]",
  "tags": {
    "displayName": "load-balancer-nat-rule"
  },
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "lbNatLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
  ],
  "properties": {
    "frontendIPConfiguration": {
      "id": "[variables('ipConfigID')]"
    },
    "protocol": "tcp",
    "frontendPort": "[copyIndex(5000)]",
    "backendPort": 22,
    "enableFloatingIP": false
  }
}
```

Di seguito è illustrato un esempio di regola NAT in ingresso come visualizzata nel portale di Azure. Per ogni macchina virtuale nella distribuzione viene creata una regola NAT SSH.

![Regola NAT in ingresso](./media/virtual-machines-linux-dotnet-core/natrule.png)

Per informazioni approfondite sul servizio di bilanciamento del carico di rete di Azure, vedere [Bilanciamento del carico per i servizi di infrastruttura di Azure](virtual-machines-linux-load-balance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="deploy-multiple-vms"></a>Distribuire più macchine virtuali
Per il funzionamento efficiente di un set di disponibilità o di un servizio di bilanciamento del carico, sono necessarie più macchine virtuali. Più macchine virtuali possono essere distribuite tramite la funzione copy del modello di Azure Resource Manager. Usando la funzione copy, non è necessario definire un numero finito di macchine virtuali, ma questo valore può essere fornito in modo dinamico in fase di distribuzione. La funzione copy utilizza il numero di istanze create e gestisce la distribuzione del numero appropriato di macchine virtuali e di risorse associate.

Nel modello di esempio di Music Store è definito un parametro che accetta un determinato numero di istanze. Questo numero viene usato nell'intero modello durante la creazione delle macchine virtuali e delle risorse associate.

```json
"numberOfInstances": {
  "type": "int",
  "minValue": 1,
  "defaultValue": 1,
  "metadata": {
    "description": "Number of VM instances to be created behind load balancer."
  }
}
```

Sulla risorsa della macchina virtuale, al ciclo copy vengono assegnati un nome e il valore del parametro relativo al numero di istanze usato per controllare il numero di copie risultanti.

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Funzione copy delle macchine virtuali](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L300). 

```json
"apiVersion": "2015-06-15",
"type": "Microsoft.Compute/virtualMachines",
"name": "[concat(variables('vmName'),copyindex())]",
"location": "[resourceGroup().location]",
"copy": {
  "name": "virtualMachineLoop",
  "count": "[parameters('numberOfInstances')]"
}
```

L'iterazione corrente della funzione copy è accessibile con la funzione `copyIndex()` . Il valore della funzione copyIndex può essere usato per assegnare un nome alle macchine virtuali e ad altre risorse. Ad esempio, se vengono distribuite due istanze di una macchina virtuale, queste devono avere nomi diversi. La funzione `copyIndex()` può essere usata come parte del nome della macchina virtuale per creare un nome univoco. Un esempio d'uso della funzione `copyindex()` a scopo di denominazione è visibile nella risorsa della macchina virtuale. In questo caso, il nome del computer è il risultato di una concatenazione del parametro `vmName` e della funzione `copyIndex()`. 

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Funzione copyIndex](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L319). 

```json
"osProfile": {
  "computerName": "[concat(parameters('vmName'),copyindex())]",
  "adminUsername": "[parameters('adminUsername')]",
  "linuxConfiguration": {
    "disablePasswordAuthentication": "true",
    "ssh": {
      "publicKeys": [
        {
          "path": "[variables('sshKeyPath')]",
          "keyData": "[parameters('sshKeyData')]"
        }
      ]
    }
  }
}
```

La funzione `copyIndex` viene usata più volte nel modello di esempio di Music Store. Le risorse e le funzioni che usano `copyIndex` includono dati specifici di una singola istanza della macchina virtuale, come l'interfaccia di rete, le regole del servizio di bilanciamento del carico e altri elementi, a seconda della funzione. 

Per altre informazioni sulla funzione copy, vedere [Creare più istanze di risorse in Azure Resource Manager](../azure-resource-manager/resource-group-create-multiple.md).

## <a name="next-step"></a>Passaggio successivo
<hr>

[Passaggio 4: Distribuzione di applicazioni con i modelli di Azure Resource Manager](virtual-machines-linux-dotnet-core-5-app-deployment.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


