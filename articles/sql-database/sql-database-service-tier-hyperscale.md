---
title: Panoramica di Hyperscale per il database SQL di Azure | Microsoft Docs
description: Questo articolo descrive il livello di servizio Hyperscale nel modello di acquisto basato su vCore nel database SQL di Azure e ne illustra le differenze rispetto ai livelli di servizio Utilizzo generico e Business critical.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/06/2019
ms.openlocfilehash: ce6fc5d32fc9e17499a56cec7f4db2849370a1ec
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566720"
---
# <a name="hyperscale-service-tier-for-up-to-100-tb"></a>Livello di servizio iperscalabile per un massimo di 100 TB

Il Database SQL di Azure si basa sull'architettura del motore di database di SQL Server che viene rettificata per l'ambiente cloud per garantire la disponibilità del 99,99% anche in caso di errori dell'infrastruttura. Esistono tre modelli di architettura usati nel database SQL di Azure:
- Utilizzo generico/Standard 
-  Hyperscale
-  Business critical/Premium

Il livello di servizio Hyperscale nel database SQL di Azure è il livello di servizio più recente nel modello di acquisto basato su vCore. Questo livello di servizio è un altamente scalabile per le prestazioni di archiviazione e calcolo, e sfrutta l'architettura di Azure per scalare orizzontalmente le risorse di archiviazione e di calcolo per un database SQL di Azure sostanzialmente oltre i limiti disponibili per i livelli di utilizzo generico e business critical.

