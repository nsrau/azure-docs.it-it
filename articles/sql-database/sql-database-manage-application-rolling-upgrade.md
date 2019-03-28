---
title: Aggiornamenti in sequenza delle applicazioni - Database SQL di Azure | Documentazione Microsoft
description: Informazioni su come usare la replica geografica del database SQL di Azure per supportare gli aggiornamenti online dell'applicazione cloud.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 02/13/2019
ms.openlocfilehash: 63f301b4618df9764460d0a9a133834fb72e33bb
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540585"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Gestire gli aggiornamenti in sequenza delle applicazioni cloud con la replica geografica attiva del database SQL

Questo articolo illustra come usare la [replica geografica attiva](sql-database-auto-failover-group.md) nel database SQL di Azure per abilitare gli aggiornamenti in sequenza di un'applicazione cloud. Gli aggiornamenti dovrebbero far parte delle attività di pianificazione e progettazione per la continuità aziendale perché sono operazioni che richiedono interruzioni. Nell'articolo vengono esaminati due metodi diversi per orchestrare il processo di aggiornamento e vengono discussi i vantaggi e gli svantaggi relativi a ciascuna opzione. Ai fini di questa spiegazione viene usata un'applicazione costituita da un sito Web connesso a un singolo database come livello dati. L'obiettivo è quello di aggiornare la versione 1 (V1) dell'applicazione alla versione 2 (V2) senza effetti significativi sull'esperienza degli utenti.

Quando si valutano le opzioni di aggiornamento, tenere presenti questi fattori:

* Impatto sulla disponibilità dell'applicazione durante gli aggiornamenti, ad esempio per quanto tempo gli aggiornamenti potrebbero limitare o peggiorare le funzioni dell'applicazione.
* Possibilità di eseguire il rollback se l'aggiornamento non riesce.
* Vulnerabilità dell'applicazione se durante l'aggiornamento si verifica un errore irreversibile non correlato all'operazione.
* Costo totale. Questo fattore include costi di ridondanza del database e costi incrementali dei componenti temporanei usati dal processo di aggiornamento.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Aggiornare le applicazioni basate sui backup del database per il ripristino di emergenza

Se l'applicazione si basa sui backup automatici del database e usa il ripristino geografico per il ripristino di emergenza, viene distribuita in una singola area di Azure. Per ridurre al minimo le interruzioni per gli utenti, creare un ambiente di gestione temporanea in tale area con tutti i componenti dell'applicazione interessati dall'aggiornamento. Il primo diagramma illustra l'ambiente operativo prima del processo di aggiornamento. L'endpoint `contoso.azurewebsites.net` rappresenta un ambiente di produzione dell'app Web. Per poter eseguire il rollback dell'aggiornamento, è necessario creare un ambiente di gestione temporanea con una copia completamente sincronizzata del database. Per creare un ambiente di gestione temporanea per l'aggiornamento seguire questi passaggi:

1. Creare un database secondario nella stessa area di Azure. Monitorare il database secondario per verificare se il processo di seeding è stato completato (1).
2. Creare un nuovo ambiente per l'app Web e chiamarlo "Staging". L'ambiente verrà registrato in DNS di Azure con l'URL `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Questi passaggi di preparazione non avranno alcun impatto sull'ambiente di produzione, che può funzionare in modalità di accesso completo.

