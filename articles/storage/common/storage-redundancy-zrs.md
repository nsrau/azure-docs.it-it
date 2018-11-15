---
title: Compilare applicazioni di Archiviazione Azure a disponibilità elevata in archiviazioni con ridondanza della zona | Microsoft Docs
description: L'archiviazione con ridondanza della zona offre un modo semplice per compilare applicazioni a disponibilità elevata. L'archiviazione con ridondanza della zona protegge dagli errori hardware nel data center e da alcune situazioni di emergenza a livello di area.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 10/24/2018
ms.author: jeking
ms.component: common
ms.openlocfilehash: b310c06f508395635976009005dd2c4db2917abc
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218740"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Archiviazione con ridondanza della zona: applicazioni di Archiviazione Azure a disponibilità elevata
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Copertura del supporto e disponibilità a livello di area
L'archiviazione con ridondanza della zona attualmente supporta gli account di tipo v2 standard per utilizzo generico. Per altre informazioni sui tipi di account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md).

L'archiviazione con ridondanza della zona è disponibile per BLOB in blocchi, BLOB di pagine non del disco, file, tabelle e code.

L'archiviazione con ridondanza della zona è disponibile a livello generale nelle aree seguenti:

- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti occidentali 2
- Stati Uniti centrali
- Europa settentrionale
- Europa occidentale
- Francia centrale
- Asia sud-orientale

Microsoft continua ad abilitare l'archiviazione con ridondanza della zona in aree di Azure aggiuntive. Fare riferimento regolarmente alla pagina [Aggiornamenti di Azure](https://azure.microsoft.com/updates/) per informazioni sulle nuove aree.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Cosa accade quando una zona smette di essere disponibile?
I dati sono ancora accessibili anche se una zona non è più disponibile. Microsoft consiglia di continuare a seguire le procedure per la gestione degli errori temporanei. Queste procedure includono l'implementazione di criteri di ripetizione dei tentativi con interruzione temporanea esponenziale.

Quando una zona non è disponibile, Azure avvia gli aggiornamenti di rete, ad esempio la modifica del puntamento DNS. Questi aggiornamenti possono interessare l'applicazione se si accede ai dati prima che l'aggiornamento sia stato completato.

L'archiviazione con ridondanza della zona potrebbe non proteggere i dati in caso di un'emergenza a livello di area in cui più zone sono interessate in modo permanente. L'archiviazione con ridondanza della zona offre invece resilienza per i dati qualora diventino temporaneamente non disponibili. Per la protezione in caso di emergenze a livello di area, Microsoft consiglia di usare l'archiviazione con ridondanza geografica (GRS). Per altre informazioni sull'archiviazione con ridondanza geografica, vedere [Archiviazione con ridondanza geografica: replica tra più aree per Archiviazione di Azure](storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Conversione nella replica di archiviazione con ridondanza della zona
La migrazione in o da archiviazione con ridondanza locale, archiviazione con ridondanza geografica e archiviazione con ridondanza geografica e accesso in lettura è molto semplice. Per modificare il tipo di ridondanza dell'account, usare il portale di Azure o l'API del provider delle risorse di archiviazione. Azure eseguirà quindi la replica dei dati in modo conforme all'opzione scelta. 

La migrazione dei dati in o da archiviazione con ridondanza della zona richiede una strategia diversa. La migrazione dell'archiviazione con ridondanza della zona comporta lo spostamento fisico dei dati da un indicatore di archiviazione singolo a più indicatori all'interno di un'area.

Sono disponibili due opzioni principali per la migrazione in e da archiviazione con ridondanza della zona: 

- Copiare o spostare manualmente i dati dall'account esistente a un nuovo account di archiviazione con ridondanza della zona.
- Richiedere una migrazione in tempo reale.

Microsoft consiglia vivamente di eseguire una migrazione manuale. Una migrazione manuale offre maggiore flessibilità rispetto a una migrazione in tempo reale. Con una migrazione manuale, è possibile controllare le tempistiche.

