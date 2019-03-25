---
title: Accesso frequente, sporadico e archivio i livelli di accesso per i BLOB - archiviazione di Azure
description: Accesso frequente, sporadico e archivio i livelli di accesso per gli account di archiviazione di Azure.
services: storage
author: Xansky
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: mhopkins
ms.subservice: blobs
ms.openlocfilehash: 424ca1cccd1b82c26a801af3f85f41eb2991e2d7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370126"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Archiviazione Blob di Azure: accesso frequente, ad accesso sporadico e archivio i livelli di accesso

Archiviazione di Azure offre livelli di accesso diversi, che consentono di archiviare dati dell'oggetto blob nel modo economicamente più conveniente. I livelli di accesso disponibili includono:

- **Hot** : ottimizzato per l'archiviazione dei dati a cui si accede di frequente.
- **Ad accesso sporadico** : ottimizzato per l'archiviazione dei dati archiviati per almeno 30 giorni e si accede raramente.
- **Archivio** : ottimizzato per l'archiviazione dei dati archiviati per almeno 180 giorni con requisiti di latenza flessibili (nell'ordine di ore) e si accede raramente.

Le considerazioni seguenti si applicano ai livelli di accesso diversi:

- Il livello di accesso archivio è disponibile solo a livello di blob e non a livello di account di archiviazione.
- I dati nel livello di accesso sporadico possono tollerare una disponibilità leggermente più bassa, ma richiedono ugualmente una durabilità elevata e caratteristiche di fase di accesso e la velocità effettiva simili come i dati attivi. Per i dati ad accesso sporadico, una leggermente più bassa disponibilità contratto di servizio (SLA) e l'accesso più alti i costi rispetto ai dati di accesso frequente sono compromessi accettabili in cambio di costi di archiviazione inferiori.
- L'archiviazione archivio è offline e offre i costi di archiviazione più bassi, ma anche i costi di accesso più alti.
- Solo i livelli di accesso frequente e sporadico possono essere impostati a livello di account.
- Accesso frequente, accesso sporadico e archivio può essere impostata a livello di oggetto.

La quantità di dati archiviati nel cloud è in crescita esponenziale. Per gestire i costi in base alle esigenze di archiviazione crescenti, può essere utile organizzare i dati in base ad attributi quali la frequenza di accesso e il periodo di conservazione pianificato, in modo da ottimizzare i costi. Dati archiviati nel cloud possono essere diversi in termini di modalità ha generato, elaborati e dell'accesso nel corso del tempo. Alcuni dati presentano accessi attivi e modifiche continue nel corso della rispettiva durata. Alcuni dati presentano un accesso frequente nelle fasi iniziali e l'accesso si riduce drasticamente con il passare del tempo. Alcuni dati rimangono inattivi nel cloud ed sono raramente, se accessibile che mai, dopo che viene archiviato.

Ognuno di questi scenari di accesso dati usufruiscono di un livello di accesso diversi che è ottimizzato per un particolare modello di accesso. Con accesso frequente, sporadico e archivio i livelli di accesso, indirizzi di archiviazione Blob di Azure l'esigenza di livelli di accesso differenziato con modelli di determinazione prezzi distinti.

## <a name="storage-accounts-that-support-tiering"></a>Account di archiviazione che supportano la suddivisione in livelli

Può solo i dati di archiviazione di oggetti ad accesso frequente, accesso sporadico a livello, o archiviare in archiviazione Blob e utilizzo generico v2 (utilizzo generico v2). Gli account per utilizzo generico v1 non supportano la suddivisione in livelli. Tuttavia, è possibile convertire facilmente esistente gli account di archiviazione Blob o per utilizzo generico v1 ad account per utilizzo generico v2 tramite un processo con un clic nel portale di Azure. Per utilizzo generico v2 offre un nuovo piano tariffario per BLOB, file, code e l'accesso a una varietà di altre nuove funzionalità di archiviazione. In futuro, alcune nuove funzionalità e tagli sui prezzi verranno offerti solo negli account per utilizzo generico v2. Pertanto, è consigliabile valutare l'utilizzo degli account per utilizzo generico v2 ma usare solo dopo aver esaminato i prezzi per tutti i servizi. Alcuni carichi di lavoro può essere più costosi in per utilizzo generico v2 per utilizzo generico v1. Per altre informazioni, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).

