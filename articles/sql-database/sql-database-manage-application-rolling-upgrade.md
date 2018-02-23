---
title: Aggiornamenti in sequenza delle applicazioni - Database SQL di Azure | Documentazione Microsoft
description: Informazioni su come usare la replica geografica del database SQL di Azure per supportare gli aggiornamenti online dell'applicazione cloud.
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 58f42859-1e37-463c-a3d8-a3ca2e867148
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 07/16/2016
ms.author: sashan
ms.openlocfilehash: deb91d55e5b796f7b1b53a99866156fe492e0a24
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>Gestione degli aggiornamenti in sequenza delle applicazioni cloud con la replica geografica attiva del database SQL
> [!NOTE]
> La [replica geografica attiva](sql-database-geo-replication-overview.md) è ora disponibile per tutti i database in tutti i livelli.
> 

Informazioni su come usare la [replica geografica](sql-database-geo-replication-overview.md) nel database SQL per abilitare gli aggiornamenti in sequenza dell'applicazione cloud. L'aggiornamento dovrebbe far parte della pianificazione e della progettazione della continuità aziendale perché è un'operazione che comporta interruzioni. In questo articolo vengono esaminati due metodi diversi per orchestrare il processo di aggiornamento e vengono discussi vantaggi e compromessi relativi a ciascuna opzione. Ai fini di questo articolo viene usata una semplice applicazione costituita da un sito Web collegato a un database singolo come livello dati. L'obiettivo consiste nell'aggiornare la versione 1 dell'applicazione alla versione 2 senza effetti significativi sull'esperienza dell'utente finale. 

Quando si valutano le opzioni di aggiornamento è necessario considerare i fattori seguenti:

* Impatto sulla disponibilità dell'applicazione durante gli aggiornamenti. Durata della limitazione o della riduzione delle prestazioni dell'applicazione.
* Possibilità di eseguire il rollback in caso di errori durante l'aggiornamento.
* Vulnerabilità dell'applicazione se si verifica un errore irreversibile non correlato durante l'aggiornamento.
* Costo totale.  Sono inclusi i costi aggiuntivi di ridondanza e incrementali dei componenti temporanei usati dal processo di aggiornamento. 

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Aggiornamento di applicazioni basate sui backup del database per il ripristino di emergenza
Se l'applicazione si basa sui backup automatici del database e usa il ripristino geografico per il ripristino di emergenza, in genere viene distribuita in una singola area di Azure. In questo caso il processo di aggiornamento prevede la creazione di una distribuzione di backup di tutti i componenti dell'applicazione coinvolti nell'aggiornamento. Per ridurre al minimo le interruzioni per l'utente finale, è possibile usare Gestione traffico di Azure (WATM) con il profilo di failover.  Il diagramma seguente illustra l'ambiente operativo prima del processo di aggiornamento. L'endpoint <i>contoso-1.azurewebsites.net</i> rappresenta uno slot di produzione dell'applicazione che deve essere aggiornata. Per poter eseguire il rollback dell'aggiornamento, è necessario creare uno slot di gestione temporanea con una copia completamente sincronizzata dell'applicazione. I passaggi seguenti sono necessari per preparare l'applicazione per l'aggiornamento:

1. Creare uno slot di gestione temporanea per l'aggiornamento. Per farlo, creare un database secondario (1) e distribuire un sito Web identico nella stessa area di Azure. Monitorare il database secondario per verificare se il processo di seeding è stato completato.
2. Creare un profilo di failover in Gestione traffico di Azure con <i>contoso-1.azurewebsites.net</i> come endpoint online e <i>contoso-2.azurewebsites.net</i> come endpoint offline. 

> [!NOTE]
> Le operazioni di preparazione non avranno effetti sull'applicazione nello slot di produzione, che funzionerà in modalità di accesso completo.
>  

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/Option1-1.png)

Dopo aver completato i passaggi di preparazione, l'applicazione è pronta per l'aggiornamento effettivo. Il diagramma seguente illustra i passaggi richiesti per il processo di aggiornamento. 

1. Impostare il database primario nello slot di produzione in modalità di sola lettura (3). In questo modo si assicura che l'istanza di produzione dell'applicazione (V1) resti di sola lettura durante l'aggiornamento, impedendo così la divergenza dei dati tra le istanze di database V1 e V2.  
2. Scollegare il database secondario usando la modalità di terminazione pianificata (4). Viene creata una copia indipendente completamente sincronizzata del database primario. Questo database verrà aggiornato.
3. Attivare il database primario in modalità lettura/scrittura ed eseguire lo script di aggiornamento nello slot di gestione temporanea (5).     

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/Option1-2.png)

