---
title: Livelli di accesso a caldo, a raffreddamento e di archiviazione per i BLOB - Archiviazione di AzureHot, cool, and archive access tiers for blobs - Azure Storage
description: Livelli di accesso a caldo, fresco e di archiviazione per gli account di archiviazione di Azure.Hot, cool, and archive access tiers for Azure storage accounts.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: f2f6be1022a7100a23f49534f2c18fc951d56284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255509"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio

Archiviazione di Azure offre diversi livelli di accesso, che consentono di archiviare i dati degli oggetti BLOB nel modo più conveniente. I livelli di accesso disponibili includono:The available access tiers include:

- **Hot** - Ottimizzato per l'archiviazione dei dati a cui si accede di frequente.
- **Cool** - Ottimizzato per l'archiviazione di dati a cui si accede raramente e che vengono archiviati per almeno 30 giorni.
- **Archivio:** ottimizzato per l'archiviazione di dati a cui si accede raramente e che vengono archiviati per almeno 180 giorni con requisiti di latenza flessibili (nell'ordine delle ore).

Le considerazioni seguenti si applicano ai diversi livelli di accesso:The following considerations apply to the different access tiers:

- Solo i livelli di accesso a caldo e freddo possono essere impostati a livello di account. Il livello di accesso all'archivio non è disponibile a livello di account.
- I livelli a caldo, a scelta rapida e di archiviazione possono essere impostati a livello di BLOB durante il caricamento o dopo il caricamento.
- I dati nel livello di accesso a freddo possono tollerare una disponibilità leggermente inferiore, ma richiedono comunque un'elevata durabilità, latenza di recupero e caratteristiche di velocità effettiva simili ai dati a caldo. Per i dati interessanti, un contratto di servizio (SLA) a disponibilità leggermente inferiore e costi di accesso più elevati rispetto ai dati a caldo sono compromessi accettabili per costi di archiviazione inferiori.
- Archiviazione archivia i dati offline e offre i costi di archiviazione più bassi, ma anche i più alti costi di reidratazione e accesso.

La quantità di dati archiviati nel cloud è in crescita esponenziale. Per gestire i costi in base alle esigenze di archiviazione crescenti, può essere utile organizzare i dati in base ad attributi quali la frequenza di accesso e il periodo di conservazione pianificato, in modo da ottimizzare i costi. I dati archiviati nel cloud possono essere diversi in base a come vengono generati, elaborati e accessibili nel corso della sua durata. Alcuni dati presentano accessi attivi e modifiche continue nel corso della rispettiva durata. Alcuni dati presentano un accesso frequente nelle fasi iniziali e l'accesso si riduce drasticamente con il passare del tempo. Alcuni dati rimangono inattivi nel cloud e raramente, se mai, sono accessibili dopo che sono stati archiviati.

Ognuno di questi scenari di accesso ai dati trae vantaggio da un livello di accesso diverso ottimizzato per un particolare modello di accesso. Con i livelli di accesso a caldo, freddi e di archiviazione, l'archiviazione BLOB di Azure risolve questa necessità di livelli di accesso differenziati con modelli di determinazione dei prezzi separati.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Account di archiviazione che supportano la suddivisione in livelli

La suddivisione in livelli dei dati di archiviazione degli oggetti tra hot, cool e archive è supportata solo negli account di archiviazione BLOB e GPv2 (General Purpose v2). Gli account GPv1 (General Purpose v1) non supportano la suddivisione in livelli. I clienti possono convertire facilmente gli account di archiviazione GPv1 o BLOB esistenti in account GPv2 tramite il portale di Azure.Customers can easily convert their existing GPv1 or Blob storage accounts to GPv2 accounts through the Azure portal. GPv2 offre nuovi prezzi e funzionalità per BLOB, file e code. Alcune caratteristiche e tagli dei prezzi sono offerti solo nei conti GPv2. Valutare utilizzando gli account GPv2 dopo aver esaminato in modo completo i prezzi. Alcuni carichi di lavoro possono essere più costosi su GPv2 rispetto a GPv1. Per altre informazioni, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).

Gli account di archiviazione BLOB e GPv2 espongono l'attributo **del livello** di accesso a livello di account. Questo attributo consente di specificare il livello di accesso predefinito per qualsiasi BLOB per cui non è impostato esplicito a livello di oggetto. Per gli oggetti con il livello impostato a livello di oggetto, il livello account non verrà applicato. Il livello di archiviazione può essere applicato solo a livello di oggetto. È possibile passare da un livello di accesso all'altro in qualsiasi momento.

