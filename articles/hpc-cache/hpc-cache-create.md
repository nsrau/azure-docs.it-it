---
title: Creare una cache HPC di Azure
description: Come creare un'istanza di Cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: efa9037b345cdfc5f165e9c5e0c1831ea97b52ed
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106492"
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

La capacità influisce anche sul costo della cache.

Scegliere la capacità impostando questi due valori:

* La velocità massima di trasferimento dei dati per la cache (velocità effettiva), in GB al secondo
* La quantità di spazio di archiviazione allocata per i dati memorizzati nella cache, in TB

Scegliere uno dei valori disponibili per la velocità effettiva e le dimensioni di archiviazione della cache.

Tenere presente che la velocità effettiva di trasferimento dei dati dipende dal carico di lavoro, dalle velocità di rete e dal tipo di destinazioni di archiviazione. I valori scelti impostano la velocità effettiva massima per l'intero sistema di cache, ma questa viene in parte usata per attività di overhead. Se, ad esempio, un client richiede un file che non è già archiviato nella cache o se il file è contrassegnato come obsoleto, la cache usa parte della velocità effettiva per recuperarla dall'archiviazione back-end.

Cache HPC di Azure determina quali file vengono memorizzati nella cache e precaricati per massimizzare le percentuali di riscontri nella cache. Il contenuto della cache viene valutato continuamente e i file vengono spostati nello spazio di archiviazione a lungo termine se vi si accede con minore frequenza. Scegliere una dimensione di archiviazione della cache in grado di ospitare comodamente il set attivo di file di lavoro con spazio aggiuntivo per i metadati e altro overhead.

![screenshot della pagina di determinazione delle dimensioni della cache](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Abilitare la crittografia Azure Key Vault (facoltativo)

Se la cache si trova in un'area che supporta le chiavi di crittografia gestite dal cliente, viene visualizzata la pagina **chiavi di crittografia del disco** tra le schede **cache** e **tag** . Al momento della pubblicazione, questa opzione è supportata negli Stati Uniti orientali, negli Stati Uniti centro-meridionali e negli Stati Uniti occidentali 2.

Se si vogliono gestire le chiavi di crittografia usate con l'archiviazione della cache, fornire le informazioni Azure Key Vault nella pagina **chiavi di crittografia del disco** . L'insieme di credenziali delle chiavi deve trovarsi nella stessa area e nella stessa sottoscrizione della cache.

È possibile ignorare questa sezione se non sono necessarie chiavi gestite dal cliente. Per impostazione predefinita, Azure crittografa i dati con chiavi gestite da Microsoft. Per altre informazioni, vedere [crittografia di archiviazione di Azure](../storage/common/storage-service-encryption.md) .

> [!NOTE]
>
> * Dopo aver creato la cache, non è possibile modificare le chiavi gestite da Microsoft e le chiavi gestite dal cliente.
> * Dopo aver creato la cache, è necessario autorizzarla ad accedere all'insieme di credenziali delle chiavi. Fare clic sul pulsante **Abilita crittografia** nella pagina **Panoramica** della cache per attivare la crittografia. Eseguire questo passaggio entro 90 minuti dalla creazione della cache.
> * I dischi della cache vengono creati dopo questa autorizzazione. Ciò significa che il tempo di creazione della cache iniziale è breve, ma la cache non sarà pronta per dieci minuti o più dopo l'autorizzazione dell'accesso.

Per una spiegazione completa del processo di crittografia della chiave gestita dal cliente, vedere [usare le chiavi di crittografia gestite dal cliente per la cache HPC di Azure](customer-keys.md).

![screenshot della pagina delle chiavi di crittografia con i campi selezionati "Customer Managed" e Key Vault visualizzati](media/create-encryption.png)

Selezionare **cliente gestito** per scegliere crittografia chiave gestita dal cliente. Vengono visualizzati i campi delle specifiche di Key Vault. Selezionare il Azure Key Vault da usare, quindi selezionare la chiave e la versione da usare per la cache. La chiave deve essere una chiave RSA a 2048 bit. Da questa pagina è possibile creare un nuovo insieme di credenziali delle chiavi, una chiave o una versione della chiave.

Dopo aver creato la cache, è necessario autorizzarla a usare il servizio Key Vault. Per informazioni dettagliate, vedere [autorizzare Azure Key Vault crittografia dalla cache](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) .

## <a name="add-resource-tags-optional"></a>Aggiungere tag di risorsa (facoltativo)

La pagina **Tag** consente di aggiungere [tag di risorsa](https://go.microsoft.com/fwlink/?linkid=873112) all'istanza di Cache HPC di Azure.

## <a name="finish-creating-the-cache"></a>Completare la creazione della cache

Dopo aver configurato la nuova cache, fare clic sulla scheda **Verifica + crea** . Il portale convalida le selezioni e consente di esaminare le scelte effettuate. Se tutte le voci sono state impostate correttamente, fare clic su **Crea**.

La creazione della cache richiede circa 10 minuti. È possibile monitorare l'avanzamento dell'operazione nel pannello delle notifiche del portale di Azure.

![screenshot delle pagine "distribuzione in corso" e "notifiche" della creazione della cache nel portale](media/hpc-cache-deploy-status.png)

Al termine dell'operazione di creazione, viene visualizzata una notifica con un collegamento alla nuova istanza di Cache HPC di Azure e la cache viene visualizzata nell'elenco **Risorse** della sottoscrizione.

![screenshot dell'istanza di Cache HPC di Azure nel portale di Azure](media/hpc-cache-new-overview.png)

> [!NOTE]
> Se la cache USA chiavi di crittografia gestite dal cliente, la cache potrebbe essere visualizzata nell'elenco delle risorse prima che lo stato della distribuzione venga modificato in completato. Non appena lo stato della cache è **in attesa della chiave** , è possibile [autorizzarlo](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) a usare l'insieme di credenziali delle chiavi.

## <a name="next-steps"></a>Passaggi successivi

Dopo che la cache è stata visualizzata nell'elenco **delle risorse** , è possibile passare al passaggio successivo.

* [Definire le destinazioni di archiviazione](hpc-cache-add-storage.md) per concedere l'accesso alla cache alle origini dati.
* Se si usano chiavi di crittografia gestite dal cliente, è necessario [autorizzare Azure Key Vault crittografia](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) dalla pagina Panoramica della cache per completare la configurazione della cache. Prima di aggiungere spazio di archiviazione, è necessario eseguire questo passaggio. Per informazioni dettagliate, vedere [usare chiavi di crittografia gestite dal cliente](customer-keys.md) .
