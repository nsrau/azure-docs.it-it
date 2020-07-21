---
title: Controllo delle versioni dei BLOB (anteprima)
titleSuffix: Azure Storage
description: Il controllo delle versioni dell'archiviazione BLOB (anteprima) mantiene automaticamente le versioni precedenti di un oggetto e le identifica con i timestamp. È possibile ripristinare le versioni precedenti di un BLOB per ripristinare i dati se vengono erroneamente modificati o eliminati.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: fd620e253e661f986f67a440272937026cb4ff7f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86528401"
---
# <a name="blob-versioning-preview"></a>Controllo delle versioni dei BLOB (anteprima)

È possibile abilitare il controllo delle versioni dell'archiviazione BLOB (anteprima) per mantenere automaticamente le versioni precedenti di un oggetto.  Quando è abilitata la funzionalità di controllo delle versioni dei BLOB, è possibile ripristinare una versione precedente di un BLOB per ripristinare i dati se vengono erroneamente modificati o eliminati.

Il controllo delle versioni dei BLOB è abilitato nell'account di archiviazione e si applica a tutti i BLOB nell'account di archiviazione. Dopo aver abilitato il controllo delle versioni dei BLOB per un account di archiviazione, archiviazione di Azure mantiene automaticamente le versioni per ogni BLOB nell'account di archiviazione.

Microsoft consiglia di usare il controllo delle versioni dei BLOB per mantenere le versioni precedenti di un BLOB per la protezione dei dati superiore. Quando possibile, usare il controllo delle versioni dei BLOB anziché gli snapshot BLOB per gestire le versioni precedenti. Gli snapshot BLOB forniscono funzionalità simili in quanto mantengono le versioni precedenti di un BLOB, ma gli snapshot devono essere gestiti manualmente dall'applicazione.

> [!IMPORTANT]
> Il controllo delle versioni dei BLOB non consente di eseguire il ripristino dall'eliminazione accidentale di un contenitore o di un account di archiviazione. Per evitare l'eliminazione accidentale dell'account di archiviazione, configurare un blocco **CannotDelete** sulla risorsa dell'account di archiviazione. Per altre informazioni sul blocco delle risorse di Azure, vedere [bloccare le risorse per impedire modifiche impreviste](../../azure-resource-manager/management/lock-resources.md).

## <a name="how-blob-versioning-works"></a>Funzionamento del controllo delle versioni dei BLOB

Una versione acquisisce lo stato di un BLOB in un determinato momento. Quando è abilitata la funzionalità di controllo delle versioni dei BLOB per un account di archiviazione, archiviazione di Azure crea automaticamente una nuova versione di un BLOB ogni volta che il BLOB viene modificato o eliminato.

Quando si crea un BLOB con il controllo delle versioni abilitato, il nuovo BLOB è la versione corrente del BLOB (o il BLOB di base). Se successivamente si modifica il BLOB, archiviazione di Azure crea una versione che acquisisce lo stato del BLOB prima della modifica. Il BLOB modificato diventa la nuova versione corrente. Viene creata una nuova versione ogni volta che si modifica il BLOB.

Quando si elimina un BLOB con il controllo delle versioni abilitato, archiviazione di Azure crea una versione che acquisisce lo stato del BLOB prima dell'eliminazione. La versione corrente del BLOB viene quindi eliminata, ma le versioni del BLOB vengono mantenute, in modo che possa essere ricreata, se necessario. 

Le versioni BLOB non sono modificabili. Non è possibile modificare il contenuto o i metadati di una versione BLOB esistente.

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

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="Diagramma che illustra come le operazioni di scrittura influiscono sui BLOB con versione":::

> [!NOTE]
> Un blob creato prima dell'abilitazione del controllo delle versioni per l'account di archiviazione non ha un ID versione. Quando il BLOB viene modificato, il BLOB modificato diventa la versione corrente e viene creata una versione per salvare lo stato del BLOB prima dell'aggiornamento. Alla versione viene assegnato un ID versione che rappresenta l'ora di creazione.

### <a name="versioning-on-delete-operations"></a>Controllo delle versioni in operazioni di eliminazione

Quando si elimina un BLOB, la versione corrente del BLOB diventa una versione precedente e il BLOB di base viene eliminato. Tutte le versioni precedenti esistenti del BLOB vengono mantenute quando il BLOB viene eliminato.

La chiamata dell'operazione [Delete Blob](/rest/api/storageservices/delete-blob) senza un ID versione Elimina il BLOB di base. Per eliminare una versione specifica, fornire l'ID per la versione nell'operazione di eliminazione.

