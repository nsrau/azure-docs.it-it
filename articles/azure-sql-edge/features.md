---
title: Funzionalità supportate di Azure SQL Edge
description: Informazioni dettagliate sulle funzionalità supportate da Azure SQL Edge.
keywords: introduzione a SQL Edge, informazioni su SQL Edge, panoramica di SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 646ce94587a9aa1bb8fd20a28b84658994b25cf1
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90886539"
---
# <a name="supported-features-of-azure-sql-edge"></a>Funzionalità supportate di Azure SQL Edge 

Azure SQL Edge è basato sulla versione più recente di SQL motore di database. Supporta un subset delle funzionalità supportate in SQL Server 2019 in Linux, oltre ad alcune funzionalità attualmente non supportate o disponibili in SQL Server 2019 in Linux (o in SQL Server in Windows).

Per un elenco completo delle funzionalità supportate in SQL Server in Linux, vedere [Edizioni e funzionalità supportate di SQL Server 2019 in Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019). Per le edizioni e le funzionalità supportate di SQL Server in Windows, vedere [edizioni e funzionalità supportate di SQL Server 2019 (15. x)](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15).

## <a name="azure-sql-edge-editions"></a>Edizioni di Azure SQL Edge

SQL Edge di Azure è disponibile con due diverse edizioni o piani software. Queste edizioni hanno set di funzionalità identici e si differenziano solo in termini di diritti di utilizzo e della quantità di memoria e dei core a cui possono accedere sul sistema host.

   |**Pianificare**  |**Descrizione**  |
   |---------|---------|
   |SQL Edge di Azure Developer  |  Solo per lo sviluppo. Ogni contenitore di sviluppatori Azure SQL Edge è limitato a un massimo di 4 core e 32 GB di memoria.  |
   |SQL Edge di Azure    |  Per la produzione. Ogni contenitore Edge di Azure SQL è limitato a un massimo di 8 core e 64 GB di memoria.  |

## <a name="operating-system"></a>Sistema operativo

I contenitori Edge di Azure SQL sono basati su Ubuntu 18,04 e, di conseguenza, sono supportati solo per l'esecuzione in host Docker che eseguono Ubuntu 18,04 LTS (scelta consigliata) o Ubuntu 20,04 LTS. È possibile eseguire i contenitori di Azure SQL Edge in altri host del sistema operativo, ad esempio, può essere eseguito in altre distribuzioni di Linux o in Windows (usando Docker CE o Docker EE). Tuttavia, Microsoft non consiglia di eseguire questa operazione perché questa configurazione potrebbe non essere testata in modo esteso.

La configurazione consigliata per l'esecuzione di Azure SQL Edge in Windows consiste nel configurare una VM Ubuntu nell'host Windows, quindi eseguire Azure SQL Edge all'interno della VM Linux.

Il file system consigliato e supportato per Azure SQL Edge è EXT4 e XFS. Se vengono usati volumi permanenti per eseguire il backup dell'archiviazione del database Edge di Azure SQL, l'host sottostante file system deve essere EXT4 e XFS.

## <a name="hardware-support"></a>Supporto per l'hardware

Azure SQL Edge richiede un processore a 64 bit (x64 o ARM64), con un minimo di un processore e un GB di RAM nell'host. Sebbene il footprint di memoria di avvio di Azure SQL Edge si avvicini a 450MB, è necessaria memoria aggiuntiva per altri moduli IoT Edge o processi in esecuzione sul dispositivo perimetrale. I requisiti effettivi di memoria e CPU per Azure SQL Edge variano in base alla complessità del carico di lavoro e al volume dei dati elaborati. Quando si sceglie un hardware per la soluzione, Microsoft consiglia di eseguire test delle prestazioni completi per assicurarsi che le caratteristiche di prestazioni richieste per la soluzione siano soddisfatte.  

## <a name="azure-sql-edge-components"></a>Componenti di Azure SQL Edge

Azure SQL Edge supporta solo il motore di database. Non include il supporto per altri componenti disponibili con SQL Server 2019 in Windows o con SQL Server 2019 in Linux. In particolare, Azure SQL Edge non supporta componenti SQL Server come Analysis Services, Reporting Services, Integration Services, Master Data Services, Machine Learning Services (in-database) e Machine Learning Server (standalone).

## <a name="supported-features"></a>Caratteristiche supportate

Oltre a supportare un subset di funzionalità di SQL Server in Linux, Azure SQL Edge include il supporto per le nuove funzionalità seguenti: 

- Lo streaming SQL, basato sullo stesso motore che consente l'analisi di flusso di Azure, offre funzionalità di streaming dei dati in tempo reale in Azure SQL Edge. 
- Chiamata di funzione T-SQL `Date_Bucket` per l'analisi dei dati di serie temporali.
- Funzionalità di Machine Learning tramite il runtime ONNX, incluso con il motore SQL.

