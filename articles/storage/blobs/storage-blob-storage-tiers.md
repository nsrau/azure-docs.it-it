---
title: Livelli di accesso ad accesso frequente, ad accesso sporadico e archivio per i BLOB-archiviazione di Azure
description: Livelli di accesso ad accesso frequente, ad accesso sporadico e archivio per gli account di archiviazione di Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 8f180308133ffba12cc3bffb19130aa7a129da9f
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68952914"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, ad accesso sporadico e archivio

Archiviazione di Azure offre diversi livelli di accesso, che consentono di archiviare i dati degli oggetti BLOB nel modo più conveniente possibile. I livelli di accesso disponibili includono:

- Ottimizzato per l'archiviazione dei dati a cui si accede di frequente.
- **Raffreddamento** ottimizzato per l'archiviazione dei dati a cui si accede raramente e che vengono archiviati per almeno 30 giorni.
- **Archivio** : ottimizzato per l'archiviazione di dati a cui si accede raramente e che vengono archiviati per almeno 180 giorni con requisiti di latenza flessibili (nell'ordine di ore).

Le considerazioni seguenti si applicano ai diversi livelli di accesso:

- Solo i livelli di accesso frequente e ad accesso sporadico possono essere impostati a livello di account. Il livello di accesso all'archivio non è disponibile a livello di account.
- I livelli ad accesso frequente, ad accesso sporadico e archivio possono essere impostati a livello di BLOB.
- I dati nel livello ad accesso sporadico possono tollerare una disponibilità leggermente inferiore, ma richiedono comunque una durabilità elevata, una latenza di recupero e caratteristiche di velocità effettiva simili ai dati sensibili. Per i dati interessanti, un contratto di servizio con disponibilità leggermente inferiore e costi di accesso più elevati rispetto ai dati sensibili sono compromessi accettabili per ridurre i costi di archiviazione.
- Archiviazione archivia i dati non in linea e offre i costi di archiviazione più bassi, ma anche i costi di accesso e reidratazione dei dati più elevati.

La quantità di dati archiviati nel cloud è in crescita esponenziale. Per gestire i costi in base alle esigenze di archiviazione crescenti, può essere utile organizzare i dati in base ad attributi quali la frequenza di accesso e il periodo di conservazione pianificato, in modo da ottimizzare i costi. I dati archiviati nel cloud possono essere diversi in termini di modalità di generazione, elaborazione e accesso per tutta la sua durata. Alcuni dati presentano accessi attivi e modifiche continue nel corso della rispettiva durata. Alcuni dati presentano un accesso frequente nelle fasi iniziali e l'accesso si riduce drasticamente con il passare del tempo. Alcuni dati rimangono inattivi nel cloud ed è possibile accedervi raramente dopo che sono stati archiviati.

Ognuno di questi scenari di accesso ai dati trae vantaggio da un livello di accesso diverso, ottimizzato per un particolare modello di accesso. Con i livelli di accesso ad accesso frequente, sporadico e archivio, l'archiviazione BLOB di Azure risponde a questa esigenza di livelli di accesso differenziati con modelli di prezzi distinti.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Account di archiviazione che supportano la suddivisione in livelli

La suddivisione in livelli dei dati di archiviazione degli oggetti ad accesso frequente, sporadico o archivio è supportata solo negli account di archiviazione BLOB e per utilizzo generico V2 (GPv2). Gli account per utilizzo generico v1 non supportano la suddivisione in livelli. I clienti possono tuttavia convertire facilmente gli account per utilizzo generico v1 o di archiviazione BLOB in account per utilizzo generico v2 tramite un semplice processo con un clic nel portale di Azure. GPv2 offre una nuova struttura dei prezzi per BLOB, file e code, nonché per l'accesso a un'ampia gamma di nuove funzionalità di archiviazione. In futuro inoltre alcune nuove funzionalità e tagli sui prezzi saranno offerti solo negli account per utilizzo generico v2. Pertanto, i clienti devono valutare l'uso di account GPv2 dopo aver esaminato in modo completo i nuovi prezzi perché alcuni carichi di lavoro possono essere più costosi in GPv2 rispetto a utilizzo generico V1. Per altre informazioni, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).