## <a name="hot-access-tier"></a>Livello di accesso frequente

Il livello di accesso a caldo ha costi di archiviazione più elevati rispetto ai livelli cool e di archiviazione, ma i costi di accesso più bassi. Esempi di scenari di utilizzo per il livello di accesso a caldo includono:Example usage scenarios for the hot access tier include:

- Dati in uso attivo o a cui è previsto l'accesso (lettura e scrittura) frequentemente.
- Dati gestiti temporaneamente per l'elaborazione e l'eventuale migrazione al livello di accesso ad accesso a freddo.

## <a name="cool-access-tier"></a>Livello di accesso sporadico

Il livello di accesso a freddo ha costi di storage inferiori e costi di accesso più elevati rispetto allo storage a caldo. Questo livello è destinato ai dati che rimangono nel livello ad accesso sporadico per almeno 30 giorni. Esempi di scenari di utilizzo per il livello di accesso a freddo includono:Example usage scenarios for the cool access tier include:

- Set di dati di backup e ripristino di emergenza a breve termine.
- Contenuto multimediale meno recente ormai visualizzato non spesso, ma che deve essere immediatamente disponibile quando vi si accede.
- Set di dati di grandi dimensioni da archiviare a costi contenuti mentre vengono raccolti altri dati per l'elaborazione successiva, *ad esempio*, archiviazione a lungo termine di dati scientifici, dati di telemetria non elaborati di un impianto di produzione.

## <a name="archive-access-tier"></a>Livello di accesso archivio

Il livello di accesso all'archivio ha il costo di archiviazione più basso. Ma ha costi di recupero dei dati più elevati rispetto ai livelli caldi e cool. Il recupero dei dati nel livello di archiviazione può richiedere diverse ore. I dati devono rimanere nel livello di archiviazione per almeno 180 giorni o essere soggetti a un addebito di eliminazione anticipata.

Mentre un BLOB si trova nell'archivio di archiviazione, i dati BLOB sono offline e non possono essere letti, sovrascritti o modificati. Per leggere o scaricare un BLOB nell'archivio, è innanzitutto necessario reidratarlo a un livello online. Non è possibile creare snapshot di un BLOB nell'archiviazione di archiviazione. I metadati del BLOB rimangono tuttavia online e disponibili ed è quindi possibile visualizzare il BLOB e le relative proprietà. Per i BLOB nell'archivio, le uniche operazioni valide sono GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier, CopyBlob e DeleteBlob.For blobs in archive, the only valid operations are GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier, CopyBlob, and DeleteBlob. Per altre informazioni, vedere [Reidratare](storage-blob-rehydration.md) i dati BLOB dal livello di archiviazione.

Esempi di scenari di utilizzo per il livello di accesso all'archivio includono:Example usage scenarios for the archive access tier include:

- Set di dati di archiviazione, backup secondario e backup a lungo termine
- Dati originali (non elaborati) che devono essere conservati, anche dopo che sono stati elaborati in un formato utilizzabile finale,
- Dati di conformità e di archiviazione che devono essere archiviati per un lungo periodo e a cui non si accede quasi mai,

## <a name="account-level-tiering"></a>Tiering a livello di account

I BLOB in tutti e tre i livelli di accesso possono coesistere all'interno dello stesso account. Qualsiasi BLOB che non dispone di un livello assegnato in modo esplicito deduce il livello dall'impostazione del livello di accesso dell'account. Se il livello di accesso proviene dall'account, viene visualizzata la proprietà BLOB **infered di livello di accesso** impostata su "true" e la proprietà BLOB del livello di **accesso** corrisponde al livello account. Nel portale di Azure la proprietà _dedotto_ del livello di accesso viene visualizzata con il livello di accesso al BLOB come **Hot (inded)** o **Cool (deded)**.

La modifica del livello di accesso dell'account si applica a tutti gli oggetti _dedotti_ del livello di accesso archiviati nell'account che non dispongono di un set di livelli esplicito. Se si attiva o disattiva il livello account da caldo a cool, verranno addebitate le operazioni di scrittura (per 10.000) per tutti i BLOB senza un livello impostato solo negli account GPv2. Non sono previsti costi per questa modifica negli account di archiviazione BLOB. Ti verranno addebitati sia le operazioni di lettura (per 10.000) che il recupero dei dati (per GB) se si passa da fresco a hot nell'archiviazione BLOB o negli account GPv2.

