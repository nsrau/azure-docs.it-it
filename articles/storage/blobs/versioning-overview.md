---
title: Controllo delle versioni dei BLOB
titleSuffix: Azure Storage
description: Il controllo delle versioni dell'archiviazione BLOB mantiene automaticamente le versioni precedenti di un oggetto e le identifica con i timestamp. È possibile ripristinare le versioni precedenti di un BLOB per ripristinare i dati se vengono erroneamente modificati o eliminati.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 191213511a6b41e3a8419660a40b8d79a5c747f2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91714935"
---
# <a name="blob-versioning"></a>Controllo delle versioni dei BLOB

È possibile abilitare il controllo delle versioni dell'archiviazione BLOB per gestire automaticamente le versioni precedenti di un oggetto.  Quando è abilitata la funzionalità di controllo delle versioni dei BLOB, è possibile ripristinare una versione precedente di un BLOB per ripristinare i dati se vengono erroneamente modificati o eliminati.

Il controllo delle versioni dei BLOB è abilitato nell'account di archiviazione e si applica a tutti i BLOB nell'account di archiviazione. Dopo aver abilitato il controllo delle versioni dei BLOB per un account di archiviazione, archiviazione di Azure mantiene automaticamente le versioni per ogni BLOB nell'account di archiviazione.

Microsoft consiglia di usare il controllo delle versioni dei BLOB per mantenere le versioni precedenti di un BLOB per la protezione dei dati superiore. Quando possibile, usare il controllo delle versioni dei BLOB anziché gli snapshot BLOB per gestire le versioni precedenti. Gli snapshot BLOB forniscono funzionalità simili in quanto mantengono le versioni precedenti di un BLOB, ma gli snapshot devono essere gestiti manualmente dall'applicazione.

Per informazioni su come abilitare il controllo delle versioni dei BLOB, vedere [abilitare e gestire il controllo delle versioni dei BLOB](versioning-enable.md).

