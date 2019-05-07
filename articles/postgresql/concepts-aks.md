---
title: Connettere Azure Kubernetes Service (AKS) con il Database di Azure per PostgreSQL - Server singolo
description: Altre informazioni sulla connessione di Azure Kubernetes Service con Database di Azure per PostgreSQL - Server singolo
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.date: 5/6/2019
ms.topic: conceptual
ms.openlocfilehash: a98d9b89db0406d67d1b067c3e53eb5c3dae7957
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068951"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>La connessione di Azure Kubernetes Service e il Database di Azure per PostgreSQL - Server singolo

Il servizio Azure Kubernetes fornisce un cluster Kubernetes gestito che è possibile usare in Azure. Di seguito sono riportate alcune opzioni da prendere in considerazione quando si usano insieme il servizio Azure Kubernetes e Database di Azure per PostgreSQL per creare un'applicazione.


## <a name="accelerated-networking"></a>Rete accelerata
Usare le macchine virtuali sottostanti abilitate alla rete accelerata nel cluster del servizio Azure Kubernetes. Quando la funzionalità di rete accelerata è abilitata in una macchina virtuale, si riduce la latenza, l'instabilità e l'utilizzo della CPU nella macchina virtuale. Sono disponibili altre informazioni su come funziona la rete accelerata, sulle versioni del sistema operativo supportate e sulle istanze di macchine virtuali supportate per [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Da novembre 2018 il servizio Azure Kubernetes supporta la funzionalità di rete accelerata nelle istanze delle macchine virtuali supportate. La rete accelerata è abilitata per impostazione predefinita nei nuovi cluster del servizio Azure Kubernetes che usano queste macchine virtuali.

È possibile verificare se il cluster del servizio Azure Kubernetes ha la funzionalità di rete accelerata:
1. Accedere al portale di Azure e selezionare il cluster del servizio Azure Kubernetes.
2. Selezionare la scheda Proprietà.
3. Copiare il nome del **gruppo di risorse dell'infrastruttura**.
4. Usare la barra di ricerca nel portale per individuare e aprire il gruppo di risorse dell'infrastruttura.
5. Selezionare una macchina virtuale in questo gruppo di risorse.
6. Passare alla scheda**Rete** della macchina virtuale.
7. Verificare che la **rete accelerata** sia abilitata.

In alternativa, eseguire i due comandi seguenti tramite l'interfaccia della riga di comando di Azure:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
Come output verrà restituito il gruppo di risorse generato dal servizio Azure Kubernetes contenente l'interfaccia di rete. Prendere nota del nome "nodeResourceGroup" e usarlo nel comando successivo. **EnableAcceleratedNetworking** sarà true o false:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Open Service Broker for Azure 
[Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) consente di effettuare il provisioning dei servizi di Azure direttamente da Kubernetes o Cloud Foundry. Si tratta di un'implementazione dell'[API di Open Service Broker](https://www.openservicebrokerapi.org/) per Azure.

Con OSBA è possibile creare un server di Database di Azure per PostgreSQL e associarlo al cluster del servizio Azure Kubernetes usando il linguaggio nativo di Kubernetes. Per informazioni su come usare OSBA e Database di Azure per PostgreSQL insieme, vedere la [pagina GitHub per OSBA](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md). 


## <a name="connection-pooling"></a>Pooling di connessioni
Un'utilità per il pooling di connessioni riduce al minimo i costi e i tempi associati alla creazione e alla chiusura di nuove connessioni al database. Il pool è una raccolta di connessioni che possono essere riutilizzate. 

Esistono più utilità per il pooling di connessioni che è possibile usare con PostgreSQL. Una di queste è [PgBouncer](https://pgbouncer.github.io/). Nel Registro Container Microsoft viene fornita un'istanza leggera inclusa in contenitori di PgBouncer, che può essere usata in un contenitore collaterale per raggruppare le connessioni dal servizio Azure Kubernetes a Database di Azure per PostgreSQL. Per informazioni su come accedere a questa immagine e su come usarla, vedere la [pagina dell'hub Docker](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/). 


## <a name="next-steps"></a>Passaggi successivi
-  [Creare un cluster del servizio Azure Kubernetes](../aks/kubernetes-walkthrough.md)
