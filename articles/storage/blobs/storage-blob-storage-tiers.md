---
title: Livelli di archiviazione di Azure ad accesso frequente, ad accesso sporadico e archivio per i BLOB | Microsoft Docs
description: Livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio per account di archiviazione di Azure.
services: storage
documentationcenter: 
author: kuhussai
manager: jwillis
editor: 
ms.assetid: eb33ed4f-1b17-4fd6-82e2-8d5372800eef
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/11/2017
ms.author: kuhussai
ms.openlocfilehash: fd3ca18fd7a9d1226d41229d37e637a62478f92a
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-blob-storage-hot-cool-and-archive-storage-tiers"></a>Archivio BLOB di Azure: livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio

## <a name="overview"></a>Panoramica

Archiviazione di Azure offre tre livelli di archiviazione per l'archivio di oggetti BLOB, per consentire di archiviare i dati nel modo economicamente più conveniente in base alla modalità d'uso. Il **livello di archiviazione ad accesso frequente** di Azure è ottimizzato per l'archiviazione di dati a cui si accede di frequente. Il **livello di archiviazione ad accesso sporadico** di Azure è ottimizzato per l'archiviazione di dati a cui si accede poco frequentemente e che vengono archiviati per almeno 30 giorni. Il **livello di archiviazione archivio** di Azure è ottimizzato per l'archiviazione di dati a cui si accede raramente e che vengono archiviati per almeno 180 giorni con requisiti di latenza flessibili, nell'ordine di ore. Il livello di archiviazione archivio è disponibile solo a livello di BLOB e non a livello di account di archiviazione. I dati nel livello di archiviazione ad accesso sporadico possono tollerare una disponibilità leggermente più bassa, ma richiedono ugualmente una durabilità elevata e caratteristiche di velocità effettiva e tempo di accesso simili a quelle dei dati ad accesso frequente. Per i dati ad accesso sporadico, contratti di servizio con una disponibilità leggermente più bassa e costi di accesso più alti rispetto a quelli per i dati ad accesso frequente sono compromessi accettabili in cambio di costi di archiviazione più bassi. L'archiviazione archivio è offline e offre i costi di archiviazione più bassi, ma anche i costi di accesso più alti.

La quantità di dati archiviati nel cloud è attualmente in crescita esponenziale. Per gestire i costi in base alle esigenze di archiviazione crescenti, può essere utile organizzare i dati in base ad attributi quali la frequenza di accesso e il periodo di conservazione pianificato, in modo da ottimizzare i costi. I dati archiviati nel cloud possono essere diversi dal punto di vista della generazione, dell'elaborazione e dell'accesso nel corso del tempo. Alcuni dati presentano accessi attivi e modifiche continue nel corso della rispettiva durata. Alcuni dati presentano un accesso frequente nelle fasi iniziali e l'accesso si riduce drasticamente con il passare del tempo. Alcuni dati rimangono inattivi sul cloud e gli utenti vi accedono raramente, se non mai, dopo l'archiviazione.

Tutti questi scenari di accesso ai dati usufruiscono di un livello di archiviazione diverso, ottimizzato per un particolare modello di accesso. Con i livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio, l'archiviazione BLOB di Azure soddisfa l'esigenza di avere livelli di archiviazione differenziati con modelli di determinazione prezzi distinti.

## <a name="storage-accounts-that-support-tiering"></a>Account di archiviazione che supportano la suddivisione in livelli

È possibile suddividere in livelli i dati di archiviazione di oggetti in dati ad accesso frequente, ad accesso sporadico o archivio solo in account di archiviazione BLOB o per utilizzo generico v2. Gli account per utilizzo generico v1 non supportano la suddivisione in livelli. I clienti possono tuttavia convertire facilmente gli account per utilizzo generico v1 o di archiviazione BLOB in account per utilizzo generico v2 tramite un semplice processo con un clic nel portale di Azure. L'utilizzo generico v2 fornisce un nuovo piano tariffario per BLOB, file e code, oltre all'accesso a diverse altre nuove funzionalità di archiviazione. In futuro inoltre alcune nuove funzionalità e tagli sui prezzi saranno offerti solo negli account per utilizzo generico v2. I clienti dovrebbero quindi valutare l'uso degli account v2, ma solo dopo avere verificato i prezzi per tutti i servizi perché alcuni carichi di lavoro nell'utilizzo generico v2 possono essere più costosi che nell'utilizzo generico v1. Per altre informazioni, vedere [Opzioni di account di archiviazione di Azure](storage-account-options.md).

