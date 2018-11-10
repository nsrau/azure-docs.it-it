---
title: Panoramica della distribuzione - Avere vFXT per Azure
description: Panoramica della distribuzione di Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: aa5737d67ea2c9cb8cc7c7098764ae67fc91137d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669818"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT per Azure - Panoramica della distribuzione

Questo articolo offre una panoramica dei passaggi necessari per attivare un cluster Avere vFXT per Azure.

La prima volta che si distribuisce un sistema Avere vFXT, si noterà che sono necessari più passaggi rispetto alla distribuzione della maggior parte degli altri strumenti di Azure. Facendosi una chiara idea del processo dall'inizio alla fine, sarà possibile determinare l'entità del lavoro necessario. Quando il sistema è in esecuzione, la sua capacità di accelerare le attività di calcolo basate sul cloud giustificherà il lavoro richiesto.

## <a name="deployment-steps"></a>Passaggi di distribuzione

Dopo aver eseguito la [pianificazione del sistema](avere-vfxt-deploy-plan.md), è possibile iniziare a creare il cluster Avere vFXT. 

Iniziare creando un macchina virtuale controller del cluster, da usare per creare il cluster vFXT.

Quando il cluster vFXT è operativo, sarà necessario sapere come connettere i client e, se necessario, come spostare i dati nel nuovo contenitore di archiviazione BLOB.  

Ecco una panoramica di tutti i passaggi.

1. Configurazione dei prerequisiti 

   Prima di creare una macchina virtuale, è necessario creare una nuova sottoscrizione per il progetto Avere vFXT, configurare la proprietà della sottoscrizione, verificare le quote e richiedere un aumento, se necessario, oltre che accettare le condizioni per l'uso del software Avere vFXT. Per istruzioni dettagliate, vedere [Preparare la creazione di Avere vFXT](avere-vfxt-prereqs.md).

1. Creare il controller del cluster

   Il *controller del cluster* è una semplice macchina virtuale che si trova nella stessa rete virtuale del cluster Avere vFXT. Il controller crea i nodi vFXT e forma il cluster, oltre a fornire un'interfaccia della riga di comando per gestire il cluster durante il suo ciclo di vita.

   Se si configura il controller con un indirizzo IP pubblico, può fungere anche da host di collegamento per la connessione al cluster dall'esterno della rete virtuale.

   Tutto il software necessario per creare il cluster vFXT e gestire i relativi nodi è preinstallato nel controller del cluster.

   Per informazioni dettagliate, vedere [Creare la macchina virtuale controller del cluster](avere-vfxt-deploy.md#create-the-cluster-controller-vm).

1. Creare un ruolo di runtime per i nodi del cluster 

   Azure usa il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/) per autorizzare le macchine virtuali del nodo del cluster a eseguire determinate attività. Ad esempio, i nodi del cluster devono essere in grado di assegnare o riassegnare gli indirizzi IP ad altri nodi del cluster. Prima di creare il cluster, è necessario definire un ruolo che fornisca le autorizzazioni adeguate.

   Il software preinstallato del controller del cluster include un ruolo prototipo che è possibile personalizzare. Per istruzioni, vedere [Creare il ruolo di accesso dei nodi del cluster](avere-vfxt-deploy.md#create-the-cluster-node-access-role).

1. Creare il cluster Avere vFXT 

   Nel controller modificare lo script di creazione del cluster appropriato ed eseguirlo per creare il cluster. In [Modificare lo script di distribuzione](avere-vfxt-deploy.md#edit-the-deployment-script) sono disponibili istruzioni dettagliate. 

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