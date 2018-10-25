---
title: Backup online e ripristino con Azure Cosmos DB | Microsoft Docs
description: Informazioni su come eseguire il backup automatico e il ripristino in un database Azure Cosmos DB.
keywords: backup e ripristino, backup online
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: govindk
ms.openlocfilehash: 657b75e5e3bb5c35bb23221235e62298fc797046
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902672"
---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Backup online automatico e ripristino con Azure Cosmos DB
Azure Cosmos DB esegue automaticamente il backup di tutti i dati a intervalli regolari. I backup automatici vengono eseguiti senza impatto sulle prestazioni o sulla disponibilità delle operazioni del database. Tutti i backup vengono archiviati separatamente in un altro servizio di archiviazione, oltre a essere replicati a livello globale per garantire la resilienza in caso di emergenze locali. I backup automatici sono destinati agli scenari in cui si elimina involontariamente il contenitore Cosmos DB e in un secondo momento è necessario il ripristino dei dati.  

L'articolo offre un breve riepilogo iniziale della ridondanza e della disponibilità dei dati in Cosmos DB e quindi illustra i backup. 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Disponibilità elevata con Cosmos DB: riepilogo
Cosmos DB è progettato per la [distribuzione globale](distribute-data-globally.md) e consente di ridimensionare la velocità effettiva in più aree di Azure con failover basato su criteri e API multihosting trasparenti. Azure Cosmos DB offre [contratti di servizio](https://azure.microsoft.com/support/legal/sla/cosmos-db) con disponibilità del 99,99% per tutti gli account in una singola area e tutti gli account in più aree con coerenza media e con disponibilità in lettura del 99,999% per tutti gli account di database in più aree. Tutte le operazioni di scrittura in Azure Cosmos DB sono sottoposte a commit durevole su dischi locali da un quorum di repliche in un data center locale prima della conferma nel client. La disponibilità elevata di Cosmos DB si basa sull'archiviazione locale e non dipende da tecnologie di archiviazione esterna. Inoltre, se l'account di database è associato a più di un'area di Azure, le operazioni di scrittura vengono replicate anche nelle altre aree. Per scalare la velocità effettiva e accedere ai dati a basse latenze, è possibile avere tutte le aree di lettura associate all'account di database che si vuole. In ogni area di lettura, i dati (replicati) vengono mantenuti in modo permanente in un set di repliche.  

Come illustrato nel diagramma seguente, un singolo contenitore Cosmos DB viene sottoposto a [partizionamento orizzontale](partition-data.md). Una "partizione" è indicata da un cerchio nel diagramma sotto e ogni partizione viene resa altamente disponibile tramite un set di repliche. Si tratta della distribuzione locale all'interno di un'unica area di Azure (indicata dall'asse X). In più, ogni partizione (con il set di repliche corrispondente) viene successivamente distribuita a livello globale in più aree associate all'account di database (ad esempio, le tre aree geografiche presenti nell'illustrazione, ovvero Stati Uniti orientali, Stati Uniti occidentali e India centrale). Il "Set di partizioni" è un'entità distribuita a livello globale che comprende più copie dei dati in ogni area (indicata dall'asse Y). È possibile assegnare la priorità alle aree associate all'account di database e, in caso di emergenza, verrà effettuato il failover trasparente di Cosmos DB nell'area successiva. È anche possibile simulare manualmente il failover per testare la disponibilità end-to-end dell'applicazione.  

L'immagine seguente illustra l'elevato grado di ridondanza con Cosmos DB.

![Elevato grado di ridondanza con Cosmos DB](./media/online-backup-and-restore/redundancy.png)

![Elevato grado di ridondanza con Cosmos DB](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>Backup online, completi, automatici
È possibile che il contenitore o il database venga eliminato. Con Cosmos DB, non solo i dati ma anche i relativi backup vengono resi altamente ridondanti e resilienti in caso di emergenze locali. I backup automatici vengono attualmente eseguiti ogni quattro ore circa; ogni volta vengono archiviati i due backup più recenti. Se i dati vengono involontariamente eliminati o danneggiati, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/options/) entro otto ore. 

I backup vengono eseguiti senza influenzare le prestazioni o la disponibilità delle operazioni del database. Cosmos DB esegue il backup in background senza utilizzare le UR di cui è stato effettuato il provisioning o influenzare le prestazioni e senza influire sulla disponibilità del database. 

A differenza dei dati archiviati all'interno di Cosmos DB, i backup automatici vengono memorizzati nel servizio di archiviazione BLOB di Azure. Per garantire il caricamento efficiente e a bassa latenza, lo snapshot del backup viene caricato in un'istanza dell'archivio BLOB di Azure nella stessa area dell'area di scrittura corrente dell'account di database Cosmos DB. Per garantire la resilienza in caso di emergenze locali, ogni snapshot dei dati di backup nell'archivio BLOB di Azure viene replicato nuovamente tramite l'archiviazione con ridondanza geografica (GRS) in un'altra area. Il diagramma seguente illustra il backup dell'intero contenitore Cosmos DB (in questo esempio con tutte le tre partizioni primarie negli Stati Uniti occidentali) in un account di archiviazione BLOB di Azure remoto negli Stati Uniti occidentali e quindi la replica con archiviazione con ridondanza geografica negli Stati Uniti orientali. 

L'immagine di seguito illustra i backup completi periodici di tutte le entità di Cosmos DB nell'archiviazione con ridondanza geografica di Azure.

![Backup completi periodici di tutte le entità di Cosmos DB nell'archiviazione con ridondanza geografica di Azure](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>Periodo di conservazione dei backup
Come descritto sopra, Azure Cosmos DB crea snapshot dei dati ogni quattro ore a livello di partizione. In qualsiasi momento risultano disponibili solo gli ultimi due snapshot. Tuttavia, se il database/contenitore viene eliminato, Azure Cosmos DB conserverà per 30 giorni gli snapshot esistenti per tutte le partizioni eliminate all'interno del database/contenitore specificato.

Per API SQL, se si desidera mantenere i propri snapshot, è possibile farlo usando le opzioni seguenti:

* Usare l'opzione di esportazione in JSON nello [strumento di migrazione dei dati](import-data.md#export-to-json-file) di Azure Cosmos DB per pianificare backup aggiuntivi.

* Usare [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) per spostare i dati periodicamente.

* Usare il [feed di modifiche](change-feed.md) di Azure Cosmos DB per leggere i dati periodicamente per il backup completo e separatamente per modifiche incrementali e passare alla destinazione BLOB. 

* Per la gestione dei backup a caldo, è possibile leggere periodicamente i dati dal feed di modifiche e posticipare la scrittura in un'altra raccolta. In questo modo non è necessario ripristinare i dati ed è possibile esaminare immediatamente i dati per il problema. 

> [!NOTE]
> Se si "effettua il provisioning della velocità effettiva per un set di contenitori a livello di database", ricordarsi che il ripristino avviene a livello di account di database completo. È inoltre necessario assicurarsi di contattare il team di supporto tecnico entro 8 ore se si elimina accidentalmente il contenitore. Se non si contatta il team di supporto entro 8 ore, infatti, non è possibile ripristinare i dati.

## <a name="restoring-a-database-from-an-online-backup"></a>Ripristino di un database da un backup online

Se si elimina accidentalmente il database o il contenitore, è possibile [inviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) o [contattare il supporto di Azure](https://azure.microsoft.com/support/options/) per ripristinare i dati dall'ultimo backup automatico. Il supporto di Azure è disponibile solo per piani selezionati, ad esempio Standard e Developer. Il supporto non è disponibile per il piano Basic. Per altre informazioni su piani di supporto diversi, vedere la pagina [Piani di supporto per Azure](https://azure.microsoft.com/support/plans/). 

Se è necessario ripristinare il database a causa di un problema di danneggiamento dei dati, inclusa l'eliminazione di documenti contenuti in un contenitore, vedere [Gestione del danneggiamento dei dati](#handling-data-corruption) perché è necessario eseguire passaggi aggiuntivi per evitare che i dati danneggiati sovrascrivano i backup esistenti. Per il ripristino di uno snapshot specifico del backup, Cosmos DB richiede che i dati siano stati disponibili per la durata del ciclo di backup per tale snapshot.

> [!NOTE]
> Le raccolte o i database possono essere ripristinati solo su richiesta esplicita del cliente. È responsabilità del cliente per eliminare il contenitore o il database immediatamente dopo la riconciliazione dei dati. Se non si eliminano i database o le raccolte ripristinati, la cosa comporta addebiti per le unità richiesta, l'archiviazione e comportano costi di uscita, archiviazione e il traffico in uscita.

## <a name="handling-data-corruption"></a>Gestione del danneggiamento dei dati

Azure Cosmos DB conserva gli ultimi due backup di ogni partizione nell'account del database. Questo modello è ideale quando un contenitore (raccolta di documenti, grafo, tabella) o un database viene eliminato involontariamente perché consente di ripristinare una delle versioni più recenti. Se tuttavia gli utenti causano un problema di danneggiamento dei dati, Azure Cosmos DB potrebbe non rilevare il danneggiamento dei dati, che potrebbero quindi aver sovrascritto i dati nei backup esistenti. 

Non appena il danneggiamento viene rilevato, l'utente deve eliminare il contenitore danneggiato (raccolta/grafo/tabella) in modo che i backup siano protetti e non vengano sovrascritti con i dati danneggiati. E ancora più importante rivolgersi al supporto Microsoft e creare un ticket di richiesta specifica di gravità 2. 

L'immagine seguente illustra la creazione di una richiesta di supporto per il ripristino di un contenitore (raccolta/grafo/tabella) tramite il portale di Azure a causa dell'eliminazione o dell'aggiornamento accidentale dei dati all'interno di un contenitore.

![Ripristinare un contenitore a causa di un errore di aggiornamento o cancellazione dei dati in Cosmos DB](./media/online-backup-and-restore/backup-restore-support.png)

Quando il ripristino viene eseguito per questo tipo di scenari, i dati vengono ripristinati in un altro account (con suffisso "-restored") e in un altro contenitore. Questo ripristino non viene eseguito in sostituzione del precedente per consentire al cliente di eseguire la convalida dei dati e spostare i dati in base alle esigenze. Il contenitore ripristinato si trova nella stessa area con gli stessi criteri di indicizzazione e UR. L'utente che è coamministratore o amministratore della sottoscrizione può visualizzare questo account ripristinato.


> [!NOTE]
> Se si ripristinano i dati per la correzione dei problemi di danneggiamento o solo per i test, pianificare di rimuovere i dati non appena l'attività è eseguita poiché i contenitori o il database ripristinati hanno costi aggiuntivi in base alla velocità effettiva con provisioning. 
## <a name="next-steps"></a>Passaggi successivi

Per replicare il database in più data center, vedere l'articolo su come [distribuire i dati a livello globale con Cosmos DB](distribute-data-globally.md). 

Per contattare il supporto di Azure, [creare un ticket dal portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