Archiviazione BLOB ed espongono gli account per utilizzo generico v2 il **livello di accesso** attributo a livello di account. Questo attributo consente di specificare il livello di accesso predefinito come accesso frequente o sporadico per tutti i blob nell'account di archiviazione che non sono impostati a livello di oggetto di un livello esplicito. Per gli oggetti con il livello impostato a livello di oggetto, il livello di account non verrà applicato. Il livello archivio può essere applicato solo a livello di oggetto. È possibile passare tra questi livelli di accesso in qualsiasi momento.

## <a name="premium-performance-block-blob-storage"></a>Archiviazione blob in blocchi prestazioni Premium

Archiviazione blob in blocchi prestazioni Premium dati utilizzati di frequente è disponibili tramite hardware ad alte prestazioni. I dati in questo livello di prestazioni vengono archiviati in unità SSD (Solid State Drive), che sono ottimizzate per la latenza bassa e coerenza. Le unità SSD offrono tassi transazionale maggiori e velocità effettiva rispetto a unità disco rigido tradizionali.

Archiviazione blob in blocchi prestazioni Premium è ideale per carichi di lavoro che richiedono tempi di risposta veloci e coerenti. È consigliabile per i carichi di lavoro che eseguono molte transazioni di dimensioni ridotte, ad esempio l'acquisizione dei dati di telemetria, messaggistica e trasformazione dei dati. Inoltre, i dati che coinvolge gli utenti finali, ad esempio la modifica di video interattivo, contenuto web statico e le transazioni online sono buoni candidati.

Archiviazione blob in blocchi prestazioni Premium è disponibile solo tramite il tipo di account archiviazione blob di blocco e attualmente non supporta la suddivisione in livelli ad accesso frequente, sporadico o archivio i livelli di accesso.

## <a name="hot-access-tier"></a>Livello di accesso frequente

Il livello ad accesso frequente ha costi di archiviazione più elevati rispetto a livelli ad accesso sporadico e archivio, ma i costi di accesso più bassi. Scenari di utilizzo di esempio per il livello ad accesso frequente includono:

- Dati di uso attivo o previsto l'accesso (lettura da e scritti) frequentemente.
- Dati di gestione temporanea per l'elaborazione e l'eventuale migrazione al livello accesso sporadico.

## <a name="cool-access-tier"></a>Livello di accesso sporadico

Il livello accesso sporadico ha costi di archiviazione più bassi e costi di accesso superiore rispetto all'archiviazione ad accesso frequente. Questo livello è destinato ai dati che rimangono nel livello ad accesso sporadico per almeno 30 giorni. Gli scenari di utilizzo di esempio per il livello accesso sporadico includono:

- Set di dati di backup e ripristino di emergenza a breve termine.
- Contenuto multimediale meno recente ormai visualizzato non spesso, ma che deve essere immediatamente disponibile quando vi si accede.
- Set di dati di grandi dimensioni da archiviare a costi contenuti mentre vengono raccolti altri dati per l'elaborazione successiva, *ad esempio*, archiviazione a lungo termine di dati scientifici, dati di telemetria non elaborati di un impianto di produzione.

## <a name="archive-access-tier"></a>Livello di accesso archivio

Il livello di accesso archivio ha il più basso costo di archiviazione e i costi di recupero dati più alti rispetto ai livelli di accesso frequente e sporadico. Questo livello è destinato ai dati che possono tollerare alcune ore di latenza di recupero e rimarranno nel livello archivio per almeno 180 giorni.

Mentre nel livello archivio è un blob, i dati del blob sono offline e non possono essere letto, copiato, sovrascritto o modificato. Non è possibile acquisire delle snapshot dei blob nel livello archivio. I metadati del BLOB rimangono tuttavia online e disponibili ed è quindi possibile visualizzare il BLOB e le relative proprietà. Per i BLOB nell'archivio, le uniche operazioni valide sono GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier e DeleteBlob.

Gli scenari di utilizzo di esempio per il livello di accesso archivio includono:

- Set di dati di archiviazione, backup secondario e backup a lungo termine
- Dati originali (non elaborati) che devono essere conservati, anche dopo che sono stati elaborati in un formato utilizzabile finale, *ad esempio*, file multimediali non elaborati dopo la transcodifica in altri formati.
- Dati di conformità e di archiviazione che devono essere archiviati per un lungo periodo e a cui non si accede quasi mai, (*Ad esempio*, videocamere di sicurezza, vecchie radiografie/risonanze magnetiche per le organizzazioni sanitarie, registrazioni audio e trascrizioni personali del cliente chiamato per servizi finanziari)

### <a name="blob-rehydration"></a>Riattivazione di BLOB

Per leggere i dati nel livello archivio, è prima necessario modificare il livello di accesso del BLOB in frequente o sporadico. Questo processo è noto come riattivazione e può richiedere fino a 15 ore. Per ottenere prestazioni ottimali, si consigliano BLOB di grandi dimensioni. La riattivazione simultanea di diversi BLOB di piccole dimensioni, può richiedere altro tempo.

Durante la riattivazione è possibile controllare la proprietà BLOB **Archive Status** (Stato di archiviazione) per assicurarsi che il livello sia stato modificato. Lo stato può essere "rehydrate-pending-to-hot" o "rehydrate-pending-to-cool" a seconda del livello di destinazione. Al termine, la proprietà relativa allo stato di archiviazione viene rimossa e la proprietà BLOB **Livello di accesso** indica un il nuovo livello frequente o sporadico.  

## <a name="blob-level-tiering"></a>Organizzazione a livello di BLOB

L'organizzazione a livello di BLOB consente di modificare il livello dei dati a livello di oggetto con una sola operazione, [Imposta livello BLOB](/rest/api/storageservices/set-blob-tier). È possibile modificare facilmente il livello di accesso di un BLOB tra frequente, sporadico o archivio, in base alle variazioni dei modelli di utilizzo, senza dover spostare i dati da un account a un altro. Tutte le modifiche ai livelli vengono applicate immediatamente. La riattivazione di un BLOB dall'archivio, tuttavia, può richiedere diverse ore.

L'ora dell'ultima modifica a livello di BLOB viene esposta tramite la proprietà BLOB **Access Tier Change Time** (Ora modifica livello di accesso). Se un blob nel livello archivio, che non può essere sovrascritto, in modo da caricare lo stesso blob non è consentito in questo scenario. È possibile sovrascrivere un blob in un livello ad accesso frequente o sporadico, nel quale caso il nuovo blob eredita il livello di blob che è stato sovrascritto.

I BLOB in tutti i livelli di accesso tre possono coesistere nello stesso account. I BLOB a cui non viene assegnato un livello in modo esplicito lo ereditano dall'impostazione del livello di accesso dell'account. Se il livello di accesso viene derivato dall'account, la proprietà BLOB **Access Tier Inferred** (Livello di accesso derivato) è impostata su "true" e la proprietà BLOB **Livello di accesso** del BLOB corrisponde al livello dell'account. Nel portale di Azure, il livello di accesso derivato proprietà viene visualizzata con livello di accesso ai blob (ad esempio, **Hot (dedotto)** o **ad accesso sporadico (dedotto)**).

> [!NOTE]
> Il livello di archiviazione archivio e l'organizzazione a livello di BLOB supportano solo BLOB in blocchi. Non è inoltre possibile modificare il livello di un BLOB in blocchi con snapshot.

> [!NOTE]
> Attualmente non sono a livelli i dati archiviati in un account di archiviazione blob di blocchi per accesso frequente, ad accesso sporadico o archivio usando [Set Blob Tier](/rest/api/storageservices/set-blob-tier) o tramite gestione del ciclo di vita di archiviazione Blob di Azure.
> Per spostare i dati, è necessario copiare in modo sincrono i BLOB dall'account di archiviazione blob di blocco al livello accesso frequente in un account diverso utilizzando la [Put Block dall'API dell'URL](/rest/api/storageservices/put-block-from-url) o una versione di AzCopy che supporta questa API.
> L'API *Put Block From URL* copia in modo sincrono i dati nel server, vale a dire che la chiamata viene completata solo dopo che tutti i dati sono stati spostati dal percorso del server originale al percorso di destinazione.

