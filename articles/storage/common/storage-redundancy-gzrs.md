---
title: Creazione di applicazioni di archiviazione di Azure a disponibilità elevata con archiviazione con ridondanza geografica (GZRS) (anteprima) | Microsoft Docs
description: L'archiviazione con ridondanza della zona geografica (GZRS) si sposa con la disponibilità elevata di archiviazione con ridondanza della zona (ZRS) con protezione da interruzioni a livello di area, come fornito dall'archiviazione con ridondanza geografica (GRS). I dati in un account di archiviazione GZRS vengono replicati tra le zone di disponibilità di Azure nell'area primaria e anche replicati in un'area geografica secondaria per la protezione da emergenze locali.
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 4523d7bf8f6c0ffc0ebfbc57d20a19baec08c91b
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720347"
---
# <a name="build-highly-available-azure-storage-applications-with-geo-zone-redundant-storage-gzrs-preview"></a>Creazione di applicazioni di archiviazione di Azure a disponibilità elevata con archiviazione con ridondanza geografica (GZRS) (anteprima)

L'archiviazione con ridondanza della zona geografica (GZRS) (anteprima) si associa alla disponibilità elevata di [archiviazione con ridondanza della zona (ZRS)](storage-redundancy-zrs.md) con protezione da interruzioni a livello di area, come fornito dall' [archiviazione con ridondanza geografica (GRS)](storage-redundancy-grs.md). I dati in un account di archiviazione GZRS vengono replicati in tre [zone di disponibilità di Azure](../../availability-zones/az-overview.md) nell'area primaria e anche replicati in un'area geografica secondaria per la protezione da emergenze locali. Ogni area di Azure è associata a un'altra area con la stessa ubicazione geografica, che insieme formano una coppia di aree. Per ulteriori informazioni ed eccezioni, fare riferimento alla [documentazione](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Con un account di archiviazione GZRS, è possibile continuare a leggere e scrivere dati se una zona di disponibilità diventa non disponibile o non è recuperabile. Inoltre, i dati sono anche durevoli in caso di un'interruzione completa dell'area o di un'emergenza in cui l'area primaria non è recuperabile. GZRS è progettato per offrire almeno il 99,99999999999999% (16 9) di durabilità degli oggetti in un determinato anno. GZRS offre anche gli stessi [obiettivi di scalabilità](storage-scalability-targets.md) di con ridondanza locale, ZRS, GRS o RA-GRS. Facoltativamente, è possibile abilitare l'accesso in lettura ai dati nell'area secondaria con archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS) se le applicazioni devono essere in grado di leggere i dati in caso di emergenza nell'area primaria.

Microsoft consiglia di usare GZRS per le applicazioni che richiedono coerenza, durabilità, disponibilità elevata, prestazioni ottimali e resilienza per il ripristino di emergenza. Per la sicurezza aggiuntiva dell'accesso in lettura all'area secondaria in caso di emergenza a livello di area, abilitare RA-GZRS per l'account di archiviazione.

## <a name="about-the-preview"></a>Informazioni sulla versione di anteprima

Solo gli account di archiviazione per utilizzo generico V2 supportano GZRS e RA-GZRS. Per altre informazioni sui tipi di account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md). I BLOB in blocchi, i BLOB di pagine che non sono dischi VHD, file, tabelle e code sono supportati da GZRS e RA-GZRS.

GZRS e RA-GZRS sono attualmente disponibili per l'anteprima nelle aree seguenti:

- Europa settentrionale
- Europa occidentale
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti centrali