> [!IMPORTANT]
> Il controllo delle versioni dei BLOB non consente di eseguire il ripristino dall'eliminazione accidentale di un contenitore o di un account di archiviazione. Per evitare l'eliminazione accidentale dell'account di archiviazione, configurare un blocco **CannotDelete** sulla risorsa dell'account di archiviazione. Per altre informazioni sul blocco delle risorse di Azure, vedere [bloccare le risorse per impedire modifiche impreviste](../../azure-resource-manager/management/lock-resources.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-blob-versioning-works"></a>Funzionamento del controllo delle versioni dei BLOB

Una versione acquisisce lo stato di un BLOB in un determinato momento. Quando è abilitata la funzionalità di controllo delle versioni dei BLOB per un account di archiviazione, archiviazione di Azure crea automaticamente una nuova versione di un BLOB ogni volta che il BLOB viene modificato o eliminato.

Quando si crea un BLOB con il controllo delle versioni abilitato, il nuovo BLOB è la versione corrente del BLOB (o il BLOB di base). Se successivamente si modifica il BLOB, archiviazione di Azure crea una versione che acquisisce lo stato del BLOB prima della modifica. Il BLOB modificato diventa la nuova versione corrente. Viene creata una nuova versione ogni volta che si modifica il BLOB.

Quando si elimina un BLOB con il controllo delle versioni abilitato, archiviazione di Azure crea una versione che acquisisce lo stato del BLOB prima dell'eliminazione. La versione corrente del BLOB viene quindi eliminata, ma le versioni del BLOB vengono mantenute, in modo che possa essere ricreata, se necessario. 

Le versioni BLOB non sono modificabili. Non è possibile modificare il contenuto o i metadati di una versione BLOB esistente.

Il controllo delle versioni dei BLOB è disponibile per gli account per utilizzo generico V2, BLOB in blocchi e archiviazione BLOB. Gli account di archiviazione con uno spazio dei nomi gerarchico abilitato per l'uso con Azure Data Lake Storage Gen2 non sono attualmente supportati.

La versione 2019-10-10 e successive dell'API REST di archiviazione di Azure supporta il controllo delle versioni dei BLOB.

### <a name="version-id"></a>ID versione

Ogni versione del BLOB è identificata da un ID versione. Il valore dell'ID versione è il timestamp in corrispondenza del quale il BLOB è stato scritto o aggiornato. L'ID versione viene assegnato al momento della creazione della versione.

È possibile eseguire operazioni di lettura o eliminazione su una versione specifica di un BLOB specificando il relativo ID versione. Se si omette l'ID versione, l'operazione viene eseguita sulla versione corrente (il BLOB di base).

Quando si chiama un'operazione di scrittura per creare o modificare un BLOB, archiviazione di Azure restituisce l'intestazione *x-ms-version-ID* nella risposta. Questa intestazione contiene l'ID versione per la versione corrente del blob creato dall'operazione di scrittura.

L'ID versione rimane invariato per la durata della versione.

### <a name="versioning-on-write-operations"></a>Controllo delle versioni durante le operazioni di scrittura

Quando si attiva il controllo delle versioni dei BLOB, ogni operazione di scrittura in un BLOB crea una nuova versione. Le operazioni di scrittura includono [Put Blob](/rest/api/storageservices/put-blob), [Put Block List](/rest/api/storageservices/put-block-list), [Copy Blob](/rest/api/storageservices/copy-blob)e [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata).

Se l'operazione di scrittura crea un nuovo BLOB, il BLOB risultante è la versione corrente del BLOB. Se l'operazione di scrittura modifica un BLOB esistente, i nuovi dati vengono acquisiti nel BLOB aggiornato, ovvero la versione corrente, e archiviazione di Azure crea una versione che salva lo stato precedente del BLOB.

Per semplicità, i diagrammi illustrati in questo articolo visualizzano l'ID versione come valore intero semplice. In realtà, l'ID versione è un timestamp. La versione corrente è mostrata in blu e le versioni precedenti sono visualizzate in grigio.

Il diagramma seguente mostra come le operazioni di scrittura influiscono sulle versioni dei BLOB. Quando viene creato un BLOB, il BLOB è la versione corrente. Quando viene modificato lo stesso BLOB, viene creata una nuova versione per salvare lo stato precedente del BLOB e il BLOB aggiornato diventa la versione corrente.

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="Diagramma che illustra come le operazioni di scrittura influiscono sui BLOB con versione.":::

> [!NOTE]
> Un blob creato prima dell'abilitazione del controllo delle versioni per l'account di archiviazione non ha un ID versione. Quando il BLOB viene modificato, il BLOB modificato diventa la versione corrente e viene creata una versione per salvare lo stato del BLOB prima dell'aggiornamento. Alla versione viene assegnato un ID versione che rappresenta l'ora di creazione.

### <a name="versioning-on-delete-operations"></a>Controllo delle versioni in operazioni di eliminazione

Quando si elimina un BLOB, la versione corrente del BLOB diventa una versione precedente e il BLOB di base viene eliminato. Tutte le versioni precedenti esistenti del BLOB vengono mantenute quando il BLOB viene eliminato.

La chiamata dell'operazione [Delete Blob](/rest/api/storageservices/delete-blob) senza un ID versione Elimina il BLOB di base. Per eliminare una versione specifica, fornire l'ID per la versione nell'operazione di eliminazione.

Il diagramma seguente illustra l'effetto di un'operazione di eliminazione su un BLOB con versione:

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="Diagramma che illustra come le operazioni di scrittura influiscono sui BLOB con versione.":::

La scrittura di nuovi dati nel BLOB crea una nuova versione del BLOB. Le versioni esistenti non sono interessate, come illustrato nella figura seguente.

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="Diagramma che illustra come le operazioni di scrittura influiscono sui BLOB con versione.":::

### <a name="blob-types"></a>Tipi di BLOB

Quando è abilitata la funzionalità di controllo delle versioni dei BLOB per un account di archiviazione, tutte le operazioni di scrittura ed eliminazione sui BLOB in blocchi attivano la creazione di una nuova versione, ad eccezione dell'operazione [Put Block](/rest/api/storageservices/put-block) .

Per i BLOB di pagine e i BLOB di Accodamento, solo un subset di operazioni write ed Delete attiva la creazione di una versione. tra cui:

- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Block List](/rest/api/storageservices/put-block-list)
- [Delete Blob](/rest/api/storageservices/delete-blob)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Copy Blob](/rest/api/storageservices/copy-blob)

