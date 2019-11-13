---
title: Compilare applicazioni di Archiviazione Azure a disponibilità elevata in archiviazioni con ridondanza della zona | Microsoft Docs
description: L'archiviazione con ridondanza della zona offre un modo semplice per compilare applicazioni a disponibilità elevata. L'archiviazione con ridondanza della zona protegge dagli errori hardware nel data center e da alcune situazioni di emergenza a livello di area.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 0196e6fb4b2cd52fd66a26e07e8d4ce71983bc16
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013204"
---
# <a name="zone-redundant-storage-zrs-for-building-highly-available-azure-storage-applications"></a>Archiviazione con ridondanza della zona (ZRS) per la creazione di applicazioni di archiviazione di Azure a disponibilità elevata

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Copertura del supporto e disponibilità a livello di area

ZRS supporta attualmente i tipi di account di archiviazione standard per utilizzo generico V2 e filestorage. Per altre informazioni sui tipi di account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md).

ZRS è disponibile per i BLOB in blocchi, i BLOB di pagine non disco, le condivisioni file standard, le tabelle e le code.

Per gli account per utilizzo generico V2, ZRS è disponibile a livello generale nelle aree seguenti:

- Asia sudorientale
- Europa occidentale
- Europa settentrionale
- Francia centrale
- Giappone orientale
- Regno Unito meridionale
- Stati Uniti centrali
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti occidentali 2

Per gli account filestorage (condivisioni file Premium), ZRS è disponibile a livello generale nelle aree seguenti:

- Europa occidentale
- Stati Uniti orientali

Microsoft continua ad abilitare l'archiviazione con ridondanza della zona in aree di Azure aggiuntive. Fare riferimento regolarmente alla pagina [Aggiornamenti di Azure](https://azure.microsoft.com/updates/) per informazioni sulle nuove aree.

**Limitazioni note**

- Il livello archivio non è attualmente supportato per gli account ZRS. Per altri dettagli [, vedere Archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, ad accesso sporadico e archivio](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) .
- I dischi gestiti non supportano ZRS. È possibile archiviare snapshot e immagini per SDD Standard Managed Disks nell'archiviazione HDD Standard e [scegliere tra le opzioni con ridondanza locale e ZRS](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Cosa accade quando una zona smette di essere disponibile?

I dati sono ancora accessibili per le operazioni di lettura e scrittura anche se una zona non è più disponibile. Microsoft consiglia di continuare a seguire le procedure per la gestione degli errori temporanei. Queste procedure includono l'implementazione di criteri di ripetizione dei tentativi con interruzione temporanea esponenziale.

Quando una zona non è disponibile, Azure avvia gli aggiornamenti di rete, ad esempio la modifica del puntamento DNS. Questi aggiornamenti possono interessare l'applicazione se si accede ai dati prima che l'aggiornamento sia stato completato.

L'archiviazione con ridondanza della zona potrebbe non proteggere i dati in caso di un'emergenza a livello di area in cui più zone sono interessate in modo permanente. L'archiviazione con ridondanza della zona offre invece resilienza per i dati qualora diventino temporaneamente non disponibili. Per la protezione in caso di emergenze a livello di area, Microsoft consiglia di usare l'archiviazione con ridondanza geografica (GRS). Per altre informazioni sull'archiviazione con ridondanza geografica, vedere [Archiviazione con ridondanza geografica: replica tra più aree per Archiviazione di Azure](storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Conversione nella replica di archiviazione con ridondanza della zona

La migrazione in o da archiviazione con ridondanza locale, archiviazione con ridondanza geografica e archiviazione con ridondanza geografica e accesso in lettura è molto semplice. Per modificare il tipo di ridondanza dell'account, usare il portale di Azure o l'API del provider delle risorse di archiviazione. Azure eseguirà quindi la replica dei dati in modo conforme all'opzione scelta. 

La migrazione dei dati a ZRS richiede una strategia diversa. La migrazione dell'archiviazione con ridondanza della zona comporta lo spostamento fisico dei dati da un indicatore di archiviazione singolo a più indicatori all'interno di un'area.

Per la migrazione a ZRS sono disponibili due opzioni principali: 

- Copiare o spostare manualmente i dati dall'account esistente a un nuovo account di archiviazione con ridondanza della zona.
- Richiedere una migrazione in tempo reale.

> [!IMPORTANT]
> La migrazione in tempo reale non è attualmente supportata per le condivisioni file Premium. Attualmente sono supportati solo i dati copiati o spostati manualmente.

Per completare la migrazione entro una determinata data, provare a eseguire una migrazione manuale. Una migrazione manuale offre maggiore flessibilità rispetto a una migrazione in tempo reale. Con una migrazione manuale, è possibile controllare le tempistiche.