### <a name="blob-lifecycle-management"></a>Gestione del ciclo di vita di Archiviazione BLOB

La gestione del ciclo di vita di Archiviazione BLOB (anteprima) offre criteri avanzati basati su regole che è possibile usare per trasferire i dati al livello di accesso più appropriato e definirne la scadenza al termine del relativo ciclo di vita. Per altre informazioni, vedere [Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md).  

### <a name="blob-level-tiering-billing"></a>Fatturazione per l'organizzazione a livello di BLOB

Quando un BLOB viene spostato in un livello ad accesso più sporadico (frequente -> sporadico, frequente -> archivio o sporadico -> archivio), l'operazione viene fatturata come operazione di scrittura nel livello di destinazione, dove vengono applicati i costi per le operazioni di scrittura (ogni 10.000) e la scrittura dati (per GB).

Quando un blob viene spostato a un livello più frequente (archivio -> sporadico, archivio -> frequente o sporadico -> frequente), l'operazione viene fatturata come una lettura dal livello di origine, in cui applicare l'operazione di lettura (ogni 10.000) e gli addebiti per il recupero (per GB) di dati del livello di origine. La tabella seguente riepiloga come vengono fatturati i vari livelli.

| | **Scrittura addebiti (operazione + accesso)** | **Lettura addebiti (operazione + accesso)**
| ---- | ----- | ----- |
| **Direzione impostazione livello BLOB** | frequente -> sporadico, frequente -> archivio, ad accesso sporadico -> archivio | archivio -> sporadico, archivio -> frequente, sporadico -> frequente

Se si attiva il livello di account da accesso frequente ad accesso sporadico, verrà addebitato l'importo per le operazioni di scrittura (ogni 10.000) per tutti i BLOB senza un livello impostato solo negli account per utilizzo generico v2. Non è previsto alcun addebito per questa modifica negli account di archiviazione BLOB. Se un account di archiviazione BLOB o per utilizzo generico v2 passa dal livello ad accesso sporadico al livello ad accesso frequente, verranno addebitati sia le operazioni di lettura (ogni 10.000) che il recupero dati (per GB). Possono essere addebitati anche i costi delle eliminazioni anticipate per i BLOB spostati al di fuori del livello di accesso sporadico o archivio.

### <a name="cool-and-archive-early-deletion"></a>Eliminazione anticipata per accesso sporadico o archivio

Oltre all'addebito mensile per GB, ogni BLOB che passa al livello ad accesso sporadico (solo account per utilizzo generico v2) è soggetto a un periodo di eliminazione anticipata ad accesso sporadico di 30 giorni e ogni BLOB che passa al livello archivio è soggetto a un periodo di eliminazione anticipata dell'archivio di 180 giorni. Questo addebito è ripartito proporzionalmente. Ad esempio, se un BLOB viene spostato al livello di accesso archivio e quindi eliminato o spostato al livello ad accesso frequente dopo 45 giorni, verrà addebitata una tariffa per eliminazione anticipata equivalente a 135 (180 meno 45) giorni di archiviazione del BLOB nel livello archivio.

