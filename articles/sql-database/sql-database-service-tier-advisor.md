<properties 
   pageTitle="Indicazioni sui livelli di prezzo del database SQL di Azure" 
   description="Quando si modificano i livelli di prezzo nel portale di Azure, vengono forniti suggerimenti in merito ai livelli di prezzo che consentono di individuare il livello più adatto all'esecuzione di un carico di lavoro di un database SQL Azure esistente. Il livello dei prezzi descrive il livello di servizio e il livello di prestazioni di un database SQL" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="08/08/2016"
   ms.author="sstein"/>  

# Indicazioni sui livelli di prezzo del database SQL

 Le indicazioni sul piano tariffario offrono suggerimenti per individuare il livello di servizio e di prestazioni più adatto all'esecuzione di un carico di lavoro di un database SQL di Azure esistente.

> [AZURE.NOTE] Suggerimenti sui piano tariffari sono disponibili solo per i database Web e Business e per i pool di database elastici e solo nel [portale di Azure](https://portal.azure.com/).


Ottenere consigli sul livello dei prezzi durante le operazioni seguenti:

- [Modificare il livello di servizio e il livello di prestazioni (livello di prezzo) di un database SQL](sql-database-scale-up.md)
- [Aggiornare il server SQL Azure a V12](sql-database-upgrade-server-portal.md)
- Accedere al server V12. Vedere [Indicazioni sui livelli di prezzo del database SQL](sql-database-service-tier-advisor.md).
- [Creare un pool di database elastici](sql-database-elastic-pool.md#elastic-database-pool-pricing-tier-recommendations)





## Overview

Il servizio Database SQL consente di analizzare le prestazioni e i requisiti di funzionalità correnti valutando l'utilizzo delle risorse cronologiche per un database SQL. Inoltre, il livello di servizio accettabile minimo viene determinato in base alle dimensioni del database e vengono abilitate le funzionalità di [continuità aziendale](sql-database-business-continuity.md) funzionalità.

Le informazioni vengono analizzate e viene suggerito il livello di prestazioni e il livello di servizio più adatti per l'esecuzione del carico di lavoro tipico del database e la gestione del set di funzionalità correnti.

- Il servizio consente di esaminare 15-30 giorni di dati cronologici precedenti (utilizzo delle risorse, dimensioni del database e attività del database) e viene eseguito un confronto tra la quantità di risorse utilizzate, le limitazioni dei livelli di servizio attualmente disponibili e i livelli di prestazioni effettivi.
- I dati vengono analizzati in intervalli di 15 secondi e ciascuna serie di risultati di ogni intervallo è suddivisa in livello di servizio esistente e livello di prestazioni più adatti per la gestione del carico di lavoro della medesima serie di risultati.
- Questi campioni di 15 secondi vengono quindi aggregati nell'analisi più grande su un periodo di 15-30 giorni più grande e vengono indicati il livello di prestazioni e il livello di servizio in grado di gestire in modo ottimale il 95% del carico di lavoro cronologico.

### Indicazioni

In base all'utilizzo del database, esistono 2 categorie di indicazioni che possono verificarsi:


| Raccomandazione | Descrizione |
| :--- | :--- |
| Aggiornamento | Eseguire l'aggiornamento a un nuovo livello. |
| Non disponibile | Un database richiede un carico di lavoro minimo o circa 35 giorni di attività. Non sono disponibili dati sufficienti per fornire un'indicazione valida. |

## Ottenere indicazioni sui livelli di prezzo

È possibile ottenere indicazioni sui livelli di prezzo selezionando un database Web o Business esistente e facendo clic su **Tutte le impostazioni** e quindi su **Piano tariffario (piano DTU)**. (I consigli sul livello dei prezzi sono disponibili anche quando si [Esegue l'aggiornamento del server SQL Azure a V12](sql-database-upgrade-server-portal.md).)

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **ESPLORA** > **database SQL**.
4. Nel pannello dei **database SQL**, fare clic sul database di cui si desidera visualizzare un suggerimento:

    ![Selezionare il database][1]

5. Nel pannello del database selezionare **Tutte le impostazioni** e quindi **Piano tariffario (piano DTU)**.


7. Verrà visualizzato il pannello **Piani tariffari consigliati**, in cui è possibile fare clic sul livello indicato e sul pulsante **Seleziona** per modificare tale livello.

    ![Iscriversi per l'anteprima][4]

8. Facoltativamente, fare clic sull'opzione relativa alla **visualizzazione dei dettagli di utilizzo** per aprire il pannello relativo ai **dettagli sull'indicazione del livello di prezzo**, che consente di visualizzare il livello consigliato per il database, un confronto delle funzionalità tra i livelli correnti e quelli consigliati e un grafico dell'analisi dell'utilizzo delle risorse cronologiche.

    ![Iscriversi per l'anteprima][5]



## Riepilogo

Le indicazioni sul livello dei prezzi offrono un'esperienza automatizzata per la raccolta dei dati di telemetria per ogni database SQL e l'indicazione della migliore combinazione di livello di servizio/livello di prestazioni in base alle esigenze di prestazioni effettive e ai. requisiti delle funzionalità di un database. Nel pannello Impostazioni fare clic su **Piano tariffario (piano DTU)** per visualizzare i suggerimenti relativi al piano tariffario per i database Web e Business.



## Passaggi successivi

A seconda dei dettagli del database specifico, l'esecuzione di un aggiornamento o di un downgrade in genere non si verifica immediatamente. Il portale fornirà notifiche come le transizioni del nuovo livello di database o è possibile monitorare lo stato di aggiornamento eseguendo una query della vista [sys.dm\_operation\_status (database SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx) nel database master del server di Database SQL.


<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png




<!---HONumber=AcomDC_0810_2016-->