Se l'aggiornamento è stato completato correttamente, è possibile far passare gli utenti finali alla copia di gestione temporanea dell'applicazione, che diventa lo slot di produzione dell'applicazione.  È richiesto qualche altro passaggio, come illustrato nel diagramma seguente.

1. Passare l'endpoint online nel profilo di Gestione traffico di Azure su <i>contoso-2.azurewebsites.net</i>, che punta alla versione V2 del sito Web (6). Questo diventa lo slot di produzione con l'applicazione V2 e il traffico dell'utente finale viene indirizzato a questo slot.  
2. Se i componenti dell'applicazione V1 non sono più necessari, è possibile rimuoverli (7).   

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/Option1-3.png)

Se il processo di aggiornamento non riesce, ad esempio a causa di un errore nello script di aggiornamento, lo slot di gestione temporanea deve essere considerato compromesso. Per eseguire il rollback dell'applicazione allo stato di pre-aggiornamento, è sufficiente ripristinare l'applicazione nello slot di produzione per l'accesso completo. I passaggi richiesti sono mostrati nel diagramma seguente.    

1. Impostare la copia del database in modalità lettura/scrittura (8). La funzionalità completa di V1 viene ripristinata nello slot di produzione.
2. Eseguire l'analisi delle cause radice e rimuovere i componenti compromessi nello slot di gestione temporanea (9). 

A questo punto l'applicazione è completamente funzionale e le operazioni di aggiornamento possono essere ripetute.

> [!NOTE]
> Il rollback non richiede modifiche al profilo di Gestione traffico di Azure perché punta già a <i>contoso-1.azurewebsites.net</i> come endpoint attivo.
> 
> 

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/Option1-4.png)

Il **vantaggio** principale di questa opzione è che è possibile aggiornare un'applicazione in un'unica area con una serie di semplici passaggi. Il costo dell'aggiornamento è relativamente basso. Il **compromesso** principale è che, se si verifica un errore irreversibile durante l'aggiornamento, il ripristino allo stato di pre-aggiornamento comporta la ridistribuzione dell'applicazione in un'area diversa e il ripristino del database dal backup con il ripristino geografico. Questo processo causa tempi di inattività significativi.   

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Aggiornamento di applicazioni basate sulla replica geografica del database per il ripristino di emergenza
Se l'applicazione usa la replica geografica per la continuità aziendale, viene distribuita in almeno due aree diverse con una distribuzione attiva nell'area primaria e una distribuzione standby nell'area di backup. Oltre ai fattori menzionati in precedenza, il processo di aggiornamento deve garantire che:

* L'applicazione rimanga protetta da errori irreversibili in qualsiasi momento durante il processo di aggiornamento
* I componenti con ridondanza geografica dell'applicazione siano aggiornati in parallelo con i componenti attivi

Per raggiungere questi obiettivi è possibile usare Gestione traffico di Azure (WATM) con il profilo di failover con un endpoint attivo e tre endpoint di backup.  Il diagramma seguente illustra l'ambiente operativo prima del processo di aggiornamento. I siti Web <i>contoso-1.azurewebsites.net</i> e <i>contoso-dr.azurewebsites.net</i> rappresentano uno slot di produzione dell'applicazione con ridondanza geografica completa. Per poter eseguire il rollback dell'aggiornamento, è necessario creare uno slot di gestione temporanea con una copia completamente sincronizzata dell'applicazione. Anche lo slot di gestione temporanea deve avere la funzionalità di ridondanza geografica perché è necessario assicurare un recupero rapido dell'applicazione in caso di errori irreversibili durante il processo di aggiornamento. I passaggi seguenti sono necessari per preparare l'applicazione per l'aggiornamento:

1. Creare uno slot di gestione temporanea per l'aggiornamento. Per farlo, creare un database secondario (1) e distribuire una copia identica di un sito Web nella stessa area di Azure. Monitorare il database secondario per verificare se il processo di seeding è stato completato.
2. Creare un database secondario con ridondanza geografica nello slot di gestione temporanea usando la replica geografica del database secondario nell'area di backup, chiamata "replica geografica concatenata". Monitorare il database secondario di backup per verificare se il processo di seeding è stato completato (3).
3. Creare una copia standby del sito Web nell'area di backup e collegarla al database secondario con ridondanza geografica (4).  
4. Aggiungere gli altri endpoint <i>contoso-2.azurewebsites.net</i> e <i>contoso-3.azurewebsites.net</i> al profilo di failover in Gestione traffico di Azure come endpoint offline (5). 

