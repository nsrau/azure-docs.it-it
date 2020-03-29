---
title: Archiviazione BLOB non modificabile - Archiviazione di AzureImmutable blob storage - Azure Storage
description: Archiviazione di Azure offre il supporto WORM (Write Once, Read Many) per l'archiviazione di oggetti BLOB, che consente agli utenti di archiviare i dati in uno stato non cancellabile e non modificabile per un intervallo specificato.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: a980c7bd068a463956191eece43ec1be233e7890
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367619"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Archiviare dati BLOB critici per il business con archiviazione non modificabileStore business-critical blob data with immutable storage

L'archiviazione non modificabile per l'archiviazione BLOB di Azure consente agli utenti di archiviare oggetti dati critici per l'azienda in uno stato WORM (Write Once, Read Many). Questo stato rende i dati non cancellabili e non modificabili per un intervallo di tempo specificato dall'utente. Per la durata dell'intervallo di conservazione, i BLOB possono essere creati e letti, ma non possono essere modificati o eliminati. L'archiviazione non modificabile è disponibile per gli account generici v1, versione 2 generica, BlobStorage e BlockBlobStorage in tutte le aree di Azure.Immutable storage is available for general-purpose v1, general-purpose v2, BlobStorage, and BlockBlobStorage accounts in all Azure regions.

