---
title: Ripristino temporizzato per BLOB in blocchi
titleSuffix: Azure Storage
description: Il ripristino temporizzato per i BLOB in blocchi offre protezione da eliminazioni accidentali o danneggiamenti consentendo di ripristinare uno stato precedente di un account di archiviazione in un determinato momento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 32d0c44abed2d4ace4c8896922ed7f6ed8b596ff
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326100"
---
# <a name="point-in-time-restore-for-block-blobs"></a>Ripristino temporizzato per BLOB in blocchi

Il ripristino temporizzato garantisce la protezione da eliminazioni accidentali o danneggiamenti consentendo di ripristinare uno stato precedente dei dati BLOB in blocchi. Il ripristino temporizzato è utile negli scenari in cui un utente o un'applicazione elimina accidentalmente i dati o quando un errore dell'applicazione danneggia i dati. Il ripristino temporizzato consente anche scenari di test che richiedono il ripristino di un set di dati a uno stato noto prima di eseguire ulteriori test.

Il ripristino temporizzato è supportato solo per gli account di archiviazione per utilizzo generico V2. Con il ripristino temporizzato è possibile ripristinare solo i dati nei livelli di accesso ad accesso frequente e sporadico.

Per informazioni su come abilitare il ripristino temporizzato per un account di archiviazione, vedere [eseguire un ripristino temporizzato sui dati BLOB in blocchi](point-in-time-restore-manage.md).

## <a name="how-point-in-time-restore-works"></a>Funzionamento del ripristino temporizzato

Per abilitare il ripristino temporizzato, è necessario creare un criterio di gestione per l'account di archiviazione e specificare un periodo di conservazione. Durante il periodo di memorizzazione, è possibile ripristinare i BLOB in blocchi dallo stato attuale a uno stato in un momento precedente.

Per avviare un ripristino temporizzato, chiamare l'operazione [Restore Ranges BLOB](/rest/api/storagerp/storageaccounts/restoreblobranges) e specificare un punto di ripristino in ora UTC. È possibile specificare gli intervalli di lessicografico di nomi di contenitori e BLOB da ripristinare oppure omettere l'intervallo per ripristinare tutti i contenitori nell'account di archiviazione. Sono supportati fino a 10 intervalli di lessicografico per ogni operazione di ripristino.

Archiviazione di Azure analizza tutte le modifiche apportate ai BLOB specificati tra il punto di ripristino richiesto, specificato nell'ora UTC e il momento attuale. L'operazione di ripristino è atomica, quindi riesce completamente a ripristinare tutte le modifiche. in caso contrario, l'operazione avrà esito negativo. Se sono presenti BLOB che non possono essere ripristinati, l'operazione ha esito negativo e le operazioni di lettura e scrittura nei contenitori interessati riprendono.

È possibile eseguire una sola operazione di ripristino in un account di archiviazione alla volta. Un'operazione di ripristino non può essere annullata quando è in corso, ma è possibile eseguire una seconda operazione di ripristino per annullare la prima operazione.

L'operazione **Restore Ranges BLOB** restituisce un ID di ripristino che identifica in modo univoco l'operazione. Per controllare lo stato di un ripristino temporizzato, chiamare l'operazione **Get Restore status** con l'ID di ripristino restituito dall'operazione **Restore Ranges BLOB** .

> [!IMPORTANT]
> Quando si esegue un'operazione di ripristino, archiviazione di Azure blocca le operazioni sui dati nei BLOB negli intervalli da ripristinare per la durata dell'operazione. Le operazioni di lettura, scrittura ed eliminazione sono bloccate nella posizione primaria. Per questo motivo, è possibile che le operazioni come l'elenco dei contenitori nell'portale di Azure non vengano eseguite come previsto mentre è in corso l'operazione di ripristino.
>
> Quando l'account di archiviazione viene replicato geograficamente, è possibile che le operazioni di lettura dalla posizione secondaria continuino durante l'operazione di ripristino.

> [!CAUTION]
> Il ripristino temporizzato supporta solo il ripristino di operazioni su BLOB in blocchi. Non è possibile ripristinare le operazioni su contenitori. Se si elimina un contenitore dall'account di archiviazione chiamando l'operazione [Delete Container](/rest/api/storageservices/delete-container) , il contenitore non può essere ripristinato con un'operazione di ripristino. Anziché eliminare un contenitore, eliminare i singoli BLOB se si desidera ripristinarli.

### <a name="prerequisites-for-point-in-time-restore"></a>Prerequisiti per il ripristino temporizzato

Il ripristino temporizzato richiede che le funzionalità di archiviazione di Azure seguenti siano abilitate prima che sia possibile abilitare il ripristino temporizzato:

- [Eliminazione temporanea](soft-delete-overview.md)
- [Feed delle modifiche](storage-blob-change-feed.md)
- [Controllo delle versioni dei BLOB](versioning-overview.md)

### <a name="retention-period-for-point-in-time-restore"></a>Periodo di memorizzazione per il ripristino temporizzato

Quando si Abilita il ripristino temporizzato per un account di archiviazione, è necessario specificare un periodo di conservazione. I BLOB in blocchi nell'account di archiviazione possono essere ripristinati durante il periodo di memorizzazione.

Il periodo di memorizzazione inizia quando si Abilita il ripristino temporizzato. Tenere presente che non è possibile ripristinare i BLOB in uno stato precedente all'inizio del periodo di memorizzazione. Ad esempio, se è stato abilitato il ripristino temporizzato il 1 ° maggio con un periodo di conservazione di 30 giorni, il 15 maggio è possibile eseguire il ripristino fino a un massimo di 15 giorni. Il 1 ° giugno è possibile ripristinare i dati da 1 a 30 giorni.