Gli account per utilizzo generico v2 espongono l'attributo **Livello di accesso** a livello di account, che consente di specificare il livello di archiviazione predefinito come ad accesso frequente o ad accesso sporadico per tutti i BLOB dell'account di archiviazione il cui livello non è impostato a livello di oggetto. Per gli oggetti con il livello impostato a livello di oggetto, il livello di account non verrà applicato. Il livello archivio è applicabile solo a livello di oggetto. È possibile passare da uno di questi livelli di archiviazione all'altro in qualsiasi momento.

## <a name="hot-access-tier"></a>Livello di accesso frequente

L'archiviazione ad accesso frequente ha costi di archiviazione più alti rispetto a quella ad accesso sporadico e al livello archivio, ma costi di accesso più bassi. Gli scenari di utilizzo di esempio per il livello di archiviazione ad accesso frequente includono:

* Dati di uso attivo e o di cui è previsto l'accesso (lettura o scrittura) di frequente.
* Dati di gestione temporanea per l'elaborazione e l'eventuale migrazione al livello di archiviazione ad accesso sporadico.

## <a name="cool-access-tier"></a>Livello di accesso sporadico

Il livello di archiviazione ad accesso sporadico ha costi di archiviazione più bassi e costi di accesso più alti rispetto all'archiviazione ad accesso frequente. Questo livello è destinato ai dati che rimangono nel livello ad accesso sporadico per almeno 30 giorni. Gli scenari di utilizzo di esempio per il livello di archiviazione ad accesso sporadico includono:

* Set di dati di backup e ripristino di emergenza a breve termine.
* Contenuto multimediale meno recente ormai visualizzato non spesso, ma che deve essere immediatamente disponibile quando vi si accede.
* Set di dati di grandi dimensioni da archiviare a costi contenuti mentre vengono raccolti altri dati per l'elaborazione successiva, *ad esempio*, archiviazione a lungo termine di dati scientifici, dati di telemetria non elaborati di un impianto di produzione.

## <a name="archive-access-tier"></a>Livello di accesso archivio

Il livello di archiviazione archivio ha costi di archiviazione più bassi e costi di recupero dati più alti rispetto all'archiviazione ad accesso sporadico e ad accesso frequente. Questo livello è destinato ai dati che possono tollerare alcune ore di latenza di recupero e rimarranno nel livello archivio per almeno 180 giorni.

Quando un BLOB si trova nel livello di archiviazione archivio, è offline e non può essere letto (eccetto i metadati che sono online e disponibili), copiato, sovrascritto o modificato. o creare snapshot del BLOB. È tuttavia possibile usare le operazioni esistenti per eliminarli, elencarli, ottenere proprietà o metadati dei BLOB oppure modificare il livello dei BLOB.

Gli scenari di utilizzo di esempio per il livello di archiviazione archivio includono:

* Set di dati di backup, archiviazione e ripristino di emergenza a lungo termine
* Dati originali (non elaborati) che devono essere conservati, anche dopo che sono stati elaborati in un formato utilizzabile finale, *ad esempio*, file multimediali non elaborati dopo la transcodifica in altri formati.
* Dati di conformità e di archiviazione che devono essere archiviati per un lungo periodo e a cui non si accede quasi mai, *ad esempio*, filmati di videocamere di sicurezza, vecchie radiografie/risonanze magnetiche per le organizzazioni sanitarie, registrazioni audio e trascrizioni di chiamate di clienti per i servizi finanziari.

### <a name="blob-rehydration"></a>Riattivazione di BLOB
Per leggere i dati nel livello archivio, è prima necessario modificare il livello di accesso del BLOB in frequente o sporadico. Questo processo è noto come riattivazione e può richiedere fino a 15 ore. Per ottenere prestazioni ottimali, si consigliano BLOB di grandi dimensioni. La riattivazione simultanea di diversi BLOB di piccole dimensioni, può richiedere altro tempo.

