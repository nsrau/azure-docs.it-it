---
title: Ripristino temporizzato per i BLOB in blocchi (anteprima)
titleSuffix: Azure Storage
description: Il ripristino temporizzato per i BLOB in blocchi offre protezione da eliminazioni accidentali o danneggiamenti consentendo di ripristinare uno stato precedente di un account di archiviazione in un determinato momento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 60f83fae6e7e685a1065d1c01327a004d9bb2864
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84675653"
---
# <a name="point-in-time-restore-for-block-blobs-preview"></a>Ripristino temporizzato per i BLOB in blocchi (anteprima)

Il ripristino temporizzato garantisce la protezione da eliminazioni accidentali o danneggiamenti consentendo di ripristinare uno stato precedente dei dati BLOB in blocchi. Il ripristino temporizzato è utile negli scenari in cui un utente o un'applicazione elimina accidentalmente i dati o quando un errore dell'applicazione danneggia i dati. Il ripristino temporizzato consente anche scenari di test che richiedono il ripristino di un set di dati a uno stato noto prima di eseguire ulteriori test.

Per informazioni su come abilitare il ripristino temporizzato per un account di archiviazione, vedere [abilitare e gestire il ripristino temporizzato per i BLOB in blocchi (anteprima)](point-in-time-restore-manage.md).

## <a name="how-point-in-time-restore-works"></a>Funzionamento del ripristino temporizzato

Per abilitare il ripristino temporizzato, è necessario creare un criterio di gestione per l'account di archiviazione e specificare un periodo di conservazione. Durante il periodo di memorizzazione, è possibile ripristinare i BLOB in blocchi dallo stato attuale a uno stato in un momento precedente.

Per avviare un ripristino temporizzato, chiamare l'operazione [Restore Ranges BLOB](/rest/api/storagerp/storageaccounts/restoreblobranges) e specificare un punto di ripristino in ora UTC. È possibile specificare gli intervalli di lessicografico di nomi di contenitori e BLOB da ripristinare oppure omettere l'intervallo per ripristinare tutti i contenitori nell'account di archiviazione. Sono supportati fino a 10 intervalli di lessicografico per ogni operazione di ripristino.

Archiviazione di Azure analizza tutte le modifiche apportate ai BLOB specificati tra il punto di ripristino richiesto, specificato nell'ora UTC e il momento attuale. L'operazione di ripristino è atomica, quindi riesce completamente a ripristinare tutte le modifiche. in caso contrario, l'operazione avrà esito negativo. Se sono presenti BLOB che non possono essere ripristinati, l'operazione ha esito negativo e le operazioni di lettura e scrittura nei contenitori interessati riprendono.

È possibile eseguire una sola operazione di ripristino in un account di archiviazione alla volta. Un'operazione di ripristino non può essere annullata quando è in corso, ma è possibile eseguire una seconda operazione di ripristino per annullare la prima operazione.

L'operazione **Restore Ranges BLOB** restituisce un ID di ripristino che identifica in modo univoco l'operazione. Per controllare lo stato di un ripristino temporizzato, chiamare l'operazione **Get Restore status** con l'ID di ripristino restituito dall'operazione **Restore Ranges BLOB** .

Tenere presenti le limitazioni seguenti per le operazioni di ripristino:

- Un blocco che è stato caricato tramite [Put Block](/rest/api/storageservices/put-block) o [Put Block dall'URL](/rest/api/storageservices/put-block-from-url), ma non eseguito tramite [Put Block List](/rest/api/storageservices/put-block-list), non fa parte di un BLOB e pertanto non viene ripristinato come parte di un'operazione di ripristino.
- Non è possibile ripristinare un BLOB con un lease attivo. Se un BLOB con un lease attivo è incluso nell'intervallo di BLOB da ripristinare, l'operazione di ripristino avrà esito negativo atomicamente.
- Gli snapshot non vengono creati o eliminati come parte di un'operazione di ripristino. Solo il BLOB di base viene ripristinato allo stato precedente.
- Se un BLOB è stato spostato tra i livelli ad accesso frequente e ad accesso sporadico nel periodo compreso tra il momento corrente e il punto di ripristino, il BLOB viene ripristinato al livello precedente. Tuttavia, un BLOB che è stato spostato nel livello archivio non verrà ripristinato.

> [!IMPORTANT]
> Quando si esegue un'operazione di ripristino, archiviazione di Azure blocca le operazioni sui dati nei BLOB negli intervalli da ripristinare per la durata dell'operazione. Le operazioni di lettura, scrittura ed eliminazione sono bloccate nella posizione primaria. Per questo motivo, è possibile che le operazioni come l'elenco dei contenitori nell'portale di Azure non vengano eseguite come previsto mentre è in corso l'operazione di ripristino.
>
> Quando l'account di archiviazione viene replicato geograficamente, è possibile che le operazioni di lettura dalla posizione secondaria continuino durante l'operazione di ripristino.

> [!CAUTION]
> Il ripristino temporizzato supporta solo il ripristino di operazioni su BLOB in blocchi. Non è possibile ripristinare le operazioni su contenitori. Se si elimina un contenitore dall'account di archiviazione chiamando l'operazione [Elimina contenitore](/rest/api/storageservices/delete-container) durante l'anteprima del ripristino temporizzato, il contenitore non può essere ripristinato con un'operazione di ripristino. Durante l'anteprima, invece di eliminare un contenitore, eliminare i singoli BLOB se si prevede di volerli ripristinarli.

### <a name="prerequisites-for-point-in-time-restore"></a>Prerequisiti per il ripristino temporizzato

Per il ripristino temporizzato è necessario che siano abilitate le funzionalità di archiviazione di Azure seguenti:

- [Eliminazione temporanea](soft-delete-overview.md)
- [Feed di modifiche (anteprima)](storage-blob-change-feed.md)
- [Controllo delle versioni dei BLOB (anteprima)](versioning-overview.md)

Abilitare queste funzionalità per l'account di archiviazione prima di abilitare il ripristino temporizzato. Assicurarsi di eseguire la registrazione per le anteprime del feed di modifiche e del controllo delle versioni dei BLOB prima di abilitarli.

### <a name="retention-period-for-point-in-time-restore"></a>Periodo di memorizzazione per il ripristino temporizzato

Quando si Abilita il ripristino temporizzato per un account di archiviazione, è necessario specificare un periodo di conservazione. I BLOB in blocchi nell'account di archiviazione possono essere ripristinati durante il periodo di memorizzazione.

Il periodo di memorizzazione inizia quando si Abilita il ripristino temporizzato. Tenere presente che non è possibile ripristinare i BLOB in uno stato precedente all'inizio del periodo di memorizzazione. Ad esempio, se è stato abilitato il ripristino temporizzato il 1 ° maggio con un periodo di conservazione di 30 giorni, il 15 maggio è possibile eseguire il ripristino fino a un massimo di 15 giorni. Il 1 ° giugno è possibile ripristinare i dati da 1 a 30 giorni.

Il periodo di memorizzazione per il ripristino temporizzato deve essere almeno un giorno inferiore rispetto al periodo di memorizzazione specificato per l'eliminazione temporanea. Se, ad esempio, il periodo di memorizzazione dell'eliminazione temporanea è impostato su 7 giorni, il periodo di conservazione del ripristino temporizzato può essere compreso tra 1 e 6 giorni.

### <a name="permissions-for-point-in-time-restore"></a>Autorizzazioni per il ripristino temporizzato

Per avviare un'operazione di ripristino, un client deve disporre delle autorizzazioni di scrittura per tutti i contenitori nell'account di archiviazione. Per concedere autorizzazioni per autorizzare un'operazione di ripristino con Azure Active Directory (Azure AD), assegnare il ruolo **collaboratore account di archiviazione** all'entità di sicurezza a livello di account di archiviazione, gruppo di risorse o sottoscrizione.

## <a name="about-the-preview"></a>Informazioni sulla versione di anteprima

Il ripristino temporizzato è supportato solo per gli account di archiviazione per utilizzo generico V2. Con il ripristino temporizzato è possibile ripristinare solo i dati nei livelli di accesso ad accesso frequente e sporadico.

Le aree seguenti supportano il ripristino temporizzato in anteprima:

- Canada centrale
- Canada orientale
- Francia centrale

L'anteprima include le limitazioni seguenti:

- Il ripristino di BLOB in blocchi Premium non è supportato.
- Il ripristino di BLOB nel livello di accesso archivio non è supportato. Se, ad esempio, un BLOB nel livello di accesso frequente è stato spostato nel livello di accesso archivio due giorni fa e si esegue un'operazione di ripristino a tre giorni fa, il BLOB non viene ripristinato nel livello di accesso frequente.
- Il ripristino Azure Data Lake Storage Gen2 spazi dei nomi flat e gerarchici non è supportato.
- Il ripristino degli account di archiviazione con chiavi fornite dal cliente non è supportato.

> [!IMPORTANT]
> L'anteprima del ripristino temporizzato è destinata all'uso solo in ambienti non di produzione. I contratti di servizio (SLA) di produzione non sono al momento disponibili.

### <a name="register-for-the-preview"></a>Registrarsi per l'anteprima

Per eseguire la registrazione per l'anteprima, eseguire i comandi seguenti:

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Register for the point-in-time restore preview
Register-AzProviderFeature -FeatureName RestoreBlobRanges -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az feature register --namespace Microsoft.Storage --name RestoreBlobRanges
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>Controllare lo stato della registrazione

La registrazione per il ripristino temporizzato è automatica e dovrebbe richiedere meno di 10 minuti. Per verificare lo stato della registrazione, eseguire i comandi seguenti:

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName RestoreBlobRanges

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/RestoreBlobRanges')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

La fatturazione per il ripristino temporizzato dipende dalla quantità di dati elaborati per eseguire l'operazione di ripristino. La quantità di dati elaborati è basata sul numero di modifiche che si sono verificate tra il punto di ripristino e il momento corrente. Supponendo, ad esempio, una frequenza relativamente costante di modifiche per bloccare i dati BLOB in un account di archiviazione, un'operazione di ripristino che torna indietro nel tempo di 1 giorno comporterebbe il costo 1/10 di un ripristino che torna indietro nel tempo di 10 giorni.

Per stimare il costo di un'operazione di ripristino, esaminare il log del feed delle modifiche per stimare la quantità di dati che è stata modificata durante il periodo di ripristino. Se, ad esempio, il periodo di memorizzazione per il feed delle modifiche è di 30 giorni e le dimensioni del feed delle modifiche sono 10 MB, il ripristino fino a un punto di 10 giorni prima comporterebbe un costo di circa un terzo del prezzo elencato per un account con ridondanza locale in tale area. Il ripristino fino a un punto di 27 giorni prima comporterebbe un costo di circa nove decimi del prezzo elencato.

Per altre informazioni sui prezzi per il ripristino temporizzato, vedere prezzi dei [BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="ask-questions-or-provide-feedback"></a>Porre domande o inviare commenti

Per porre domande sull'anteprima del ripristino temporizzato o per inviare commenti e suggerimenti, contattare Microsoft all'indirizzo pitrdiscussion@microsoft.com .

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare e gestire il ripristino temporizzato per i BLOB in blocchi (anteprima)](point-in-time-restore-manage.md)
- [Supporto del feed di modifiche in Archiviazione BLOB di Azure (anteprima)](storage-blob-change-feed.md)
- [Abilitare l'eliminazione temporanea per i BLOB](soft-delete-enable.md)
- [Abilitare e gestire il controllo delle versioni dei BLOB](versioning-enable.md)
