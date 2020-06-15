---
title: Funzionalità supportate di SQL Edge di Azure (anteprima)
description: Informazioni dettagliate sulle funzionalità supportate da SQL Edge di Azure (anteprima)
keywords: introduzione a SQL Edge, informazioni su SQL Edge, panoramica di SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f0994ac1d28118869f0d5c2844a034623d101ee8
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684379"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Funzionalità supportate di SQL Edge di Azure (anteprima) 

Questo articolo fornisce informazioni dettagliate sulle funzionalità supportate da SQL Edge di Azure. SQL Edge di Azure è basato sulla versione più recente del motore di database di Microsoft SQL Server in Linux e supporta un subset delle funzionalità supportate in SQL Server 2019 per Linux, oltre ad alcune funzionalità attualmente non supportate o disponibili in SQL Server 2019 in Linux o in Windows. Per un elenco completo delle funzionalità supportate in SQL Server in Linux, vedere [Edizioni e funzionalità supportate di SQL Server 2019 in Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019). Per le edizioni e le funzionalità supportate di SQL Server in Windows, vedere [Edizioni e funzionalità supportate di SQL Server 2019 (15.x)](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15).

> [!NOTE]
> SQL Edge di Azure è attualmente in anteprima e pertanto NON deve essere usato in ambienti di produzione. Microsoft può consigliare l'esecuzione di SQL Edge di Azure in ambienti di produzione dopo la convalida della distribuzione e degli scenari dei casi d'uso.

## <a name="azure-sql-edge-editions"></a>Edizioni di SQL Edge di Azure

SQL Edge di Azure è disponibile con due diverse edizioni o piani software. Queste edizioni hanno set di funzionalità identici e si differenziano solo in termini di diritti di utilizzo e per la quantità di CPU/memoria accessibile nel sistema host.

   |**Pianificare**  |**Descrizione**  |
   |---------|---------|
   |SQL Edge di Azure Developer  |  SKU solo per lo sviluppo, ogni contenitore SQL Edge di Azure Developer è limitato a un massimo di 4 core e 32 GB di memoria  |
   |SQL Edge di Azure    |  SKU di produzione, ogni contenitore SQL Edge di Azure è limitato a 8 core e 64 GB di memoria  |

## <a name="operating-system"></a>Sistema operativo

I contenitori di SQL Edge di Azure sono attualmente basati su Ubuntu 16.04 e, di conseguenza, sono supportati solo per l'esecuzione in host Docker che eseguono Ubuntu 16.04 (versione consigliata) o 18.04. SQL Edge di Azure può essere eseguito anche in altri host del sistema operativo, ad esempio altre distribuzioni di Linux o in Windows (tramite Docker CE o Docker EE), tuttavia queste configurazioni non vengono testate in modo esteso da Microsoft.

SQL Edge di Azure è attualmente supportato solo per la distribuzione tramite Azure IoT Edge. Per altre informazioni sui sistemi supportati per Azure IoT Edge, vedere [Sistemi supportati da Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support).

La configurazione consigliata per l'esecuzione di SQL Edge di Azure in Windows prevede la configurazione di una macchina virtuale Ubuntu nell'host Windows e quindi l'esecuzione di SQL Edge nella macchina virtuale Linux.

## <a name="hardware-support"></a>Supporto hardware

SQL Edge di Azure richiede un processore a 64 bit (Intel, AMD o ARM) con almeno un processore e un GB di RAM nell'host. Sebbene il footprint della memoria di avvio di SQL Edge di Azure sia prossimo a 500 MB, l'ulteriore memoria è necessaria per gli altri moduli IoT Edge in esecuzione nel dispositivo perimetrale.

## <a name="azure-sql-edge-components"></a>Componenti di SQL Edge di Azure

SQL Edge di Azure supporta solo il motore di database e non include il supporto per altri componenti disponibili con SQL Server 2019 in Windows o con SQL Server 2019 in Linux. In particolare, SQL Edge di Azure non supporta componenti di SQL Server come Analysis Services, Reporting Services, Integration Services, Master Data Services, Machine Learning Services (In-Database) e Machine Learning Server (Standalone).

## <a name="supported-features"></a>Funzionalità supportate

Oltre a supportare un subset di funzionalità di SQL Server in Linux, SQL Edge di Azure include il supporto per le nuove funzionalità seguenti. 