> [!NOTE]
> Le operazioni di preparazione non avranno effetti sull'applicazione nello slot di produzione, che funzionerà in modalità di accesso completo.
> 
> 

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/Option2-1.png)

Dopo aver completato i passaggi di preparazione, lo slot di gestione temporanea è pronto per l'aggiornamento. Il diagramma seguente illustra questi passaggi di aggiornamento.

1. Impostare il database primario nello slot di produzione in modalità di sola lettura (6). In questo modo si assicura che l'istanza di produzione dell'applicazione (V1) resti di sola lettura durante l'aggiornamento, impedendo così la divergenza dei dati tra le istanze di database V1 e V2.  
2. Scollegare il database secondario nella stessa area usando la modalità di terminazione pianificata (7). Viene creata una copia indipendente completamente sincronizzata del database primario, che diventerà automaticamente primaria dopo la terminazione. Questo database verrà aggiornato.
3. Attivare il database primario nello slot di gestione temporanea in modalità lettura/scrittura ed eseguire lo script di aggiornamento (8).    

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/Option2-2.png)

Se l'aggiornamento è stato completato correttamente, è possibile far passare gli utenti finali alla versione V2 dell'applicazione. Il diagramma seguente illustra i passaggi richiesti.

1. Passare all'endpoint attivo nel profilo di Gestione traffico di Azure su <i>contoso-2.azurewebsites.net</i>, che ora punta alla versione V2 del sito Web (9). Questo diventa uno slot di produzione con l'applicazione V2 e il traffico dell'utente finale viene indirizzato a questo slot. 
2. Se l'applicazione V1 non è più necessaria, è possibile rimuoverla (10 e 11).  

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/Option2-3.png)

Se il processo di aggiornamento non riesce, ad esempio a causa di un errore nello script di aggiornamento, lo slot di gestione temporanea deve essere considerato compromesso. Per eseguire il rollback dell'applicazione allo stato di pre-aggiornamento, è sufficiente ripristinare l'uso dell'applicazione nello slot di produzione con l'accesso completo. I passaggi richiesti sono mostrati nel diagramma seguente.    

1. Impostare la copia del database primario nello slot di produzione in modalità lettura/scrittura (12). La funzionalità completa di V1 viene ripristinata nello slot di produzione.
2. Eseguire l'analisi delle cause radice e rimuovere i componenti compromessi nello slot di gestione temporanea (13 e 14). 

A questo punto l'applicazione è completamente funzionale e le operazioni di aggiornamento possono essere ripetute.

> [!NOTE]
> Il rollback non richiede modifiche al profilo di Gestione traffico di Azure perché punta già a <i>contoso-1.azurewebsites.net</i> come endpoint attivo.
> 
> 

![Configurazione della replica geografica del database SQL. Ripristino di emergenza cloud.](media/sql-database-manage-application-rolling-upgrade/Option2-4.png)

Il **vantaggio** principale di questa opzione è che è possibile aggiornare in parallelo sia l'applicazione che la copia con ridondanza geografica senza compromettere la continuità aziendale durante l'aggiornamento. Il **compromesso** principale è che viene richiesta una doppia ridondanza per ogni componente dell'applicazione, che comporta un aumento dei costi e un flusso di lavoro più complesso. 

## <a name="summary"></a>Summary
I due metodi di aggiornamento descritti nell'articolo differiscono in termini di complessità e di costo, ma entrambi si concentrano sulla riduzione dei tempi quando l'utente finale è limitato alle operazioni di sola lettura. Questo periodo di tempo viene definito direttamente dalla durata dello script di aggiornamento. Non dipende dalle dimensioni del database, dal livello di servizio scelto, dalla configurazione del sito Web e da altri fattori difficilmente controllabili. Ciò è possibile perché tutti i passaggi di preparazione sono separati dalle operazioni di aggiornamento e possono essere eseguiti senza alcun impatto sull'applicazione di produzione. L'efficienza dello script di aggiornamento è il fattore determinante per l'esperienza dell'utente finale durante gli aggiornamenti. Il modo ottimale per migliorarla, quindi, consiste nel concentrare gli sforzi sullo script di aggiornamento, per renderlo il più efficiente possibile.  

## <a name="next-steps"></a>Passaggi successivi
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md).
* Per informazioni sull'uso dei backup automatici per il ripristino, vedere [Ripristinare un database SQL di Azure mediante i backup automatici del database](sql-database-recovery-using-backups.md).
* Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md).