Durante la riattivazione è possibile controllare la proprietà BLOB **Archive Status** (Stato di archiviazione) per assicurarsi che il livello sia stato modificato. Lo stato può essere "rehydrate-pending-to-hot" o "rehydrate-pending-to-cool" a seconda del livello di destinazione. Al termine, la proprietà relativa allo stato di archiviazione viene rimossa e la proprietà BLOB **Livello di accesso** indica un il nuovo livello frequente o sporadico.  

## <a name="blob-level-tiering"></a>Organizzazione a livello di BLOB

L'organizzazione a livello di BLOB consente di modificare il livello dei dati a livello di oggetto con una sola operazione, [Imposta livello BLOB](/rest/api/storageservices/set-blob-tier). È possibile modificare facilmente il livello di accesso di un BLOB tra frequente, sporadico o archivio, in base alle variazioni dei modelli di utilizzo, senza dover spostare i dati da un account a un altro. Tutte le modifiche ai livelli sono immediate, a eccezione della riattivazione di un BLOB dal livello archivio che può richiedere alcune ore. L'ora dell'ultima modifica a livello di BLOB viene esposta tramite la proprietà BLOB **Access Tier Change Time** (Ora modifica livello di accesso). Se un BLOB si trova nel livello archivio non può essere sovrascritto e quindi in questo scenario non è consentito caricare lo stesso BLOB. È possibile sovrascrivere un BLOB nei livelli ad accesso frequente e sporadico e in questo caso il nuovo BLOB eredita il livello del BLOB precedente che è stato sovrascritto.

In tutti e tre i livelli di archiviazione i BLOB possono coesistere nello stesso account. I BLOB a cui non viene assegnato un livello in modo esplicito lo ereditano dall'impostazione del livello di accesso dell'account. Se il livello di accesso viene derivato dall'account, la proprietà BLOB **Access Tier Inferred** (Livello di accesso derivato) è impostata su "true" e la proprietà BLOB **Livello di accesso** del BLOB corrisponde al livello dell'account. Nel portale di Azure la proprietà Access Tier Inferred (Livello di accesso derivato) è visualizzata insieme al livello di accesso del BLOB, ad esempio Hot (inferred) (Frequente - derivato) o Cool (inferred) (Sporadico - derivato).

> [!NOTE]
> Il livello di archiviazione archivio e l'organizzazione a livello di BLOB supportano solo BLOB in blocchi. Non è inoltre possibile modificare il livello di un BLOB in blocchi con snapshot.

### <a name="blob-level-tiering-billing"></a>Fatturazione per l'organizzazione a livello di BLOB

Quando un BLOB viene spostato in un livello ad accesso più sporadico (frequente -> sporadico, frequente -> archivio o sporadico -> archivio), l'operazione viene fatturata come operazione di scrittura nel livello di destinazione e vengono applicati i costi per le operazioni di scrittura (ogni 10.000) e la scrittura dati (per GB). Se un BLOB viene spostato in un livello ad accesso più frequente (archivio -> sporadico, archivio -> frequente o sporadico -> frequente), l'operazione viene fatturata come un'operazione di lettura nel livello di origine e vengono applicati i costi per le operazioni di lettura (ogni 10.000) e il recupero dati (per GB).

Se si attiva il livello di account da accesso frequente ad accesso sporadico, verrà addebitato l'importo per le operazioni di scrittura (ogni 10.000) per tutti i BLOB senza un livello impostato solo negli account per utilizzo generico v2. Non è previsto alcun addebito negli account di archiviazione BLOB. Verranno addebitate sia le operazioni di lettura (ogni 10.000) che il recupero dati (per GB) quando si attiva l'account di archiviazione BLOB o per utilizzo generico v2 dal livello accesso sporadico al livello accesso frequente. Possono essere addebitati anche i costi delle eliminazioni anticipate per i BLOB spostati al di fuori del livello di accesso sporadico o archivio.

