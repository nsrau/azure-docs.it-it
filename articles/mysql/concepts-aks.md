---
title: Connettere servizio Kubernetes di Azure con il Database di Azure per MySQL
description: Informazioni sulla connessione del servizio Kubernetes di Azure con il Database di Azure per MySQL
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 11/28/2018
ms.openlocfilehash: 54deae9fcf9fdc786aa917bae518a2177a7acaff
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52577128"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Connessione del servizio Kubernetes di Azure e del Database di Azure per MySQL

Il servizio Kubernetes di Azure fornisce un cluster Kubernetes gestito che è possibile usare in Azure. Di seguito sono riportate alcune opzioni da prendere in considerazione quando si usano insieme il servizio Kubernetes di Azure e il Database di Azure per MySQL per creare un'applicazione.


## <a name="accelerated-networking"></a>Rete accelerata
Usare le macchine virtuali sottostanti abilitate alla rete accelerata nel cluster del servizio Kubernetes di Azure. Quando la funzionalità di rete accelerata è abilitata in una macchina virtuale, si riduce la latenza, l'instabilità e l'utilizzo della CPU nella macchina virtuale. Altre informazioni su come funziona la rete accelerata, le versioni del sistema operativo supportate e le istanze di macchine virtuali supportate per [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Da novembre 2018 il servizio Kubernetes di Azure supporta la funzionalità di rete accelerata nelle istanze delle macchine virtuali supportate. La rete accelerata è abilitata per impostazione predefinita sui nuovi cluster del servizio Kubernetes di Azure che usano queste macchine virtuali.

È possibile verificare se il cluster del servizio Kubernetes di Azure dispone della funzionalità di rete accelerata:
1. Accedere al portale di Azure e selezionare il cluster del servizio Kubernetes di Azure.
2. Selezionare la scheda Proprietà.
3. Copiare il nome del **gruppo di risorse dell'infrastruttura**.
4. Usare la barra di ricerca nel portale per individuare e aprire il gruppo di risorse dell'infrastruttura.
5. Selezionare una macchina virtuale in questo gruppo di risorse.
6. Passare alla scheda**Rete** della macchina virtuale.
7. Verificare che la **rete accelerata** sia abilitata.


## <a name="open-service-broker-for-azure"></a>Open Service Broker for Azure 
[Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) consente di effettuare il provisioning dei servizi di Azure direttamente da Kubernetes o Cloud Foundry. Si tratta di un'implementazione dell'[API di Open Service Broker](https://www.openservicebrokerapi.org/) per Azure.

Con OSBA è possibile creare un'istanza del Database di Azure per MySQL e associarla al cluster del servizio Kubernetes di Azure usando il linguaggio nativo di Kubernetes. Informazioni su come usare OSBA e il Database di Azure per MySQL insieme nella [pagina Github per OSBA](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md). 



## <a name="next-steps"></a>Passaggi successivi
- [Creare un cluster del servizio Kubernetes di Azure](../aks/kubernetes-walkthrough.md)
- Informazioni su come [installare WordPress da un grafico Helm usando OSBA e il Database di Azure per MySQL](../aks/integrate-azure.md)
