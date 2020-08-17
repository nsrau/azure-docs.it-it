---
title: Panoramica della distribuzione - Avere vFXT per Azure
description: Informazioni su come distribuire un cluster vFXT per Azure con questa panoramica. Per gli articoli correlati sono disponibili istruzioni specifiche per la distribuzione.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4c63fdf2164dd4dce12912669eec29c79755cc2a
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271227"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT per Azure - Panoramica della distribuzione

Questo articolo offre una panoramica dei passaggi necessari per attivare un cluster Avere vFXT per Azure.

È necessario eseguire diverse attività prima e dopo la creazione del cluster vFXT da Azure Marketplace. Facendosi una chiara idea del processo dall'inizio alla fine, sarà possibile determinare l'entità del lavoro necessario.

## <a name="deployment-steps"></a>Passaggi di distribuzione

Dopo aver eseguito la [pianificazione del sistema](avere-vfxt-deploy-plan.md), è possibile iniziare a creare il cluster Avere vFXT.

Un modello di Azure Resource Manager in Azure Marketplace raccoglie le informazioni necessarie e distribuisce automaticamente l'intero cluster.

Quando il cluster vFXT è attivo e in esecuzione, è ancora necessario eseguire alcuni passaggi di configurazione prima di usarlo. Se è stato creato un nuovo contenitore di archiviazione BLOB, è opportuno spostarvi i dati. Se si usa un sistema di archiviazione NAS, è necessario aggiungerlo dopo aver creato il cluster. Sarà necessario connettere i client al cluster.

Ecco una panoramica di tutti i passaggi.

1. Configurazione dei prerequisiti

   Prima di creare una macchina virtuale, è necessario creare una nuova sottoscrizione per il progetto Avere vFXT, configurare la proprietà della sottoscrizione, verificare le quote e richiedere un aumento, se necessario, oltre che accettare le condizioni per l'uso del software Avere vFXT. Per istruzioni dettagliate, vedere [Preparare la creazione di Avere vFXT](avere-vfxt-prereqs.md).

1. Creare il cluster Avere vFXT

   Usare Azure Marketplace per creare il cluster Avere vFXT per Azure. Un modello raccoglie le informazioni necessarie ed esegue gli script per creare il prodotto finale.

   La creazione del cluster richiede questi passaggi, che vengono tutti eseguiti dal modello del marketplace:

   * Creare una nuova infrastruttura di rete e gruppi di risorse, se necessario
   * Creare un controller cluster

     Il controller del cluster è una semplice macchina virtuale che si trova nella stessa rete virtuale del cluster Avere vFXT e dispone del software necessario per creare e gestire il cluster. Il controller crea i nodi vFXT e forma il cluster, oltre a fornire un'interfaccia della riga di comando per gestire il cluster durante il suo ciclo di vita.

     Se durante la distribuzione si crea una nuova rete virtuale o una nuova subnet, il controller avrà un indirizzo IP pubblico. Ciò significa che il controller può fungere da Jump host per la connessione al cluster dall'esterno della rete virtuale.

   * Creare le macchine virtuali del nodo del cluster

   * Creare il cluster dai singoli nodi

   * Facoltativamente, creare un nuovo contenitore BLOB e configurarlo come archivio back-end per il cluster

   La creazione del cluster è descritta in dettaglio in [distribuire il cluster vFXT](avere-vfxt-deploy.md).

1. Configurare il cluster

   Connettersi all'interfaccia di configurazione di Avere vFXT (pannello di controllo di Avere) per personalizzare le impostazioni del cluster. Acconsentire esplicitamente al monitoraggio del supporto e aggiungere il sistema di archiviazione se si usa l'archiviazione hardware o contenitori BLOB aggiuntivi.

   * [Accedere al cluster vFXT](avere-vfxt-cluster-gui.md)
   * [Abilitare il supporto](avere-vfxt-enable-support.md)
   * [Configurare l'archiviazione](avere-vfxt-add-storage.md) (se necessario)

1. Montare i client

   Seguire le indicazioni in [Montare il cluster Avere vFXT](avere-vfxt-mount-clients.md) per informazioni sul bilanciamento del carico e sul modo in cui i computer client devono montare il cluster.

1. Aggiungere dati (se necessario)

   Dato che vFXT è una cache multiclient scalabile, il modo migliore per spostare i dati in un nuovo contenitore di archiviazione back-end è la strategia di copia di file multiclient multithread.

   Se è necessario spostare i dati working set in un nuovo contenitore BLOB o in un altro sistema di archiviazione back-end, seguire le istruzioni riportate in [spostamento dei dati nel cluster vFXT](avere-vfxt-data-ingest.md).

## <a name="next-steps"></a>Passaggi successivi

Continuare a [preparare la creazione di vFXT](avere-vfxt-prereqs.md) per completare le attività dei prerequisiti.