### <a name="cool-and-archive-early-deletion-effective-february-1-2018"></a>Eliminazione anticipata per accesso sporadico o archivio (in vigore dal 1° febbraio 2018)

Oltre all'addebito mensile per GB, ogni BLOB che passa al livello ad accesso sporadico (solo account per utilizzo generico v2) è soggetto a un periodo di eliminazione anticipata ad accesso sporadico di 30 giorni e ogni BLOB che passa al livello archivio è soggetto a un periodo di eliminazione anticipata dell'archivio di 180 giorni. Questo addebito è ripartito proporzionalmente. Ad esempio, se un BLOB viene spostato al livello di accesso archivio e quindi eliminato o spostato al livello ad accesso frequente dopo 45 giorni, verrà addebitata una tariffa per eliminazione anticipata equivalente a 135 (180 meno 45) giorni di archiviazione del BLOB nel livello archivio.

## <a name="comparison-of-the-storage-tiers"></a>Confronto tra i livelli di archiviazione

La tabella seguente illustra un confronto tra i livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio.

| | **Livello di archiviazione ad accesso frequente** | **Livello di archiviazione ad accesso sporadico** | **Livello di archiviazione archivio**
| ---- | ----- | ----- | ----- |
| **Disponibilità** | 99,9% | 99% | N/D |
| **Disponibilità** <br> **(letture RA-GRS)**| 99,99% | 99,9% | N/D |
| **Costi di utilizzo** | Costi di archiviazione più elevati e costi di accesso e transazione più bassi | Costi di archiviazione più bassi e costi di accesso e transazione più elevati | Costi di archiviazione più bassi e costi di accesso e transazione più alti |
| **Dimensioni minime oggetti** | N/D | N/D | N/D |
| **Durata archiviazione minima** | N/D | 30 giorni (solo per utilizzo generico v2) | 180 giorni
| **Latency** <br> **(tempo per il primo byte)** | millisecondi | millisecondi | Meno di 15 ore
| **Obiettivi di scalabilità e prestazioni** | Uguali a quelli degli account di archiviazione di uso generico | Uguali a quelli degli account di archiviazione di uso generico | Uguali a quelli degli account di archiviazione di uso generico |

> [!NOTE]
> Gli account di archiviazione BLOB supportano gli stessi obiettivi di prestazioni e scalabilità degli account di archiviazione per utilizzo generico. Per ulteriori informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

## <a name="quickstart-scenarios"></a>Scenari introduttivi

Questa sezione presenta gli scenari seguenti usando il portale di Azure:

* Come modificare il livello di accesso all'account predefinito di un account per utilizzo generico v2 o di archiviazione BLOB.
* Come modificare il livello di un BLOB in un account per utilizzo generico v2 o di archiviazione BLOB.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Modificare il livello di accesso all'account predefinito di un account per utilizzo generico v2 o di archiviazione BLOB

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Per passare all'account di archiviazione, selezionare Tutte le risorse, quindi selezionare l'account di archiviazione.

3. Nel pannello Impostazioni fare clic su **Configurazione** per visualizzare e/o modificare la configurazione dell'account.

4. Selezionare il livello di archiviazione corretto per le proprie esigenze: impostare il **livello di accesso** su **sporadico** o **frequente**.

5. Fare clic su Salva nella parte superiore del pannello.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Modificare il livello di un BLOB in un account per utilizzo generico v2 o di archiviazione BLOB

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Per passare al BLOB nell'account di archiviazione, selezionare Tutte le risorse, selezionare l'account di archiviazione, selezionare il contenitore e quindi selezionare il BLOB.

3. Nel pannello delle proprietà del BLOB fare clic sul menu a discesa **Livello di accesso** per selezionare il livello di archiviazione **Frequente**, **Sporadico** o **Archivio**.

5. Fare clic su Salva nella parte superiore del pannello.

## <a name="faq"></a>domande frequenti

**È consigliabile usare gli account di archiviazione BLOB o per utilizzo generico v2 se si vuole suddividere in livelli i dati?**

