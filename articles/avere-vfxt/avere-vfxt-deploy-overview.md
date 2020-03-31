---
title: Panoramica della distribuzione - Avere vFXT per Azure
description: Panoramica della distribuzione di Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78140fea74272dff6056bebfbd44ed9d55b0e1db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153684"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT per Azure - Panoramica della distribuzione

Questo articolo offre una panoramica dei passaggi necessari per attivare un cluster Avere vFXT per Azure.

È necessario eseguire diverse attività prima e dopo la creazione del cluster vFXT da Azure Marketplace. Facendosi una chiara idea del processo dall'inizio alla fine, sarà possibile determinare l'entità del lavoro necessario.

## <a name="deployment-steps"></a>Passaggi di distribuzione

Dopo aver eseguito la [pianificazione del sistema](avere-vfxt-deploy-plan.md), è possibile iniziare a creare il cluster Avere vFXT.

Un modello di Azure Resource Manager in Azure Marketplace raccoglie le informazioni necessarie e distribuisce automaticamente l'intero cluster.

Dopo che il cluster vFXT è attivo e in esecuzione, è ancora necessario eseguire alcuni passaggi di configurazione prima di utilizzarlo. Se è stato creato un nuovo contenitore di archiviazione BLOB, è consigliabile spostare i dati in esso. Se si utilizza un sistema di archiviazione NAS, è necessario aggiungerlo dopo aver creato il cluster. Si desidera connettere i client al cluster.

Ecco una panoramica di tutti i passaggi.

1. Configurazione dei prerequisiti

   Prima di creare una macchina virtuale, è necessario creare una nuova sottoscrizione per il progetto Avere vFXT, configurare la proprietà della sottoscrizione, verificare le quote e richiedere un aumento, se necessario, oltre che accettare le condizioni per l'uso del software Avere vFXT. Per istruzioni dettagliate, vedere [Preparare la creazione di Avere vFXT](avere-vfxt-prereqs.md).

1. Creare il cluster Avere vFXT

   Usare Azure Marketplace per creare il cluster Avere vFXT per Azure. Un modello raccoglie le informazioni necessarie ed esegue gli script per creare il prodotto finale.

   La creazione del cluster richiede questi passaggi, che vengono tutti eseguiti dal modello del marketplace:

   * Creare una nuova infrastruttura di rete e gruppi di risorse, se necessarioCreate new network infrastructure and resource groups, if needed
   * Creare un controller clusterCreate a cluster controller

     Il controller del cluster è una semplice macchina virtuale che si trova nella stessa rete virtuale del cluster Avere vFXT e dispone del software necessario per creare e gestire il cluster. Il controller crea i nodi vFXT e forma il cluster, oltre a fornire un'interfaccia della riga di comando per gestire il cluster durante il suo ciclo di vita.

     Se si crea una nuova rete virtuale o una nuova subnet durante la distribuzione, il controller diverrà un indirizzo IP pubblico. Ciò significa che il controller può fungere da host di salto per la connessione al cluster dall'esterno della rete virtuale.

   * Creare le macchine virtuali del nodo del clusterCreate the cluster node VMs

   * Creare il cluster dai singoli nodiCreate the cluster from the individual nodes

   * Facoltativamente, creare un nuovo contenitore BLOB e configurarlo come archivio back-end per il clusterOptionally, create a new Blob container and configure it as back-end storage for the cluster

   La creazione del cluster è descritta in dettaglio in [Deploy the vFXT cluster](avere-vfxt-deploy.md).

1. Configurare il cluster

   Connettersi all'interfaccia di configurazione di Avere vFXT (pannello di controllo di Avere) per personalizzare le impostazioni del cluster. Accettare l'opzione per il monitoraggio del supporto e aggiungere il sistema di archiviazione se si usa l'archiviazione hardware o altri contenitori BLOB.

   * [Accedere al cluster vFXT](avere-vfxt-cluster-gui.md)
   * [Abilitare il supporto](avere-vfxt-enable-support.md)
   * [Configurare l'archiviazione](avere-vfxt-add-storage.md) (se necessario)

1. Montare i client

   Seguire le indicazioni in [Montare il cluster Avere vFXT](avere-vfxt-mount-clients.md) per informazioni sul bilanciamento del carico e sul modo in cui i computer client devono montare il cluster.

1. Aggiungere dati (se necessario)

   Poiché Avere vFXT è una cache multi-client scalabile, il modo migliore per spostare i dati in un nuovo contenitore di archiviazione back-end è con una strategia di copia di file multi-client multithread.

   Se è necessario spostare i dati del working set in un nuovo contenitore BLOB o in un altro sistema di archiviazione back-end, seguire le istruzioni in [Spostamento dei dati nel cluster vFXT](avere-vfxt-data-ingest.md).

## <a name="next-steps"></a>Passaggi successivi

Continuare a [preparare per creare Avere vFXT](avere-vfxt-prereqs.md) per completare le attività preliminari.
