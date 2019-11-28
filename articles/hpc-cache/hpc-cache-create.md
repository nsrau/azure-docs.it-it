---
title: Creare una cache HPC di Azure
description: Come creare un'istanza di Cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: rohogue
ms.openlocfilehash: 07aba1b1536635e414fc5fab4ece148683909188
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168643"
---
# <a name="create-an-azure-hpc-cache"></a>Creare una cache HPC di Azure

Usare il portale di Azure per creare la cache.

![screenshot della panoramica della cache nel portale di Azure, con il pulsante Crea nella parte inferiore](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definire i dettagli di base

![screenshot della pagina dei dettagli del progetto nel portale di Azure](media/hpc-cache-create-basics.png)

In **Dettagli progetto** selezionare la sottoscrizione e il gruppo di risorse in cui verrà ospitata la cache. Assicurarsi che la sottoscrizione sia presente nell'elenco di [accesso](hpc-cache-prereqs.md#azure-subscription).

In **Dettagli servizio** impostare il nome della cache e questi altri attributi:

* Località: selezionare una delle [aree supportate](hpc-cache-overview.md#region-availability).
* Rete virtuale: è possibile selezionare una rete virtuale esistente oppure crearne una nuova.
* Subnet: scegliere o creare una subnet con almeno 64 indirizzi IP (/24) che verranno usati solo per questa istanza di Cache HPC di Azure.

## <a name="set-cache-capacity"></a>Impostare la capacità della cache
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Nella pagina **Cache** è necessario impostare la capacità della cache. Il valore qui impostato determina la quantità di dati che la cache può contenere e la velocità con cui può gestire le richieste client.

Dopo il periodo di anteprima pubblica, la capacità influirà anche sul costo della cache.

Scegliere la capacità impostando questi due valori:

* La velocità massima di trasferimento dei dati per la cache (velocità effettiva), in GB al secondo
* La quantità di spazio di archiviazione allocata per i dati memorizzati nella cache, in TB

Scegliere uno dei valori disponibili per la velocità effettiva e le dimensioni di archiviazione della cache.

Tenere presente che la velocità effettiva di trasferimento dei dati dipende dal carico di lavoro, dalle velocità di rete e dal tipo di destinazioni di archiviazione. I valori scelti impostano la velocità effettiva massima per l'intero sistema di cache, ma questa viene in parte usata per attività di overhead. Se, ad esempio, un client richiede un file che non è già archiviato nella cache o se il file è contrassegnato come obsoleto, la cache usa parte della velocità effettiva per recuperarla dall'archiviazione back-end.

Cache HPC di Azure determina quali file vengono memorizzati nella cache e precaricati per massimizzare le percentuali di riscontri nella cache. Il contenuto della cache viene valutato continuamente e i file vengono spostati nello spazio di archiviazione a lungo termine se vi si accede con minore frequenza. Scegliere una dimensione di archiviazione della cache in grado di ospitare comodamente il set attivo di file di lavoro con spazio aggiuntivo per i metadati e altro overhead.

![screenshot della pagina di determinazione delle dimensioni della cache](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>Aggiungere tag di risorsa (facoltativo)

La pagina **Tag** consente di aggiungere [tag di risorsa](https://go.microsoft.com/fwlink/?linkid=873112) all'istanza di Cache HPC di Azure.

## <a name="finish-creating-the-cache"></a>Completare la creazione della cache

Dopo aver configurato la nuova cache, fare clic sulla scheda **Rivedi e crea**. Il portale convalida le selezioni e consente di esaminare le scelte effettuate. Se tutte le voci sono state impostate correttamente, fare clic su **Crea**.

La creazione della cache richiede circa 10 minuti. È possibile monitorare l'avanzamento dell'operazione nel pannello delle notifiche del portale di Azure.

![screenshot delle pagine "distribuzione in corso" e "notifiche" della creazione della cache nel portale](media/hpc-cache-deploy-status.png)

Al termine dell'operazione di creazione, viene visualizzata una notifica con un collegamento alla nuova istanza di Cache HPC di Azure e la cache viene visualizzata nell'elenco **Risorse** della sottoscrizione.
<!-- double check on notification -->

![screenshot dell'istanza di Cache HPC di Azure nel portale di Azure](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Passaggi successivi

Quando la cache viene visualizzata nell'elenco **Risorse**, definire le destinazioni di archiviazione per consentire alla cache di accedere alle origini dati.

* [Aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md)