È consigliabile usare gli account per utilizzo generico v2 invece di quelli di archiviazione BLOB per la suddivisione in livelli. L'utilizzo generico v2 supporta tutte le funzionalità supportate dagli account di archiviazione BLOB oltre a molte altre. I prezzi dell'archivio BLOB e dell'utilizzo generico v2 sono quasi identici, ma alcune nuove funzionalità e tagli sui prezzi saranno disponibili solo negli account per utilizzo generico v2. Gli account per utilizzo generico v1 non supportano la suddivisione in livelli.

Il piano tariffario per gli account per utilizzo generico v1 e v2 è diverso e i clienti devono valutarli entrambi con attenzione prima di decidere di usare gli account per utilizzo generico v2. È possibile convertire facilmente un account di archiviazione BLOB o per utilizzo generico v1 esistente in utilizzo generico v2 tramite un semplice processo con un clic. Per altre informazioni, vedere [Opzioni di account di archiviazione di Azure](storage-account-options.md).

**È possibile archiviare oggetti in tutti e tre i livelli di archiviazione (ad accesso frequente, ad accesso sporadico e archivio) nello stesso account?**

Sì. L'attributo **Livello di accesso** impostato a livello di account rappresenta il livello predefinito che si applica a tutti gli oggetti in tale account in assenza di un livello impostato in modo esplicito. L'organizzazione a livello di BLOB, tuttavia, consente di impostare il livello di accesso a livello di oggetto indipendentemente dal livello di accesso impostato per l'account. I BLOB in tutti e tre i livelli di archiviazione (frequente, sporadico o archivio) possono trovarsi nello stesso account.

**È possibile modificare il livello di archiviazione predefinito nell'account di archiviazione BLOB o per utilizzo generico v2?**

Sì, è possibile modificare il livello di archiviazione predefinito impostando l'attributo **Livello di accesso** nell'account di archiviazione. La modifica del livello di archiviazione si applica a tutti gli oggetti archiviati nell'account per i quali non è impostato in modo esplicito un livello. L'attivazione del livello di archiviazione da frequente a sporadico comporta l'addebito dei costi per le operazioni di scrittura (ogni 10.000) per tutti i BLOB senza un livello impostato solo negli account per utilizzo generico v2 e l'attivazione da sporadico a frequente comporta l'addebito dei costi sia per le operazioni di lettura (ogni 10.000) che di quelli per il recupero dati (per GB) per tutti i BLOB negli account di archiviazione BLOB e per utilizzo generico v2.

**È possibile impostare il livello di accesso all'account predefinito su archivio?**

No. Solo i livelli di archiviazione ad accesso sporadico e ad accesso frequente possono essere impostati come livello di accesso all'account predefinito. L'archivio può essere impostato solo a livello di oggetto.

**In quali aree sono disponibili i livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio?**