Il diagramma seguente illustra l'effetto di un'operazione di eliminazione su un BLOB con versione:

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="Diagramma che mostra l'eliminazione di un BLOB con versione":::

La scrittura di nuovi dati nel BLOB crea una nuova versione del BLOB. Le versioni esistenti non sono interessate, come illustrato nella figura seguente.

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="Diagramma che mostra la ricreazione del BLOB con versione dopo l'eliminazione":::

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

Per informazioni su come abilitare o disabilitare il controllo delle versioni dei BLOB, vedere [abilitare o disabilitare il controllo delle versioni dei BLOB](versioning-enable.md).

La disabilitazione del controllo delle versioni dei BLOB non elimina i BLOB, le versioni o gli snapshot esistenti. Quando si disattiva il controllo delle versioni dei BLOB, le versioni esistenti rimangono accessibili nell'account di archiviazione. Nessuna nuova versione viene creata successivamente.

Se un BLOB è stato creato o modificato dopo che il controllo delle versioni è stato disabilitato nell'account di archiviazione, la sovrascrittura del BLOB crea una nuova versione. Il BLOB aggiornato non è più la versione corrente e non ha un ID versione. Tutti gli aggiornamenti successivi del BLOB sovrascriveranno i dati senza salvare lo stato precedente.

È possibile leggere o eliminare versioni usando l'ID versione dopo la disabilitazione del controllo delle versioni. È anche possibile elencare le versioni di un BLOB dopo la disabilitazione del controllo delle versioni.

Il diagramma seguente mostra in che modo la modifica di un BLOB dopo la disabilitazione del controllo delle versioni consente di creare un BLOB senza controllo delle versioni. Tutte le versioni esistenti associate al BLOB vengono mantenute.

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="Diagramma che mostra il BLOB di base modificato dopo la disabilitazione del controllo delle versioni":::

## <a name="blob-versioning-and-soft-delete"></a>Controllo delle versioni dei BLOB e eliminazione temporanea

Il controllo delle versioni dei BLOB e l'eliminazione temporanea BLOB interagiscono per offrire una protezione ottimale dei dati. Quando si Abilita l'eliminazione temporanea, è necessario specificare per quanto tempo archiviazione di Azure deve conservare un BLOB eliminato temporaneamente. Eventuali versioni del BLOB eliminate temporaneamente rimangono nel sistema e possono essere annullate all'interno del periodo di memorizzazione dell'eliminazione temporanea. Per altre informazioni sull'eliminazione temporanea dei BLOB, vedere [eliminazione temporanea per i BLOB di archiviazione di Azure](storage-blob-soft-delete.md).

### <a name="deleting-a-blob-or-version"></a>Eliminazione di un BLOB o di una versione

L'eliminazione temporanea offre protezione aggiuntiva per l'eliminazione delle versioni BLOB. Se per l'account di archiviazione sono abilitate sia il controllo delle versioni che l'eliminazione temporanea, quando si elimina un BLOB, archiviazione di Azure crea una nuova versione per salvare lo stato del BLOB immediatamente prima dell'eliminazione ed elimina la versione corrente. La nuova versione non viene eliminata temporaneamente e non viene rimossa al termine del periodo di memorizzazione dell'eliminazione temporanea.

Quando si elimina una versione precedente del BLOB, la versione viene eliminata temporaneamente. La versione temporaneamente eliminata viene mantenuta nel periodo di conservazione specificato nelle impostazioni di eliminazione temporanea per l'account di archiviazione e viene eliminata definitivamente al termine del periodo di memorizzazione dell'eliminazione temporanea.

Per rimuovere una versione precedente di un BLOB, eliminarla in modo esplicito specificando l'ID versione.

Il diagramma seguente mostra cosa accade quando si elimina una versione BLOB o BLOB.

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="Diagramma che mostra l'eliminazione di una versione con l'eliminazione temporanea abilitata":::

Se per un account di archiviazione sono abilitate sia il controllo delle versioni che l'eliminazione temporanea, non viene creato alcuno snapshot con eliminazione temporanea quando viene modificata o eliminata una versione BLOB o BLOB.

### <a name="restoring-a-soft-deleted-version"></a>Ripristino di una versione eliminata temporaneamente

È possibile ripristinare una versione BLOB eliminata temporaneamente chiamando l'operazione di annullamento dell' [eliminazione del BLOB](/rest/api/storageservices/undelete-blob) sulla versione mentre è attivo il periodo di conservazione dell'eliminazione temporanea. L'operazione **Undelete BLOB** Ripristina tutte le versioni eliminate temporaneamente del BLOB.

