---        
title: Esplorare le esercitazioni sul database SQL di Azure | Documentazione Microsoft
description: "Informazioni sulle caratteristiche e sulle funzionalità del database SQL"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 04c0fd7f-d260-4e43-a4f0-41cdcd5e3786
ms.service: sql-database
ms.custom: overview
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 02/08/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 9b09383350284c8f1cd3e384c802c25c962b1226
ms.openlocfilehash: 1fe15e7ad3667d42995cd487c793fae496216fca
ms.lasthandoff: 02/16/2017

        
---
 
# <a name="explore-azure-sql-database-tutorials"></a>Esplorare le esercitazioni sul database SQL di Azure
I collegamenti nelle tabelle seguenti indirizzano a una panoramica delle aree di funzionalità elencate e a una semplice esercitazione introduttiva per ogni area. 

## <a name="create-servers-databases-and-server-level-firewall-rules"></a>Creare server, database e regole firewall a livello di server
Nelle esercitazioni seguenti verranno creati server, database e regole firewall a livello di server e verranno fornite informazioni su come connettersi ed eseguire query su server e database.

| Esercitazione | Descrizione |
| --- | --- | 
| [Primo database SQL di Azure](sql-database-get-started.md) | Al termine di questa esercitazione introduttiva, saranno disponibili un database di esempio e un database vuoto in esecuzione in un gruppo di risorse di Azure e collegato a un server logico. Vengono anche create due regole firewall a livello di server configurate per abilitare l'entità di sicurezza a livello di server per l'accesso al server da due indirizzi IP specificati. Infine, l'esercitazione illustra come eseguire query in un database nel portale di Azure e come connettersi ed eseguire query tramite SQL Server Management Studio. |
| [Effettuare il provisioning e accedere a un database SQL di Azure usando PowerShell](sql-database-get-started-powershell.md) | Al termine di questa esercitazione, si disporrà di un database di esempio e di un database vuoto in esecuzione in un gruppo di risorse di Azure e collegato a un server logico. Sarà inoltre disponibile una regola del firewall a livello di server configurata per abilitare l'entità a livello di server per accedere al server da un indirizzo IP specificato (o da un intervallo di indirizzi IP). |
| [Usare C# per creare un database SQL con la libreria del database SQL per .NET](sql-database-get-started-csharp.md)| In questa esercitazione viene usato C# per creare un server del database SQL, una regola firewall e un database SQL. È inoltre possibile creare un'applicazione di Active Directory (AD) e l'entità servizio necessaria per autenticare l'app C#. |
|  | |

## <a name="backups-long-term-retention-and-database-recovery"></a>Backup, conservazione a lungo termine e ripristino del database
Nelle esercitazioni seguenti, viene illustrato come usare i [backup di database](sql-database-automated-backups.md), la [conservazione dei backup a lungo termine](sql-database-long-term-retention.md) e il [ripristino di database tramite backup](sql-database-recovery-using-backups.md).

| Esercitazione | Descrizione |
| --- | --- | 
| [Eseguire il backup e ripristinare un database SQL di Azure con il portale di Azure](sql-database-get-started-backup-recovery-portal.md) | In questa esercitazione viene illustrato come usare il portale di Azure per visualizzare i backup, ripristinare un database a un punto nel tempo, configurare una conservazione a lungo termine dei backup e ripristinare un database da un backup nell'insieme di credenziali di Servizi di ripristino di Azure
| [Eseguire il backup e ripristino con PowerShell](sql-database-get-started-backup-recovery-powershell.md) | In questa esercitazione viene illustrato come usare PowerShell per visualizzare i backup, ripristinare un database a un punto nel tempo, configurare una conservazione a lungo termine dei backup e ripristinare un database da un backup nell'insieme di credenziali di Servizi di ripristino di Azure
|  | |

## <a name="sharded-databases"></a>Database partizionati
Nelle esercitazioni seguenti verrà illustrato come [aumentare il numero di istanze dei database con il gestore delle mappe partizioni](sql-database-elastic-scale-shard-map-management.md).

| Esercitazione | Descrizione |
| --- | --- | 
| [Creare un'applicazione partizionata](sql-database-elastic-scale-get-started.md) |Questa esercitazione mostra come usare le funzionalità degli strumenti dei database elastici tramite una semplice applicazione partizionata. |
| [Distribuire un servizio di divisione e unione](sql-database-elastic-scale-configure-deploy-split-and-merge.md) |Questa esercitazione mostra come spostare i dati di un database tra database partizionati. |
|  | |

## <a name="elastic-database-jobs"></a>Processi di database elastici
Le esercitazioni seguenti mostrano come usare i [processi di database elastico](sql-database-elastic-jobs-overview.md).

| Esercitazione | Descrizione |
| --- | --- | 
| [Introduzione ai processi elastici del database SQL di Azure](sql-database-elastic-jobs-getting-started.md) |Questa esercitazione illustra come creare e gestire processi che gestiscono un gruppo di database correlati. |
|  | |

## <a name="elastic-queries"></a>Query elastiche
Le esercitazioni seguenti mostrano come eseguire le [query elastiche](sql-database-elastic-query-overview.md).

| Esercitazione | Descrizione |
| --- | --- | 
| [Creare query elastiche](sql-database-elastic-query-getting-started-vertical.md) | Questa esercitazione mostra come eseguire query T-SQL valide per più database usando un singolo punto di connessione |
| [Creare report in database cloud con scalabilità orizzontale](sql-database-elastic-query-getting-started.md) |Questa esercitazione mostra come creare report da tutti i database in un database con partizionamento orizzontale |
| [Eseguire query in database cloud con schemi diversi](sql-database-elastic-query-vertical-partitioning.md) | Questa esercitazione mostra come eseguire query T-SQL valide per più database con schemi diversi |
| [Creazione di report tra database cloud con scalabilità orizzontale](sql-database-elastic-query-horizontal-partitioning.md) |Questa esercitazione mostra come creare report che si estendono a tutti i database di un database partizionato. |
|  | |

## <a name="database-authentication-and-authorization"></a>Autenticazione e autorizzazione del database
Le esercitazioni seguenti mostrano come [creare e gestire account di accesso e utenti](sql-database-manage-logins.md).

| Esercitazione | Descrizione |
| --- | --- | --- |
| [Autenticazione e autorizzazione di SQL](sql-database-control-access-sql-authentication-get-started.md) | In questa esercitazione vengono fornite informazioni sulla creazione di accessi e di utenti utilizzando l'autenticazione SQL Server, aggiungerli ai ruoli, nonché concedere loro le autorizzazioni |
| [Autenticazione e autorizzazione di Azure AD](sql-database-control-access-aad-authentication-get-started.md) | In questa esercitazione vengono fornite informazioni sulla creazione di accessi e di utenti utilizzando l'autenticazione Azure Active Directory |
|  | |

## <a name="secure-and-protect-data"></a>Garantire sicurezza e protezione dei dati
Le esercitazioni seguenti mostrano come [proteggere i dati del database SQL di Azure](sql-database-security-overview.md).

| Esercitazione | Descrizione |
| --- | --- | 
| [Proteggere i dati sensibili tramite la procedura guidata Crittografia sempre attiva ](sql-database-always-encrypted-azure-key-vault.md) |Questa esercitazione mostra come usare la procedura guidata Crittografia sempre attiva per proteggere i dati sensibili in un database SQL di Azure. |
|  | |

## <a name="develop"></a>Sviluppare
Le esercitazioni seguenti illustrano vari aspetti sullo sviluppo di applicazioni e database.

| Esercitazione | Descrizione |
| --- | --- | 
| [Creare un report con Excel](sql-database-connect-excel.md) |Questa esercitazione mostra come connettere Excel a un database SQL nel cloud per importare dati e creare tabelle e grafici in base ai valori nel database. |
| [Creare un'app con SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) |Questa esercitazione mostra come creare un'applicazione usando SQL Server |
| [Tabelle temporali](sql-database-temporal-tables.md) | Questa esercitazione illustra i concetti relativi alle tabelle temporali.
| [Usare Entity Framework con gli strumenti elastici](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) |Questa esercitazione illustra le modifiche necessarie in un'applicazione Entity Framework per l'integrazione con le funzionalità degli strumenti del database elastico. |
| [Adottare OLTP in memoria](sql-database-in-memory-oltp-migration.md) | Questa esercitazione mostra come usare [OLTP in memoria](sql-database-in-memory.md) per migliorare le prestazioni di elaborazione delle transazioni. |
| [Code First to a New Database](https://msdn.microsoft.com/data/jj193542.aspx) (Code First per un nuovo database) | In questa esercitazione vengono illustrati i concetti relativi alla distribuzione Code First.
| [Tailspin Surveys sample application](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) (Applicazione di esempio Tailspin Surveys) | In questa esercitazione viene usata l'applicazione di esempio Tailspon Surveys. |
| [Contoso Clinic demo application](https://github.com/Microsoft/azure-sql-security-sample) (Applicazione demo Contoso Clinic) | In questa esercitazione viene usata l'applicazione demo Contoso Clinic. |
|  | |

## <a name="data-sync"></a>Sincronizzazione dei dati
Questa esercitazione illustra i concetti relativi alla [sincronizzazione dei dati](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Esercitazione | Descrizione |
| --- | --- | 
| [Introduzione all'anteprima di sincronizzazione dati di SQL Azure](sql-database-get-started-sql-data-sync.md) |In questa esercitazione vengono fornite le nozioni di base della sincronizzazione dati SQL Azure tramite il portale di Azure classico. |
|  | |

## <a name="monitor-and-tune"></a>Monitorare e ottimizzare
Le esercitazioni seguenti illustrano il monitoraggio e l'ottimizzazione.
| Esercitazione | Descrizione |
| --- | --- | 
| [Elastic Pool telemetry using PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) (Telemetria del pool elastico con PowerShell)| In questa esercitazione viene illustrato come raccogliere i dati di telemetria del pool elastico usando PowerShell. |
| [Elastic Pool custom dashboard for SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) (Dashboard personalizzato dei pool elastici per SaaS) | In questa esercitazione viene illustrato come creare un dashboard personalizzato per il monitoraggio dei pool elastici. |
| [Acquisire il codice di destinazione del file evento per eventi estesi](sql-database-xevent-code-event-file.md)| In questa esercitazione viene illustrato come acquisire eventi estesi in un file evento di destinazione.|
| [Acquisire il codice di destinazione del buffer circolare per gli eventi estesi](sql-database-xevent-code-ring-buffer.md)| In questa esercitazione viene illustrato come acquisire eventi estesi nel codice del buffer circolare.|
|  | |