I livello di archiviazione ad accesso frequente e ad accesso sporadico con l'organizzazione a livello di BLOB sono disponibili in tutte le aree. Lo spazio di archiviazione sarà inizialmente disponibile solo in alcune aree. Per un elenco completo, vedere [Prodotti di Azure disponibili in base all'area](https://azure.microsoft.com/regions/services/).

**I BLOB nel livello di archiviazione ad accesso sporadico si comportano in modo diverso rispetto a quelli del livello di archiviazione ad accesso frequente?**

I BLOB nel livello di archiviazione ad accesso frequente hanno la stessa latenza dei BLOB negli account di archiviazione per utilizzo generico v1, v2 e BLOB. I BLOB nel livello di archiviazione ad accesso sporadico hanno una latenza simile, in millisecondi, a quella dei BLOB negli account di archiviazione per utilizzo generico v1, v2 e BLOB. I BLOB nel livello di archiviazione archivio prevedono diverse ore di latenza negli account di archiviazione BLOB, per utilizzo generico v1 e per utilizzo generico v2.

I BLOB nel livello di archiviazione ad accesso sporadico hanno un contratto di servizio con disponibilità leggermente inferiore rispetto a quello dei BLOB nel livello di archiviazione ad accesso frequente. Per informazioni dettagliate, vedere [Contratto di Servizio per Archiviazione](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Le operazioni nei livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio sono le stesse?**

Sì. Tutte le operazioni nell'accesso frequente e nell'accesso sporadico sono coerenti al 100%. Tutte le operazioni di archiviazione valide, tra cui eliminazione, elenco, acquisizione di proprietà/metadati BLOB e impostazione del livello BLOB, sono coerenti al 100% con l'accesso frequente e l'accesso sporadico. Un BLOB non può essere letto o modificato mentre è nel livello archivio.

**Quando si riattiva un BLOB dal livello archivio al livello ad accesso frequente o sporadico, come è possibile sapere quando la riattivazione è stata completata?**

Durante la riattivazione, è possibile usare l'operazione di acquisizione delle proprietà BLOB per eseguire il polling dell'attributo **Archive Status** (Stato di archiviazione) per verificare quando la modifica del livello è completa. Lo stato può essere "rehydrate-pending-to-hot" o "rehydrate-pending-to-cool" a seconda del livello di destinazione. Al termine, la proprietà relativa allo stato di archiviazione viene rimossa e la proprietà BLOB **Livello di accesso** indica un il nuovo livello frequente o sporadico.  

**Dopo avere impostato il livello di un BLOB, quando inizieranno a essere fatturati i costi con la tariffa appropriata?**

Ogni BLOB viene sempre fatturato in base al livello indicato dalla proprietà BLOB **Livello di accesso**. Quando si imposta un nuovo livello in un BLOB, la proprietà **Livello di accesso** rispecchierà immediatamente il nuovo livello per tutte le transizioni, tranne che per la riattivazione di un BLOB dal livello archivio a quello ad accesso frequente o sporadico, che può richiedere diverse ore. In questo caso i costi continueranno a essere fatturati con le tariffe archivio fino al completamento della riattivazione, quando **Livello di accesso** rispecchierà il nuovo livello. Solo da quel momento i costi verranno fatturati con la nuova tariffa per l'accesso frequente o sporadico.

**Come si determina se verranno addebitati i costi per un'eliminazione anticipata quando si elimina o si sposta un BLOB al di fuori del livello ad accesso sporadico o archivio?**

Per i BLOB eliminati o spostati al di fuori del livello ad accesso sporadico (solo account per utilizzo generico v2) o archivio rispettivamente prima di 30 giorni e 180 giorni verranno addebitati i costi per un'eliminazione anticipata ripartita in modo proporzionale (in vigore dal 1° febbraio 2018). Per determinare per quanto tempo un BLOB è stato nel livello ad accesso sporadico o archivio, controllare la proprietà BLOB **Access Tier Change Time** (Ora modifica livello di accesso) che fornisce un indicatore dell'ultima modifica del livello. Per altre informazioni, vedere la sezione [Eliminazione anticipata per accesso sporadico o archivio](#cool-and-archive-early-deletion).

**Quali SDK e strumenti di Azure supportano la risorsa di archiviazione e l'organizzazione a livello di BLOB?**

Il portale di Azure, PowerShell e gli strumenti dell'interfaccia della riga di comando e le librerie client .NET, Java, Python e Node.js supportano tutti l'organizzazione a livello di BLOB e la risorsa di archiviazione.  

**Quanti dati è possibile archiviare nei livelli ad accesso frequente, ad accesso sporadico e archivio?**

L'archiviazione dati e gli altri limiti vengono impostati a livello di account e non per ogni livello di archiviazione. È quindi possibile scegliere di usare tutti i limiti in un solo livello o in tutti e tre i livelli. Per ulteriori informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

## <a name="next-steps"></a>Passaggi successivi

### <a name="evaluate-hot-cool-and-archvie-in-gpv2-blob-storage-accounts"></a>Valutare accesso frequente, accesso sporadico e archivio negli account di archiviazione BLOB per utilizzo generico v2

[Controllare la disponibilità di accesso frequente, accesso sporadico e archivio in base all'area](https://azure.microsoft.com/regions/#services)

[Valutare l'utilizzo degli account di archiviazione attuali abilitando le metriche di Archiviazione di Azure](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Controllare i prezzi di accesso frequente, accesso sporadico e archivio negli account per utilizzo generico v2 e di archiviazione BLOB in base all'area](https://azure.microsoft.com/pricing/details/storage/)

[Verificare i prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/)