Per informazioni su come impostare e cancellare le conservazioni legali o creare criteri di conservazione basati sul tempo usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure, vedere Impostare e gestire i criteri di [immutabilità per l'archiviazione BLOB.](storage-blob-immutability-policies-manage.md)

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-immutable-blob-storage"></a>Informazioni sull'archiviazione BLOB non modificabile

Lo storage immutabile aiuta l'organizzazione&mdash;sanitaria, le&mdash;istituzioni finanziarie e i settori correlati, in particolare le organizzazioni broker-dealer, a memorizzare i dati in modo sicuro. L'archiviazione non modificabile può anche essere sfruttata in qualsiasi scenario per proteggere i dati critici da modifiche o eliminazioni.

Le applicazioni tipiche includono:

- **Conformità alle normative**: l'archiviazione non modificabile per Archiviazione BLOB di Azure aiuta le organizzazioni a soddisfare i requisiti di SEC 17a-4(f), CFTC 1.31(d), FINRA e altre normative. Un white paper tecnico di Cohasset Associates descrive in dettaglio come lo storage immutabile risolva questi requisiti normativi è scaricabile tramite [Microsoft Service Trust Portal](https://aka.ms/AzureWormStorage). Il [Centro protezione di Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) contiene informazioni dettagliate sulle certificazioni di conformità.

- **Conservazione sicura dei documenti:** l'archiviazione non modificabile per l'archiviazione BLOB di Azure garantisce che i dati non possano essere modificati o eliminati da alcun utente, inclusi gli utenti con privilegi amministrativi dell'account.

- **Conservazione legale:** l'archiviazione non modificabile per l'archiviazione BLOB di Azure consente agli utenti di archiviare informazioni riservate critiche per controversie legali o uso aziendale in uno stato a prova di manomissione per la durata desiderata fino alla rimozione dell'esenzione. Questa funzionalità non è limitata solo ai casi d'uso legali, ma può anche essere considerata come un'esenzione basata su eventi o un blocco aziendale, in cui è richiesta la necessità di proteggere i dati in base ai trigger di evento o ai criteri aziendali.

L'archiviazione non modificabile supporta le funzionalità seguenti:Immutable storage supports the following features:

- Supporto dei criteri di **[conservazione basato sul tempo:](#time-based-retention-policies)** gli utenti possono impostare criteri per archiviare i dati per un intervallo specificato. Quando viene impostato un criterio di conservazione basato sul tempo, i BLOB possono essere creati e letti, ma non modificati o eliminati. Al termine del periodo di conservazione, i BLOB possono essere eliminati ma non sovrascritti.

- Supporto dei **[criteri di conservazione:](#legal-holds)** se l'intervallo di conservazione non è noto, gli utenti possono impostare blocchi legali per archiviare i dati non modificabili fino a quando la conservazione a fini giudiziari non viene cancellata.  Quando viene impostato un criterio di conservazione legale, i BLOB possono essere creati e letti, ma non modificati o eliminati. Ogni conservazione a fini giudiziari è associata a un tag alfanumerico definito dall'utente (ad esempio un ID caso, un nome di evento e così via) utilizzato come stringa identificatore. 

- **Supporto per tutti i livelli BLOB**: i criteri WORM sono indipendenti dal livello di Archiviazione BLOB di Azure e si applicano a tutti i livelli (ad accesso frequente, ad accesso sporadico e archivio). Gli utenti possono trasferire i dati nel livello con i costi ottimali per i carichi di lavoro, mantenendo al tempo stesso la non modificabilità dei dati.

- **Configurazione a livello di contenitore**: gli utenti possono configurare criteri di conservazione basati sul tempo e tag di blocco a fini giudiziari a livello di contenitore. Grazie a semplici impostazioni a livello di contenitore, gli utenti possono creare e bloccare i criteri di conservazione basati sul tempo, estendere gli intervalli di conservazione, impostare e rimuovere i blocchi a fini giudiziari e così via. Questi criteri si applicano a tutti i BLOB nel contenitore, nuovi ed esistenti.

- **Supporto della registrazione di**controllo: ogni contenitore include un log di controllo dei criteri. Mostra fino a sette comandi di conservazione basati sul tempo per i criteri di conservazione basati sul tempo bloccati e contiene l'ID utente, il tipo di comando, gli indicatori di data e ora e l'intervallo di conservazione. Per i blocchi a fini giudiziari, il log contiene l'ID utente, il tipo di comando, i timestamp e i tag di blocco a fini giudiziari. Questo registro viene mantenuto per tutta la durata della politica, in conformità con le linee guida normative SEC 17a-4(f). Il [log attività](../../azure-monitor/platform/platform-logs-overview.md) di Azure mostra un log più completo di tutte le attività del piano di controllo. mentre si abilitano [i log di diagnostica](../../azure-monitor/platform/platform-logs-overview.md) di Azure vengono mantenute e visualizzate le operazioni del piano dati. È responsabilità dell'utente archiviare questi log in modo permanente, come potrebbe essere richiesto per scopi legali o di altro tipo.

## <a name="how-it-works"></a>Funzionamento

L'archiviazione non modificabile per Archiviazione BLOB di Azure supporta due tipi di criteri non modificabili o WORM: conservazione basata sul tempo e blocchi a fini giudiziari. Quando un criterio di conservazione basato sul tempo o un blocco legale viene applicato in un contenitore, tutti i BLOB esistenti vengono spostati in uno stato WORM non modificabile in meno di 30 secondi. Tutti i nuovi BLOB caricati in tale contenitore protetto da criteri verranno spostati anche in uno stato non modificabile. Quando tutti i BLOB sono in uno stato non modificabile, i criteri non modificabili vengono confermati e tutte le operazioni di sovrascrittura o eliminazione nel contenitore non modificabile non sono consentite.

L'eliminazione di contenitori e account di archiviazione non è consentita se sono presenti BLOB in un contenitore protetti da conservazione legale o da criteri basati sul tempo bloccati. I criteri di conservazione legale proteggeranno l'eliminazione di BLOB, contenitori e account di archiviazione. Entrambi i criteri sbloccati e bloccati basati sul tempo proteggeranno dall'eliminazione dei BLOB per il tempo specificato. Entrambi i criteri sbloccati e bloccati basati sul tempo proteggeranno dall'eliminazione del contenitore solo se esiste almeno un BLOB all'interno del contenitore. Solo un contenitore con criteri basati sul tempo *bloccati* proteggerà dalle eliminazioni degli account di archiviazione. I contenitori con criteri basati sul tempo sbloccati non offrono protezione di eliminazione dell'account di archiviazione né conformità.

Per altre informazioni su come impostare e bloccare i criteri di conservazione basati sul tempo, vedere Impostare e gestire i criteri di [immutabilità per l'archiviazione BLOB.](storage-blob-immutability-policies-manage.md)

## <a name="time-based-retention-policies"></a>Criteri di conservazione basati sul tempo

> [!IMPORTANT]
> Un criterio di conservazione basato sul tempo deve essere *bloccato* affinché il BLOB si blocchi in uno stato non modificabile (protetto da scrittura ed eliminazione) conforme per SEC 17a-4(f) e altre conformità alle normative. È consigliabile bloccare il criterio in un periodo di tempo ragionevole, in genere inferiore a 24 ore. Lo stato iniziale di un criterio di conservazione basato sul tempo applicato viene *sbloccato,* consentendo di testare la funzionalità e apportare modifiche al criterio prima di bloccarla. Mentre lo stato *sbloccato* fornisce protezione contro l'immutabilità, non è consigliabile usare lo stato *sbloccato* per scopi diversi dalle prove di funzionalità a breve termine. 

Quando vengono applicati criteri di conservazione basati sul tempo a un contenitore, tutti i BLOB nel contenitore rimangono nello stato non modificabile per la durata del periodo di conservazione *effettivo*. Il periodo di conservazione effettivo per i BLOB è uguale alla differenza tra il tempo di **creazione** del BLOB e l'intervallo di conservazione specificato dall'utente. Poiché gli utenti possono estendere l'intervallo di conservazione, l'archiviazione non modificabile usa il valore più recente dell'intervallo di conservazione specificato dall'utente per calcolare il periodo di conservazione effettivo.

Si supponga, ad esempio, che un utente crei un criterio di conservazione basato sul tempo con un intervallo di conservazione di cinque anni. Un BLOB esistente in tale contenitore, _testblob1_, è stato creato un anno fa. così, il periodo di conservazione effettivo per _testblob1_ è di quattro anni. Quando un nuovo BLOB, _testblob2_, viene caricato nel contenitore, il periodo di conservazione effettivo per _testblob2_ è a cinque anni dal momento della creazione.

Un criterio di conservazione basato su tempo sbloccato è consigliato solo per il test delle funzionalità e un criterio deve essere bloccato per essere conforme a SEC 17a-4(f) e ad altre normative. Una volta che un criterio di conservazione basato sul tempo è bloccato, il criterio non può essere rimosso ed è consentito un massimo di cinque aumenti fino al periodo di conservazione effettivo.

Ai criteri di conservazione si applicano i limiti seguenti:The following limits apply to retention policies:

- Per un account di archiviazione, il numero massimo di contenitori con criteri non modificabili basati sul tempo bloccati è 10.000.For a storage account, the maximum number of containers with locked time-based immutable policies is 10,000.
- L'intervallo di conservazione minimo è 1 giorno. Il massimo è 146.000 giorni (400 anni).
- Per un contenitore, il numero massimo di modifiche per estendere un intervallo di conservazione per i criteri non modificabili basati sul tempo bloccati è 5.For a container, the maximum number of edits to extend a retention interval for locked time-based immutable policies is 5.
- Per un contenitore, per un criterio bloccato vengono mantenuti un massimo di sette registri di controllo dei criteri di conservazione basati sul tempo.

### <a name="allow-protected-append-blobs-writes"></a>Consenti scritture SCRITTURe BLOB di accodamento protettoAllow protected append blobs writes

I BLOB di accodamento sono costituiti da blocchi di dati e ottimizzati per le operazioni di accodamento dei dati richieste dagli scenari di controllo e registrazione. Per impostazione specifica, i BLOB di aggiunta consentono solo l'aggiunta di nuovi blocchi alla fine del BLOB. Indipendentemente dall'immutabilità, la modifica o l'eliminazione di blocchi esistenti in un BLOB di accodamento non è consentita. Per altre informazioni su aggiunta di BLOB, vedere [Informazioni sull'aggiunta](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)di BLOB .

Solo i criteri di `allowProtectedAppendWrites` conservazione basati sul tempo hanno un'impostazione che consente di scrivere nuovi blocchi in un BLOB di accodamento mantenendo al contempo la protezione e la conformità dell'immutabilità. Se abilitata, è possibile creare un BLOB di accodamento direttamente nel contenitore protetto dai criteri e continuare ad aggiungere nuovi blocchi di dati alla fine dei BLOB di accodamento esistenti usando l'API *AppendBlock.If* enabled, you are allowed to create an append blob directly in the policy protected container and continue to add new blocks of data to the end of existing append blobs using the AppendBlock API. È possibile aggiungere solo nuovi blocchi e tutti i blocchi esistenti non possono essere modificati o eliminati. La protezione dall'immutabilità della conservazione del tempo è ancora valida, impedendo l'eliminazione del BLOB di accodamento fino a quando non è trascorso il periodo di conservazione effettivo. L'abilitazione di questa impostazione non influisce sul comportamento di immutabilità dei BLOB in blocchi o dei BLOB di pagine.

Poiché questa impostazione fa parte di un criterio di conservazione basato sul tempo, i BLOB di aggiunta rimangono nello stato non modificabile per la durata del periodo di conservazione *effettivo.* Poiché i nuovi dati possono essere aggiunti oltre la creazione iniziale del BLOB di accodamento, c'è una leggera differenza nel modo in cui viene determinato il periodo di conservazione. La conservazione effettiva è la differenza tra **l'ora dell'ultima modifica** del BLOB di accodamento e l'intervallo di conservazione specificato dall'utente. Analogamente, quando l'intervallo di conservazione viene esteso, l'archiviazione non modificabile utilizza il valore più recente dell'intervallo di conservazione specificato dall'utente per calcolare il periodo di conservazione effettivo.

Si supponga, ad esempio, che un utente `allowProtectedAppendWrites` crei un criterio di conservazione basato sul tempo con abilitato e un intervallo di conservazione di 90 giorni. Nel contenitore _logblob1_di oggi viene creato un BLOB di accodamento, i nuovi log continuano ad essere aggiunti al BLOB di accodamento per i successivi 10 giorni. pertanto, il periodo di conservazione effettivo per _logblob1_ è di 100 giorni a partire da oggi (l'ora dell'ultima aggiunta è di 90 giorni).

I criteri di conservazione basati sul tempo sbloccati consentono di abilitare e disabilitare l'impostazione `allowProtectedAppendWrites` in qualsiasi momento. Una volta che il criterio di `allowProtectedAppendWrites` conservazione basato sul tempo è bloccato, l'impostazione non può essere modificata.

I criteri di `allowProtectedAppendWrites` conservazione a fini giudiziari non possono essere abilitati e qualsiasi conservazione legale annullerà la proprietà 'allowProtectedAppendWrites'. Se una conservazione a fini giudiziari viene `allowProtectedAppendWrites` applicata a un criterio di conservazione basato sul tempo con abilitato, l'API *AppendBlock* avrà esito negativo fino a quando il blocco legale non viene rimosso.

## <a name="legal-holds"></a>Blocchi a fini giudiziari

Le conservazioni legali sono blocchi temporanei che possono essere utilizzati per scopi di indagine legale o politiche di protezione generale. Ogni politica di conservazione a fini giudiziari deve essere associata a uno o più tag. I tag vengono utilizzati come identificatore denominato, ad esempio un ID caso o un evento, per classificare e descrivere lo scopo dell'esenzione.

A un contenitore possono essere applicati contemporaneamente sia criteri di conservazione basati sul tempo che un blocco a fini giudiziari. Tutti i BLOB nel contenitore rimangono nello stato non modificabile finché non vengono rimossi tutti i blocchi a fini giudiziari, anche se il relativo periodo di conservazione effettivo è scaduto. Viceversa, un BLOB rimane in uno stato non modificabile fino alla scadenza del periodo di conservazione effettivo anche se sono stati rimossi tutti i blocchi a fini giudiziari.

Le sospensioni legali si applicano ai seguenti limiti:

- Per un account di archiviazione, il numero massimo di contenitori con un'impostazione di conservazione legale è 10.000.For a storage account, the maximum number of containers with a legal hold setting is 10,000.
- Per un contenitore, il numero massimo di tag di blocco a fini giudiziari è 10.
- La lunghezza minima di un tag di conservazione a fini giudiziari è di tre caratteri alfanumerici. La lunghezza massima è di 23 caratteri alfanumerici.
- Per un contenitore, un massimo di 10 registri di controllo dei criteri di conservazione legale vengono conservati per la durata del criterio.

## <a name="scenarios"></a>Scenari
Nella tabella seguente vengono illustrati i tipi di operazioni di archiviazione BLOB disabilitate per i diversi scenari non modificabili. Per altre informazioni, vedere la documentazione dell'API REST del servizio BLOB di Azure.For more information, see the [Azure Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) documentation.

|Scenario  |Stato BLOB  |Operazioni BLOB negate  |Protezione di container e account
|---------|---------|---------|---------|
|L'intervallo di conservazione effettivo nel BLOB non è ancora scaduto e/o è impostato un blocco a fini giudiziari     |Non modificabile: protetto da eliminazione e scrittura         | Put Blob<sup>1</sup>, Put Block<sup>1</sup>, Put Block List<sup>1</sup>, Delete Container, Delete Blob, Set Blob Metadata, Put Page, Set Blob Properties, Snapshot Blob, Incremental Copy Blob, Append Block<sup>2</sup>         |Eliminazione del contenitore negata; Eliminazione dell'account di archiviazione negata         |
|L'intervallo di conservazione effettivo nel BLOB è scaduto e non è impostata alcuna conservazione a fini giudiziari    |Solo protetto da scrittura (le operazioni di eliminazione sono consentite)         |Put Blob<sup>1</sup>, Put Block<sup>1</sup>, Put Block Block List<sup>1</sup>, Set Blob Metadata, Put Page, Set Blob Properties, Snapshot Blob, Incremental Copy Blob, Append Block<sup>2</sup>         |Eliminazione del contenitore negata se esiste almeno un BLOB all'interno del contenitore protetto. Eliminazione dell'account di archiviazione negata solo per i criteri basati sul tempo *bloccatoStorage* Account deletion denied only for locked time-based policies         |
|Nessun criterio WORM applicato (nessuna conservazione basata sul tempo e nessun tag di conservazione legale)     |Modificabile         |nessuno         |nessuno         |

<sup>1</sup> Il servizio BLOB consente a queste operazioni di creare un nuovo BLOB una sola volta.1 The blob service allows these operations to create a new blob once. Tutte le successive operazioni di sovrascrittura in un percorso BLOB esistente in un contenitore non modificabile non sono consentite.

<sup>2</sup> Il blocco di accodamento è consentito solo per i criteri di conservazione basati sul tempo con la `allowProtectedAppendWrites` proprietà abilitata. Per altre informazioni, vedere la sezione [Consentire scritture BLOB con aggiunta protetta.](#allow-protected-append-blobs-writes)

## <a name="pricing"></a>Prezzi

Per l'uso di questa funzionalità non sono previsti costi aggiuntivi. I dati non modificabili hanno lo stesso prezzo dei dati modificabili. Per informazioni dettagliate sui prezzi nell'archiviazione BLOB di Azure, vedere la pagina dei prezzi di Archiviazione di Azure.For pricing details on Azure Blob storage, see the [Azure Storage pricing page.](https://azure.microsoft.com/pricing/details/storage/blobs/)

## <a name="faq"></a>Domande frequenti

**È possibile fornire la documentazione della conformità WORM?**

Sì. Per documentare la conformità, Microsoft ha mantenuto una società di valutazione indipendente leader specializzata nella gestione dei record e nella governance delle informazioni, Cohasset Associates, per valutare l'archiviazione BLOB non modificabile e la sua conformità con i requisiti specifici dell'industria dei servizi finanziari. Cohasset ha convalidato che l'archiviazione BLOB non modificabile, se usata per mantenere BLOB basati sul tempo in uno stato WORM, soddisfa i requisiti di archiviazione pertinenti della regola CFTC 1.31(c)-(d), della regola FINRA 4511 e della regola SEC 17a-4. Microsoft ha preso di mira questo insieme di regole, in quanto rappresentano le linee guida più prescrittive a livello globale per la conservazione dei record per gli istituti finanziari. Il report Cohasset è disponibile nel [Centro protezione servizi Microsoft](https://aka.ms/AzureWormStorage). Per richiedere una lettera di attestazione da Microsoft per quanto riguarda la conformità di immutabilità WORM, contattare il supporto di Azure.To request a letter of attestation from Microsoft regarding WORM immutability compliance, please contact Azure support.

**La funzionalità si applica solo ai BLOB bloccati e aggiunti o anche ai BLOB di pagine?**

L'archiviazione non modificabile può essere usata con qualsiasi tipo di BLOB come impostato a livello di contenitore, ma è consigliabile usare WORM per i contenitori che archiviano principalmente BLOB a blocchi e aggiungono BLOB. I BLOB esistenti in un contenitore saranno protetti da un criterio WORM appena impostato. Tuttavia, è necessario creare eventuali nuovi BLOB di pagine all'esterno del contenitore WORM e quindi copiarli. Una volta copiato in un contenitore WORM, non sono consentite ulteriori modifiche a un BLOB di pagine. L'impostazione di un criterio WORM in un contenitore in cui sono archiviati i dischi rigidi virtuali (BLOB di pagine) per tutte le macchine virtuali attive è sconsigliata in quanto bloccherà il disco della macchina virtuale. È consigliabile esaminare attentamente la documentazione e testare gli scenari prima di bloccare i criteri basati sul tempo.

**È necessario creare un nuovo account di archiviazione per usare questa funzionalità?**

No, è possibile usare l'archiviazione non modificabile con qualsiasi account v1, v2 generico, BlobStorage o BlockBlobStorage esistente o appena creato. Sono supportati gli account di archiviazione generici v1, ma è consigliabile eseguire l'aggiornamento a una versione 2 generica in modo da poter sfruttare altre funzionalità. Per informazioni sull'aggiornamento di un account di archiviazione v1 generico esistente, vedere [Aggiornare un account di archiviazione.](../common/storage-account-upgrade.md)

**È possibile applicare sia una sospensione legale che un criterio di conservazione basato sul tempo?**

Sì, un contenitore può avere contemporaneamente una conservazione a fini giudiziari e un criterio di conservazione basato sul tempo; Tuttavia, l'impostazione 'allowProtectedAppendWrites' non verrà applicata fino a quando non viene cancellata la conservazione a fini giudiziari. Tutti i BLOB nel contenitore rimangono nello stato non modificabile finché non vengono rimossi tutti i blocchi a fini giudiziari, anche se il relativo periodo di conservazione effettivo è scaduto. Viceversa, un BLOB rimane in uno stato non modificabile fino alla scadenza del periodo di conservazione effettivo anche se sono stati rimossi tutti i blocchi a fini giudiziari. 

**Esistono politiche di conservazione a fini giudiziari solo per procedimenti legali o esistono altri scenari d'uso?**

No, conservazione legale è solo il termine generale utilizzato per un criterio di conservazione non basato sul tempo. Non deve essere utilizzato solo per procedimenti relativi a controversie legali. I criteri di conservazione in attività sono utili per disabilitare la sovrascrittura e le eliminazioni per proteggere i dati WORM aziendali importanti, in cui il periodo di conservazione è sconosciuto. È possibile utilizzarlo come criterio aziendale per proteggere i carichi di lavoro WORM mission-critical o utilizzarlo come criterio di gestione temporanea prima che un trigger di evento personalizzato richieda l'utilizzo di criteri di conservazione basati sul tempo. 

**È possibile rimuovere un criterio di conservazione basato sul tempo _bloccato_ o una conservazione a fini giudiziari?**

Solo i criteri di conservazione basati sul tempo sbloccati possono essere rimossi da un contenitore. Una volta che un criterio di conservazione basato sul tempo è bloccato, non può essere rimosso; sono consentite solo estensioni effettive del periodo di conservazione. I tag di conservazione a fini giudiziari possono essere eliminati. Quando tutti i tag legali vengono eliminati, la conservazione legale viene rimossa.

**Cosa succede se si tenta di eliminare un contenitore con criteri di conservazione basati sul tempo o conservazione a fini giudiziari?**

L'operazione Elimina contenitore avrà esito negativo se almeno un BLOB è presente all'interno del contenitore con un criterio di conservazione basato sul tempo bloccato o sbloccato o se il contenitore dispone di una conservazione legale. L'operazione Elimina contenitore avrà esito positivo solo se non esistono BLOB all'interno del contenitore e non sono presenti blocchi legali. 

**Cosa succede se si tenta di eliminare un account di archiviazione con un contenitore che dispone di criteri di conservazione basati sul tempo o di conservazione legale?**

L'eliminazione dell'account di archiviazione avrà esito negativo se è presente almeno un contenitore con un blocco legale o un criterio basato sul tempo **bloccato.** Un contenitore con criteri basati sul tempo sbloccati non protegge dall'eliminazione dell'account di archiviazione. È necessario rimuovere tutte le conservazioni legali ed eliminare tutti i contenitori **bloccati** prima di poter eliminare l'account di archiviazione. Per informazioni sull'eliminazione di contenitori, vedere la domanda precedente. È anche possibile applicare ulteriori protezioni per le eliminazioni per l'account di archiviazione con i blocchi di [Azure Resource Manager.](../../azure-resource-manager/management/lock-resources.md)

**È possibile spostare i dati tra diversi livelli di BLOB (hot, cool, archive) quando il BLOB si trova nello stato non modificabile?**

Sì, è possibile usare il comando Imposta livello BLOB per spostare i dati tra i livelli BLOB mantenendo i dati nello stato non modificabile conforme. L'archiviazione non modificabile è supportata nei livelli BLOB ad accesso frequente, ad accesso sporadico e archivio.

**Che cosa accade se non si effettua il pagamento e il periodo di conservazione non è scaduto?**

In caso di mancato pagamento, verranno applicati i normali criteri di conservazione dati previsti nei termini e nelle condizioni del contratto stipulato con Microsoft. Per informazioni generali, vedere [Gestione dei dati in Microsoft](https://www.microsoft.com/en-us/trust-center/privacy/data-management). 

**È disponibile una versione di valutazione o un periodo di tolleranza per provare la funzionalità?**

Sì. Quando un criterio di conservazione basato sul tempo viene creato per la prima volta, si trova in uno stato *sbloccato.* In questo stato è possibile apportare qualsiasi modifica all'intervallo di conservazione, ad esempio aumentandolo o riducendolo, ed è anche possibile eliminare i criteri. Dopo che sono stati bloccati, i criteri rimangono bloccati fino alla scadenza dell'intervallo di conservazione. Questo criterio bloccato impedisce l'eliminazione e la modifica dell'intervallo di conservazione. È consigliabile usare lo stato *sbloccato* solo per scopi di valutazione e bloccare i criteri entro un periodo di 24 ore. Ciò consente la conformità a SEC 17a-4(f) e altre normative.

**È possibile usare l'eliminazione temporanea insieme ai criteri BLOB non modificabili?**

Sì, se i requisiti di conformità consentono l'abilitazione dell'eliminazione temporanea. [L'eliminazione temporanea per l'archiviazione BLOB](storage-blob-soft-delete.md) di Azure si applica a tutti i contenitori all'interno di un account di archiviazione, indipendentemente da un blocco legale o da criteri di conservazione basati sul tempo. È consigliabile abilitare l'eliminazione temporanea per una protezione aggiuntiva prima che i criteri WORM non modificabili vengano applicati e confermati.

## <a name="next-steps"></a>Passaggi successivi

- [Impostare e gestire i criteri di immutabilità per l'archiviazione BLOBSet and manage immutability policies for Blob storage](storage-blob-immutability-policies-manage.md)
- [Impostare le regole per la suddivisione ed eliminazione automatica dei dati BLOB con la gestione del ciclo di vitaSet rules to automatically tier and delete blob data with lifecycle management](storage-lifecycle-management-concepts.md)
- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](../blobs/storage-blob-soft-delete.md)
- [Proteggere sottoscrizioni, gruppi di risorse e risorse con i blocchi di Azure Resource Manager.](../../azure-resource-manager/management/lock-resources.md)
