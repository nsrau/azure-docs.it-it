<properties 
   pageTitle="Indicazioni sui livelli di prezzo del database SQL di Azure" 
   description="Quando si modificano i livelli di prezzo nel portale di Azure, vengono forniti suggerimenti in merito ai livelli di prezzo che consentono di individuare il livello più adatto all'esecuzione di un carico di lavoro di un database SQL Azure esistente." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="05/18/2015"
   ms.author="sstein"/>

# Indicazioni sui livelli di prezzo del database SQL

 Quando si modificano i livelli di prezzo nel portale di Azure, vengono forniti suggerimenti in merito ai livelli di prezzo che consentono di individuare il livello più adatto all'esecuzione di un carico di lavoro di un database SQL Azure esistente.

> [AZURE.NOTE]Suggerimenti sui livelli dei prezzi sono disponibili solo per i database Web e Business e solo nel [portale Azure](https://portal.azure.com/).


## Panoramica

Azure consente di analizzare le prestazioni e i requisiti di funzionalità correnti valutando l'utilizzo delle risorse cronologiche per un database SQL. Inoltre, il livello di servizio accettabile minimo viene determinato in base alle dimensioni del database e vengono abilitate le funzionalità di [continuità aziendale](https://msdn.microsoft.com/library/azure/hh852669.aspx) funzionalità.

Le informazioni vengono analizzate e viene suggerito il livello di prestazioni e il livello di servizio più adatti per l'esecuzione del carico di lavoro tipico del database e la gestione del set di funzionalità correnti.

- Il servizio consente di esaminare 15-30 giorni di dati cronologici precedenti (utilizzo delle risorse, dimensioni del database e attività del database) e viene eseguito un confronto tra la quantità di risorse utilizzate, le limitazioni dei livelli di servizio attualmente disponibili e i livelli di prestazioni effettivi.
- I dati vengono analizzati in intervalli di 15 secondi e ciascuna serie di risultati di ogni intervallo è suddivisa in livello di servizio esistente e livello di prestazioni più adatti per la gestione del carico di lavoro della medesima serie di risultati.
- Questi campioni di 15 secondi vengono quindi aggregati nell'analisi più grande su un periodo di 15-30 giorni più grande e vengono indicati il livello di prestazioni e il livello di servizio in grado di gestire in modo ottimale il 95% del carico di lavoro cronologico.

### Recommendations

In base all'utilizzo del database, esistono 2 categorie di indicazioni che possono verificarsi:


| Raccomandazione | Descrizione |
| :--- | :--- |
| Aggiornamento | Eseguire l'aggiornamento a un nuovo livello. |
| Non disponibile | Un database richiede un carico di lavoro minimo o circa 14 giorni di attività. Non sono disponibili dati sufficienti per fornire un'indicazione valida. |

## Ottenere indicazioni sui livelli di prezzo

È possibile ottenere le indicazioni sui livelli di prezzo selezionando un database Web o Business esistente e facendo clic sul riquadro **Livello di prezzo**.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **SFOGLIA** nel menu a sinistra.
3. Fare clic su **Database SQL** nel pannello **Sfoglia**.
4. Nel pannello relativo ai **database SQL**, fare clic sul database che si desidera venga analizzato dal servizio.

    ![Selezionare il database][1]

5. Nel pannello relativo ai database, selezionare il riquadro **Livello di prezzo**.

    ![Pricing tier][2]


7. Dopo aver fatto clic sul riquadro **livello di prezzo** verrà visualizzato il pannello **livelli di prezzo consigliati**, in cui è possibile fare clic sul livello indicato e sul pulsante di **selezione** per modificare tale livello.

    ![Iscriversi per l'anteprima][4]

8. Facoltativamente, fare clic sull'opzione relativa alla **visualizzazione dei dettagli di utilizzo** per aprire il pannello relativo ai **dettagli sull'indicazione del livello di prezzo**, che consente di visualizzare il livello consigliato per il database, un confronto delle funzionalità tra i livelli correnti e quelli consigliati e un grafico dell'analisi dell'utilizzo delle risorse cronologiche.

    ![Iscriversi per l'anteprima][5]



## Riepilogo

Le indicazioni sul livello dei prezzi offrono un'esperienza automatizzata per la raccolta dei dati di telemetria per ogni database SQL e l'indicazione della migliore combinazione di livello di servizio/livello di prestazioni in base alle esigenze di prestazioni effettive e ai. requisiti delle funzionalità di un database. Fare clic sul riquadro relativo al **Livello prezzi** per visualizzare le indicazioni sul livello di prezzo.



## Passaggi successivi

A seconda dei dettagli del database specifico, l'esecuzione di un aggiornamento o di un downgrade in genere non si verifica immediatamente. Il portale di gestione fornirà notifiche come le transizioni del nuovo livello di database o è possibile monitorare lo stato di aggiornamento eseguendo una query della vista [sys.dm_operation_status (database SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx) nel database master del server di Database SQL.


<!--Image references-->
[1]: ./media/sql-database-pricing-tier-recommendations/select-database.png
[2]: ./media/sql-database-pricing-tier-recommendations/pricing-tier.png
[3]: ./media/sql-database-pricing-tier-recommendations/preview-sign-up.png
[4]: ./media/sql-database-pricing-tier-recommendations/choose-pricing-tier.png
[5]: ./media/sql-database-pricing-tier-recommendations/usage-details.png

<!---HONumber=58--> 