Per eseguire una migrazione manuale, sono disponibili alcune opzioni:
- Usare strumenti esistenti, ad esempio AzCopy, una delle librerie client di Archiviazione di Azure o strumenti affidabili di terze parti.
- Se si ha familiarità con Hadoop o HDInsight, collegare account di origine e di destinazione (dell'archiviazione con ridondanza della zona) al cluster. Quindi, parallelizzare il processo di copia dei dati con uno strumento, ad esempio DistCp.
- Creare i propri strumenti con una delle librerie client di Archiviazione di Azure.

Una migrazione manuale può comportare tempi di inattività dell'applicazione. Se l'applicazione richiede disponibilità elevata, Microsoft offre anche un'opzione di migrazione in tempo reale. Una migrazione in tempo reale è una migrazione sul posto senza tempi di inattività. 

Quando si esegue una migrazione in tempo reale, è possibile usare l'account di archiviazione durante la migrazione dei dati tra gli indicatori di archiviazione di origine e di destinazione. Durante il processo di migrazione si hanno a disposizione gli stessi livelli di durabilità e disponibilità normalmente previsti dal contratto di servizio.

Tenere presenti le limitazioni seguenti relative alla migrazione in tempo reale:

- Sebbene Microsoft gestisca tempestivamente la richiesta di migrazione in tempo reale, non c'è alcuna garanzia per quanto riguarda il tempo necessario per il completamento di questo tipo di migrazione. Se è necessario eseguire la migrazione dei dati in un'archiviazione con ridondanza della zona entro una certa data, Microsoft consiglia di eseguire una migrazione manuale. In genere, maggiore è la quantità di dati nell'account, più tempo richiederà la migrazione dei dati. 
- La migrazione in tempo reale è supportata solo per gli account di archiviazione che usano la replica con l'archiviazione con ridondanza locale o l'archiviazione con ridondanza geografica. Se l'account usa l'archiviazione con ridondanza geografica e accesso in lettura, è necessario innanzitutto modificare il tipo di replica dell'account in archiviazione con ridondanza locale o archiviazione con ridondanza geografica prima di procedere. Questo passaggio intermedio rimuove, prima della migrazione, l'endpoint secondario di sola lettura fornito dall'archiviazione con ridondanza geografica e accesso in lettura.
- L'account deve contenere dati.
- È possibile migrare solo i dati nella stessa area. Se si vuole eseguire la migrazione dei dati in un account di archiviazione con ridondanza della zona che si trova in un'area diversa rispetto all'account di origine, è necessario eseguire una migrazione manuale.
- Solo gli account di archiviazione standard supportano la migrazione in tempo reale. Per gli account di archiviazione premium, è necessario usare la migrazione manuale.
- La migrazione in tempo reale da ZRS a con ridondanza locale, GRS o RA-GRS non è supportata. Sarà necessario spostare manualmente i dati su un account di archiviazione nuovo o esistente.
- I dischi gestiti sono disponibili solo per con ridondanza locale e non è possibile eseguirne la migrazione a ZRS. È possibile archiviare snapshot e immagini per SDD Standard Managed Disks nell'archiviazione HDD Standard e [scegliere tra le opzioni con ridondanza locale e ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). Per l'integrazione con i set di disponibilità, vedere [Introduzione a Managed Disks di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).
- Non è possibile eseguire la migrazione di account con ridondanza locale o GRS con dati archiviati in ZRS.

È possibile richiedere la migrazione in tempo reale tramite il [portale del supporto tecnico di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Nel portale selezionare l'account di archiviazione da convertire in archiviazione con ridondanza della zona.
1. Selezionare **Nuova richiesta di supporto**
2. Completare le **nozioni di base** con le informazioni relative all'account. Nella sezione **Assistenza** selezionare **Gestione Account di archiviazione** e la risorsa che si vuole convertire in archiviazione con ridondanza della zona. 
3. Selezionare **Avanti**. 
4. Specificare i valori seguenti nella sezione **Problema**: 
    - **Gravità**: lasciare il valore predefinito.
    - **Tipo di problema**: selezionare **Migrazione dei dati**.
    - **Category**: selezionare **migrate to ZRS**.
    - **Titolo**: digitare un titolo descrittivo, ad esempio **migrazione di account con archiviazione con ridondanza della zona**.
    - **Dettagli**: digitare ulteriori dettagli nella casella **Dettagli** , ad esempio, si desidera eseguire la migrazione a ZRS da [con ridondanza locale, GRS] nell'area \_\_. 
5. Selezionare **Avanti**.
6. Verificare che le informazioni di contatto nel pannello **Informazioni contatto** siano corrette.
7. Selezionare **Create**.

Un addetto del supporto tecnico contatterà l'utente e fornirà l'assistenza necessaria.

## <a name="live-migration-to-zrs-faq"></a>Domande frequenti sulla migrazione in tempo reale a ZRS

**È necessario pianificare i tempi di inattività durante la migrazione?**

Non si verifica alcun tempo di inattività causato dalla migrazione. Durante una migrazione in tempo reale, è possibile continuare a usare l'account di archiviazione mentre viene eseguita la migrazione dei dati tra timbri di archiviazione di origine e di destinazione. Durante il processo di migrazione si hanno a disposizione gli stessi livelli di durabilità e disponibilità normalmente previsti dal contratto di servizio.

**Si è verificata una perdita di dati associata alla migrazione?**

La migrazione non è associata ad alcuna perdita di dati. Durante il processo di migrazione si hanno a disposizione gli stessi livelli di durabilità e disponibilità normalmente previsti dal contratto di servizio.

**Sono necessari aggiornamenti per le applicazioni al termine della migrazione?**

Al termine della migrazione, il tipo di replica degli account verrà modificato in "archiviazione con ridondanza della zona (ZRS)". Gli endpoint di servizio, le chiavi di accesso, la firma di accesso condiviso e altre opzioni di configurazione dell'account rimangono invariati e intatti.

**È possibile richiedere una migrazione in tempo reale degli account per utilizzo generico V1 a ZRS?**

ZRS supporta solo account per utilizzo generico V2, quindi prima di inviare una richiesta di migrazione in tempo reale a ZRS assicurarsi di aggiornare gli account a utilizzo generico V2. Per altri dettagli, vedere [Panoramica dell'account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview) e [passare a un account di archiviazione per utilizzo generico V2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) .

**È possibile richiedere una migrazione in tempo reale degli account di archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) a ZRS?**

Prima di inviare una richiesta di migrazione in tempo reale a ZRS, verificare che le applicazioni o i carichi di lavoro non richiedano più l'accesso all'endpoint secondario di sola lettura e modificare il tipo di replica degli account di archiviazione in archiviazione con ridondanza geografica (GRS). Per ulteriori informazioni, vedere [modifica della strategia di replica](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy) .

**È possibile richiedere una migrazione in tempo reale degli account di archiviazione a ZRS in un'altra area?**

Se si desidera eseguire la migrazione dei dati in un account ZRS che si trova in un'area diversa dall'area dell'account di origine, è necessario eseguire una migrazione manuale.

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>Archiviazione con ridondanza della zona (versione classica): opzione legacy per la ridondanza dei BLOB in blocchi
> [!NOTE]
> Microsoft imposterà come deprecati gli account della versione classica dell'archiviazione con ridondanza della zona e ne eseguirà la migrazione il 31 marzo 2021. Altri dettagli verranno inviati ai clienti della versione classica dell'archiviazione con ridondanza della zona prima di essere deprecati. 
>
> Quando ZRS diventa [disponibile](#support-coverage-and-regional-availability) a livello generale in un'area, i clienti non saranno in grado di creare account ZRS classici dal portale in quest'area. L'uso di Microsoft PowerShell e dell'interfaccia della riga di comando di Azure per creare gli account della versione classica dell'archiviazione con ridondanza della zona sarà disponibile finché la versione classica dell'archiviazione con ridondanza della zona viene deprecata.

La versione classica dell'archiviazione con ridondanza della zona replica i dati in modo asincrono tra data center in una o due aree. I dati replicati potrebbero non essere disponibili a meno che Microsoft non avvii il failover all'area secondaria. Gli account della versione classica dell'archiviazione con ridondanza della zona non possono essere convertiti in o da archiviazione con ridondanza locale, archiviazione con ridondanza geografica o archiviazione con ridondanza geografica e accesso in lettura. Gli account della versione classica dell'archiviazione con ridondanza della zona non supportano inoltre le metriche o la registrazione.

La versione classica dell'archiviazione con ridondanza della zona è disponibile solo per i **BLOB in blocchi** negli account di archiviazione per utilizzo generico V1 (GPv1). Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md).

Per eseguire manualmente la migrazione di dati di un account di archiviazione con ridondanza della zona in o da un account di archiviazione con ridondanza locale, archiviazione con ridondanza della zona (versione classica), archiviazione con ridondanza geografica o archiviazione con ridondanza geografica e accesso in lettura, usare uno degli strumenti seguenti: AzCopy, Azure Storage Explorer, Azure PowerShell o l'interfaccia della riga di comando di Azure. È anche possibile creare una soluzione di migrazione personalizzata con una delle librerie client di Archiviazione di Azure.

È anche possibile aggiornare gli account ZRS classici a ZRS nel portale oppure usando Azure PowerShell o l'interfaccia della riga di comando di Azure nelle aree in cui è disponibile ZRS. Per eseguire l'aggiornamento a ZRS nel portale di Azure, passare alla sezione di **configurazione** dell'account e scegliere **Aggiorna**:

![Aggiornare ZRS classico a ZRS nel portale](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.png)

Per eseguire l'aggiornamento a ZRS usando PowerShell, chiamare il comando seguente:
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

Per eseguire l'aggiornamento a ZRS tramite CLI, chiamare il comando seguente:
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>Vedere anche
- [Replica di Archiviazione di Azure](storage-redundancy.md)
- [Archiviazione con ridondanza locale: ridondanza dei dati a basso costo per Archiviazione di Azure](storage-redundancy-lrs.md)
- [Archiviazione con ridondanza geografica: replica tra più aree per Archiviazione di Azure](storage-redundancy-grs.md)