## <a name="unsupported-features"></a>Funzionalità non supportate

L'elenco seguente include le SQL Server 2019 in funzionalità Linux che non sono attualmente supportate in Azure SQL Edge.

| Area | Funzionalità o servizio non supportato |
|-----|-----|
| **Progettazione di database** | OLTP in memoria e comandi DDL correlati e funzioni Transact-SQL, viste del catalogo e viste a gestione dinamica. |
| &nbsp; | `HierarchyID` tipo di dati e comandi DDL correlati e funzioni Transact-SQL, viste del catalogo e viste a gestione dinamica. |
| &nbsp; | `Spatial` tipo di dati e comandi DDL correlati e funzioni Transact-SQL, viste del catalogo e viste a gestione dinamica. |
| &nbsp; | Stretch DB, nonché comandi DDL correlati e funzioni Transact-SQL, viste del catalogo e viste a gestione dinamica. |
| &nbsp; | Ricerca e indici full-text, nonché comandi DDL correlati, funzioni Transact-SQL, viste del catalogo e viste a gestione dinamica.|
| &nbsp; | `FileTable`, `FILESTREAM` e comandi DDL correlati, funzioni Transact-SQL, viste del catalogo e viste a gestione dinamica.|
| **Motore di database** | Replica. Si noti che è possibile configurare Azure SQL Edge come Sottoscrittore push di una topologia di replica. |
| &nbsp; | Polybase. Si noti che è possibile configurare Azure SQL Edge come destinazione per le tabelle esterne in polibase. |
| &nbsp; | Estendibilità del linguaggio tramite Java e Spark. |
| &nbsp; | Active Directory l'integrazione. |
| &nbsp; | Compattazione automatica di database. È possibile impostare la proprietà Auto Shrink per un database utilizzando il `ALTER DATABASE <database_name> SET AUTO_SHRINK ON` comando. Tuttavia, la modifica non ha alcun effetto. L'attività di compattazione automatica non viene eseguita sul database. Gli utenti possono comunque compattare i file di database usando i comandi "DBCC". |
| &nbsp; | Snapshot del database. |
| &nbsp; | Supporto per la memoria persistente. |
| &nbsp; | Microsoft Distributed Transaction Coordinator. |
| &nbsp; | Governance delle risorse di Resource Governor e IO. |
| &nbsp; | Estensione del pool di buffer. |
| &nbsp; | Query distribuita con connessioni di terze parti. |
| &nbsp; | Server collegati. |
| &nbsp; | Stored procedure estese di sistema, ad esempio `XP_CMDSHELL` . |
| &nbsp; | Assembly CLR, comandi DDL correlati e funzioni Transact-SQL, viste del catalogo e viste a gestione dinamica. |
| &nbsp; | Funzioni T-SQL dipendenti da CLR, ad esempio `ASSEMBLYPROPERTY` , `FORMAT` , `PARSE` e `TRY_PARSE` . |
| &nbsp; | Viste, funzioni e clausole di query del catalogo di data e ora dipendenti da CLR. |
| &nbsp; | Estensione del pool di buffer. |
| &nbsp; | Posta elettronica database. |
| &nbsp; | Broker di servizio |
| &nbsp; | Gestione basata su criteri |
| &nbsp; | data warehouse di gestione |
| &nbsp; | Database indipendenti |
| **SQL Server Agent** |  Sottosistemi: CmdExec, PowerShell, lettura coda, SSIS, SSAS e SSRS. |
| &nbsp; | Avvisi. |
| &nbsp; | Backup gestito. |
| **Disponibilità elevata** | Gruppi di disponibilità Always On.  |
| &nbsp; | Gruppi di disponibilità di base. |
| &nbsp; | Always On istanza del cluster di failover. |
| &nbsp; | Mirroring del database. |
| &nbsp; | Aggiunta di memoria e CPU a caldo. |
| **Sicurezza** | Extensible Key Management. |
| &nbsp; | Active Directory l'integrazione.|
| &nbsp; | Supporto per le enclavi sicure.|
| **Services** | Browser SQL Server. |
| &nbsp; | Machine Learning tramite R e Python. |
| &nbsp; | StreamInsight. |
| &nbsp; | Analysis Services. |
| &nbsp; | Reporting Services. |
| &nbsp; | Data Quality Services. |
| &nbsp; | Master Data Services. |
| &nbsp; | Riesecuzione distribuita. |
| **Gestione** | Punto di controllo Utilità SQL Server. |

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire SQL Edge di Azure](deploy-portal.md)
- [Configurare SQL Edge di Azure](configure.md)
- [Connettersi a un'istanza di SQL Edge di Azure usando gli strumenti client di SQL Server](connect.md)
- [Eseguire il backup e il ripristino di database in Azure SQL Edge](backup-restore.md)