Il ripristino di versioni con eliminazione temporanea con l'operazione di **annullamento dell'eliminazione del BLOB** non promuove la versione corrente. Per ripristinare la versione corrente, ripristinare innanzitutto tutte le versioni eliminate temporaneamente, quindi usare l'operazione [Copy Blob](/rest/api/storageservices/copy-blob) per copiare una versione precedente per ripristinare il BLOB.

Il diagramma seguente illustra come ripristinare le versioni BLOB eliminate temporaneamente con l'operazione di **annullamento dell'eliminazione del BLOB** e come ripristinare la versione corrente del BLOB con l'operazione di copia del **BLOB** .

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="Diagramma che illustra come ripristinare le versioni eliminate temporaneamente":::

Una volta trascorso il periodo di memorizzazione dell'eliminazione temporanea, eventuali versioni di BLOB eliminate temporaneamente vengono eliminate definitivamente.

## <a name="blob-versioning-and-blob-snapshots"></a>Controllo delle versioni dei BLOB e snapshot BLOB

Uno snapshot BLOB è una copia di sola lettura di un BLOB che viene eseguita in un momento specifico. Gli snapshot BLOB e le versioni BLOB sono simili, ma uno snapshot viene creato manualmente dall'utente o dall'applicazione, mentre una versione BLOB viene creata automaticamente durante un'operazione di scrittura o eliminazione quando il controllo delle versioni dei BLOB è abilitato per l'account di archiviazione.

> [!IMPORTANT]
> Quando si Abilita il controllo delle versioni dei BLOB, è consigliabile aggiornare anche l'applicazione per interrompere l'acquisizione di snapshot di BLOB in blocchi. Se il controllo delle versioni è abilitato per l'account di archiviazione, tutti gli aggiornamenti e le eliminazioni di BLOB in blocchi vengono acquisiti e conservati dalle versioni. L'acquisizione di snapshot non offre protezioni aggiuntive per i dati BLOB in blocchi se il controllo delle versioni dei BLOB è abilitato e può aumentare i costi e la complessità dell'applicazione.

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>Snapshot di un BLOB quando è abilitato il controllo delle versioni

Sebbene non sia consigliato, è possibile creare uno snapshot di un BLOB con controllo delle versioni. Se non è possibile aggiornare l'applicazione per interrompere l'acquisizione di snapshot dei BLOB quando si Abilita il controllo delle versioni, l'applicazione può supportare sia snapshot che versioni.

Quando si crea uno snapshot di un BLOB con versione, viene creata una nuova versione nello stesso momento in cui viene creato lo snapshot. Quando viene creato uno snapshot, viene creata anche una nuova versione corrente.

Il diagramma seguente mostra cosa accade quando si crea uno snapshot di un BLOB con versione. Nel diagramma le versioni e gli snapshot dei BLOB con ID versione 2 e 3 contengono dati identici.

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="Diagramma che Mostra gli snapshot di un BLOB con versione":::

## <a name="authorize-operations-on-blob-versions"></a>Autorizzare le operazioni nelle versioni BLOB

È possibile autorizzare l'accesso alle versioni BLOB usando uno degli approcci seguenti:

- Utilizzando il controllo degli accessi in base al ruolo (RBAC) per concedere le autorizzazioni a un'entità di sicurezza Azure Active Directory (Azure AD). Microsoft consiglia di usare Azure AD per una maggiore sicurezza e semplicità d'uso. Per altre informazioni sull'uso di Azure AD con le operazioni BLOB, vedere [autorizzare l'accesso a BLOB e code usando Azure Active Directory](../common/storage-auth-aad.md).
- Usando una firma di accesso condiviso (SAS) per delegare l'accesso alle versioni BLOB. Specificare l'ID versione per il tipo di risorsa firmato `bv` , che rappresenta una versione BLOB, per creare un token SAS per le operazioni in una versione specifica. Per altre informazioni sulle firme di accesso condiviso, vedere [Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso](../common/storage-sas-overview.md).
- Utilizzando le chiavi di accesso dell'account per autorizzare le operazioni sulle versioni dei BLOB con la chiave condivisa. Per altre informazioni, vedere [Authorize with Shared Key](/rest/api/storageservices/authorize-with-shared-key) (Autorizzazione con chiave condivisa).