Gli account di archiviazione BLOB e GPv2 espongono l'attributo **livello di accesso** a livello di account. Questo attributo consente di specificare il livello di accesso predefinito come frequente o sporadico per qualsiasi BLOB nell'account di archiviazione per cui non è impostato un livello esplicito a livello di oggetto. Per gli oggetti con il livello impostato a livello di oggetto, il livello di account non verrà applicato. Il livello archivio può essere applicato solo a livello di oggetto. È possibile passare da un livello di accesso all'altro in qualsiasi momento.

## <a name="premium-performance-block-blob-storage"></a>Archiviazione BLOB in blocchi di prestazioni Premium

L'archiviazione BLOB in blocchi di prestazioni Premium rende disponibili i dati ad accesso frequente tramite hardware a prestazioni elevate. I dati in questo livello di prestazioni vengono archiviati in unità SSD (Solid-State Drive), ottimizzate per una latenza bassa e coerente. Le unità SSD forniscono frequenze transazionali e velocità effettiva più elevate rispetto ai dischi rigidi tradizionali.

L'archiviazione BLOB in blocchi di prestazioni Premium è ideale per i carichi di lavoro che richiedono tempi di risposta rapidi e coerenti. È ideale per i carichi di lavoro che eseguono molte piccole transazioni, ad esempio l'acquisizione di dati di telemetria, la messaggistica e la trasformazione dei dati. Anche i dati che coinvolgono gli utenti finali, ad esempio la modifica interattiva dei video, il contenuto Web statico e le transazioni online, sono candidati ottimali.

L'archiviazione BLOB in blocchi di prestazioni Premium è disponibile solo tramite il tipo di account di archiviazione BLOB in blocchi e non supporta attualmente la suddivisione in livelli per i livelli di accesso frequente, sporadico o archivio.

## <a name="hot-access-tier"></a>Livello di accesso frequente

Il livello di accesso frequente presenta costi di archiviazione più elevati rispetto ai livelli di accesso sporadico e archivio, ma i costi di accesso più bassi. Gli scenari di utilizzo di esempio per il livello di accesso a caldo includono:

- Dati usati in modo attivo o a cui è previsto l'accesso (lettura e scrittura) di frequente.
- Dati gestiti temporaneamente per l'elaborazione e l'eventuale migrazione al livello di accesso sporadico.

## <a name="cool-access-tier"></a>Livello di accesso sporadico

Il livello di accesso sporadico presenta costi di archiviazione più bassi e costi di accesso più elevati rispetto all'archiviazione a caldo. Questo livello è destinato ai dati che rimangono nel livello ad accesso sporadico per almeno 30 giorni. Gli scenari di utilizzo di esempio per il livello ad accesso sporadico includono:

- Set di dati di backup e ripristino di emergenza a breve termine.
- Contenuto multimediale meno recente ormai visualizzato non spesso, ma che deve essere immediatamente disponibile quando vi si accede.
- Set di dati di grandi dimensioni da archiviare a costi contenuti mentre vengono raccolti altri dati per l'elaborazione successiva, *ad esempio*, archiviazione a lungo termine di dati scientifici, dati di telemetria non elaborati di un impianto di produzione.

## <a name="archive-access-tier"></a>Livello di accesso archivio

Il livello di accesso all'archivio presenta i costi di archiviazione più bassi e i costi di recupero dei dati più elevati rispetto ai livelli ad accesso frequente e sporadico. Questo livello è destinato ai dati che possono tollerare alcune ore di latenza di recupero e rimarranno nel livello archivio per almeno 180 giorni.

Mentre un BLOB si trova nell'archiviazione dell'archivio, i dati BLOB sono offline e non possono essere letti, copiati, sovrascritti o modificati. Non è possibile creare snapshot di un BLOB nella risorsa di archiviazione dell'archivio. I metadati del BLOB rimangono tuttavia online e disponibili ed è quindi possibile visualizzare il BLOB e le relative proprietà. Per i BLOB nell'archivio, le uniche operazioni valide sono GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier e DeleteBlob.

Gli scenari di utilizzo di esempio per il livello di accesso dell'archivio includono:

- Set di dati di archiviazione, backup secondario e backup a lungo termine
- Dati originali (non elaborati) che devono essere conservati, anche dopo che sono stati elaborati in un formato utilizzabile finale, *ad esempio*, file multimediali non elaborati dopo la transcodifica in altri formati.
- Dati di conformità e di archiviazione che devono essere archiviati per un lungo periodo e a cui non si accede quasi mai, (*Ad esempio*, il metraggio della fotocamera per la sicurezza, i vecchi raggi X/MRIs per le organizzazioni sanitarie, le registrazioni audio e le trascrizioni delle chiamate dei clienti per i servizi finanziari)

### <a name="blob-rehydration"></a>Riattivazione di BLOB

[!INCLUDE [storage-blob-rehydrate-include](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="account-level-tiering"></a>Suddivisione in livelli a livello di account

I BLOB in tutti e tre i livelli di accesso possono coesistere nello stesso account. I BLOB a cui non viene assegnato un livello in modo esplicito lo ereditano dall'impostazione del livello di accesso dell'account. Se il livello di accesso viene derivato dall'account, la proprietà BLOB **Access Tier Inferred** (Livello di accesso derivato) è impostata su "true" e la proprietà BLOB **Livello di accesso** del BLOB corrisponde al livello dell'account. Nella portale di Azure la proprietà del _livello di accesso dedotto_ viene visualizzata con il livello di accesso BLOB come **attivo (dedotto)** o sporadico **(dedotto)** .

La modifica del livello di accesso dell'account si applica a tutti gli oggetti dedotti del _livello di accesso_ archiviati nell'account che non dispongono di un set di livelli esplicito. Se si attiva il livello di account da accesso frequente ad accesso sporadico, verrà addebitato l'importo per le operazioni di scrittura (ogni 10.000) per tutti i BLOB senza un livello impostato solo negli account per utilizzo generico v2. Non è previsto alcun addebito per questa modifica negli account di archiviazione BLOB. Verranno addebitate le operazioni di lettura (per 10.000) e il recupero dati (per GB) se si passa da accesso sporadico ad accesso frequente in account di archiviazione BLOB o GPv2.

## <a name="blob-level-tiering"></a>Organizzazione a livello di BLOB

L'organizzazione a livello di BLOB consente di modificare il livello dei dati a livello di oggetto con una sola operazione, [Imposta livello BLOB](/rest/api/storageservices/set-blob-tier). È possibile modificare facilmente il livello di accesso di un BLOB tra frequente, sporadico o archivio, in base alle variazioni dei modelli di utilizzo, senza dover spostare i dati da un account a un altro. Tutte le modifiche ai livelli vengono applicate immediatamente. La riattivazione di un BLOB dall'archivio, tuttavia, può richiedere diverse ore.

L'ora dell'ultima modifica a livello di BLOB viene esposta tramite la proprietà BLOB **Access Tier Change Time** (Ora modifica livello di accesso). Se un BLOB si trova nel livello archivio, non può essere sovrascritto, quindi il caricamento dello stesso BLOB non è consentito in questo scenario. È possibile sovrascrivere un BLOB in un livello ad accesso frequente o sporadico, nel qual caso il nuovo BLOB eredita il livello del BLOB che è stato sovrascritto.

> [!NOTE]
> Il livello di archiviazione archivio e l'organizzazione a livello di BLOB supportano solo BLOB in blocchi. Attualmente non è possibile modificare il livello di un BLOB in blocchi con snapshot.

### <a name="blob-lifecycle-management"></a>Gestione del ciclo di vita di Archiviazione BLOB

La gestione del ciclo di vita dell'archiviazione BLOB offre criteri avanzati basati su regole che è possibile usare per passare i dati al livello di accesso migliore e per scadere i dati alla fine del ciclo di vita. Per altre informazioni, vedere [Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md).  

> [!NOTE]
> I dati archiviati in un account di archiviazione BLOB in blocchi (prestazioni Premium) attualmente non possono essere suddivisi in livelli ad accesso frequente, sporadico o archivio usando l' [impostazione del livello BLOB](/rest/api/storageservices/set-blob-tier) o la gestione del ciclo di vita dell'archiviazione BLOB di Azure
> Per spostare i dati, è necessario copiare in modo sincrono i BLOB dall'account di archiviazione BLOB in blocchi al livello di accesso frequente in un account diverso usando il [blocco put dall'API URL](/rest/api/storageservices/put-block-from-url) o una versione di AzCopy che supporta questa API.
> L'API *Put Block From URL* copia in modo sincrono i dati nel server, vale a dire che la chiamata viene completata solo dopo che tutti i dati sono stati spostati dal percorso del server originale al percorso di destinazione.

### <a name="blob-level-tiering-billing"></a>Fatturazione per l'organizzazione a livello di BLOB

Quando un BLOB viene spostato in un livello ad accesso più sporadico (frequente -> sporadico, frequente -> archivio o sporadico -> archivio), l'operazione viene fatturata come operazione di scrittura nel livello di destinazione, dove vengono applicati i costi per le operazioni di scrittura (ogni 10.000) e la scrittura dati (per GB).

Quando un BLOB viene spostato in un livello più caldo (Archivio-> sporadico, > ad accesso frequente o ad accesso sporadico > frequente), l'operazione viene fatturata come lettura dal livello di origine, in cui si applicano le operazioni di lettura (per 10.000) e il recupero dati (per GB) del livello di origine. Possono essere addebitati anche i costi delle eliminazioni anticipate per i BLOB spostati al di fuori del livello di accesso sporadico o archivio. La tabella seguente riepiloga come vengono fatturati i vari livelli.

| | **Scrittura addebiti (operazione + accesso)** | **Lettura addebiti (operazione + accesso)**
| ---- | ----- | ----- |
| **Direzione impostazione livello BLOB** | Hot-> Cool,<br> Archivio > a caldo,<br> Archivio > Cool | Archivio-> Cool,<br> Archivio-> attivo,<br> Cool-> Hot

### <a name="cool-and-archive-early-deletion"></a>Eliminazione anticipata per accesso sporadico o archivio

Oltre all'addebito mensile per GB, ogni BLOB che passa al livello ad accesso sporadico (solo account per utilizzo generico v2) è soggetto a un periodo di eliminazione anticipata ad accesso sporadico di 30 giorni e ogni BLOB che passa al livello archivio è soggetto a un periodo di eliminazione anticipata dell'archivio di 180 giorni. Questo addebito è ripartito proporzionalmente. Ad esempio, se un BLOB viene spostato al livello di accesso archivio e quindi eliminato o spostato al livello ad accesso frequente dopo 45 giorni, verrà addebitata una tariffa per eliminazione anticipata equivalente a 135 (180 meno 45) giorni di archiviazione del BLOB nel livello archivio.

Se non sono state apportate modifiche al livello di accesso, è possibile calcolare l'eliminazione anticipata usando la proprietà BLOB, **Last-modified**. In caso contrario, è possibile usare quando il livello di accesso è stato modificato l'ultima volta in un ambiente sporadico o archiviato visualizzando la proprietà BLOB: **Access-Tier-Change-Time**. Per altre informazioni sulle proprietà di BLOB, vedere l'articolo relativo all'operazione [Get Blob Properties](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Confronto tra le opzioni di archiviazione BLOB in blocchi

La tabella seguente illustra un confronto tra l'archiviazione BLOB in blocchi di prestazioni Premium e i livelli di accesso ad accesso frequente, ad accesso sporadico e archivio.

|                                           | **Prestazioni Premium**   | **Livello critico** | **Livello ad accesso sporadico**       | **Livello archivio**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Disponibilità**                          | 99.9%                     | 99.9%        | 99%                 | Offline           |
| **Disponibilità** <br> **(letture RA-GRS)**  | N/D                       | 99,99%       | 99.9%               | Offline           |
| **Costi di utilizzo**                         | Costi di archiviazione più elevati, costi di accesso e transazione inferiori | Costi di archiviazione più elevati e costi di accesso e transazione più bassi | Costi di archiviazione più bassi e costi di accesso e transazione più elevati | Costi di archiviazione minimi e costi di accesso e transazione più alti |
| **Dimensioni minime oggetti**                   | N/D                       | N/D          | N/D                 | N/D               |
| **Durata archiviazione minima**              | N/D                       | N/D          | 30 giorni<sup>1</sup> | 180 giorni
| **Latency** <br> **(tempo per il primo byte)** | Millisecondi a singola cifra | millisecondi | millisecondi        | ore<sup>2</sup> |

<sup>1</sup> gli oggetti nel livello di accesso sporadico per gli account GPv2 hanno una durata di conservazione minima di 30 giorni. Gli account di archiviazione BLOB non hanno una durata minima di conservazione per il livello di accesso sporadico.

<sup>2</sup> spazio di archiviazione supporta attualmente 2 priorità di reidratazione, alta e standard, che offrono latenze di recupero diverse. Per altre informazioni, vedere reidratazione dei [BLOB](#blob-rehydration).

> [!NOTE]
> Gli account di archiviazione BLOB supportano gli stessi obiettivi di prestazioni e scalabilità degli account di archiviazione per utilizzo generico V2. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="quickstart-scenarios"></a>Scenari introduttivi

Questa sezione presenta gli scenari seguenti usando il portale di Azure:

- Come modificare il livello di accesso predefinito di un account di archiviazione BLOB o per utilizzo generico v2.
- Come modificare il livello di un BLOB in un account di archiviazione BLOB o per utilizzo generico v2.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Modificare il livello di accesso all'account predefinito di un account per utilizzo generico v2 o di archiviazione BLOB

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Per passare all'account di archiviazione, selezionare Tutte le risorse, quindi selezionare l'account di archiviazione.

1. Nel pannello Impostazioni fare clic su **Configurazione** per visualizzare e/o modificare la configurazione dell'account.

1. Selezionare il livello di accesso appropriato per le proprie esigenze: Impostare il **livello di accesso** su **ad accesso sporadico** oppure **ad accesso frequente**.

1. Fare clic su **Salva** nella parte superiore del pannello.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Modificare il livello di un BLOB in un account di archiviazione BLOB o GPv2

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Per passare al BLOB nell'account di archiviazione, selezionare **tutte le risorse**, selezionare l'account di archiviazione, selezionare il contenitore e quindi selezionare il BLOB.

1. Nel pannello delle **proprietà del BLOB** selezionare il menu a discesa **livello di accesso** perselezionare il livello di accesso frequente, **ad**accesso sporadico o **Archivio** .

1. Fare clic su **Salva** nella parte superiore del pannello.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Tutti gli account di archiviazione usano un modello di determinazione dei prezzi per l'archiviazione BLOB in blocchi in base al livello di ogni BLOB. Tenere presenti le considerazioni seguenti sulla fatturazione:

- **Costi della risorsa di archiviazione**: Oltre alla quantità di dati archiviati, il costo di archiviazione dei dati varia a seconda del livello di accesso. Il costo per gigabyte diminuisce passando a un livello ad accesso più sporadico.
- **Costi per l'accesso ai dati**: gli addebiti per l'accesso ai dati aumentano passando a un livello ad accesso più sporadico. Per i dati nel livello di accesso ad accesso sporadico e archivio, viene addebitato un costo di accesso ai dati per Gigabyte per le letture.
- **Costi di transazione**: sono previsti costi per transazione per tutti i livelli e tali costi aumentano passando a un livello ad accesso più sporadico.
- **Costi del trasferimento dati con replica geografica**: si applicano solo agli account con replica geografica, incluse l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza geografica e accesso in lettura. Il trasferimento dati con la replica geografica comporta un addebito per gigabyte.
- **Costi di trasferimento dati in uscita**: i trasferimenti dati in uscita (dati che vengono trasferiti al di fuori di un'area di Azure) vengono fatturati in base all'utilizzo della larghezza di banda per singolo gigabyte, come per gli account di archiviazione per utilizzo generico.
- **Modifica del livello di accesso**: La modifica del livello di accesso dell'account comporterà un addebito per la modifica del livello per i BLOB derivati dal _livello di accesso_ archiviati nell'account che non dispongono di un set di livelli esplicito. Per informazioni sulla modifica del livello di accesso per un singolo BLOB, vedere [fatturazione a livelli a livello di BLOB](#blob-level-tiering-billing).

> [!NOTE]
> Per altre informazioni sui prezzi per i BLOB in blocchi, vedere la pagina [prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) . Per altre informazioni sugli addebiti per i trasferimenti dati in uscita, vedere la pagina [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Domande frequenti

**È consigliabile usare account di archiviazione BLOB o per utilizzo generico v2 se si vogliono suddividere in livelli i dati?**

Per la suddivisione in livelli è consigliabile usare account per utilizzo generico v2 anziché di archiviazione BLOB. Gli account per utilizzo generico v2 supportano tutte le funzionalità supportate dagli account di archiviazione BLOB e molte altre. I prezzi di archiviazione BLOB e utilizzo generico v2 sono quasi identici, ma alcune nuove funzionalità e alcuni tagli sui prezzi saranno disponibili solo negli account per utilizzo generico v2. Gli account per utilizzo generico v1 non supportano la suddivisione in livelli.

Il piano tariffario per gli account per utilizzo generico v1 e v2 è diverso e i clienti devono valutarli entrambi con attenzione prima di decidere di usare gli account per utilizzo generico v2. È possibile convertire facilmente un account di archiviazione BLOB o per utilizzo generico v1 esistente in account per utilizzo generico v2 tramite un semplice processo con un clic. Per altre informazioni, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).

**È possibile archiviare gli oggetti in tutti e tre i livelli di accesso (ad accesso frequente, sporadico e archivio) nello stesso account?**

Sì. L'attributo livello di **accesso** impostato a livello di account è il livello di account predefinito che si applica a tutti gli oggetti in tale account senza un livello impostato esplicito. L'organizzazione a livello di BLOB, tuttavia, consente di impostare il livello di accesso a livello di oggetto indipendentemente dal livello di accesso impostato per l'account. I BLOB in uno dei tre livelli di accesso (ad accesso frequente, sporadico o Archivio) potrebbero esistere nello stesso account.

**È possibile modificare il livello di accesso predefinito dell'account di archiviazione BLOB o GPv2?**

Sì, è possibile modificare il livello di account predefinito impostando l'attributo **livello di accesso** nell'account di archiviazione. La modifica del livello dell'account si applica a tutti gli oggetti archiviati nell'account che non dispongono di un set di livelli esplicito, ad esempio **Hot (dedotto)** o **cool (dedotto**). L'attivazione/disattivazione del livello account dalle operazioni di scrittura ad accesso frequente a sporadico (per 10.000) per tutti i BLOB senza un livello di impostazione solo per gli account GPv2 e l'attivazione/disattivazione da accesso sporadico a frequente comporta sia operazioni di lettura (per 10.000) che costi di recupero dati (per GB) per tutti i BLOB nell'archiviazione BLOB e account GPv2.

**È possibile impostare il livello di accesso all'account predefinito su archivio?**

No. Solo i livelli di accesso frequente e ad accesso sporadico possono essere impostati come livello di accesso dell'account predefinito. L'archivio può essere impostato solo a livello di oggetto.

**In quali aree sono disponibili i livelli di accesso ad accesso frequente, ad accesso sporadico e archivio?**

I livelli di accesso frequente e sporadico con la suddivisione in livelli a livello di BLOB sono disponibili in tutte le aree. Lo spazio di archiviazione sarà inizialmente disponibile solo in alcune aree. Per un elenco completo, vedere [Prodotti di Azure disponibili in base all'area](https://azure.microsoft.com/regions/services/).

**I BLOB nel livello ad accesso sporadico si comportano in modo diverso rispetto a quelli del livello di accesso frequente?**

I BLOB nel livello di accesso frequente hanno la stessa latenza dei BLOB negli account di archiviazione BLOB e utilizzo generico V1, GPv2. I BLOB nel livello di accesso sporadico hanno una latenza simile (in millisecondi) come BLOB negli account di archiviazione BLOB e utilizzo generico V1, GPv2. I BLOB nel livello di accesso dell'archivio hanno diverse ore di latenza negli account di archiviazione BLOB e utilizzo generico V1, GPv2.

I BLOB nel livello ad accesso sporadico hanno un livello di servizio di disponibilità leggermente inferiore rispetto ai BLOB archiviati nel livello di accesso frequente. Per altre informazioni, vedere [Contratto di Servizio per Archiviazione](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Le operazioni nei livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio sono le stesse?**

Tutte le operazioni nell'accesso frequente e nell'accesso sporadico sono coerenti al 100%. Tutte le operazioni di archiviazione valide, tra cui GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier e DeleteBlob, sono coerenti al 100% con accesso frequente e sporadico. Non è possibile leggere o modificare i dati BLOB nel livello archivio fino a quando non viene reidratato; solo le operazioni di lettura dei metadati BLOB sono supportate durante l'archiviazione.

**Quando si riattiva un BLOB dal livello archivio al livello ad accesso frequente o sporadico, come è possibile sapere quando la riattivazione è stata completata?**

Durante la riattivazione, è possibile usare l'operazione di acquisizione delle proprietà BLOB per eseguire il polling dell'attributo **Archive Status** (Stato di archiviazione) per verificare quando la modifica del livello è completa. Lo stato può essere "rehydrate-pending-to-hot" o "rehydrate-pending-to-cool" a seconda del livello di destinazione. Al termine, la proprietà relativa allo stato di archiviazione viene rimossa e la proprietà BLOB **Livello di accesso** indica un il nuovo livello frequente o sporadico.  

**Dopo avere impostato il livello di un BLOB, quando inizieranno a essere fatturati i costi con la tariffa appropriata?**

Ogni BLOB viene sempre fatturato in base al livello indicato dalla proprietà **Livello di accesso** del BLOB. Quando si imposta un nuovo livello per un BLOB, la proprietà **Livello di accesso** rispecchia immediatamente il nuovo livello per tutte le transizioni. La riattivazione di un BLOB dal livello archivio a un livello ad accesso frequente o sporadico, tuttavia, può richiedere diverse ore. In questo caso i costi vengono fatturati con le tariffe archivio fino al completamento della riattivazione, quando la proprietà **Livello di accesso** rispecchia il nuovo livello. Da questo momento i costi del BLOB vengono fatturati con la tariffa per l'accesso frequente o sporadico.

**Come si determina se verranno addebitati i costi per un'eliminazione anticipata quando si elimina o si sposta un BLOB al di fuori del livello ad accesso sporadico o archivio?**

Per i BLOB eliminati o spostati al di fuori del livello ad accesso sporadico (solo in caso di account per utilizzo generico v2) o del livello archivio rispettivamente prima di 30 giorni e 180 giorni verrà addebitato un costo per eliminazione anticipata ripartito proporzionalmente. Per determinare per quanto tempo un BLOB è stato nel livello ad accesso sporadico o archivio, controllare la proprietà BLOB **Access Tier Change Time** (Ora modifica livello di accesso), che fornisce un indicatore dell'ultima modifica del livello. Se il livello del BLOB non è mai stato modificato, è possibile controllare la proprietà BLOB dell' **Ultima modifica** . Per altre informazioni, vedere [eliminazione anticipata e archiviazione](#cool-and-archive-early-deletion)ad accesso sporadico.

**Quali SDK e strumenti di Azure supportano la risorsa di archiviazione e l'organizzazione a livello di BLOB?**

Il portale di Azure, PowerShell e gli strumenti dell'interfaccia della riga di comando e le librerie client .NET, Java, Python e Node.js supportano tutti l'organizzazione a livello di BLOB e la risorsa di archiviazione.  

**Quanti dati è possibile archiviare nei livelli ad accesso frequente, ad accesso sporadico e archivio?**

L'archiviazione dei dati insieme ad altri limiti viene impostata a livello di account e non per livello di accesso. È quindi possibile scegliere di usare tutti i limiti in un solo livello o in tutti e tre i livelli. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Passaggi successivi

### <a name="evaluate-hot-cool-and-archive-in-gpv2-and-blob-storage-accounts"></a>Valutazione di accesso frequente, ad accesso sporadico e archivio negli account di archiviazione BLOB e GPv2

[Controllare la disponibilità di accesso frequente, accesso sporadico e archivio in base all'area](https://azure.microsoft.com/regions/#services)

[Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)

[Valutare l'utilizzo degli account di archiviazione attuali abilitando le metriche di Archiviazione di Azure](../common/storage-enable-and-view-metrics.md)

[Controllare i prezzi di accesso frequente, accesso sporadico e archivio negli account di archiviazione BLOB e per utilizzo generico v2 in base all'area](https://azure.microsoft.com/pricing/details/storage/)

[Verificare i prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/)