Le operazioni seguenti non attivano la creazione di una nuova versione. Per acquisire le modifiche da tali operazioni, eseguire uno snapshot manuale:

- [Pagina put](/rest/api/storageservices/put-page) (BLOB di pagine)
- [Blocco Append](/rest/api/storageservices/append-block) (BLOB di Accodamento)

Tutte le versioni di un BLOB devono essere dello stesso tipo di BLOB. Se un BLOB ha versioni precedenti, non è possibile sovrascrivere un BLOB di un tipo con un altro tipo, a meno che non si elimini per la prima volta il BLOB e tutte le relative versioni.

### <a name="access-tiers"></a>Livelli di accesso

È possibile spostare qualsiasi versione di un BLOB in blocchi, inclusa la versione corrente, in un livello di accesso BLOB diverso chiamando l'operazione di [impostazione del livello BLOB](/rest/api/storageservices/set-blob-tier) . È possibile sfruttare i prezzi di capacità inferiori spostando le versioni precedenti di un BLOB nel livello ad accesso sporadico o archivio. Per altre informazioni, vedere [archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, ad accesso sporadico e archivio](storage-blob-storage-tiers.md).

Per automatizzare il processo di trasferimento dei BLOB in blocchi al livello appropriato, usare la gestione del ciclo di vita del BLOB. Per altre informazioni sulla gestione del ciclo di vita, vedere [gestire il ciclo di vita dell'archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md).

## <a name="enable-or-disable-blob-versioning"></a>Abilitare o disabilitare il controllo delle versioni dei BLOB

Per informazioni su come abilitare o disabilitare il controllo delle versioni dei BLOB, vedere [Enable and Manage BLOB Versioning](versioning-enable.md).

La disabilitazione del controllo delle versioni dei BLOB non elimina i BLOB, le versioni o gli snapshot esistenti. Quando si disattiva il controllo delle versioni dei BLOB, le versioni esistenti rimangono accessibili nell'account di archiviazione. Nessuna nuova versione viene creata successivamente.

Se un BLOB è stato creato o modificato dopo che il controllo delle versioni è stato disabilitato nell'account di archiviazione, la sovrascrittura del BLOB crea una nuova versione. Il BLOB aggiornato non è più la versione corrente e non ha un ID versione. Tutti gli aggiornamenti successivi del BLOB sovrascriveranno i dati senza salvare lo stato precedente.

È possibile leggere o eliminare versioni usando l'ID versione dopo la disabilitazione del controllo delle versioni. È anche possibile elencare le versioni di un BLOB dopo la disabilitazione del controllo delle versioni.

Il diagramma seguente mostra in che modo la modifica di un BLOB dopo la disabilitazione del controllo delle versioni consente di creare un BLOB senza controllo delle versioni. Tutte le versioni esistenti associate al BLOB vengono mantenute.

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="Diagramma che illustra come le operazioni di scrittura influiscono sui BLOB con versione.":::

## <a name="blob-versioning-and-soft-delete"></a>Controllo delle versioni dei BLOB e eliminazione temporanea

Il controllo delle versioni dei BLOB e l'eliminazione temporanea BLOB interagiscono per offrire una protezione ottimale dei dati. Quando si Abilita l'eliminazione temporanea, è necessario specificare per quanto tempo archiviazione di Azure deve conservare un BLOB eliminato temporaneamente. Eventuali versioni del BLOB eliminate temporaneamente rimangono nel sistema e possono essere annullate all'interno del periodo di memorizzazione dell'eliminazione temporanea. Per altre informazioni sull'eliminazione temporanea dei BLOB, vedere [eliminazione temporanea per i BLOB di archiviazione di Azure](storage-blob-soft-delete.md).

### <a name="deleting-a-blob-or-version"></a>Eliminazione di un BLOB o di una versione

L'eliminazione temporanea offre protezione aggiuntiva per l'eliminazione delle versioni BLOB. Se per l'account di archiviazione sono abilitate sia il controllo delle versioni che l'eliminazione temporanea, quando si elimina un BLOB, archiviazione di Azure crea una nuova versione per salvare lo stato del BLOB immediatamente prima dell'eliminazione ed elimina la versione corrente. La nuova versione non viene eliminata temporaneamente e non viene rimossa al termine del periodo di memorizzazione dell'eliminazione temporanea.

Quando si elimina una versione precedente del BLOB, la versione viene eliminata temporaneamente. La versione temporaneamente eliminata viene mantenuta nel periodo di conservazione specificato nelle impostazioni di eliminazione temporanea per l'account di archiviazione e viene eliminata definitivamente al termine del periodo di memorizzazione dell'eliminazione temporanea.

Per rimuovere una versione precedente di un BLOB, eliminarla in modo esplicito specificando l'ID versione.

Il diagramma seguente mostra cosa accade quando si elimina una versione BLOB o BLOB.

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="Diagramma che illustra come le operazioni di scrittura influiscono sui BLOB con versione.":::

Se per un account di archiviazione sono abilitate sia il controllo delle versioni che l'eliminazione temporanea, non viene creato alcuno snapshot con eliminazione temporanea quando viene modificata o eliminata una versione BLOB o BLOB.

### <a name="restoring-a-soft-deleted-version"></a>Ripristino di una versione eliminata temporaneamente

È possibile ripristinare una versione BLOB eliminata temporaneamente chiamando l'operazione di annullamento dell' [eliminazione del BLOB](/rest/api/storageservices/undelete-blob) sulla versione mentre è attivo il periodo di conservazione dell'eliminazione temporanea. L'operazione **Undelete BLOB** Ripristina tutte le versioni eliminate temporaneamente del BLOB.

Il ripristino di versioni con eliminazione temporanea con l'operazione di **annullamento dell'eliminazione del BLOB** non promuove la versione corrente. Per ripristinare la versione corrente, ripristinare innanzitutto tutte le versioni eliminate temporaneamente, quindi usare l'operazione [Copy Blob](/rest/api/storageservices/copy-blob) per copiare una versione precedente per ripristinare il BLOB.

Il diagramma seguente illustra come ripristinare le versioni BLOB eliminate temporaneamente con l'operazione di **annullamento dell'eliminazione del BLOB** e come ripristinare la versione corrente del BLOB con l'operazione di copia del **BLOB** .

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="Diagramma che illustra come le operazioni di scrittura influiscono sui BLOB con versione.":::

Una volta trascorso il periodo di memorizzazione dell'eliminazione temporanea, eventuali versioni di BLOB eliminate temporaneamente vengono eliminate definitivamente.

## <a name="blob-versioning-and-blob-snapshots"></a>Controllo delle versioni dei BLOB e snapshot BLOB

Uno snapshot BLOB è una copia di sola lettura di un BLOB che viene eseguita in un momento specifico. Gli snapshot BLOB e le versioni BLOB sono simili, ma uno snapshot viene creato manualmente dall'utente o dall'applicazione, mentre una versione BLOB viene creata automaticamente durante un'operazione di scrittura o eliminazione quando il controllo delle versioni dei BLOB è abilitato per l'account di archiviazione.

> [!IMPORTANT]
> Quando si Abilita il controllo delle versioni dei BLOB, è consigliabile aggiornare anche l'applicazione per interrompere l'acquisizione di snapshot di BLOB in blocchi. Se il controllo delle versioni è abilitato per l'account di archiviazione, tutti gli aggiornamenti e le eliminazioni di BLOB in blocchi vengono acquisiti e conservati dalle versioni. L'acquisizione di snapshot non offre protezioni aggiuntive per i dati BLOB in blocchi se il controllo delle versioni dei BLOB è abilitato e può aumentare i costi e la complessità dell'applicazione.

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>Snapshot di un BLOB quando è abilitato il controllo delle versioni

Sebbene non sia consigliato, è possibile creare uno snapshot di un BLOB con controllo delle versioni. Se non è possibile aggiornare l'applicazione per interrompere l'acquisizione di snapshot dei BLOB quando si Abilita il controllo delle versioni, l'applicazione può supportare sia snapshot che versioni.

Quando si crea uno snapshot di un BLOB con versione, viene creata una nuova versione nello stesso momento in cui viene creato lo snapshot. Quando viene creato uno snapshot, viene creata anche una nuova versione corrente.

Il diagramma seguente mostra cosa accade quando si crea uno snapshot di un BLOB con versione. Nel diagramma le versioni e gli snapshot dei BLOB con ID versione 2 e 3 contengono dati identici.

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="Diagramma che illustra come le operazioni di scrittura influiscono sui BLOB con versione.":::

## <a name="authorize-operations-on-blob-versions"></a>Autorizzare le operazioni nelle versioni BLOB

È possibile autorizzare l'accesso alle versioni BLOB usando uno degli approcci seguenti:

- Usando il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per concedere le autorizzazioni a un'entità di sicurezza Azure Active Directory (Azure AD). Microsoft consiglia di usare Azure AD per una maggiore sicurezza e semplicità d'uso. Per altre informazioni sull'uso di Azure AD con le operazioni BLOB, vedere [autorizzare l'accesso a BLOB e code usando Azure Active Directory](../common/storage-auth-aad.md).
- Usando una firma di accesso condiviso (SAS) per delegare l'accesso alle versioni BLOB. Specificare l'ID versione per il tipo di risorsa firmato `bv` , che rappresenta una versione BLOB, per creare un token SAS per le operazioni in una versione specifica. Per altre informazioni sulle firme di accesso condiviso, vedere [Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso](../common/storage-sas-overview.md).
- Utilizzando le chiavi di accesso dell'account per autorizzare le operazioni sulle versioni dei BLOB con la chiave condivisa. Per altre informazioni, vedere [Authorize with Shared Key](/rest/api/storageservices/authorize-with-shared-key) (Autorizzazione con chiave condivisa).