Microsoft continua ad abilitare GZRS e RA-GZRS in altre aree di Azure. Per informazioni sulle aree supportate, vedere regolarmente la pagina [aggiornamenti](https://azure.microsoft.com/updates/) dei servizi di Azure.

Per informazioni sui prezzi di anteprima, vedere prezzi di anteprima di GZRS per [BLOB](https://azure.microsoft.com/pricing/details/storage/blobs), [file](https://azure.microsoft.com/pricing/details/storage/files/), [Code](https://azure.microsoft.com/pricing/details/storage/queues/)e [tabelle](https://azure.microsoft.com/pricing/details/storage/tables/).

> [!IMPORTANT]
> Microsoft consiglia di non usare le funzionalità di anteprima per i carichi di lavoro di produzione.

## <a name="how-gzrs-and-ra-gzrs-work"></a>Funzionamento di GZRS e RA-GZRS

Quando i dati vengono scritti in un account di archiviazione con GZRS o RA-GZRS abilitati, i dati vengono prima replicati in modo sincrono nell'area primaria tra tre zone di disponibilità. I dati vengono quindi replicati in modo asincrono in una seconda area a centinaia di chilometri di distanza. Quando i dati vengono scritti nell'area secondaria, vengono ulteriormente replicati in modo sincrono tre volte all'interno di tale area usando l' [archiviazione con ridondanza locale (con ridondanza locale)](storage-redundancy-lrs.md).

> [!IMPORTANT]
> La replica asincrona comporta un ritardo tra il momento in cui i dati vengono scritti nell'area primaria e quando viene replicata nell'area secondaria. Nel caso in cui si verifichi un'emergenza a livello di area, è possibile che le modifiche non ancora replicate nell'area secondaria vadano perse se non è possibile recuperare i dati dall'area primaria.

Quando si crea un account di archiviazione, si specifica la modalità di replica dei dati in tale account e si specifica anche l'area primaria per tale account. L'area secondaria abbinata per un account con replica geografica viene determinata in base all'area primaria e non può essere modificata. Per informazioni aggiornate sulle aree supportate da Azure, vedere [continuità aziendale e ripristino di emergenza (BCdR): aree abbinate di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Per informazioni sulla creazione di un account di archiviazione con GZRS o RA-GZRS, vedere [creare un account di archiviazione](storage-quickstart-create-account.md).

### <a name="use-ra-gzrs-for-high-availability"></a>Usare RA-GZRS per la disponibilità elevata

Quando si Abilita RA-GZRS per l'account di archiviazione, i dati possono essere letti dall'endpoint secondario e dall'endpoint primario per l'account di archiviazione. L'endpoint secondario aggiunge il suffisso *-secondario* al nome dell'account. Se ad esempio l'endpoint primario per il servizio BLOB è `myaccount.blob.core.windows.net`, l'endpoint secondario sarà. `myaccount-secondary.blob.core.windows.net` Le chiavi di accesso per l'account di archiviazione sono identiche per gli endpoint primario e secondario.

Per sfruttare i vantaggi di RA-GZRS in caso di interruzione a livello di area, è necessario progettare l'applicazione in anticipo per gestire questo scenario. L'applicazione deve leggere e scrivere nell'endpoint primario, ma passare all'uso dell'endpoint secondario nel caso in cui l'area primaria diventi non disponibile. Per indicazioni sulla progettazione per la disponibilità elevata con RA-GZRS, vedere Progettazione di applicazioni a disponibilità elevata con [ra-GZRS o RA-GRS](https://docs.microsoft.com/azure/storage/common/storage-designing-ha-apps-with-ragrs).

Poiché i dati vengono replicati in modo asincrono nell'area secondaria, l'area secondaria è spesso dietro l'area primaria. Per determinare quali operazioni di scrittura sono state replicate nell'area secondaria, l'applicazione verifica l'ora dell'ultima sincronizzazione dell'account di archiviazione. Tutte le operazioni di scrittura scritte nell'area primaria precedenti all'ora dell'ultima sincronizzazione sono state replicate correttamente nell'area secondaria, vale a dire che sono disponibili per la lettura dal database secondario. Tutte le operazioni di scrittura scritte nell'area primaria dopo l'ora dell'ultima sincronizzazione potrebbero essere state replicate nell'area secondaria, vale a dire che potrebbero non essere disponibili per le operazioni di lettura.

È possibile eseguire una query sul valore della proprietà dell' **ora dell'ultima sincronizzazione** usando Azure PowerShell, l'interfaccia della riga di comando di Azure o una delle librerie client di archiviazione di Azure. La proprietà **ora ultima sincronizzazione** è un valore di data/ora GMT.

Per informazioni aggiuntive sulle prestazioni e sulla scalabilità con RA-GZRS, vedere l' [elenco di controllo di prestazioni e scalabilità archiviazione di Microsoft Azure](storage-performance-checklist.md).

### <a name="availability-zone-outages"></a>Interruzioni della zona di disponibilità

In caso di errore che influisce su una zona di disponibilità nell'area primaria, le applicazioni possono continuare a leggere e scrivere nell'account di archiviazione usando le altre zone di disponibilità per tale area. Microsoft consiglia di continuare a seguire le procedure per la gestione degli errori temporanei quando si usa GZRS o ZRS. Queste procedure includono l'implementazione di criteri di ripetizione dei tentativi con interruzione temporanea esponenziale.

Quando una zona di disponibilità non è più disponibile, Azure esegue gli aggiornamenti della rete, ad esempio il reindirizzamento DNS. Questi aggiornamenti possono influire sull'applicazione se si accede ai dati prima del completamento degli aggiornamenti.

### <a name="regional-outages"></a>Interruzioni a livello di area

Se un errore interessa l'intera area primaria, Microsoft tenterà prima di tutto di ripristinare l'area primaria. Se il ripristino non è possibile, Microsoft effettuerà il failover nell'area secondaria, in modo che l'area secondaria diventi la nuova area primaria. Se per l'account di archiviazione è abilitata l'archiviazione RA-GZRS, le applicazioni progettate per questo scenario possono leggere dall'area secondaria in attesa del failover. Se l'account di archiviazione non dispone di RA-GZRS abilitato, le applicazioni non saranno in grado di leggere dal database secondario fino al completamento del failover.

> [!NOTE]
> GZRS e RA-GZRS sono attualmente in anteprima solo nell'area Stati Uniti orientali. Il failover dell'account gestito dal cliente (anteprima) non è ancora disponibile negli Stati Uniti orientali 2, quindi i clienti non possono attualmente gestire gli eventi di failover degli account con gli account GZRS e RA-GZRS. Durante l'anteprima, Microsoft gestirà gli eventi di failover che interessano gli account GZRS e RA-GZRS.

Poiché i dati vengono replicati in modo asincrono nell'area secondaria, un errore che interessa l'area primaria può causare la perdita di dati se non è possibile recuperare l'area primaria. L'intervallo tra le scritture più recenti nell'area primaria e l'ultima scrittura nell'area secondaria è noto come obiettivo del punto di ripristino (RPO). e indica il punto nel tempo in cui è possibile recuperare i dati. Archiviazione di Azure ha in genere un RPO di meno di 15 minuti, anche se attualmente non esiste alcun contratto di contratto per il tempo necessario per la replica dei dati nell'area secondaria.

L'obiettivo del tempo di ripristino (RTO) è una misura del tempo necessario per eseguire il failover e riportare l'account di archiviazione online. Questa misura indica il tempo richiesto da Azure per eseguire il failover cambiando le voci DNS primarie in modo che puntino alla posizione secondaria.

## <a name="migrate-a-storage-account-to-gzrs-or-ra-gzrs"></a>Eseguire la migrazione di un account di archiviazione a GZRS o RA-GZRS

È possibile eseguire la migrazione di qualsiasi account di archiviazione esistente a GZRS o RA-GZRS. La migrazione da un account ZRS esistente a GZRS o RA-GZRS è semplice, mentre la migrazione da un account con ridondanza locale, GRS o RA-GRS è più complessa. Le sezioni seguenti descrivono come eseguire la migrazione in entrambi i casi.

### <a name="migrating-from-a-zrs-account"></a>Migrazione da un account ZRS

Per convertire un account ZRS esistente in RA-GZRS, usare il cmdlet [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) per modificare lo SKU per l'account. Ricordarsi di sostituire i valori segnaposto con i propri valori:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -SkuName "Standard_RAGZRS"
```

### <a name="migrating-from-an-lrs-grs-or-ra-grs-account"></a>Migrazione da un account con ridondanza locale, GRS o RA-GRS

Sono disponibili due opzioni per la migrazione a GZRS o RA-GZRS da un account con ridondanza locale, GRS o RA-GRS:

- È possibile copiare o spostare manualmente i dati in un nuovo account GZRS o RA-GZRS da un account esistente.
- È possibile richiedere una migrazione in tempo reale.

#### <a name="perform-a-manual-migration"></a>Eseguire una migrazione manuale

Se è necessario completare la migrazione entro una determinata data, provare a eseguire una migrazione manuale. Una migrazione manuale offre maggiore flessibilità rispetto a una migrazione in tempo reale. Con una migrazione manuale, è possibile controllare le tempistiche.

Per eseguire manualmente la migrazione dei dati da un account esistente a un account GZRS o RA-GZRS, usare uno strumento che consente di copiare i dati in modo efficiente. Di seguito sono riportati alcuni esempi:

- Usare un'utilità come AzCopy o uno strumento di terze parti affidabile. Per informazioni su AzCopy, vedere [Introduzione a AzCopy](storage-use-azcopy-v10.md).
- Se si ha familiarità con Hadoop o HDInsight, associare gli account di archiviazione di origine e di destinazione al cluster. Successivamente, parallelizzare il processo di copia dei dati con uno strumento come DistCp.
- Creare i propri strumenti con una delle librerie client di Archiviazione di Azure.

#### <a name="perform-a-live-migration"></a>Eseguire una migrazione in tempo reale

Una migrazione manuale può comportare tempi di inattività dell'applicazione. Se l'applicazione richiede disponibilità elevata, Microsoft offre anche un'opzione di migrazione in tempo reale. Una migrazione in tempo reale è una migrazione sul posto senza tempi di inattività.

Durante una migrazione in tempo reale, è possibile usare l'account di archiviazione mentre viene eseguita la migrazione dei dati tra gli account di archiviazione di origine e di destinazione. Durante il processo di migrazione in tempo reale, l'account continua a rispettare il contratto di stato per la durabilità e la disponibilità. Non si verificano tempi di inattività o perdite di dati causati dalla migrazione in tempo reale.

Solo gli account per utilizzo generico V2 supportano GZRS/RA-GZRS, quindi prima di inviare una richiesta di migrazione in tempo reale a GZRS/RA-GZRS, è necessario aggiornare l'account a utilizzo generico V2. Per altre informazioni, vedere [Panoramica](https://docs.microsoft.com/azure/storage/common/storage-account-overview) dell'account di archiviazione [di Azure e eseguire l'aggiornamento a un account di archiviazione per utilizzo generico V2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).

Al termine della migrazione, l'impostazione di replica dell'account di archiviazione verrà aggiornata all' **archiviazione con ridondanza della zona geografica (GZRS)** o all' **archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS)** . Gli endpoint di servizio, le chiavi di accesso, le firme di accesso condiviso (SAS) e tutte le altre opzioni di configurazione dell'account rimangono invariati e intatti.

Tenere presenti le limitazioni seguenti relative alla migrazione in tempo reale:

- Sebbene Microsoft gestisca tempestivamente la richiesta di migrazione in tempo reale, non c'è alcuna garanzia per quanto riguarda il tempo necessario per il completamento di questo tipo di migrazione. Se è necessario eseguire la migrazione dei dati a GZRS o RA-GZRS da una determinata data, Microsoft consiglia di eseguire una migrazione manuale. In genere, maggiore è la quantità di dati nell'account, più tempo richiederà la migrazione dei dati.
- L'account deve contenere dati.
- È possibile migrare solo i dati nella stessa area.
- Solo gli account di archiviazione standard supportano la migrazione in tempo reale. Per gli account di archiviazione premium, è necessario usare la migrazione manuale.
- La migrazione in tempo reale da un account GZRS o RA-GZRS a un account con ridondanza locale, GRS o RA-GRS non è supportata. Sarà necessario spostare manualmente i dati in un account di archiviazione nuovo o esistente.
- È possibile richiedere una migrazione in tempo reale da RA-GRS a RA-GZRS. Tuttavia, la migrazione da RA-GRS a GZRS non è supportata. In questo caso, è necessario richiedere una migrazione in tempo reale a RA-GZRS e quindi convertire manualmente l'account di archiviazione per l'uso di GZRS.
- Managed disks supporta solo con ridondanza locale e non è possibile eseguirne la migrazione a GZRS o RA-GZRS. Per l'integrazione con i set di disponibilità, vedere [Introduzione a Managed Disks di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).
- È possibile archiviare snapshot e immagini per SDD Standard Managed Disks nell'archiviazione HDD Standard e [scegliere tra le opzioni con ridondanza locale, ZRS, GZRS e ra-GZRS](https://azure.microsoft.com/pricing/details/managed-disks/).
- Gli account che contengono condivisioni file di grandi dimensioni non sono supportati per GZRS.

Per richiedere una migrazione in tempo reale, usare la [portale di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Dal portale selezionare l'account di archiviazione di cui eseguire la migrazione a GZRS o RA-GZRS e seguire queste istruzioni:

1. Selezionare **nuova richiesta di supporto**.
2. Completare le **nozioni** di base in base alle informazioni sull'account. Nella sezione **servizio** selezionare **gestione** account di archiviazione e specificare l'account da migrare.
3. Selezionare  **Avanti**.
4. Specificare i valori seguenti nella sezione **problema** :
    - **Gravità**: lasciare il valore predefinito.
    - **Tipo di problema**: Selezionare **migrazione dei dati**.
    - **Categoria**: Selezionare **migrate to (RA-) GZRS in a Region**.
    - **Titolo**: Digitare un titolo descrittivo, ad esempio, **(RA-) GZRS migrazione dell'account**.
    - **Dettagli**: Digitare ulteriori dettagli nei **dettagli** box, ad esempio "si desidera eseguire la migrazione a GZRS da [con ridondanza locale, GRS] nell'area \_ @ no__t-3". o "Desidero eseguire la migrazione a RA-GZRS da [con ridondanza locale, RA-GRS] nell'area \_ @ no__t-1".
5. Selezionare  **Avanti**.
6. Verificare che le informazioni di contatto siano corrette nel pannello **informazioni** di contatto.
7. Selezionare **Crea**.

Un rappresentante del supporto contatterà l'utente per fornire assistenza.

## <a name="see-also"></a>Vedere anche

- [Replica di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
- [Archiviazione con ridondanza locale: ridondanza dei dati a basso costo per Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
- [Archiviazione con ridondanza della zona (ZRS): applicazioni di Archiviazione di Azure a disponibilità elevata](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 