Il controllo delle versioni del BLOB è progettato per proteggere i dati da eliminazioni accidentali o dannose. Per migliorare la protezione, l'eliminazione di una versione BLOB richiede autorizzazioni speciali. Le sezioni seguenti descrivono le autorizzazioni necessarie per eliminare una versione BLOB.

### <a name="rbac-action-to-delete-a-blob-version"></a>Azione RBAC per eliminare una versione BLOB

La tabella seguente illustra le azioni RBAC che supportano l'eliminazione di un BLOB o di una versione BLOB.

| Descrizione | Operazione del servizio BLOB | Azione dati RBAC obbligatoria | Supporto del ruolo incorporato RBAC |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| Eliminazione della versione corrente del BLOB | Delete Blob | **Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete** | Collaboratore ai dati del BLOB di archiviazione |
| Eliminazione di una versione | Delete Blob | **Microsoft. storage/storageAccounts/blobServices/Containers/Blobs/deleteBlobVersion/Action** | Proprietario dei dati del BLOB di archiviazione |

### <a name="shared-access-signature-sas-parameters"></a>Parametri della firma di accesso condiviso (SAS)

La risorsa firmata per una versione BLOB è `bv` . Per altre informazioni, vedere [creare una](/rest/api/storageservices/create-service-sas) firma di accesso condiviso del servizio o [creare una firma di accesso condiviso dell'utente](/rest/api/storageservices/create-user-delegation-sas).

La tabella seguente illustra l'autorizzazione necessaria per una firma di accesso condiviso per eliminare una versione BLOB.

| **Autorizzazione** | **Simbolo URI** | **Operazioni consentite** |
|----------------|----------------|------------------------|
| Delete         | x              | Eliminare una versione BLOB. |

## <a name="about-the-preview"></a>Informazioni sulla versione di anteprima

Il controllo delle versioni dei BLOB è disponibile in anteprima nelle aree seguenti:

- Stati Uniti orientali 2
- Stati Uniti centrali
- Europa settentrionale
- Europa occidentale
- Francia centrale
- Canada orientale
- Canada centrale

La versione di anteprima è destinata solo all'uso in ambienti non di produzione.

La versione 2019-10-10 e successive dell'API REST di archiviazione di Azure supporta il controllo delle versioni dei BLOB.

### <a name="storage-account-support"></a>Supporto dell'account di archiviazione

Il controllo delle versioni dei BLOB è disponibile per i tipi di account di archiviazione seguenti:

- Account di archiviazione per utilizzo generico V2
- Bloccare gli account di archiviazione BLOB
- Account di archiviazione BLOB

Se l'account di archiviazione è un account per utilizzo generico V1, usare il portale di Azure per eseguire l'aggiornamento a un account per utilizzo generico V2. Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).

Gli account di archiviazione con uno spazio dei nomi gerarchico abilitato per l'uso con Azure Data Lake Storage Gen2 non sono attualmente supportati.

### <a name="register-for-the-preview"></a>Registrarsi per l'anteprima

