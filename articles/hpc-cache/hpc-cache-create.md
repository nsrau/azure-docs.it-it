---
title: Creare una cache HPC di Azure
description: Come creare un'istanza di Cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: e1b69f17d964647944f23f4d16a0a1a5f112b60d
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036989"
---
# <a name="create-an-azure-hpc-cache"></a>Creare una cache HPC di Azure

Usare il portale di Azure per creare la cache.

![screenshot della panoramica della cache nel portale di Azure, con il pulsante Crea nella parte inferiore](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definire i dettagli di base

![screenshot della pagina dei dettagli del progetto nel portale di Azure](media/hpc-cache-create-basics.png)

In **Dettagli progetto** selezionare la sottoscrizione e il gruppo di risorse in cui verrà ospitata la cache HPC di Azure. Assicurarsi che la sottoscrizione sia presente nell'elenco di [accesso all'anteprima](hpc-cache-prereqs.md#azure-subscription).

In **Dettagli servizio** impostare il nome della cache e questi altri attributi:

* Località: selezionare una delle [aree supportate](hpc-cache-overview.md#region-availability).
* Rete virtuale: è possibile selezionare una rete virtuale esistente oppure crearne una nuova.
* Subnet: scegliere o creare una subnet con almeno 64 indirizzi IP (/24) che verranno usati solo per la cache HPC di Azure.

## <a name="set-cache-capacity"></a>Impostare la capacità della cache
<!-- change link in GUI -->

Nella pagina **Cache** è necessario impostare la capacità della cache HPC di Azure. Questo valore determina la quantità di dati che può essere mantenuta nella cache e la velocità con cui la cache può soddisfare le richieste client. Dopo il periodo di anteprima pubblica, la capacità influirà anche sul costo della cache.

La capacità della cache viene misurata in operazioni di I/O al secondo. Scegliere la capacità impostando questi due valori:

* La velocità massima di trasferimento dei dati per la cache (velocità effettiva), in GB al secondo
* La quantità di spazio di archiviazione allocata per i dati memorizzati nella cache, in TB

Scegliere uno dei valori disponibili per la velocità effettiva e le dimensioni di archiviazione della cache. La capacità in operazioni di I/O al secondo viene calcolata e mostrata sotto i selettori dei valori.

Tenere presente che la velocità effettiva di trasferimento dei dati dipende dal carico di lavoro, dalle velocità di rete e dal tipo di destinazioni di archiviazione. Se un file non si trova nella cache o è contrassegnato come obsoleto, il servizio userà parte della velocità effettiva per recuperarlo dallo spazio di archiviazione back-end. Il valore scelto imposta la velocità effettiva massima per l'intera cache e non è tutta disponibile per tutte le richieste client.

Per l'archiviazione nella cache, Cache HPC di Azure determina quali file vengono memorizzati nella cache e precaricati per massimizzare le percentuali di riscontri nella cache. Il contenuto della cache viene valutato continuamente e i file vengono spostati nello spazio di archiviazione a lungo termine se vi si accede con minore frequenza. Scegliere una dimensione di archiviazione della cache in grado di ospitare comodamente il set attivo di file di lavoro con spazio aggiuntivo per i metadati e altro overhead.

![screenshot della pagina di determinazione delle dimensioni della cache](media/hpc-cache-create-iops.png)

## <a name="add-storage-targets"></a>Aggiungere destinazioni di archiviazione

Le destinazioni di archiviazione costituiscono lo spazio di archiviazione back-end a lungo termine per il contenuto della cache.

È possibile definire le destinazioni di archiviazione durante la creazione della cache, ma è anche possibile aggiungerle successivamente con il collegamento nella sezione **Configura** della pagina della cache nel portale.

![screenshot della pagina delle destinazioni di archiviazione](media/hpc-cache-storage-targets-pop.png)

Fare clic sul collegamento **Add storage target** (Aggiungi destinazione archiviazione) per definire i sistemi di archiviazione back-end. Lo spazio di archiviazione può essere costituito da contenitori BLOB di Azure o da sistemi NFS locali.

È possibile definire fino a dieci destinazioni di archiviazione diverse.

Le istruzioni dettagliate sull'aggiunta di una destinazione di archiviazione sono incluse in [Aggiungere spazio di archiviazione](hpc-cache-add-storage.md). La procedura è diversa a seconda che si tratti dell'archiviazione BLOB o di esportazioni NFS.

Ecco alcuni suggerimenti: 

* Per entrambi i tipi di archiviazione, è necessario specificare come trovare il sistema di archiviazione back-end (un indirizzo NFS o il nome di un contenitore BLOB) e il percorso dello spazio dei nomi lato client.

* Quando si crea una destinazione di archiviazione BLOB, assicurarsi che la cache disponga delle autorizzazioni di accesso all'account di archiviazione, come descritto in [Aggiungere i ruoli di controllo di accesso](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account). Se non si è certi che la configurazione del ruolo abbia esito positivo, creare prima la cache e quindi aggiungere l'archivio BLOB in un secondo momento.

* Quando si crea una destinazione di archiviazione NFS, specificare un [modello di utilizzo](hpc-cache-add-storage.md#choose-a-usage-model). L'impostazione di tale modello consente alla cache di ottimizzare il flusso di lavoro.

## <a name="add-resource-tags-optional"></a>Aggiungere tag di risorsa (facoltativo)

La pagina **Tag** consente di aggiungere [tag di risorsa](https://go.microsoft.com/fwlink/?linkid=873112) alla cache HPC di Azure. 

## <a name="finish-creating-the-cache"></a>Completare la creazione della cache

Dopo aver configurato la nuova cache, fare clic sulla scheda **Rivedi e crea**. Il portale convalida le selezioni e consente di esaminare le scelte effettuate. Se tutte le voci sono state impostate correttamente, fare clic su **Crea**. 

La creazione della cache richiede circa 10 minuti. È possibile monitorare l'avanzamento dell'operazione nel pannello delle notifiche del portale di Azure. 

![screenshot delle pagine "distribuzione in corso" e "notifiche" della creazione della cache nel portale](media/hpc-cache-deploy-status.png)

Al termine dell'operazione di creazione, viene visualizzata una notifica con un collegamento alla nuova istanza di Cache HPC di Azure e la cache viene visualizzata nell'elenco **Risorse** della sottoscrizione. 

![screenshot dell'istanza di Cache HPC di Azure nel portale di Azure](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo che la cache viene visualizzata nell'elenco **Risorse**, è possibile montarla per l'accesso client, usarla per spostare i dati del set di lavoro in una nuova destinazione di archiviazione BLOB di Azure o definire origini dati aggiuntive.

* [Montare la cache HPC di Azure](hpc-cache-mount.md)
* [Spostare i dati nell'archivio BLOB di Azure per la cache HPC di Azure](hpc-cache-ingest.md)
* [Aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md)