![Configurazione della replica geografica del database SQL per il ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Al termine dei passaggi di preparazione, l'applicazione è pronta per l'aggiornamento effettivo. Il diagramma seguente illustra i passaggi richiesti per il processo di aggiornamento:

1. Impostare il server primario in modalità di sola lettura (3). In questo modo si ha la certezza che l'ambiente di produzione dell'app Web (V1) rimanga di sola lettura durante l'aggiornamento, evitando così la divergenza dei dati tra le istanze di database V1 e V2.
2. Disconnettere il database secondario usando la modalità di terminazione pianificata (4). Questa azione crea una copia indipendente completamente sincronizzata del database primario. Questo database verrà aggiornato.
3. Attivare la modalità di lettura/scrittura nel database secondario ed eseguire lo script di aggiornamento (5).

![Configurazione della replica geografica del database SQL per il ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Se l'aggiornamento ha esito positivo, è possibile far passare gli utenti alla copia aggiornata dell'applicazione, che diventa un ambiente di produzione. L'operazione di scambio richiede alcuni passaggi aggiuntivi, come illustrato nel diagramma seguente:

1. Attivare un'operazione di scambio tra l'ambiente di produzione e quello di gestione temporanea dell'app Web (6). Questa operazione consente di scambiare gli URL dei due ambienti. `contoso.azurewebsites.net` punta ora alla versione V2 del sito Web e del database (ambiente di produzione). 
2. Se la versione V1, che dopo lo scambio è diventata una copia di gestione temporanea, non è più necessaria, è possibile rimuovere le autorizzazioni dell'ambiente di gestione temporanea (7).

![Configurazione della replica geografica del database SQL per il ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Se il processo di aggiornamento non riesce, ad esempio a causa di un errore nello script di aggiornamento, tenere presente che l'ambiente di gestione temporanea sarà compromesso. Per eseguire il rollback dell'applicazione allo stato di pre-aggiornamento, ripristinare l'applicazione nell'ambiente di produzione per l'accesso completo. Il diagramma seguente illustra i passaggi per il ripristino:

1. Impostare la copia del database in modalità lettura/scrittura (8). Questa azione ripristina la funzionalità completa della versione V1 nella copia di produzione.
2. Analizzare la causa radice del problema e rimuovere le autorizzazioni dell'ambiente di gestione temporanea (9).

A questo punto, l'applicazione è perfettamente funzionante ed è possibile ripetere i passaggi per l'aggiornamento.

> [!NOTE]
> Il rollback non richiede modifiche al DNS perché non è stata ancora eseguita un'operazione di scambio.

![Configurazione della replica geografica del database SQL per il ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Il vantaggio principale di questa opzione è dato dalla possibilità di aggiornare un'applicazione in una singola area con una serie di semplici passaggi. Il costo dell'aggiornamento è relativamente basso. 

Lo svantaggio principale consiste invece nel fatto che, in caso di errore irreversibile durante l'aggiornamento, il ripristino allo stato di pre-aggiornamento richiede la ridistribuzione dell'applicazione in un'area diversa e il ripristino del database dal backup con il ripristino geografico. Questo processo comporta tempi di inattività significativi.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Aggiornare le applicazioni basate sulla replica geografica del database per il ripristino di emergenza

Se l'applicazione usa la replica geografica attiva o i gruppi di failover automatico per la continuità aziendale, viene distribuita in almeno due aree diverse. Sono presenti un database primario attivo in un'area primaria e un database secondario di sola lettura in un'area di backup. Oltre ai fattori indicati all'inizio di questo articolo, il processo di aggiornamento deve garantire anche quanto segue:

* L'applicazione deve rimanere costantemente protetta da errori irreversibili durante il processo di aggiornamento.
* I componenti con ridondanza geografica dell'applicazione devono essere aggiornati in parallelo con i componenti attivi.

Per raggiungere questi obiettivi, oltre a usare gli ambienti per app Web, è possibile sfruttare i vantaggi offerti da Gestione traffico di Azure usando un profilo di failover con un endpoint attivo e un endpoint di backup. Il diagramma seguente illustra l'ambiente operativo prima del processo di aggiornamento. I siti Web `contoso-1.azurewebsites.net` e `contoso-dr.azurewebsites.net` rappresentano un ambiente di produzione dell'applicazione con ridondanza geografica completa. L'ambiente di produzione include i componenti seguenti:

* L'ambiente di produzione dell'app Web `contoso-1.azurewebsites.net` nell'area primaria (1)
* Il database primario nell'area primaria (2)
* Un'istanza di standby dell'app Web nell'area di backup (3)
* Il database secondario con replica geografica nell'area di backup (4)
* Un profilo di prestazioni di Gestione traffico con un endpoint online `contoso-1.azurewebsites.net` e un endpoint offline `contoso-dr.azurewebsites.net`

Per poter eseguire il rollback dell'aggiornamento, è necessario creare un ambiente di gestione temporanea con una copia completamente sincronizzata dell'applicazione. Anche l'ambiente di gestione temporanea deve disporre della ridondanza geografica perché è necessario assicurare un recupero rapido dell'applicazione in caso di errori irreversibili durante il processo di aggiornamento. Creare un ambiente di gestione temporanea per l'aggiornamento seguendo i passaggi indicati qui sotto:

1. Distribuire un ambiente di gestione temporanea dell'app Web nell'area primaria (6).
2. Creare un database secondario nell'area primaria di Azure (7). Configurare l'ambiente di gestione temporanea dell'app Web per stabilire la connessione. 
3. Creare un altro database secondario con ridondanza geografica nell'area di backup eseguendo la replica del database secondario nell'area primaria. Questo metodo viene chiamato *replica geografica concatenata* (8).
4. Distribuire un ambiente di gestione temporanea dell'istanza dell'app Web nell'area di backup (9) e configurarlo per connettere il database secondario con ridondanza geografica creato nel passaggio (8).

> [!NOTE]
> Questi passaggi di preparazione non avranno alcun impatto sull'applicazione nell'ambiente di produzione, che continuerà a funzionare perfettamente in modalità di lettura/scrittura.

![Configurazione della replica geografica del database SQL per il ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Al termine dei passaggi di preparazione, l'ambiente di gestione temporanea è pronto per l'aggiornamento. Il diagramma seguente illustra questi passaggi di aggiornamento:

1. Impostare il database primario nell'ambiente di produzione in modalità di sola lettura (10). In questo modo si ha la certezza che il database di produzione (V1) non venga modificato durante l'aggiornamento, evitando così la divergenza dei dati tra le istanze di database V1 e V2.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Terminare la replica geografica scollegando il database secondario (11). Questa azione crea una copia indipendente ma completamente sincronizzata del database di produzione. Questo database verrà aggiornato. L'esempio seguente usa Transact-SQL, ma [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) è inoltre disponibile. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABSE V1
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Eseguire lo script di aggiornamento su `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net` e sul database primario di gestione temporanea (12). Le modifiche del database verranno replicate automaticamente sul database secondario di gestione temporanea.

![Configurazione della replica geografica del database SQL per il ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Se l'aggiornamento ha esito positivo, è possibile far passare gli utenti alla versione V2 dell'applicazione. Il diagramma seguente illustra i passaggi richiesti:

1. Attivare un'operazione di scambio tra l'ambiente di produzione e quello di gestione temporanea dell'app Web nell'area primaria (13) e nell'area di backup (14). A questo punto la versione V2 dell'applicazione diventa un ambiente di produzione con una copia ridondante nell'area di backup.
2. Se la versione V1 dell'applicazione (15 e 16) non è più necessaria, è possibile rimuovere le autorizzazioni dell'ambiente di gestione temporanea.

![Configurazione della replica geografica del database SQL per il ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Se il processo di aggiornamento non riesce, ad esempio a causa di un errore nello script di aggiornamento, tenere presente che lo stato dell'ambiente di gestione temporanea sarà incoerente. Per eseguire il rollback dell'applicazione allo stato di pre-aggiornamento, tornare a usare la versione V1 dell'applicazione nell'ambiente di produzione. Il diagramma seguente mostra i passaggi da seguire:

1. Impostare la copia del database primario nell'ambiente di produzione in modalità di lettura/scrittura (17). Questa azione ripristina la funzionalità completa della versione V1 nell'ambiente di produzione.
2. Analizzare la causa radice del problema e riparare o rimuovere l'ambiente di gestione temporanea (18 e 19).

A questo punto, l'applicazione è perfettamente funzionante ed è possibile ripetere i passaggi per l'aggiornamento.

> [!NOTE]
> Il rollback non richiede modifiche al DNS perché non è stata ancora eseguita un'operazione di scambio.

![Configurazione della replica geografica del database SQL per il ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

Il vantaggio principale di questa opzione è dato dalla possibilità di aggiornare in parallelo sia l'applicazione sia la copia con ridondanza geografica senza compromettere la continuità aziendale durante l'aggiornamento.

Lo svantaggio principale consiste invece nella necessità di una doppia ridondanza per ogni componente dell'applicazione, con un conseguente aumento dei costi e un flusso di lavoro più complesso.

## <a name="summary"></a>Summary

I due metodi di aggiornamento descritti nell'articolo differiscono in termini di complessità e di costi, ma sono entrambi finalizzati a ridurre il periodo di tempo in cui gli utenti possono eseguire solo operazioni di sola lettura. Questo periodo di tempo viene definito direttamente dalla durata dello script di aggiornamento. Non dipende dalle dimensioni del database, dal livello di servizio scelto, dalla configurazione del sito Web o da altri fattori difficilmente controllabili. Tutti i passaggi di preparazione sono separati dalle operazioni di aggiornamento e non hanno alcun impatto sull'applicazione di produzione. L'efficienza dello script di aggiornamento è un fattore determinante per l'esperienza degli utenti durante gli aggiornamenti. Il modo ottimale per migliorare questa esperienza consiste quindi nel concentrare gli sforzi sullo script di aggiornamento, per renderlo il più efficiente possibile.

## <a name="next-steps"></a>Passaggi successivi

* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
* Per altre informazioni sulla replica geografica attiva del database SQL di Azure, vedere [Creare database secondari leggibili usando la replica geografica attiva](sql-database-active-geo-replication.md).
* Per altre informazioni sui gruppi di failover del database SQL di Azure, vedere [Usare i gruppi di failover automatico per consentire il failover trasparente e coordinato di più database](sql-database-auto-failover-group.md).
* Per altre informazioni sugli ambienti di gestione temporanea nel Servizio app di Azure, vedere [Configurare gli ambienti di gestione temporanea nel Servizio app di Azure](../app-service/deploy-staging-slots.md).
* Per informazioni su profili di Gestione traffico di Azure, vedere [Gestire un profilo di Gestione traffico di Azure](../traffic-manager/traffic-manager-manage-profiles.md).