Per eseguire la registrazione nell'anteprima del controllo delle versioni dei BLOB, usare PowerShell o l'interfaccia della riga di comando di Azure per inviare una richiesta di registrazione della funzionalità con la sottoscrizione. Dopo che la richiesta è stata approvata, è possibile abilitare il controllo delle versioni dei BLOB con gli account di archiviazione BLOB in blocchi, archiviazione BLOB o Premium per utilizzo generico nuovi o esistenti.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per eseguire la registrazione con PowerShell, chiamare il comando [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
# Register for blob versioning (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire la registrazione con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ feature Register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage \
    --name Versioning
```

---

### <a name="check-the-status-of-your-registration"></a>Verificare lo stato della registrazione

Per verificare lo stato della registrazione, usare PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per verificare lo stato della registrazione con PowerShell, chiamare il comando [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per verificare lo stato della registrazione con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage \
    --name Versioning
```

---

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

L'abilitazione del controllo delle versioni dei BLOB può comportare ulteriori addebiti di archiviazione dati per l'account Nel progettare l'applicazione, è importante essere coscienti di come i costi possono aumentare, in modo da poterli ridurre al minimo.

Le versioni BLOB, come gli snapshot BLOB, vengono fatturate alla stessa tariffa dei dati attivi. Se una versione condivide blocchi o pagine con il relativo BLOB di base, si paga solo per eventuali blocchi o pagine aggiuntive non condivisi tra la versione e il BLOB di base.

> [!NOTE]
> L'abilitazione del controllo delle versioni per i dati sovrascritti di frequente può comportare un aumento della capacità di archiviazione e una latenza maggiore durante le operazioni di elenco. Per attenuare questi problemi, archiviare i dati sovrascritti di frequente in un account di archiviazione separato con il controllo delle versioni disabilitato.

### <a name="important-billing-considerations"></a>Considerazioni importanti sulla fatturazione

Quando si Abilita il controllo delle versioni BLOB, assicurarsi di tenere presenti i punti seguenti:

- L'account di archiviazione viene addebitato per i blocchi o le pagine univoche, sia che si trovino nel BLOB o in una versione precedente del BLOB. Per l'account non vengono addebitati costi aggiuntivi per le versioni associate a un BLOB finché non si aggiorna il BLOB su cui si basano. Dopo l'aggiornamento, il BLOB è diverso rispetto alle versioni precedenti. Quando si verifica questo problema, vengono addebitati i blocchi o le pagine univoche in ogni BLOB o versione.
- Quando si sostituisce un blocco all'interno di un BLOB in blocchi, tale blocco viene successivamente addebitato come blocco univoco. Questo vale anche se il blocco ha lo stesso ID di blocco e gli stessi dati presenti nella versione. Dopo che il blocco è stato nuovamente sottoposta a commit, è diverso dalla relativa controparte in qualsiasi versione e verranno addebitati i dati. Lo stesso vale per una pagina in un BLOB di pagine che viene aggiornata con dati identici.
- L'archiviazione BLOB non dispone di un metodo per determinare se due blocchi contengono dati identici. Ogni blocco che viene caricato e inviato viene trattato come univoco, persino se contiene gli stessi dati e ha lo stesso ID blocco. Poiché i costi aumentano per i blocchi univoci, è importante tenere presente che l'aggiornamento di un BLOB quando il controllo delle versioni è abilitato comporterà blocchi univoci aggiuntivi e addebiti aggiuntivi.
- Quando è abilitata la funzionalità di controllo delle versioni dei BLOB, progettare le operazioni di aggiornamento sui BLOB in blocchi in modo che aggiornino il minor numero possibile di blocchi. Le operazioni di scrittura che consentono un controllo granulare sui blocchi sono [Put Block](/rest/api/storageservices/put-block) e [Put Block List](/rest/api/storageservices/put-block-list). L'operazione [Put Blob](/rest/api/storageservices/put-blob) , d'altra parte, sostituisce l'intero contenuto di un BLOB e pertanto può causare addebiti aggiuntivi.

### <a name="versioning-billing-scenarios"></a>Scenari di fatturazione di controllo delle versioni

Negli scenari seguenti viene illustrato il modo in cui vengono addebitati i costi per un BLOB in blocchi e le relative versioni.

#### <a name="scenario-1"></a>Scenario 1

Nello scenario 1, la versione del BLOB è precedente. Il BLOB non è stato aggiornato dopo la creazione della versione, quindi gli addebiti vengono addebitati solo per i blocchi univoci 1, 2 e 3.

![Risorse di archiviazione di Azure](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenario 2

Nello scenario 2, un blocco (blocco 3 nel diagramma) nel BLOB è stato aggiornato. Anche se il blocco aggiornato contiene gli stessi dati e lo stesso ID, non corrisponde al blocco 3 della versione precedente. Di conseguenza, all'account vengono addebitati quattro blocchi.

![Risorse di archiviazione di Azure](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenario 3

Nello scenario 3, il BLOB è stato aggiornato, ma la versione non lo è. Il blocco 3 è stato sostituito con il blocco 4 nel BLOB di base, ma la versione precedente riflette ancora il blocco 3. Di conseguenza, all'account vengono addebitati quattro blocchi.

![Risorse di archiviazione di Azure](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenario 4

Nello Scenario 4, il BLOB di base è stato completamente aggiornato e non contiene nessuno dei blocchi originali. Di conseguenza, all'account vengono addebitati tutti gli otto blocchi univoci &mdash; quattro nel BLOB di base e quattro nella versione precedente. Questo scenario può verificarsi se si scrive in un BLOB con l'operazione Put Blob, perché sostituisce l'intero contenuto del BLOB di base.

![Risorse di archiviazione di Azure](./media/versioning-overview/versions-billing-scenario-4.png)

## <a name="see-also"></a>Vedere anche

- [Abilitare il controllo delle versioni dei BLOB](versioning-enable.md)
- [Creazione di uno snapshot di un BLOB](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Eliminazione temporanea per i BLOB di archiviazione di Azure](storage-blob-soft-delete.md)