## <a name="blob-level-tiering"></a>Organizzazione a livello di BLOB

La suddivisione in livelli a livello di BLOB consente di caricare dati nel livello di accesso di propria scelta usando le operazioni [Inserisci BLOB](/rest/api/storageservices/put-blob) o [Elenco blocchi](/rest/api/storageservices/put-block-list) blocco e di modificare il livello dei dati a livello di oggetto usando l'operazione Imposta livello [BLOB](/rest/api/storageservices/set-blob-tier) o la funzionalità di gestione del ciclo di [vita.](#blob-lifecycle-management) È possibile caricare i dati nel livello di accesso richiesto, quindi modificare facilmente il livello di accesso al BLOB tra i livelli di accesso a caldo, a scelta rapida o di archiviazione in base alle modifiche dei modelli di utilizzo, senza dover spostare i dati tra gli account. Tutte le richieste di modifica del livello avvengono immediatamente e le modifiche di livello tra hot e cool sono istantanee. La riattivazione di un BLOB dall'archivio, tuttavia, può richiedere diverse ore.

L'ora dell'ultima modifica a livello di BLOB viene esposta tramite la proprietà BLOB **Access Tier Change Time** (Ora modifica livello di accesso). Quando si sovrascrive un BLOB nel livello attivo o interessante, il BLOB appena creato eredita il livello del BLOB che è stato sovrascritto a meno che il nuovo livello di accesso al BLOB non sia impostato in modo esplicito al momento della creazione. Se un BLOB si trova nel livello di archiviazione, non può essere sovrascritto, pertanto il caricamento dello stesso BLOB non è consentito in questo scenario. 

> [!NOTE]
> Il livello di archiviazione archivio e l'organizzazione a livello di BLOB supportano solo BLOB in blocchi. Non è attualmente possibile modificare il livello di un BLOB in blocchi con snapshot.

### <a name="blob-lifecycle-management"></a>Gestione del ciclo di vita di Archiviazione BLOB

La gestione del ciclo di vita dell'archiviazione BLOB offre criteri avanzati basati su regole che è possibile usare per eseguire la transizione dei dati al livello di accesso migliore e per la scadenza dei dati alla fine del ciclo di vita. Per altre informazioni, vedere [Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md).  

> [!NOTE]
> I dati archiviati in un account di archiviazione BLOB in blocchi (prestazioni Premium) non possono attualmente essere suddivisi in livelli per l'installazione a caldo, l'installazione locale o l'archiviazione usando il livello di archiviazione BLOB di Azure.Data stored in a block blob storage account (Premium performance) cannot currently be tiered to hot, cool, or archive using [Set Blob Tier](/rest/api/storageservices/set-blob-tier) or using Azure Blob Storage lifecycle management.
> Per spostare i dati, è necessario copiare in modo sincrono i BLOB dall'account di archiviazione BLOB in blocchi al livello di accesso a caldo in un account diverso usando [l'API Put Block From URL](/rest/api/storageservices/put-block-from-url) o una versione di AzCopy che supporta questa API.
> L'API *Put Block From URL* copia in modo sincrono i dati nel server, vale a dire che la chiamata viene completata solo dopo che tutti i dati sono stati spostati dal percorso del server originale al percorso di destinazione.

### <a name="blob-level-tiering-billing"></a>Fatturazione per l'organizzazione a livello di BLOB

Quando un BLOB viene caricato o spostato nel livello di installazione, fresco o di archiviazione, viene addebitato alla frequenza corrispondente immediatamente dopo la modifica del livello.

Quando un BLOB viene spostato in un livello ad accesso più sporadico (frequente -> sporadico, frequente -> archivio o sporadico -> archivio), l'operazione viene fatturata come operazione di scrittura nel livello di destinazione, dove vengono applicati i costi per le operazioni di scrittura (ogni 10.000) e la scrittura dati (per GB).