- Streaming SQL, basato sullo stesso motore alla base di Analisi di flusso di Azure, offre funzionalità di streaming dei dati in tempo reale in SQL Edge di Azure. 
- Nuova chiamata di funzione T-SQL Date_Bucket per l'analisi dei dati di serie temporali.
- Funzionalità di Machine Learning tramite il runtime ONNX, incluso con il motore di SQL.

## <a name="sql-server-on-linux-features-not-supported-in-azure-sql-edge"></a>Funzionalità di SQL Server in Linux non supportate in SQL Edge di Azure

L'elenco seguente include le funzionalità di SQL Server 2019 in Linux attualmente non supportate in SQL Edge di Azure.

| Area | Funzionalità o servizio non supportato |
|-----|-----|
| **Progettazione di database** | OLTP in memoria con comandi DDL e funzioni, viste del catalogo e DMV Transact-SQL correlati |
| &nbsp; | Tipo di dati HierarchyID con comandi DDL e funzioni, viste del catalogo e DMV Transact-SQL correlati |
| &nbsp; | Tipo di dati spaziali con comandi DDL e funzioni, viste del catalogo e DMV Transact-SQL correlati |
| &nbsp; | Stretch Database con comandi DDL e funzioni, viste del catalogo e DMV Transact-SQL correlati |
| &nbsp; | Indici e ricerca full-text con comandi DDL e funzioni, viste del catalogo e DMV Transact-SQL correlati|
| &nbsp; | FileTable, FILESTREAM con comandi DDL e funzioni, viste del catalogo e DMV Transact-SQL correlati|
| **Motore di database** | Replica. SQL Edge di Azure può essere comunque configurato come sottoscrittore push di una topologia di replica. |
| &nbsp; | Polybase. SQL Edge di Azure può essere comunque configurato come destinazione per le tabelle esterne in Polybase |
| &nbsp; | Estendibilità del linguaggio tramite Java e Spark |
| &nbsp; | Integrazione di Active Directory |
| &nbsp; | Snapshot del database |
| &nbsp; | Supporto della memoria persistente |
| &nbsp; | Microsoft Distributed Transaction Coordinator |
| &nbsp; | Resource Governor e governance delle risorse di I/O |
| &nbsp; | Estensione pool di buffer |
| &nbsp; | Query distribuita con connessioni di terze parti |
| &nbsp; | Server collegati |
| &nbsp; | Stored procedure estese di sistema (XP_CMDSHELL e così via) |
| &nbsp; | Assembly CLR con comandi DDL e funzioni, viste del catalogo e DMV Transact-SQL correlati |
| &nbsp; | Funzioni T-SQL dipendenti da CLR come ASSEMBLYPROPERTY, FORMAT, PARSE, TRY_PARSE |
| &nbsp; | Viste del catalogo, funzioni e clausole di query di data e ora dipendenti da CLR |
| &nbsp; | Estensione pool di buffer |
| &nbsp; | Posta elettronica database |
| **SQL Server Agent** |  Sottosistemi: CmdExec, PowerShell, Agente di lettura coda, SSIS, SSAS, SSRS |
| &nbsp; | Avvisi |
| &nbsp; | Backup gestito |
| **Disponibilità elevata** | Gruppi di disponibilità AlwaysOn  |
| &nbsp; | Gruppi di disponibilità di base |
| &nbsp; | Istanza del cluster di failover AlwaysOn |
| &nbsp; | Mirroring del database |
| &nbsp; | Aggiunta di memoria a caldo e CPU |
| **Sicurezza** | Extensible Key Management |
| &nbsp; | Integrazione in Active Directory|
| &nbsp; | Supporto per le enclave sicure|
| **Services** | SQL Server Browser |
| &nbsp; | Machine Learning tramite R e Python |
| &nbsp; | StreamInsight |
| &nbsp; | Analysis Services |
| &nbsp; | Reporting Services |
| &nbsp; | Data Quality Services |
| &nbsp; | Master Data Services |
| &nbsp; | Distributed Replay |
| **Gestione** | Punto di controllo dell'Utilità SQL Server |

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire SQL Edge di Azure](deploy-portal.md)
- [Configurare SQL Edge di Azure](configure.md)
- [Connettersi a un'istanza di SQL Edge di Azure usando gli strumenti client di SQL Server](connect.md)
- [Eseguire il backup e il ripristino di database in SQL Edge di Azure](backup-restore.md)
