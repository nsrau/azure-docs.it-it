---
title: Panoramica della distribuzione - Avere vFXT per Azure
description: Panoramica della distribuzione di Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 88305e5d877f5bc3cc7ebb116f69b0f49f162b79
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59268382"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT per Azure - Panoramica della distribuzione

Questo articolo offre una panoramica dei passaggi necessari per attivare un cluster Avere vFXT per Azure.

È necessario eseguire diverse attività prima e dopo la creazione del cluster vFXT da Azure Marketplace. Facendosi una chiara idea del processo dall'inizio alla fine, sarà possibile determinare l'entità del lavoro necessario. 

## <a name="deployment-steps"></a>Passaggi di distribuzione

Dopo aver eseguito la [pianificazione del sistema](avere-vfxt-deploy-plan.md), è possibile iniziare a creare il cluster Avere vFXT. 

Un modello di Azure Resource Manager in Azure Marketplace raccoglie le informazioni necessarie e distribuisce automaticamente l'intero cluster. 

Quando il cluster vFXT è operativo, sarà necessario sapere come connettere i client e, se necessario, come spostare i dati nel nuovo contenitore di archiviazione BLOB.  

Ecco una panoramica di tutti i passaggi.

1. Configurazione dei prerequisiti 

   Prima di creare una macchina virtuale, è necessario creare una nuova sottoscrizione per il progetto Avere vFXT, configurare la proprietà della sottoscrizione, verificare le quote e richiedere un aumento, se necessario, oltre che accettare le condizioni per l'uso del software Avere vFXT. Per istruzioni dettagliate, vedere [Preparare la creazione di Avere vFXT](avere-vfxt-prereqs.md).

1. Creare il cluster Avere vFXT 

   Usare Azure Marketplace per creare il cluster Avere vFXT per Azure. Un modello raccoglie le informazioni necessarie ed esegue gli script per creare il prodotto finale.

   La creazione del cluster richiede questi passaggi, che vengono tutti eseguiti dal modello del marketplace: 

   * Creazione di una nuova infrastruttura di rete e di nuovi gruppi di risorse, se necessario
   * Creazione di un *controller del cluster*  

     Il controller del cluster è una semplice macchina virtuale che si trova nella stessa rete virtuale del cluster Avere vFXT e dispone del software necessario per creare e gestire il cluster. Il controller crea i nodi vFXT e forma il cluster, oltre a fornire un'interfaccia della riga di comando per gestire il cluster durante il suo ciclo di vita.

     Se si crea una nuova rete virtuale durante la distribuzione, il controller avrà un indirizzo IP pubblico. Ciò significa che il controller può essere utilizzato come un host di salto per la connessione al cluster dall'esterno della rete virtuale.

   * Creazione delle macchine virtuali del nodo del cluster

   * Configurazione delle macchine virtuali per formare il cluster del nodo cluster

   * Facoltativamente, creazione di un nuovo contenitore BLOB e configurazione di tale contenitore come risorsa di archiviazione back-end per il cluster

1. Configurare il cluster 

   Connettersi all'interfaccia di configurazione di Avere vFXT (pannello di controllo di Avere) per personalizzare le impostazioni del cluster. Acconsentire esplicitamente al monitoraggio del personale di supporto e aggiungere il sistema di archiviazione, se si usa un data center locale.

   * [Accedere al cluster vFXT](avere-vfxt-cluster-gui.md)
   * [Abilitare il supporto](avere-vfxt-enable-support.md)
   * [Configurare l'archiviazione](avere-vfxt-add-storage.md) (se necessario)

1. Montare i client

   Seguire le indicazioni in [Montare il cluster Avere vFXT](avere-vfxt-mount-clients.md) per informazioni sul bilanciamento del carico e sul modo in cui i computer client devono montare il cluster.

1. Aggiungere dati (se necessario)

   Poiché Avere vFXT è una cache multi-client scalabile, il modo migliore per spostare i dati in un nuovo contenitore di archiviazione back-end consiste nell'usare una strategia di copia di file multi-client a thread multipli. Per informazioni dettagliate, vedere [Spostamento dei dati nel cluster vFXT](avere-vfxt-data-ingest.md).

## <a name="next-steps"></a>Passaggi successivi

Continuare con [Preparare la creazione di Avere vFXT](avere-vfxt-prereqs.md) per completare le attività preliminari per la distribuzione di Avere vFXT in Azure. 