Per eseguire una migrazione manuale, sono disponibili alcune opzioni:
- Usare strumenti esistenti, ad esempio AzCopy, una delle librerie client di Archiviazione di Azure o strumenti affidabili di terze parti.
- Se si ha familiarità con Hadoop o HDInsight, collegare account di origine e di destinazione (dell'archiviazione con ridondanza della zona) al cluster. Quindi, parallelizzare il processo di copia dei dati con uno strumento, ad esempio DistCp.
- Creare i propri strumenti con una delle librerie client di Archiviazione di Azure.

Una migrazione manuale può comportare tempi di inattività dell'applicazione. Se l'applicazione richiede disponibilità elevata, Microsoft offre anche un'opzione di migrazione in tempo reale. Una migrazione in tempo reale è una migrazione sul posto. 

Quando si esegue una migrazione in tempo reale, è possibile usare l'account di archiviazione durante la migrazione dei dati tra gli indicatori di archiviazione di origine e di destinazione. Durante il processo di migrazione, si hanno a disposizione gli stessi livelli di durabilità e disponibilità normalmente previsti dal contratto di servizio.

Tenere presenti le limitazioni seguenti relative alla migrazione in tempo reale:

- Sebbene Microsoft gestisca tempestivamente la richiesta di migrazione in tempo reale, non c'è alcuna garanzia per quanto riguarda il tempo necessario per il completamento di questo tipo di migrazione. Se è necessario eseguire la migrazione dei dati in un'archiviazione con ridondanza della zona entro una certa data, Microsoft consiglia di eseguire una migrazione manuale. In genere, maggiore è la quantità di dati nell'account, più tempo richiederà la migrazione dei dati. 
- La migrazione in tempo reale è supportata solo per gli account di archiviazione che usano la replica con l'archiviazione con ridondanza locale o l'archiviazione con ridondanza geografica. Se l'account usa l'archiviazione con ridondanza geografica e accesso in lettura, è necessario innanzitutto modificare il tipo di replica dell'account in archiviazione con ridondanza locale o archiviazione con ridondanza geografica prima di procedere. Questo passaggio intermedio rimuove, prima della migrazione, l'endpoint secondario di sola lettura fornito dall'archiviazione con ridondanza geografica e accesso in lettura.
- L'account deve contenere dati.
- È possibile migrare solo i dati nella stessa area. Se si vuole eseguire la migrazione dei dati in un account di archiviazione con ridondanza della zona che si trova in un'area diversa rispetto all'account di origine, è necessario eseguire una migrazione manuale.
- Solo gli account di archiviazione standard supportano la migrazione in tempo reale. Per gli account di archiviazione premium, è necessario usare la migrazione manuale.

È possibile richiedere la migrazione in tempo reale tramite il [portale del supporto tecnico di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Nel portale selezionare l'account di archiviazione da convertire in archiviazione con ridondanza della zona.
1. Selezionare **Nuova richiesta di supporto**
2. Completare le **nozioni di base** con le informazioni relative all'account. Nella sezione **Assistenza** selezionare **Gestione Account di archiviazione** e la risorsa che si vuole convertire in archiviazione con ridondanza della zona. 
3. Selezionare **Avanti**. 
4. Specificare i valori seguenti nella sezione **Problema**: 
    - **Gravità**: lasciare il valore predefinito.
    - **Tipo di problema**: selezionare **Migrazione dei dati**.
    - **Categoria**: selezionare **Migrate to ZRS within a region** (Migrazione a ZRS in un'area).
    - **Titolo**: digitare un titolo descrittivo, ad esempio **migrazione di account con archiviazione con ridondanza della zona**.
    - **Informazioni dettagliate**: digitare dettagli aggiuntivi nella casella **Dettagli**, ad esempio Vorrei eseguire la migrazione all'archiviazione con ridondanza locale da [LRS, GRS] nell'area ______. 
5. Selezionare **Avanti**.
6. Verificare che le informazioni di contatto nel pannello **Informazioni contatto** siano corrette.
7. Selezionare **Crea**.

Un addetto del supporto tecnico contatterà l'utente e fornirà l'assistenza necessaria. 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>Archiviazione con ridondanza della zona (versione classica): opzione legacy per la ridondanza dei BLOB in blocchi
> [!NOTE]
> Microsoft imposterà come deprecati gli account della versione classica dell'archiviazione con ridondanza della zona e ne eseguirà la migrazione il 31 marzo 2021. Altri dettagli verranno inviati ai clienti della versione classica dell'archiviazione con ridondanza della zona prima di essere deprecati. 
>
> Quando l'archiviazione con ridondanza della zona diventa [disponibile a livello generale](#support-coverage-and-regional-availability) in un'area, i clienti non possono creare account della versione classica dell'archiviazione con ridondanza della zona dal portale in quell'area. L'uso di Microsoft PowerShell e dell'interfaccia della riga di comando di Azure per creare gli account della versione classica dell'archiviazione con ridondanza della zona sarà disponibile finché la versione classica dell'archiviazione con ridondanza della zona viene deprecata.

La versione classica dell'archiviazione con ridondanza della zona replica i dati in modo asincrono tra data center in una o due aree. I dati replicati potrebbero non essere disponibili a meno che Microsoft non avvii il failover all'area secondaria. Gli account della versione classica dell'archiviazione con ridondanza della zona non possono essere convertiti in o da archiviazione con ridondanza locale, archiviazione con ridondanza geografica o archiviazione con ridondanza geografica e accesso in lettura. Gli account della versione classica dell'archiviazione con ridondanza della zona non supportano inoltre le metriche o la registrazione.

La versione classica dell'archiviazione con ridondanza della zona è disponibile solo per i **BLOB in blocchi** negli account di archiviazione per utilizzo generico V1 (GPv1). Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md).

Per eseguire manualmente la migrazione di dati di un account di archiviazione con ridondanza della zona in o da un account di archiviazione con ridondanza locale, archiviazione con ridondanza della zona (versione classica), archiviazione con ridondanza geografica o archiviazione con ridondanza geografica e accesso in lettura, usare uno degli strumenti seguenti: AzCopy, Azure Storage Explorer, Azure PowerShell o l'interfaccia della riga di comando di Azure. È anche possibile creare una soluzione di migrazione personalizzata con una delle librerie client di Archiviazione di Azure.

## <a name="see-also"></a>Vedere anche 
- [Replica di Archiviazione di Azure](storage-redundancy.md)
- [Archiviazione con ridondanza locale: ridondanza dei dati a basso costo per Archiviazione di Azure](storage-redundancy-lrs.md)
- [Archiviazione con ridondanza geografica: replica tra più aree per Archiviazione di Azure](storage-redundancy-grs.md)