È possibile calcolare l'eliminazione anticipata usando la proprietà di BLOB, **creation-time**, se non si è verificata alcuna variazione dei livelli di accesso. In caso contrario, è possibile usare il livello di accesso data dell'ultima modifica da accesso sporadico o archivio visualizzando la proprietà blob: **livello di accesso-ora di modifica**. Per altre informazioni sulle proprietà di BLOB, vedere l'articolo relativo all'operazione [Get Blob Properties](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Confronto tra le opzioni di archiviazione blob di blocco

La seguente tabella mostra un confronto tra archiviazione blob in blocchi prestazioni premium e l'accesso frequente, sporadico e archivio al livello di accesso.

|                                           | **Prestazioni Premium** | **Livello ad accesso frequente** | **Livello ad accesso sporadico** | **Livello spazio di archiviazione**
| ----------------------------------------- | ---------------- | ------------ | ----- | ----- |
| **Disponibilità**                          | 99,9%            | 99,9%        | 99% | N/D |
| **Disponibilità** <br> **(letture RA-GRS)**  | N/D              | 99,99%       | 99,9% | N/D |
| **Costi di utilizzo**                         | Costi di archiviazione più elevati, accesso più basso e costi di transazione | Costi di archiviazione più elevati e costi di accesso e transazione più bassi | Costi di archiviazione più bassi e costi di accesso e transazione più elevati | Costi di archiviazione minimi e costi di accesso e transazione più alti |
| **Dimensioni minime oggetti**                   | N/D | N/D | N/D | N/D |
| **Durata archiviazione minima**              | N/D | N/D | 30 giorni (solo per utilizzo generico v2) | 180 giorni
| **Latency** <br> **(tempo per il primo byte)** | Millisecondi a cifra singola | millisecondi | millisecondi | Meno di 15 ore

> [!NOTE]
> Per obiettivi di scalabilità e prestazioni, vedere [obiettivi di scalabilità e prestazioni di archiviazione di Azure per gli account di archiviazione](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="quickstart-scenarios"></a>Scenari introduttivi

Questa sezione presenta gli scenari seguenti usando il portale di Azure:

- Come modificare il livello di accesso predefinito di un account di archiviazione BLOB o per utilizzo generico v2.
- Come modificare il livello di un BLOB in un account di archiviazione BLOB o per utilizzo generico v2.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Modificare il livello di accesso all'account predefinito di un account per utilizzo generico v2 o di archiviazione BLOB

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Per passare all'account di archiviazione, selezionare Tutte le risorse, quindi selezionare l'account di archiviazione.

1. Nel pannello Impostazioni fare clic su **Configurazione** per visualizzare e/o modificare la configurazione dell'account.

1. Selezionare il livello di accesso corretti per le proprie esigenze: Impostare il **livello di accesso** su **ad accesso sporadico** oppure **ad accesso frequente**.

1. Fare clic su **Salva** nella parte superiore del pannello.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Modificare il livello di un blob in un account di archiviazione Blob o per utilizzo generico v2

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Per passare al blob nell'account di archiviazione, selezionare **tutte le risorse**, selezionare l'account di archiviazione, selezionare il contenitore e quindi selezionare il blob.

1. Nel **proprietà Blob** pannello Seleziona la **livello di accesso** dal menu a discesa per selezionare il **frequente**, **ad accesso sporadico**, o **archivio**  livello di accesso.

1. Fare clic su **Salva** nella parte superiore del pannello.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Tutti gli account di archiviazione usano per l'archivio BLOB un modello di determinazione prezzi basato sul livello di ogni BLOB. Tenere presenti le considerazioni seguenti sulla fatturazione:

- **Costi della risorsa di archiviazione**: Oltre alla quantità di dati archiviati, il costo di archiviazione dei dati varia a seconda del livello di accesso. Il costo per gigabyte diminuisce passando a un livello ad accesso più sporadico.
- **Costi per l'accesso ai dati**: gli addebiti per l'accesso ai dati aumentano passando a un livello ad accesso più sporadico. Per i dati nel livello di accesso ad accesso sporadico e archivio, viene addebitato un costo di accesso dei dati per gigabyte per le letture.
- **Costi di transazione**: sono previsti costi per transazione per tutti i livelli e tali costi aumentano passando a un livello ad accesso più sporadico.
- **Costi del trasferimento dati con replica geografica**: si applicano solo agli account con replica geografica, incluse l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza geografica e accesso in lettura. Il trasferimento dati con la replica geografica comporta un addebito per gigabyte.
- **Costi di trasferimento dati in uscita**: i trasferimenti dati in uscita (dati che vengono trasferiti al di fuori di un'area di Azure) vengono fatturati in base all'utilizzo della larghezza di banda per singolo gigabyte, come per gli account di archiviazione per utilizzo generico.
- **La modifica del livello di accesso**: Modifica del livello di accesso account da sporadico a frequente comporta un addebito corrispondente a quello per la lettura di tutti i dati esistenti nell'account di archiviazione. Il passaggio dell'account dal livello di accesso all'archiviazione ad accesso frequente a quello ad accesso sporadico comporta invece un addebito corrispondente a quello per la scrittura di tutti i dati nel livello ad accesso sporadico (solo per account per utilizzo generico v2).

> [!NOTE]
> Per altre informazioni sulla determinazione prezzi per gli account di archiviazione BLOB, vedere la pagina [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/). Per altre informazioni sugli addebiti per i trasferimenti dati in uscita, vedere la pagina [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Domande frequenti

**È consigliabile usare account di archiviazione BLOB o per utilizzo generico v2 se si vogliono suddividere in livelli i dati?**

Per la suddivisione in livelli è consigliabile usare account per utilizzo generico v2 anziché di archiviazione BLOB. Gli account per utilizzo generico v2 supportano tutte le funzionalità supportate dagli account di archiviazione BLOB e molte altre. I prezzi di archiviazione BLOB e utilizzo generico v2 sono quasi identici, ma alcune nuove funzionalità e alcuni tagli sui prezzi saranno disponibili solo negli account per utilizzo generico v2. Gli account per utilizzo generico v1 non supportano la suddivisione in livelli.

Il piano tariffario per gli account per utilizzo generico v1 e v2 è diverso e i clienti devono valutarli entrambi con attenzione prima di decidere di usare gli account per utilizzo generico v2. È possibile convertire facilmente un account di archiviazione BLOB o per utilizzo generico v1 esistente in account per utilizzo generico v2 tramite un semplice processo con un clic. Per altre informazioni, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).

**È possibile archiviare gli oggetti in tutti e tre (frequente, sporadico e archivio) al livello nello stesso account di accesso?**

Sì. L'attributo **Livello di accesso** impostato a livello di account rappresenta il livello predefinito che si applica a tutti gli oggetti in tale account in assenza di un livello impostato in modo esplicito. L'organizzazione a livello di BLOB, tuttavia, consente di impostare il livello di accesso a livello di oggetto indipendentemente dal livello di accesso impostato per l'account. I BLOB in tutti i tre livelli di accesso (frequente, sporadico o archivio) può esistere nello stesso account.

**È possibile modificare il livello di accesso predefinita dell'account di archiviazione Blob o per utilizzo generico v2?**

Sì, è possibile modificare il livello di accesso predefinito impostando la **livello di accesso** attributo dell'account di archiviazione. La modifica del livello di accesso si applica a tutti gli oggetti archiviati nell'account che non hanno un livello esplicito. Attivazione/disattivazione del livello di accesso da accesso frequente ad accesso sporadico comporta le operazioni di scrittura (ogni 10.000) per tutti i BLOB senza un livello impostato in solo gli account per utilizzo generico v2 e attivazione/disattivazione da sporadico a frequente comporta entrambe le operazioni di lettura (ogni 10.000) e gli addebiti per il recupero dei dati (per GB) per tutti i BLOB nell'archiviazione Blob e gli account per utilizzo generico v2.

**È possibile impostare il livello di accesso all'account predefinito su archivio?**

 No. Solo i livelli di accesso frequente e sporadico possono essere impostati come il livello di accesso predefinito. L'archivio può essere impostato solo a livello di oggetto.

**In cui le aree sono di frequente, sporadico e archivio i livelli di accesso disponibili in?**

I livelli ad accesso sporadico e accesso con la suddivisione in livelli a livello di blob sono disponibili in tutte le aree. Lo spazio di archiviazione sarà inizialmente disponibile solo in alcune aree. Per un elenco completo, vedere [Prodotti di Azure disponibili in base all'area](https://azure.microsoft.com/regions/services/).

**I BLOB nel livello di accesso sporadico si comportano in modo diverso rispetto a quelli del livello di accesso frequente?**

I BLOB nel livello di accesso frequente hanno la stessa latenza dei blob negli account di archiviazione per utilizzo generico V1, v2 e Blob. I BLOB nel livello di accesso sporadico hanno una latenza simile, in millisecondi, come BLOB negli account di archiviazione per utilizzo generico V1, v2 e Blob. I BLOB nel livello di accesso archivio prevedono diverse ore di latenza negli account di archiviazione per utilizzo generico V1, v2 e Blob.

I BLOB nel livello di accesso sporadico hanno un leggermente più bassa disponibilità servizio a livello di (servizio SLA) rispetto a quello dei BLOB nel livello di accesso frequente. Per altre informazioni, vedere [Contratto di Servizio per Archiviazione](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Le operazioni nei livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio sono le stesse?**

Sì. Tutte le operazioni nell'accesso frequente e nell'accesso sporadico sono coerenti al 100%. Tutte le operazioni di archiviazione valide, tra cui eliminazione, elenco, acquisizione di proprietà/metadati BLOB e impostazione del livello BLOB, sono coerenti al 100% con l'accesso frequente e l'accesso sporadico. Un BLOB non può essere letto o modificato mentre è nel livello archivio.

**Quando si riattiva un BLOB dal livello archivio al livello ad accesso frequente o sporadico, come è possibile sapere quando la riattivazione è stata completata?**

Durante la riattivazione, è possibile usare l'operazione di acquisizione delle proprietà BLOB per eseguire il polling dell'attributo **Archive Status** (Stato di archiviazione) per verificare quando la modifica del livello è completa. Lo stato può essere "rehydrate-pending-to-hot" o "rehydrate-pending-to-cool" a seconda del livello di destinazione. Al termine, la proprietà relativa allo stato di archiviazione viene rimossa e la proprietà BLOB **Livello di accesso** indica un il nuovo livello frequente o sporadico.  

**Dopo avere impostato il livello di un BLOB, quando inizieranno a essere fatturati i costi con la tariffa appropriata?**

Ogni BLOB viene sempre fatturato in base al livello indicato dalla proprietà **Livello di accesso** del BLOB. Quando si imposta un nuovo livello per un BLOB, la proprietà **Livello di accesso** rispecchia immediatamente il nuovo livello per tutte le transizioni. La riattivazione di un BLOB dal livello archivio a un livello ad accesso frequente o sporadico, tuttavia, può richiedere diverse ore. In questo caso i costi vengono fatturati con le tariffe archivio fino al completamento della riattivazione, quando la proprietà **Livello di accesso** rispecchia il nuovo livello. Da questo momento i costi del BLOB vengono fatturati con la tariffa per l'accesso frequente o sporadico.

**Come si determina se verranno addebitati i costi per un'eliminazione anticipata quando si elimina o si sposta un BLOB al di fuori del livello ad accesso sporadico o archivio?**

Per i BLOB eliminati o spostati al di fuori del livello ad accesso sporadico (solo in caso di account per utilizzo generico v2) o del livello archivio rispettivamente prima di 30 giorni e 180 giorni verrà addebitato un costo per eliminazione anticipata ripartito proporzionalmente. Per determinare per quanto tempo un BLOB è stato nel livello ad accesso sporadico o archivio, controllare la proprietà BLOB **Access Tier Change Time** (Ora modifica livello di accesso), che fornisce un indicatore dell'ultima modifica del livello. Per altre informazioni, vedere [eliminazione anticipata per accesso sporadico e archivio](#cool-and-archive-early-deletion).

**Quali SDK e strumenti di Azure supportano la risorsa di archiviazione e l'organizzazione a livello di BLOB?**

Il portale di Azure, PowerShell e gli strumenti dell'interfaccia della riga di comando e le librerie client .NET, Java, Python e Node.js supportano tutti l'organizzazione a livello di BLOB e la risorsa di archiviazione.  

**Quanti dati è possibile archiviare nei livelli ad accesso frequente, ad accesso sporadico e archivio?**

Archiviazione dei dati e gli altri limiti vengono impostati a livello di account e non per ogni livello di accesso. È quindi possibile scegliere di usare tutti i limiti in un solo livello o in tutti e tre i livelli. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Passaggi successivi

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Valutare i livelli ad accesso frequente, ad accesso sporadico e archivio negli account di archiviazione BLOB e per utilizzo generico v2

[Controllare la disponibilità di accesso frequente, accesso sporadico e archivio in base all'area](https://azure.microsoft.com/regions/#services)

[Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)

[Valutare l'utilizzo degli account di archiviazione attuali abilitando le metriche di Archiviazione di Azure](../common/storage-enable-and-view-metrics.md)

[Controllare i prezzi di accesso frequente, accesso sporadico e archivio negli account di archiviazione BLOB e per utilizzo generico v2 in base all'area](https://azure.microsoft.com/pricing/details/storage/)

[Verificare i prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/)