> 
> [!NOTE]
> Per informazioni dettagliate sui livelli di servizio Utilizzo generico e Business critical nel modello di acquisto basato su vCore, vedere i livelli di servizio [Utilizzo generico](sql-database-service-tier-general-purpose.md) e [Business critical](sql-database-service-tier-business-critical.md). Per un confronto tra il modello di acquisto basato su vCore e quello basato su DTU, vedere [Modelli di acquisto e risorse del database SQL di Azure](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>Funzionalità del livello di servizio Hyperscale

Il livello di servizio Hyperscale nel database SQL di Azure offre le seguenti funzionalità aggiuntive:

- Supporto per database di dimensioni massime di 100 TB
- Backup di database quasi istantanei (basati su snapshot di file archiviati nell'archivio BLOB di Azure) indipendentemente dalle dimensioni senza effetti di i/o sulle risorse di calcolo  
- Ripristino dei database (basati su snapshot di file) in pochi minuti anziché in ore o giorni (non è un'operazione di dimensionamento dei dati)
- Prestazioni complessive più elevate grazie alla maggiore velocità effettiva dei log e ai tempi di esecuzione di commit delle transazioni più veloci, indipendentemente dai volumi di dati
- Rapida scalabilità orizzontale: è possibile effettuare il provisioning di uno o più nodi di sola lettura per l'offload del carico di lavoro di lettura e per l'uso come hot standby
- Rapida scalabilità orizzontale: in un tempo costante è possibile aumentare le risorse di calcolo per supportare ingenti carichi di lavoro secondo necessità, e quindi ridurre nuovamente le risorse di calcolo quando non necessarie.

Il livello di servizio Hyperscale elimina molti dei limiti pratici che generalmente caratterizzano i database cloud. Se la maggior parte dei database sono limitati dalle risorse disponibili in un singolo nodo, i database nel livello di servizio Hyperscale non presentano limiti di questo tipo. Grazie all'architettura di archiviazione flessibile, lo spazio di archiviazione aumenta in base alle esigenze. Infatti, i database Hyperscale non vengono creati con un limite definito per la dimensione massima. Un database Hyperscale può espandersi in base alle esigenze, e la fatturazione avviene solo in base alla capacità in uso. Per i carichi di lavoro con intense attività di lettura, il livello di servizio Hyperscale consente la rapida scalabilità orizzontale effettuando il provisioning di repliche in lettura aggiuntive in base alle necessità per l'offload dei carichi di lavoro di lettura.

Inoltre, il tempo necessario per creare i backup dei database oppure aumentare o diminuire le prestazioni non è più associato al volume dei dati presenti nel database. È possibile eseguire il backup dei database Hyperscale praticamente istantaneamente. È anche possibile ridimensionare un database aumentando o diminuendo la capacità di decine di terabyte in pochi minuti. Questa funzionalità consente di non essere vincolati alle scelte di configurazione iniziali.

Per altre informazioni sulle dimensioni di calcolo per il livello di servizio Hyperscale, vedere [Caratteristiche del livello di servizio](sql-database-service-tiers-vcore.md#service-tier-characteristics).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Destinazione d'uso del livello di servizio Hyperscale

Il livello di servizio Hyperscale è destinato principalmente ai clienti che dispongono di database di grandi dimensioni in sede e che vogliono modernizzare le proprie applicazioni passando al cloud, oppure per i clienti che giù dispongono del cloud ma sono limitati dalle dimensioni massime del database (1-4 TB). È destinato anche ai clienti che sono alla ricerca di alte prestazioni e scalabilità elevata per l'archiviazione e il calcolo.

Il livello di servizio Hyperscale supporta tutti i carichi di lavoro di Microsoft SQL Server ma è ottimizzato principalmente per OLTP. Il livello di servizio Hyperscale supporta anche carichi di lavoro ibridi e analitici (data mart).

> [!IMPORTANT]
> I pool elastici non supportano il livello di servizio Hyperscale.

## <a name="hyperscale-pricing-model"></a>Modello di prezzi del livello di servizio Hyperscale

Il livello di servizio Hyperscale è disponibile solo nel [modello vCore](sql-database-service-tiers-vcore.md). Per allinearsi alla nuova architettura, il modello di prezzi è leggermente diverso da quello del livello di servizio Utilizzo generico o Business critical:

- **Calcolo**:

  Il prezzo dell'unità di calcolo del livello di servizio Hyperscale è per replica. Il prezzo del [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) viene applicato automaticamente alle repliche con scalabilità in lettura. Per impostazione predefinita, viene creata una replica primaria e una replica di sola lettura per database iperscalare.  Gli utenti possono modificare il numero totale di repliche, incluso il database primario da 1-5.

- **Archiviazione**:

  Non è necessario specificare le dimensioni massime dei dati durante la configurazione di un database Hyperscale. Nel livello con iperscalabilità vengono addebitate le risorse di archiviazione per il database in base all'utilizzo effettivo. Lo spazio di archiviazione viene allocato automaticamente tra 10 GB e 100 TB, con incrementi regolati in modo dinamico tra 10 GB e 40 GB.  

Per altre informazioni sui prezzi di Hyperscale, vedere [Prezzi di Database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Architettura con funzioni distribuite

A differenza dei motori di database tradizionali che centralizzano tutte le funzioni di gestione dati in un unico percorso o processo (perfino i cosiddetti database distribuiti per la produzione dispongono attualmente di più copie di un motore dati monolitico), un database Hyperscale separa il motore di elaborazione query, in cui far divergere la semantica dei vari motori di dati, dai componenti che offrono archiviazione a lungo termine e la durabilità dei dati. In questo modo, la capacità di archiviazione può essere facilmente aumentata secondo necessità (l'obiettivo iniziale è 100 TB). Le repliche di sola lettura condividono gli stessi componenti di archiviazione, pertanto non è necessaria alcuna copia di dati per creare una nuova replica leggibile. 

Il diagramma seguente illustra i diversi tipi di nodi in un database Hyperscale:

![architettura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Un database Hyperscale contiene i seguenti tipi diversi di nodi:

### <a name="compute-node"></a>Nodo di calcolo

Il nodo di calcolo è dove si trova il motore relazionale e dove si verificano tutti i gli elementi del linguaggio, dell'elaborazione delle query e così via. Tutte le interazioni dell'utente con un database Hyperscale vengono eseguite tramite questi nodi di calcolo. I nodi di calcolo presentano cache basae su SSD (con etichetta RBPEX - estensione del pool di buffer resiliente nel diagramma precedente) per ridurre al minimo il numero di round trip di rete necessari per recuperare una pagina di dati. È presente un nodo di calcolo primario in cui vengono elaborati tutti i carichi di lavoro di lettura/scrittura e le transazioni. Sono presenti uno o più nodi di calcolo secondari che fungono da nodi di hot standby per finalità di failover, oltre a fungere da nodi di calcolo di sola lettura per l'offload di carichi di lavoro di lettura (se si desidera questa funzionalità).

### <a name="page-server-node"></a>Nodo server di pagina

I servers di pagina sono sistemi che rappresentano un motore di archiviazione con scalabilità orizzontale.  Ogni server di pagina è responsabile di un subset delle pagine nel database.  Ogni controllo server di pagina è nominalmente compreso tra 128 GB e 1 TB di dati. Nessun dato è condiviso su più di un server di pagina (al di là delle repliche mantenute per ridondanza e disponibilità). Il compito di un server di pagina consiste nel fornire su richiesta le pagine del database ai nodi di calcolo e nel mantenere le pagine aggiornate man mano che le transazioni aggiornano i dati. I server di pagina vengono tenuti aggiornati riproducendo i record di log dal servizio di log. Per migliorare le prestazioni, i server di pagina gestiscono anche le cache basate su SSD. L'archiviazione a lungo termine delle pagine di dati viene mantenuta in Archiviazione di Azure per garantire maggiore affidabilità.

### <a name="log-service-node"></a>Nodo del servizio di log

Il nodo del servizio di log accetta i record di log dal nodo di calcolo primario, li rende persistenti in una cache durevole e inoltra i record del log ai restanti nodi di calcolo (in modo che possano aggiornare le cache) nonché ai server di pagina pertinenti, in modo che i dati possano essere qui aggiornati. In questo modo, tutte le modifiche ai dati dal nodo di calcolo primario vengono propagate tramite il servizio di log a tutti i nodi di calcolo secondari e ai server di pagina. Infine, i record di log vengono inviati all'archiviazione a lungo termine in Archiviazione di Azure, ovvero un repository di archiviazione infinito. Questo meccanismo elimina la necessità di troncamento frequente dei log. Il servizio di log include inoltre la cache locale per velocizzare l'accesso.

### <a name="azure-storage-node"></a>Nodo di archiviazione di Azure

Il nodo di archiviazione di Azure è la destinazione finale dei dati dal server di pagina. Questo archivio viene usato per scopi di backup così come per la replica tra aree di Azure. I backup sono costituiti da snapshot di file di dati. Le operazioni di ripristino sono veloci grazie a questi snapshot e i dati possono essere ripristinati in qualsiasi punto nel tempo.

## <a name="backup-and-restore"></a>Backup e ripristino

I backup sono snapshot di file di base e di conseguenza sono quasi istantanei. La separazione tra calcolo e archiviazione consente di portare backup/operazione di ripristino a livello di archiviazione per ridurre il carico di elaborazione nel nodo di calcolo primario. Di conseguenza, il backup di un database di grandi dimensioni non compromette le prestazioni del nodo di calcolo primario. Analogamente, le operazioni di ripristino vengono eseguite tramite la copia di snapshot di file e di conseguenza non costituiscono un'operazione di dimensionamento dei dati. Per i ripristini nello stesso account di archiviazione, l'operazione di ripristino è veloce.

## <a name="scale-and-performance-advantages"></a>Vantaggi di scalabilità e prestazioni

Con la possibilità di accelerare/diminuore la velocità dei nodi di calcolo di sola lettura aggiuntivi, l'architettura Hyperscale offre significative funzionalità di scalabilità di lettura e consente inoltre di liberare il nodo di calcolo primario per la gestione di più richieste di scrittura. Inoltre, i nodi di calcolo possono essere aumentati o diminuiti rapidamente grazie all'architettura di archiviazione condivisa dell'architettura Hyperscale.

## <a name="create-a-hyperscale-database"></a>Creare un database con iperscalabilità

È possibile creare un database con iperscalabilità usando il [portale di Azure](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) o l'[interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). I database con iperscalabilità sono disponibili solo con il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).

Il comando T-SQL seguente crea un database Hyperscale. Nell'istruzione `CREATE DATABASE` è necessario specificare sia l'edizione che l'obiettivo del servizio. Per un elenco degli obiettivi di servizio validi, vedere i [limiti delle risorse](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale-service-tier) .

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Verrà creato un database con iperscalabilità nell'hardware quinta generazione con 4 core.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Eseguire la migrazione di un database SQL di Azure esistente al livello di servizio Hyperscale

È possibile spostare i database SQL di Azure esistenti nel livello di servizio Hyperscale usando il [portale di Azure](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) o l'[interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). A questo punto, si tratta di una migrazione unidirezionale. Non è possibile spostare i database da Hyperscale a un altro livello di servizio. È consigliabile creare una copia dei database di produzione ed eseguire la migrazione al livello di servizio Hyperscale per il modello di verifica.

Il comando T-SQL seguente sposta un database nel livello di servizio Hyperscale. Nell'istruzione `ALTER DATABASE` è necessario specificare sia l'edizione che l'obiettivo del servizio.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Connettersi a una replica scalabilità in lettura di un database Hyperscale

Nei database con iperscalabilità l'argomento `ApplicationIntent` nella stringa di connessione fornita dal client indica se la connessione viene instradata alla replica in scrittura o a una replica secondaria di sola lettura. Se `ApplicationIntent` è impostato su `READONLY` e il database non ha una replica secondaria, la connessione viene indirizzata alla replica primaria con il comportamento predefinito `ReadWrite`.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```
## <a name="disaster-recovery-for-hyperscale-databases"></a>Ripristino di emergenza per database iperscalari
### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Ripristino di un database con iperscalabilità in un'altra area geografica
Se è necessario ripristinare un database iperscalabile del database SQL di Azure in un'area diversa da quella in cui è attualmente ospitata, come parte di un'operazione di ripristino di emergenza o di un'esercitazione, una rilocazione o qualsiasi altro motivo, il metodo principale consiste nell'eseguire un ripristino geografico del database.  Ciò comporta esattamente la stessa procedura usata per ripristinare qualsiasi altro database SQL di AZURE in un'area diversa:
1. Creare un server di database SQL nell'area di destinazione se non si dispone già di un server appropriato.  Il server deve appartenere alla stessa sottoscrizione del server originale (di origine).
2. Seguire le istruzioni riportate nell'argomento relativo al [ripristino geografico](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) della pagina relativa al ripristino dei database SQL di Azure dai backup automatici.

> [!NOTE]
> Poiché l'origine e la destinazione si trovano in aree separate, il database non può condividere l'archiviazione snapshot con il database di origine come nei ripristini non geografici, che vengono completati molto rapidamente.  Nel caso di un ripristino geografico di un database con iperscalabilità, sarà un'operazione di dimensioni dei dati, anche se la destinazione si trova nell'area abbinata dell'archiviazione con replica geografica.  Ciò significa che l'esecuzione di un ripristino geografico può richiedere tempo proporzionale alla dimensione del database da ripristinare.  Se la destinazione si trova nell'area abbinata, la copia sarà all'interno di un Data Center, che sarà notevolmente più veloce rispetto a una copia a lunga distanza su Internet, ma copierà comunque tutti i bit.

## <a name=regions></a>Aree disponibili

Il livello iperscalabile del database SQL di Azure è attualmente disponibile nelle aree geografiche seguenti:

- Australia orientale
- Australia sud-orientale
- Brasile meridionale
- Canada centrale
- Stati Uniti centrali
- Cina orientale 2
- Cina settentrionale 2
- Asia orientale
- East US
- Stati Uniti orientali 2
- Francia centrale
- Giappone orientale
- Giappone occidentale
- Corea del Sud centrale
- Corea del Sud meridionale
- Stati Uniti centro-settentrionali
- Europa settentrionale
- Sudafrica settentrionale
- Stati Uniti centro-meridionali
- Asia sud-orientale
- Regno Unito meridionale
- Regno Unito occidentale
- Europa occidentale
- Stati Uniti occidentali
- Stati Uniti occidentali 2

Se si vuole creare un database con iperscalabilità in un'area non elencata come supportata, è possibile inviare una richiesta di onboarding tramite portale di Azure. Si sta lavorando per espandere l'elenco delle aree supportate, quindi controllare l'elenco delle aree più recenti.

Per richiedere la possibilità di creare database con iperscalabilità in aree non elencate:

1. Passare al pannello [Guida e supporto di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

2. Fare clic su [ **nuova richiesta di supporto**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

    ![Pannello Guida e supporto tecnico di Azure](media/sql-database-service-tier-hyperscale/whitelist-request-screen-1.png)

3. Per **tipo di problema**selezionare **limiti per servizio e sottoscrizione (quote)**

4. Scegliere la sottoscrizione da usare per creare i database

5. Per **tipo di quota**selezionare **database SQL**

6. Fare clic su **Avanti: Soluzioni**

1. Fare clic su **specificare i dettagli**

    ![Dettagli del problema](media/sql-database-service-tier-hyperscale/whitelist-request-screen-2.png)

8. Scegliere il **tipo di quota del database SQL**: **Altra richiesta di quota**

9. Compilare il modello seguente:

    ![Dettagli quota](media/sql-database-service-tier-hyperscale/whitelist-request-screen-3.png)

    Nel modello specificare le informazioni seguenti

    > Richiedere la creazione di un database SQL con iperscalabilità di Azure in una nuova area<br/> Area: [compilare l'area richiesta]  <br/>
    > Risorse di calcolo/core totali, incluse le repliche leggibili <br/>
    > Numero di TB stimato 
    >

10. Scegliere **Severity C** (Gravità C).

11. Scegliere il metodo di contatto appropriato e specificare i dettagli.

12. Fare clic su **Salva** e **continua**

## <a name="known-limitations"></a>Limitazioni note
Queste sono le limitazioni correnti per il livello di servizio con iperscalabilità di GA.  Si sta lavorando attivamente per rimuovere il maggior numero possibile di limitazioni.

| Problema | DESCRIZIONE |
| :---- | :--------- |
| Il riquadro Gestisci backup per un server logico non mostra che i database iperscalari verranno filtrati da SQL Server  | L'iperscalabilità ha un metodo separato per la gestione dei backup e, di conseguenza, le impostazioni di conservazione a lungo termine e di conservazione dei backup temporizzate non vengono applicate/non sono valide. Di conseguenza i database Hyperscale non compaiono nel riquadro Gestisci backup. |
| Ripristino temporizzato | Quando viene eseguita la migrazione di un database nel livello di servizio iperscalabile, il ripristino fino a un punto nel tempo precedente alla migrazione non è supportato.|
| Ripristino del database non iperscalabile in Hypserscale e viceversa | Non è possibile ripristinare un database iperscalabile in un database non iperscalabile, né ripristinare un database senza iperscalabilità in un database con iperscalabilità.|
| Se le dimensioni di un database aumentano durante la migrazione a causa di un carico di lavoro inattivo, superando il limite di 1 TB per file, la migrazione non riesce. | Soluzioni: <br> - Se possibile, eseguire la migrazione del database in un momento in cui non è in esecuzione un carico di lavoro di aggiornamento.<br> - Riprovare a eseguire la migrazione, che riuscirà se durante il processo non viene superato il limite di 1 TB.|
| Istanza gestita | Istanza gestita di database SQL di Azure attualmente non è supportato con i database con iperscalabilità. |
| Pool elastici |  I pool elastici non sono attualmente supportati con l'iperscalabilità del database SQL.|
| La migrazione al livello di servizio Hyperscale è attualmente un'operazione unidirezionale | Dopo aver completato la migrazione di un database a Hyperscale, non è possibile eseguirne la migrazione direttamente a un livello di servizio diverso. Attualmente l'unico modo per eseguire la migrazione di un database da Hyperscale a un livello di servizio diverso consiste nell'esportarlo/importarlo usando un file BACPAC.|
| Migrazione di database con oggetti in memoria permanenti | L'iperscalabilità supporta solo oggetti in memoria non permanenti (tipi di tabella, SPs e funzioni native).  Prima di eseguire la migrazione di un database al livello di servizio iperscalare, è necessario eliminare e ricreare le tabelle in memoria permanenti e gli altri oggetti come oggetti non in memoria.|
| Rilevamento delle modifiche dei dati | Non sarà possibile usare il rilevamento dei dati delle modifiche con i database con iperscalabilità. |
| Replica geografica  | Non è ancora possibile configurare la replica geografica per l'iperscalabilità del database SQL di Azure.  È possibile eseguire ripristini geografici (ripristinando il database in un'area geografica diversa, per il ripristino di emergenza o per altri scopi) |
| Integrazione di Transparent Data Encryption/AKV | La crittografia Transparent database con Azure Key Vault (in genere denominata Bring-your-own-key o BYOK) non è ancora supportata per l'iperscalabilità del database SQL di Azure, ma Transparent Data Encryption con chiavi gestite dal servizio è completamente supportato. |
|Funzionalità di database intelligenti | 1. Create index, drop index Advisor Models non viene sottoposto a training per i database con iperscalabilità. <br/>2. Problema dello schema, DbParameterization: i consulenti aggiunti di recente non sono supportati per il database con iperscalabilità.|



## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su Hyperscale, vedere le [domande frequenti su Hyperscale](sql-database-service-tier-hyperscale-faq.md).
- Per informazioni sui livelli di servizio, vedere [Livelli di servizio](sql-database-service-tiers.md)
- Per informazioni sui limiti a livello di server e sottoscrizione, vedere [Panoramica dei limiti delle risorse in un server logico](sql-database-resource-limits-logical-server.md).
- Per informazioni sui limiti del modello di acquisto per un database singolo, vedere [Limiti del modello di acquisto basato su vCore per il database SQL di Azure per un database singolo](sql-database-vcore-resource-limits-single-databases.md).
- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](sql-database-features.md).