Il controllo delle versioni del BLOB è progettato per proteggere i dati da eliminazioni accidentali o dannose. Per migliorare la protezione, l'eliminazione di una versione BLOB richiede autorizzazioni speciali. Le sezioni seguenti descrivono le autorizzazioni necessarie per eliminare una versione BLOB.

### <a name="azure-rbac-action-to-delete-a-blob-version"></a>Azione RBAC di Azure per eliminare una versione BLOB

La tabella seguente illustra le azioni RBAC di Azure che supportano l'eliminazione di un BLOB o di una versione BLOB.

| Descrizione | Operazione del servizio BLOB | Azione dati RBAC di Azure obbligatoria | Supporto del ruolo incorporato di Azure |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| Eliminazione della versione corrente del BLOB | Delete Blob | **Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete** | Collaboratore ai dati del BLOB di archiviazione |
| Eliminazione di una versione | Delete Blob | **Microsoft. storage/storageAccounts/blobServices/Containers/Blobs/deleteBlobVersion/Action** | Proprietario dei dati del BLOB di archiviazione |

### <a name="shared-access-signature-sas-parameters"></a>Parametri della firma di accesso condiviso (SAS)

La risorsa firmata per una versione BLOB è `bv` . Per altre informazioni, vedere [creare una](/rest/api/storageservices/create-service-sas) firma di accesso condiviso del servizio o [creare una firma di accesso condiviso dell'utente](/rest/api/storageservices/create-user-delegation-sas).

La tabella seguente illustra l'autorizzazione necessaria per una firma di accesso condiviso per eliminare una versione BLOB.

| **Autorizzazione** | **Simbolo URI** | **Operazioni consentite** |
|----------------|----------------|------------------------|
| Elimina         | x              | Eliminare una versione BLOB. |

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

L'abilitazione del controllo delle versioni dei BLOB può comportare ulteriori addebiti di archiviazione dati per l'account Nel progettare l'applicazione, è importante essere coscienti di come i costi possono aumentare, in modo da poterli ridurre al minimo.

Le versioni BLOB, come gli snapshot BLOB, vengono fatturate alla stessa tariffa dei dati attivi. Il modo in cui vengono fatturate le versioni varia a seconda che il livello sia stato impostato in modo esplicito per il BLOB di base o per qualsiasi versione o snapshot. Per altre informazioni sui livelli di BLOB, vedere [Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio](storage-blob-storage-tiers.md).

Se non è stato modificato il livello di un BLOB o di una versione, verranno addebitati i blocchi di dati univoci nel BLOB, le relative versioni e eventuali snapshot. Per ulteriori informazioni, vedere [fatturazione quando il livello BLOB non è stato impostato in modo esplicito](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Se è stato modificato il livello di un BLOB o di una versione, viene addebitato l'intero oggetto, indipendentemente dal fatto che il BLOB e la versione si trovino di nuovo nello stesso livello. Per ulteriori informazioni, vedere [fatturazione quando il livello BLOB è stato impostato in modo esplicito](#billing-when-the-blob-tier-has-been-explicitly-set).

> [!NOTE]
> L'abilitazione del controllo delle versioni per i dati sovrascritti di frequente può comportare un aumento della capacità di archiviazione e una latenza maggiore durante le operazioni di elenco. Per attenuare questi problemi, archiviare i dati sovrascritti di frequente in un account di archiviazione separato con il controllo delle versioni disabilitato.

Per altre informazioni sui dettagli di fatturazione per gli snapshot BLOB, vedere [snapshot BLOB](snapshots-overview.md).

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Fatturazione quando il livello BLOB non è stato impostato in modo esplicito

Se non è stato impostato in modo esplicito il livello BLOB per un BLOB di base o una delle relative versioni, verranno addebitati solo i blocchi o le pagine univoche nel BLOB, le relative versioni e eventuali snapshot. I dati condivisi tra un BLOB e le relative versioni vengono addebitati una sola volta. Quando un BLOB viene aggiornato, i dati in un BLOB di base divengono divergenti dai dati archiviati nelle versioni e i dati univoci vengono addebitati per blocco o pagina.

Quando si sostituisce un blocco all'interno di un BLOB in blocchi, tale blocco viene successivamente addebitato come blocco univoco. Questo vale anche se il blocco ha lo stesso ID di blocco e gli stessi dati della versione precedente. Dopo che il blocco è stato nuovamente sottoposta a commit, è diverso dalla controparte della versione precedente e verranno addebitati i dati. Lo stesso vale per una pagina in un BLOB di pagine che viene aggiornata con dati identici.

L'archiviazione BLOB non dispone di un metodo per determinare se due blocchi contengono dati identici. Ogni blocco che viene caricato e inviato viene trattato come univoco, persino se contiene gli stessi dati e ha lo stesso ID blocco. Poiché i costi aumentano per i blocchi univoci, è importante tenere presente che l'aggiornamento di un BLOB quando il controllo delle versioni è abilitato comporterà ulteriori blocchi univoci e costi aggiuntivi.

Quando è abilitata la funzionalità di controllo delle versioni dei BLOB, chiamare le operazioni di aggiornamento sui BLOB in blocchi in modo che aggiornino il minor numero possibile di blocchi. Le operazioni di scrittura che consentono un controllo granulare sui blocchi sono [Put Block](/rest/api/storageservices/put-block) e [Put Block List](/rest/api/storageservices/put-block-list). L'operazione [Put Blob](/rest/api/storageservices/put-blob) , d'altra parte, sostituisce l'intero contenuto di un BLOB e pertanto può causare addebiti aggiuntivi.

Negli scenari seguenti viene illustrato il modo in cui vengono addebitati i costi per un BLOB in blocchi e le relative versioni quando il livello BLOB non è stato impostato in modo esplicito.

#### <a name="scenario-1"></a>Scenario 1

Nello scenario 1, la versione del BLOB è precedente. Il BLOB non è stato aggiornato dopo la creazione della versione, quindi gli addebiti vengono addebitati solo per i blocchi univoci 1, 2 e 3.

![Diagramma 1 che mostra la fatturazione per i blocchi univoci nel BLOB di base e nella versione precedente.](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenario 2

Nello scenario 2, un blocco (blocco 3 nel diagramma) nel BLOB è stato aggiornato. Anche se il blocco aggiornato contiene gli stessi dati e lo stesso ID, non corrisponde al blocco 3 della versione precedente. Di conseguenza, all'account vengono addebitati quattro blocchi.

![Diagramma 2 che mostra la fatturazione per i blocchi univoci nel BLOB di base e nella versione precedente.](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenario 3

Nello scenario 3, il BLOB è stato aggiornato, ma la versione non lo è. Il blocco 3 è stato sostituito con il blocco 4 nel BLOB di base, ma la versione precedente riflette ancora il blocco 3. Di conseguenza, all'account vengono addebitati quattro blocchi.

![Diagramma 3 che mostra la fatturazione per blocchi univoci nel BLOB di base e nella versione precedente.](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenario 4

Nello Scenario 4, il BLOB di base è stato completamente aggiornato e non contiene nessuno dei blocchi originali. Di conseguenza, all'account vengono addebitati tutti gli otto blocchi univoci &mdash; quattro nel BLOB di base e quattro nella versione precedente. Questo scenario può verificarsi se si scrive in un BLOB con l'operazione [Put Blob](/rest/api/storageservices/put-blob) , perché sostituisce l'intero contenuto del BLOB di base.

![Diagramma 4 che mostra la fatturazione per blocchi univoci nel BLOB di base e nella versione precedente.](./media/versioning-overview/versions-billing-scenario-4.png)

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Fatturazione quando il livello BLOB è stato impostato in modo esplicito

Se il livello BLOB è stato impostato in modo esplicito per un BLOB o una versione (o snapshot), viene addebitata la lunghezza totale del contenuto dell'oggetto nel nuovo livello, indipendentemente dal fatto che condivida blocchi con un oggetto nel livello originale. Viene addebitata anche la lunghezza totale del contenuto della versione meno recente nel livello originale. Qualsiasi altra versione o snapshot precedente che rimane nel livello originale viene addebitato per i blocchi univoci che possono condividere, come descritto in [fatturazione quando il livello BLOB non è stato impostato in modo esplicito](#billing-when-the-blob-tier-has-not-been-explicitly-set).

#### <a name="moving-a-blob-to-a-new-tier"></a>Trasferimento di un BLOB in un nuovo livello

La tabella seguente descrive il comportamento di fatturazione per un BLOB o una versione quando viene spostato in un nuovo livello.

| Quando il livello BLOB è impostato in modo esplicito su... | Viene addebitata la fatturazione... |
|-|-|
| Un BLOB di base con una versione precedente | Il BLOB di base nel nuovo livello e la versione meno recente nel livello originale, più eventuali blocchi univoci in altre versioni. <sup>1</sup> |
| Un BLOB di base con una versione precedente e uno snapshot | Il BLOB di base nel nuovo livello, la versione meno recente nel livello originale e lo snapshot meno recente nel livello originale, più eventuali blocchi univoci in altre versioni o snapshot<sup>1</sup>. |
| Una versione precedente | La versione nel nuovo livello e il BLOB di base nel livello originale, più eventuali blocchi univoci in altre versioni. <sup>1</sup> |

<sup>1</sup> Se sono presenti altre versioni o snapshot precedenti che non sono stati spostati dal livello originale, le versioni o gli snapshot vengono addebitati in base al numero di blocchi univoci che contengono, come descritto in [fatturazione quando il livello BLOB non è stato impostato in modo esplicito](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Il diagramma seguente illustra il modo in cui gli oggetti vengono fatturati quando un BLOB con versione viene spostato in un livello diverso.

:::image type="content" source="media/versioning-overview/versioning-billing-tiers.png" alt-text="Diagramma che illustra come le operazioni di scrittura influiscono sui BLOB con versione.":::

L'impostazione esplicita del livello per un BLOB, una versione o uno snapshot non può essere annullata. Se si sposta un BLOB in un nuovo livello e quindi lo si sposta di nuovo al livello originale, viene addebitata la lunghezza totale del contenuto dell'oggetto anche se condivide blocchi con altri oggetti nel livello originale.

Le operazioni che impostano in modo esplicito il livello di un BLOB, una versione o uno snapshot includono:

- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- [Inserisci BLOB](/rest/api/storageservices/put-blob) con il livello specificato
- [Inserisci elenco di blocchi](/rest/api/storageservices/put-block-list) con il livello specificato
- [Copia BLOB](/rest/api/storageservices/copy-blob) con il livello specificato

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>Eliminazione di un BLOB quando l'eliminazione temporanea è abilitata

Quando l'eliminazione temporanea BLOB è abilitata, se si elimina o sovrascrive un BLOB di base per il quale è stato impostato in modo esplicito il livello, tutte le versioni precedenti del BLOB eliminato temporaneamente vengono fatturate in base alla lunghezza totale del contenuto. Per altre informazioni sul modo in cui il controllo delle versioni dei BLOB e l'eliminazione temporanea interagiscono, vedere [controllo delle versioni dei BLOB e eliminazione](#blob-versioning-and-soft-delete)temporanea.

La tabella seguente descrive il comportamento di fatturazione per un BLOB eliminato temporaneamente, a seconda che il controllo delle versioni sia abilitato o disabilitato. Quando è abilitata la funzionalità di controllo delle versioni, viene creata una versione quando un BLOB viene eliminato temporaneamente. Quando il controllo delle versioni è disabilitato, l'eliminazione temporanea di un BLOB crea uno snapshot di eliminazione temporanea.

| Quando si sovrascrive un BLOB di base con il livello impostato in modo esplicito... | Viene addebitata la fatturazione... |
|-|-|
| Se l'eliminazione e il controllo delle versioni del BLOB sono entrambi abilitati | Tutte le versioni esistenti alla lunghezza del contenuto completa indipendentemente dal livello. |
| Se l'eliminazione temporanea BLOB è abilitata ma il controllo delle versioni è disabilitato | Tutti gli snapshot di eliminazione temporanea esistenti alla lunghezza del contenuto completa indipendentemente dal livello. |

## <a name="see-also"></a>Vedere anche

- [Abilitare e gestire il controllo delle versioni dei BLOB](versioning-enable.md)
- [Creazione di uno snapshot di un BLOB](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Eliminazione temporanea per i BLOB di archiviazione di Azure](storage-blob-soft-delete.md)