Quando un BLOB viene spostato in un livello più caldo (archivio->cool, >ad archivio a caldo o a caldo di >), l'operazione viene fatturata come lettura dal livello di origine, in cui si applicano l'operazione di lettura (per 10.000) e gli addebiti per il recupero dei dati (per GB) del livello di origine. Possono essere addebitati anche i costi delle eliminazioni anticipate per i BLOB spostati al di fuori del livello di accesso sporadico o archivio. [La reidratazione dei dati dall'archivio](storage-blob-rehydration.md) richiede tempo e i dati verranno addebitati i prezzi di archiviazione fino a quando i dati non vengono ripristinati online e il livello BLOB diventa caldo o freddo. Nella tabella seguente viene riepilogato il modo in cui vengono fatturate le modifiche ai livelli:

| | **Scrittura addebiti (operazione + accesso)** | **Lettura addebiti (operazione + accesso)**
| ---- | ----- | ----- |
| **Direzione impostazione livello BLOB** | >cool,<br> >archivio,<br> Archivio cool-> | archivio->cool,<br> archivio >caldo,<br> cool->caldo

### <a name="cool-and-archive-early-deletion"></a>Eliminazione anticipata per accesso sporadico o archivio

Qualsiasi BLOB che viene spostato nel livello Cool (solo account GPv2) è soggetto a un periodo di eliminazione anticipata di 30 giorni. Qualsiasi BLOB spostato nel livello archivio è soggetto a un periodo di eliminazione anticipata dell'archivio di 180 giorni. Questo addebito è ripartito proporzionalmente. Ad esempio, se un BLOB viene spostato nell'archivio e quindi eliminato o spostato nel livello di scelta rapida dopo 45 giorni, ti verrà addebitata una commissione di eliminazione anticipata equivalente a 135 (180 meno 45) giorni di archiviazione del BLOB nell'archivio.

È possibile calcolare l'eliminazione anticipata usando la proprietà **BLOB, Last-Modified**, se non sono state apportate modifiche al livello di accesso. In caso contrario, è possibile usare l'ultima modifica del livello di accesso in modo interessante o archiviato visualizzando la proprietà BLOB: **access-tier-change-time**. Per altre informazioni sulle proprietà di BLOB, vedere l'articolo relativo all'operazione [Get Blob Properties](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Confronto tra le opzioni di archiviazione dei BLOB di blocchiComparing block blob storage options

La tabella seguente mostra un confronto tra l'archiviazione BLOB con blocchi di prestazioni premium e i livelli di accesso a caldo, ad accesso a freddo e ad archivio.

|                                           | **Prestazioni premium**   | **Livello a caldo** | **Livello Cool**       | **Livello di archiviazione**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Disponibilità**                          | 99,9%                     | 99,9%        | 99%                 | Offline           |
| **Disponibilità** <br> **(letture RA-GRS)**  | N/D                       | 99,99%       | 99,9%               | Offline           |
| **Costi di utilizzo**                         | Costi di storage più elevati, minori costi di accesso e di transazione | Costi di archiviazione più elevati e costi di accesso e transazione più bassi | Costi di archiviazione più bassi e costi di accesso e transazione più elevati | Costi di archiviazione minimi e costi di accesso e transazione più alti |
| **Dimensioni minime oggetti**                   | N/D                       | N/D          | N/D                 | N/D               |
| **Durata archiviazione minima**              | N/D                       | N/D          | 30 giorni<sup>1</sup> | 180 giorni
| **Latenza** <br> **(Tempo al primo byte)** | Millisecondi a una cifra | millisecondi | millisecondi        | ore<sup>2</sup> |

<sup>1</sup> Gli oggetti nel livello freddo negli account GPv2 hanno una durata minima di conservazione di 30 giorni. Gli account di archiviazione BLOB non hanno una durata minima di conservazione per il livello di raffreddamento.

<sup>2</sup> Archiviazione Archiviazione supporta attualmente 2 priorità reidratate, Alta e Standard, che offre diverse latenze di recupero. Per altre informazioni, vedere [Reidratare](storage-blob-rehydration.md)i dati BLOB dal livello di archiviazione.

> [!NOTE]
> Gli account di archiviazione BLOB supportano gli stessi obiettivi di prestazioni e scalabilità degli account di archiviazione nella versione 2 generici. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione BLOB](scalability-targets.md).

## <a name="quickstart-scenarios"></a>Scenari introduttivi

In questa sezione vengono illustrati gli scenari seguenti usando il portale di Azure e PowerShell:In this section, the following scenarios are demonstrated using the Azure portal and powershell:

- Come modificare il livello di accesso predefinito di un account di archiviazione BLOB o per utilizzo generico v2.
- Come modificare il livello di un BLOB in un account di archiviazione BLOB o per utilizzo generico v2.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Modificare il livello di accesso all'account predefinito di un account per utilizzo generico v2 o di archiviazione BLOB

# <a name="portal"></a>[Portale](#tab/azure-portal)
1. Accedere al [portale](https://portal.azure.com)di Azure .

1. Nel portale di Azure cercare e selezionare **Tutte le risorse**.

1. Selezionare l'account di archiviazione.

1. In **Impostazioni**selezionare **Configurazione** per visualizzare e modificare la configurazione dell'account.

1. Selezionare il livello di accesso corretto per le proprie esigenze: impostare il **livello di accesso** su **Freddo** o **Caldo**.

1. Nella parte superiore, fare clic su **Salva** .

![Modificare il livello dell'account di archiviazioneChange storage account tier](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Lo script di PowerShell seguente può essere usato per modificare il livello di account. La `$rgName` variabile deve essere inizializzata con il nome del gruppo di risorse. La `$accountName` variabile deve essere inizializzata con il nome dell'account di archiviazione. 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Modificare il livello di un BLOB in un account di archiviazione GPv2 o BLOBChange the tier of a blob in a GPv2 or Blob storage account
# <a name="portal"></a>[Portale](#tab/azure-portal)
1. Accedere al [portale](https://portal.azure.com)di Azure .

1. Nel portale di Azure cercare e selezionare **Tutte le risorse**.

1. Selezionare l'account di archiviazione.

1. Selezionare il contenitore e quindi selezionare il BLOB.

1. In **Proprietà BLOB**selezionare **Cambia livello**.

1. Selezionare il livello di accesso **Hot**, **Cool**o **Archive.** Se il BLOB è attualmente in archivio e si vuole reidratarsi a un livello online, è anche possibile selezionare una priorità **di** reidratazione standard o **alta**.

1. Seleziona **Salva** in basso.

![Modificare il livello dell'account di archiviazioneChange storage account tier](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Lo script di PowerShell seguente può essere usato per modificare il livello BLOB. La `$rgName` variabile deve essere inizializzata con il nome del gruppo di risorse. La `$accountName` variabile deve essere inizializzata con il nome dell'account di archiviazione. La `$containerName` variabile deve essere inizializzata con il nome del contenitore. La `$blobName` variabile deve essere inizializzata con il nome del BLOB. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```
---

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Tutti gli account di archiviazione usano un modello di determinazione dei prezzi per l'archiviazione BLOB di blocchi in base al livello di ogni BLOB. Tenere presenti le considerazioni seguenti sulla fatturazione:

- **Costi di archiviazione**: Oltre alla quantità di dati archiviati, il costo di archiviazione dei dati varia a seconda del livello di accesso. Il costo per gigabyte diminuisce passando a un livello ad accesso più sporadico.
- **Costi di accesso ai dati**: i costi di accesso ai dati aumentano passando a un livello ad accesso più sporadico. Per i dati nel livello di accesso ad accesso all'accesso ad accesso all'accesso all'accesso all'accesso all'accesso all'archiviazione, viene addebitato un costo di accesso ai dati per gigabyte per le letture.
- **Costi di transazione**: C'è un addebito per transazione per tutti i livelli che aumenta man mano che il livello diventa più fresco.
- **Costi di trasferimento dati con la replica geografica**: si applicano solo agli account per cui è configurata la replica geografica, incluse l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza geografica e accesso in lettura. Il trasferimento dati con la replica geografica comporta un addebito per gigabyte.
- **Costi di trasferimento dati in uscita**: i trasferimenti dati in uscita (dati che vengono trasferiti al di fuori di un'area di Azure) vengono fatturati in base all'utilizzo di larghezza di banda per singolo gigabyte, come per gli account di archiviazione di uso generico.
- **Modifica del livello**di accesso: la modifica del livello di accesso dell'account comporterà costi di modifica del livello per i BLOB _dedotti_ dal livello di accesso archiviati nell'account che non dispongono di un set di livelli esplicito. Per informazioni sulla modifica del livello di accesso per un singolo BLOB, vedere Fatturazione a [livello di BLOB.](#blob-level-tiering-billing)

> [!NOTE]
> Per altre informazioni sui prezzi per i BLOB di blocchi, vedere la pagina [Prezzi di Archiviazione di Azure.For](https://azure.microsoft.com/pricing/details/storage/blobs/) more information about pricing for Block blobs, see Azure Storage Pricing page. Per altre informazioni sugli addebiti per i trasferimenti dati in uscita, vedere la pagina [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Domande frequenti

**È consigliabile usare account di archiviazione BLOB o per utilizzo generico v2 se si vogliono suddividere in livelli i dati?**

Per la suddivisione in livelli è consigliabile usare account per utilizzo generico v2 anziché di archiviazione BLOB. Gli account per utilizzo generico v2 supportano tutte le funzionalità supportate dagli account di archiviazione BLOB e molte altre. I prezzi di archiviazione BLOB e utilizzo generico v2 sono quasi identici, ma alcune nuove funzionalità e alcuni tagli sui prezzi saranno disponibili solo negli account per utilizzo generico v2. Gli account GPv1 non supportano la suddivisione in livelli.

Il piano tariffario per gli account per utilizzo generico v1 e v2 è diverso e i clienti devono valutarli entrambi con attenzione prima di decidere di usare gli account per utilizzo generico v2. È possibile convertire facilmente un account di archiviazione BLOB o per utilizzo generico v1 esistente in account per utilizzo generico v2 tramite un semplice processo con un clic. Per altre informazioni, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).

**È possibile archiviare gli oggetti in tutti e tre i livelli di accesso (hot, cool e archive) nello stesso account?**

Sì. L'attributo **Livello di accesso** impostato a livello di account è il livello di account predefinito che si applica a tutti gli oggetti in tale account senza un livello di set esplicito. La suddivisione in livelli a livello di BLOB consente di impostare il livello di accesso a livello di oggetto, indipendentemente dall'impostazione del livello di accesso nell'account. I BLOB in uno dei tre livelli di accesso (hot, cool o archive) possono esistere all'interno dello stesso account.

**È possibile modificare il livello di accesso predefinito dell'account di archiviazione BLOB o GPv2?**

Sì, è possibile modificare il livello di account predefinito impostando l'attributo Livello di **accesso** nell'account di archiviazione. La modifica del livello di account si applica a tutti gli oggetti archiviati nell'account che non dispongono di un set di livelli esplicito (ad esempio, **Hot (deded)** o **Cool (inferito)**). L'alternare il livello di account da caldo a freddo comporta operazioni di scrittura (per 10.000) per tutti i BLOB senza un set di soli in account GPv2 e passare da cool a hot comporta sia operazioni di lettura (per 10.000) che costi di recupero dati (per GB) per tutti i BLOB nell'archiviazione BLOB e gPv2.

**È possibile impostare il livello di accesso all'account predefinito per l'archiviazione?**

No. Solo i livelli di accesso a caldo e ad accesso freddo possono essere impostati come livelli di accesso dell'account predefinito. L'archivio può essere impostato solo a livello di oggetto. Al caricamento del BLOB, specificare il livello di accesso di propria scelta come hot, cool o archivio indipendentemente dal livello di account predefinito. Questa funzionalità consente di scrivere i dati direttamente nel livello di archiviazione per realizzare risparmi sui costi dal momento in cui si creano i dati nell'archiviazione BLOB.

**In quali aree sono disponibili i livelli di accesso a caldo, fresco e archivio?**

I livelli di accesso a caldo e freddi e ai livelli di livellamento a livello di BLOB sono disponibili in tutte le aree. Lo spazio di archiviazione sarà inizialmente disponibile solo in alcune aree. Per un elenco completo, vedere [Prodotti di Azure disponibili in base all'area](https://azure.microsoft.com/regions/services/).

**I BLOB nel livello di accesso a freddo si comportano in modo diverso rispetto a quelli nel livello di accesso a caldo?**

I BLOB nel livello di accesso a caldo hanno la stessa latenza dei BLOB negli account di archiviazione GPv1, GPv2 e BLOB. I BLOB nel livello di accesso a freddo hanno una latenza simile (in millisecondi) dei BLOB negli account di archiviazione GPv1, GPv2 e BLOB. I BLOB nel livello di accesso all'archivio hanno diverse ore di latenza negli account di archiviazione GPv1, GPv2 e BLOB.

I BLOB nel livello di accesso a freddo hanno un livello di servizio (SLA) di disponibilità leggermente inferiore rispetto ai BLOB archiviati nel livello di accesso a caldo. Per altre informazioni, vedere [Contratto di Servizio per Archiviazione](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Le operazioni tra i livelli hot, cool e archive sono le stesse?**

Tutte le operazioni nell'accesso frequente e nell'accesso sporadico sono coerenti al 100%. Tutte le operazioni di archiviazione valide, tra cui GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier ed DeleteBlob sono coerenti al 100% con hot and cool. I dati BLOB non possono essere letti o modificati nel livello di archiviazione fino a quando non vengono reidratati. solo le operazioni di lettura dei metadati BLOB sono supportate durante l'archivio.

**Quando si reidrata un BLOB dal livello di archiviazione al livello con caldo o freddo, come è possibile sapere quando la reidratazione è completa?**

Durante la reidratazione, è possibile usare l'operazione get blob properties per eseguire il polling dell'attributo **Archive Status** e confermare quando la modifica del livello è stata completata. Lo stato può essere "rehydrate-pending-to-hot" o "rehydrate-pending-to-cool" a seconda del livello di destinazione. Al termine, la proprietà dello stato dell'archivio viene rimossa e la proprietà BLOB **del livello di accesso** riflette il nuovo livello attivo o interessante. Per altre informazioni, vedere [Reidratare](storage-blob-rehydration.md) i dati BLOB dal livello di archiviazione.

**Dopo avere impostato il livello di un BLOB, quando inizieranno a essere fatturati i costi con la tariffa appropriata?**

Ogni BLOB viene sempre fatturato in base al livello indicato dalla proprietà **Livello di accesso** del BLOB. Quando si imposta un nuovo livello online per un BLOB, la proprietà **Livello di accesso** riflette immediatamente il nuovo livello per tutte le transizioni. Tuttavia, la reidratazione di un BLOB dal livello di archiviazione offline a un livello con accesso a caldo o freddo può richiedere diverse ore. In questo caso, vengono fatturate le frequenze di archiviazione fino al completamento della riattivazione, a questo punto la proprietà Livello di **accesso** riflette il nuovo livello. Una volta reidratato al livello online, ti viene addebitato il BLOB alla velocità di caldo o freddo.

**Come faccio a determinare se incorrereino in un addebito di eliminazione anticipata quando elimino o sposto un BLOB dal livello cool o archivio?**

Per i BLOB eliminati o spostati al di fuori del livello ad accesso sporadico (solo in caso di account per utilizzo generico v2) o del livello archivio rispettivamente prima di 30 giorni e 180 giorni verrà addebitato un costo per eliminazione anticipata ripartito proporzionalmente. Per determinare per quanto tempo un BLOB è stato nel livello ad accesso sporadico o archivio, controllare la proprietà BLOB **Access Tier Change Time** (Ora modifica livello di accesso), che fornisce un indicatore dell'ultima modifica del livello. Se il livello del BLOB non è mai stato modificato, è possibile controllare la proprietà del BLOB **Ultimo modificato.** Per ulteriori informazioni, consultate [Cool e archivia l'eliminazione anticipata](#cool-and-archive-early-deletion).

**Quali strumenti e SDK di Azure supportano il tiering a livello di BLOB e l'archiviazione di archiviazione?**

Il portale di Azure, PowerShell e gli strumenti dell'interfaccia della riga di comando e le librerie client .NET, Java, Python e Node.js supportano tutti l'organizzazione a livello di BLOB e la risorsa di archiviazione.  

**Quanti dati è possibile archiviare nei livelli hot, cool e archive?**

L'archiviazione dei dati insieme ad altri limiti viene impostata a livello di account e non per livello di accesso. È possibile scegliere di usare tutti i limiti in un livello o in tutti e tre i livelli. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per gli account di archiviazione standard.](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="next-steps"></a>Passaggi successivi

Valutare gli account di archiviazione Per caldo, fresco e archiviazione negli account di archiviazione GPv2 e BLOB

- [Verificare la disponibilità di hot, cool e archiviper regione](https://azure.microsoft.com/regions/#services)
- [Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)
- [Informazioni sulla reidratazione dei dati BLOB dal livello di archiviazioneLearn about rehydrating blob data from the archive tier](storage-blob-rehydration.md)
- [Determinare se le prestazioni premium andrebbero a vantaggio dell'app](storage-blob-performance-tiers.md)
- [Valutare l'utilizzo degli account di archiviazione attuali abilitando le metriche di Archiviazione di Azure](../common/storage-enable-and-view-metrics.md)
- [Controllare i prezzi di accesso frequente, accesso sporadico e archivio negli account di archiviazione BLOB e per utilizzo generico v2 in base all'area](https://azure.microsoft.com/pricing/details/storage/)
- [Verificare i prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/)
