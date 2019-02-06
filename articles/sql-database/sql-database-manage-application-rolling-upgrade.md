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
ms.date: 01/29/2019
ms.openlocfilehash: 50f6f114a4d90f48218f751e1649e8694e664491
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55295747"
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>Gestione degli aggiornamenti in sequenza delle applicazioni cloud con la replica geografica attiva del database SQL

Vedere come usare la [replica geografica attiva](sql-database-auto-failover-group.md) nel database SQL per abilitare gli aggiornamenti in sequenza dell'applicazione cloud. L'aggiornamento dovrebbe far parte della pianificazione e della progettazione della continuità aziendale perché è un'operazione che comporta interruzioni. In questo articolo vengono esaminati due metodi diversi per orchestrare il processo di aggiornamento e vengono discussi vantaggi e compromessi relativi a ciascuna opzione. Ai fini di questo articolo viene usata un'applicazione costituita da un sito Web collegato a un database singolo come livello dati. L'obiettivo consiste nell'aggiornare la versione 1 dell'applicazione alla versione 2 senza effetti significativi sull'esperienza dell'utente finale.

Quando si valutano le opzioni di aggiornamento è necessario considerare i fattori seguenti:

* Impatto sulla disponibilità dell'applicazione durante gli aggiornamenti. Durata della limitazione o della riduzione delle prestazioni dell'applicazione.
* Possibilità di eseguire il rollback se l'aggiornamento non riesce.
* Vulnerabilità dell'applicazione se si verifica un errore irreversibile non correlato durante l'aggiornamento.
* Costo totale.  Sono inclusi i costi aggiuntivi di ridondanza e incrementali dei componenti temporanei usati dal processo di aggiornamento.

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Aggiornamento di applicazioni basate sui backup del database per il ripristino di emergenza

Se l'applicazione si basa sui backup automatici del database e usa il ripristino geografico per il ripristino di emergenza, viene distribuita in una singola area di Azure. Per ridurre al minimo l'interruzione dell'utente finale, nell'area verrà creato un ambiente di gestione temporanea dotato di tutti i componenti dell'applicazione coinvolti nell'aggiornamento. Il diagramma seguente illustra l'ambiente operativo prima del processo di aggiornamento. L'endpoint `contoso.azurewebsites.net` rappresenta uno slot di produzione dell'applicazione Web. Per poter eseguire il rollback dell'aggiornamento, è necessario creare uno slot di staging con una copia completamente sincronizzata del database. Creare un ambiente di gestione temporanea per l'aggiornamento seguendo i passaggi indicati qui sotto:

1. Creare un database secondario nella stessa area di Azure. Monitorare il database secondario per verificare se il processo di seeding è stato completato (1).
2. Creare un nuovo slot di distribuzione per l'applicazione Web chiamato "Staging". Lo slot sarà registrato in DNS con l'URL `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Le operazioni di preparazione non avranno effetti sullo slot di produzione, che funzionerà in modalità di accesso completo.
>  

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Dopo aver completato i passaggi di preparazione, l'applicazione è pronta per l'aggiornamento effettivo. Il diagramma seguente illustra i passaggi richiesti per il processo di aggiornamento.

1. Impostare il server primario in modalità di sola lettura (3). In questo modo si assicura che lo slot di produzione dell'applicazione Web (V1) resti di sola lettura durante l'aggiornamento, impedendo così la divergenza dei dati tra le istanze di database V1 e V2.  
2. Scollegare il database secondario usando la modalità di terminazione pianificata (4). Viene creata una copia indipendente completamente sincronizzata del database primario. Questo database verrà aggiornato.
3. Attivare il database primario in modalità lettura/scrittura ed eseguire lo script di aggiornamento (5).

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Se l'aggiornamento è stato completato correttamente, è possibile far passare gli utenti finali alla copia aggiornata dell'applicazione. Tale applicazione diventerà uno slot di produzione.  La commutazione richiede altri passaggi come illustrato nel diagramma seguente.

1. Attivare un'operazione di scambio tra gli slot di produzione e di staging dell'applicazione Web (6). In questo modo gli URL dei due slot saranno commutati. A questo punto `contoso.azurewebsites.net` punterà alla versione V2 del sito Web e del database (ambiente di produzione).  
2. Se la versione V1, che è diventata una copia di staging dopo lo scambio, non è più necessaria, è possibile rimuovere le autorizzazioni dell'ambiente di gestione temporanea (7).

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Se il processo di aggiornamento non riesce, ad esempio a causa di un errore nello script di aggiornamento, lo slot di gestione temporanea deve essere considerato compromesso. Per eseguire il rollback dell'applicazione allo stato di pre-aggiornamento, è necessario ripristinare l'applicazione nello slot di produzione per l'accesso completo. I passaggi richiesti sono mostrati nel diagramma seguente.

1. Impostare la copia del database in modalità lettura/scrittura (8). La funzionalità completa di V1 viene ripristinata nella copia di produzione.
2. Eseguire l'analisi della causa radice e rimuovere le autorizzazioni dell'ambiente di gestione temporanea (9).

A questo punto l'applicazione è completamente funzionale e le operazioni di aggiornamento possono essere ripetute.

> [!NOTE]
> Il rollback non richiede modifiche al DNS perché non è ancora stata eseguita un'operazione di scambio.

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Il **vantaggio** principale di questa opzione è che è possibile aggiornare un'applicazione in un'unica area con una serie di semplici passaggi. Il costo dell'aggiornamento è relativamente basso. Il **compromesso** principale è che, se si verifica un errore irreversibile durante l'aggiornamento, il ripristino allo stato di pre-aggiornamento comporta la ridistribuzione dell'applicazione in un'area diversa e il ripristino del database dal backup con il ripristino geografico. Questo processo causa tempi di inattività significativi.

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Aggiornamento di applicazioni basate sulla replica geografica del database per il ripristino di emergenza

Se l'applicazione usa la replica geografica attiva o i gruppi di failover per la continuità aziendale, viene distribuita in almeno due aree diverse con un database primario attivo nell'area primaria e un database secondario di sola lettura nell'area di backup. Oltre ai fattori menzionati in precedenza, il processo di aggiornamento deve garantire che:

* L'applicazione rimanga protetta da errori irreversibili in qualsiasi momento durante il processo di aggiornamento
* I componenti con ridondanza geografica dell'applicazione siano aggiornati in parallelo con i componenti attivi

Per raggiungere questi obiettivi, oltre a usare gli slot di distribuzione dell'applicazione Web, è possibile usare Gestione traffico di Azure (ATM) con un profilo di failover con un endpoint attivo e un endpoint di backup.  Il diagramma seguente illustra l'ambiente operativo prima del processo di aggiornamento. I siti Web `contoso-1.azurewebsites.net` e `contoso-dr.azurewebsites.net` rappresentano un ambiente di produzione dell'applicazione con ridondanza geografica completa. L'ambiente di produzione include i componenti seguenti:

1. Slot di produzione dell'applicazione Web `contoso-1.azurewebsites.net` nell'area primaria (1)
2. Database primario nell'area primaria (2) 
3. Un'istanza di standby dell'applicazione Web nell'area di backup (3)
4. Database secondario con replica geografica nell'area di backup (4)
5. Profilo di prestazioni di gestione traffico di Azure con endpoint online `contoso-1.azurewebsites.net` ed endpoint offline `contoso-dr.azurewebsites.net`

Per poter eseguire il rollback dell'aggiornamento, è necessario creare un ambiente di gestione temporanea con una copia completamente sincronizzata dell'applicazione. Anche l'ambiente di gestione temporanea deve avere la funzionalità di ridondanza geografica perché è necessario assicurare un recupero rapido dell'applicazione in caso di errori irreversibili durante il processo di aggiornamento. Creare un ambiente di gestione temporanea per l'aggiornamento seguendo i passaggi indicati qui sotto:

1. Distribuire uno slot di produzione dell'applicazione Web nell'area primaria (6)
2. Creare un database secondario nell'area primaria di Azure (7). Configurare lo slot di staging dell'applicazione Web per eseguire la connessione. 
3. Creare un altro database secondario con ridondanza geografica nell'area di backup usando la replica geografica del database secondario nell'area primaria, chiamata "replica geografica concatenata" (8).
3. Distribuire uno slot di staging dell'istanza dell'applicazione Web nell'area di backup (9) e configurarlo per connettere la replica geografica secondaria creata nel passaggio (9).


> [!NOTE]
> Le operazioni di preparazione non avranno effetti sull'applicazione nello slot di produzione, che resterà completamente funzionante in modalità di lettura/scrittura.

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Dopo aver completato i passaggi di preparazione, l'ambiente di gestione temporanea è pronto per l'aggiornamento. Il diagramma seguente illustra questi passaggi di aggiornamento.

1. Impostare il database primario nello slot di produzione in modalità di sola lettura (10). In questo modo si assicura che il database di produzione (V1) non cambi durante l'aggiornamento, impedendo così la divergenza dei dati tra le istanze di database V1 e V2.  
2. Scollegare il database secondario nella stessa area usando la modalità di terminazione pianificata (11). Viene creata una copia indipendente ma completamente sincronizzata del database di produzione. Questo database verrà aggiornato.
3. Eseguire lo script di aggiornamento su `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net` e sul database primario di gestione temporanea (12). Le modifiche del database verranno replicate automaticamente sul database secondario di gestione temporanea 

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Se l'aggiornamento è stato completato correttamente, è possibile far passare gli utenti finali alla versione V2 dell'applicazione. Il diagramma seguente illustra i passaggi richiesti.

1. Attivare un'operazione di scambio tra gli slot di produzione e di staging dell'applicazione Web nell'area primaria (13) e nell'area di backup (14). A questo punto V2 dell'applicazione diventa uno slot di produzione con una copia ridondante nell'area di backup.
2. È possibile rimuovere le autorizzazioni dell'ambiente di gestione temporanea se non è più necessaria l'applicazione V1 (15 e 16).  

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Se il processo di aggiornamento non riesce, ad esempio a causa di un errore nello script di aggiornamento, l'ambiente di gestione temporanea deve essere considerato incoerente. Per eseguire il rollback dell'applicazione allo stato di pre-aggiornamento, è necessario usare V1 dell'applicazione nell'ambiente di produzione. I passaggi richiesti sono mostrati nel diagramma seguente.

1. Impostare la copia del database primario nello slot di produzione in modalità lettura/scrittura (17). La funzionalità completa di V1 viene ripristinata nello slot di produzione.
2. Eseguire l'analisi della causa radice e riparare o rimuovere l'ambiente di gestione temporanea (18 e 19).

A questo punto l'applicazione è completamente funzionale e le operazioni di aggiornamento possono essere ripetute.

> [!NOTE]
> Il rollback non richiede modifiche al DNS perché non è ancora stata eseguita un'operazione di scambio.

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

Il **vantaggio** principale di questa opzione è che è possibile aggiornare in parallelo sia l'applicazione che la copia con ridondanza geografica senza compromettere la continuità aziendale durante l'aggiornamento. Il **compromesso** principale è che viene richiesta una doppia ridondanza per ogni componente dell'applicazione, che comporta un aumento dei costi e un flusso di lavoro più complesso.

## <a name="summary"></a>Summary

I due metodi di aggiornamento descritti nell'articolo differiscono in termini di complessità e di costo, ma entrambi si concentrano sulla riduzione dei tempi quando l'utente finale è limitato alle operazioni di sola lettura. Questo periodo di tempo viene definito direttamente dalla durata dello script di aggiornamento. Non dipende dalle dimensioni del database, dal livello di servizio scelto, dalla configurazione del sito Web e da altri fattori difficilmente controllabili. Tutti i passaggi di preparazione sono separati dalle operazioni di aggiornamento e non hanno alcun impatto sull'applicazione di produzione. L'efficienza dello script di aggiornamento è un fattore determinante per l'esperienza dell'utente finale durante gli aggiornamenti. Il modo ottimale per migliorarla, quindi, consiste nel concentrare gli sforzi sullo script di aggiornamento, per renderlo il più efficiente possibile.  

## <a name="next-steps"></a>Passaggi successivi

* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
* Per altre informazioni sulla replica geografica attiva del database SQL di Azure, vedere [Creare i database secondari leggibili usando la replica geografica attiva](sql-database-active-geo-replication.md).
* Per altre informazioni sui gruppi di failover del database SQL d Azure, vedere [Usare i gruppi di failover automatico per consentire il failover trasparente e coordinato di più database](sql-database-auto-failover-group.md).
* Per altre informazioni sugli slot di distribuzione e sull'ambiente di gestione temporanea nel Servizio app di Azure, vedere [Configurare gli ambienti di gestione temporanea nel Servizio app di Azure](../app-service/deploy-staging-slots.md).  
* Per informazioni su profili di gestione traffico di Azure, vedere [Gestire un profilo di Gestione traffico di Azure](../traffic-manager/traffic-manager-manage-profiles.md).  