Il periodo di memorizzazione per il ripristino temporizzato deve essere almeno un giorno inferiore rispetto al periodo di memorizzazione specificato per l'eliminazione temporanea. Se, ad esempio, il periodo di memorizzazione dell'eliminazione temporanea è impostato su 7 giorni, il periodo di conservazione del ripristino temporizzato può essere compreso tra 1 e 6 giorni.

### <a name="permissions-for-point-in-time-restore"></a>Autorizzazioni per il ripristino temporizzato

Per avviare un'operazione di ripristino, un client deve disporre delle autorizzazioni di scrittura per tutti i contenitori nell'account di archiviazione. Per concedere autorizzazioni per autorizzare un'operazione di ripristino con Azure Active Directory (Azure AD), assegnare il ruolo **collaboratore account di archiviazione** all'entità di sicurezza a livello di account di archiviazione, gruppo di risorse o sottoscrizione.

## <a name="limitations-and-known-issues"></a>Limitazioni e problemi noti

Il ripristino temporizzato per i BLOB in blocchi presenta le limitazioni e i problemi noti seguenti:

- Come parte di un'operazione di ripristino temporizzato, è possibile ripristinare solo i BLOB in blocchi in un account di archiviazione standard per utilizzo generico V2. I BLOB di Accodamento, i BLOB di pagine e i BLOB in blocchi Premium non vengono ripristinati. 
- Se un contenitore è stato eliminato durante il periodo di memorizzazione, il contenitore non verrà ripristinato con l'operazione di ripristino temporizzato. Se si tenta di ripristinare un intervallo di BLOB che include BLOB in un contenitore eliminato, l'operazione di ripristino temporizzato avrà esito negativo. Per informazioni sulla protezione dei contenitori dall'eliminazione, vedere l'articolo [relativo all'eliminazione temporanea per i contenitori (anteprima)](soft-delete-container-overview.md).
- Se un BLOB è stato spostato tra i livelli ad accesso frequente e ad accesso sporadico nel periodo compreso tra il momento corrente e il punto di ripristino, il BLOB viene ripristinato al livello precedente. Il ripristino dei BLOB in blocchi nel livello archivio non è supportato. Se, ad esempio, un BLOB nel livello di accesso frequente è stato spostato nel livello di accesso archivio due giorni fa e si esegue un'operazione di ripristino a tre giorni fa, il BLOB non viene ripristinato nel livello di accesso frequente. Per ripristinare un BLOB archiviato, spostarlo prima dal livello archivio. Per altre informazioni, vedere [reidratare i dati BLOB dal livello archivio](storage-blob-rehydration.md).
- Un blocco che è stato caricato tramite [Put Block](/rest/api/storageservices/put-block) o [Put Block dall'URL](/rest/api/storageservices/put-block-from-url), ma non eseguito tramite [Put Block List](/rest/api/storageservices/put-block-list), non fa parte di un BLOB e pertanto non viene ripristinato come parte di un'operazione di ripristino.
- Non è possibile ripristinare un BLOB con un lease attivo. Se un BLOB con un lease attivo è incluso nell'intervallo di BLOB da ripristinare, l'operazione di ripristino avrà esito negativo atomicamente. Interrompere eventuali lease attivi prima di avviare l'operazione di ripristino.
- Gli snapshot non vengono creati o eliminati come parte di un'operazione di ripristino. Solo il BLOB di base viene ripristinato allo stato precedente.
- Il ripristino Azure Data Lake Storage Gen2 spazi dei nomi flat e gerarchici non è supportato.

> [!IMPORTANT]
> Se si ripristinano i BLOB in blocchi in un punto precedente al 22 settembre 2020, verranno applicate le limitazioni di anteprima per il ripristino temporizzato. Microsoft consiglia di scegliere un punto di ripristino uguale o successivo al 22 settembre 2020 per sfruttare i vantaggi della funzionalità di ripristino temporizzato disponibile a livello generale.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

La fatturazione per il ripristino temporizzato dipende dalla quantità di dati elaborati per eseguire l'operazione di ripristino. La quantità di dati elaborati è basata sul numero di modifiche che si sono verificate tra il punto di ripristino e il momento corrente. Supponendo, ad esempio, una frequenza relativamente costante di modifiche per bloccare i dati BLOB in un account di archiviazione, un'operazione di ripristino che torna indietro nel tempo di 1 giorno comporterebbe il costo 1/10 di un ripristino che torna indietro nel tempo di 10 giorni.

Per stimare il costo di un'operazione di ripristino, esaminare il log del feed delle modifiche per stimare la quantità di dati che è stata modificata durante il periodo di ripristino. Se, ad esempio, il periodo di memorizzazione per il feed delle modifiche è di 30 giorni e le dimensioni del feed delle modifiche sono 10 MB, il ripristino fino a un punto di 10 giorni prima comporterebbe un costo di circa un terzo del prezzo elencato per un account con ridondanza locale in tale area. Il ripristino fino a un punto di 27 giorni prima comporterebbe un costo di circa nove decimi del prezzo elencato.

Per altre informazioni sui prezzi per il ripristino temporizzato, vedere prezzi dei [BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire un ripristino temporizzato sui dati BLOB in blocchi](point-in-time-restore-manage.md)
- [Supporto del feed delle modifiche nell'archiviazione BLOB di Azure](storage-blob-change-feed.md)
- [Abilitare l'eliminazione temporanea per i BLOB](soft-delete-enable.md)
- [Abilitare e gestire il controllo delle versioni dei BLOB](versioning-enable.md)
