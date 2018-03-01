---
title: Monitorare le richieste e l'archiviazione di Azure Cosmos DB | Microsoft Docs
description: Informazioni su come monitorare l'account Azure Cosmos DB per le metriche relative alle prestazioni, ad esempio richieste ed errori del server, e all'utilizzo, ad esempio l'utilizzo di risorse di archiviazione.
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 4c6a2e6f-6e78-48e3-8dc6-f4498b235a9e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: mimig
ms.openlocfilehash: 0e9a47e6a1a5c7a44c5553742d6c5c81f8ca7286
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-azure-cosmos-db"></a>Monitorare Azure Cosmos DB
È possibile monitorare gli account Azure Cosmos DB nel [portale di Azure](https://portal.azure.com/). Per ogni account di Azure Cosmos DB, è disponibile un set completo di metriche per il monitoraggio di velocità effettiva, archiviazione, disponibilità, latenza e coerenza.

È possibile esaminare le metriche nella pagina Account, nella nuova pagina Metriche o in Monitoraggio di Azure.

## <a name="view-performance-metrics-on-the-metrics-page"></a>Visualizzare le metriche delle prestazioni nella pagina Metriche
1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Tutti i servizi**, scorrere fino a **Database**, fare clic su **Azure Cosmos DB** e quindi fare clic sul nome dell'account Azure Cosmos DB per il quale si vogliono visualizzare le metriche delle prestazioni.
2. Una volta caricata la nuova pagina, nel menu delle risorse, sotto **Monitoraggio**, fare clic su **Metriche**.
3. Nella pagina Metriche selezionare la raccolta da esaminare dall'elenco a discesa **Raccolte**.

   Il portale di Azure visualizza l'insieme delle metriche di raccolta disponibili. Si noti che velocità effettiva, archiviazione, disponibilità, latenza e coerenza delle metriche vengono fornite in schede separate. Per ottenere altre informazioni sulle metriche fornite, fare clic sulla doppia freccia nella parte superiore destra del riquadro di ogni metrica.

   ![Schermata della sezione Monitoraggio che mostra la suite di metriche disponibili](./media/monitor-accounts/metrics-suite.png)

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>Visualizzare le metriche delle prestazioni utilizzando Monitoraggio di Azure
1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Monitoraggio** nella barra degli strumenti a sinistra.
2. Nel menu delle risorse fare clic su **Metriche**.
3. Nella finestra **Monitoraggio - Metriche** selezionare dal menu a discesa **Gruppo di risorse** il gruppo di risorse associato all'account Azure Cosmos DB da monitorare. 
4. Nel menu a discesa **Risorsa** selezionare l'account di database da monitorare.
5. Nell'elenco delle **Metriche disponibili** selezionare le metriche da visualizzare. Utilizzare il tasto CTRL per selezionare più voci. 

## <a name="view-performance-metrics-on-the-account-page"></a>Visualizzare le metriche delle prestazioni nella pagina Account
1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Tutti i servizi**, scorrere fino a **Database**, fare clic su **Azure Cosmos DB** e quindi fare clic sul nome dell'account Azure Cosmos DB per il quale si vogliono visualizzare le metriche delle prestazioni.
2. La sezione **Monitoraggio** visualizza i seguenti riquadri per impostazione predefinita:
   
   * Richieste totali per il giorno corrente.
   * Spazio di archiviazione usato.
   
   ![Schermata della sezione Monitoraggio che mostra le richieste e l'uso delle risorse di archiviazione](./media/monitor-accounts/documentdb-total-requests-and-usage.png)
3. Fare clic sulla doppia freccia in alto a destra del riquadro **Richieste**  per aprire una pagina **Metrica** dettagliata.
4. La pagina **Metrica** mostra i dettagli relativi alle richieste totali. 

## <a name="set-up-alerts-in-the-portal"></a>Configurare gli avvisi nel portale
1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Tutti i servizi**, **Azure Cosmos DB** e infine sul nome dell'account Azure Cosmos DB per cui si vogliono configurare gli avvisi relativi alle metriche delle prestazioni.
2. Nel menu delle risorse fare clic su **Regole di avviso** per aprire la relativa pagina.  
   ![Screenshot della parte di regole di avviso selezionata](./media/monitor-accounts/madocdb10.5.png)
3. Nella pagina **Regole di avviso** fare clic su **Aggiungi avviso**.  
   ![Schermata della pagina Regole di avviso, con il pulsante Aggiungi avviso evidenziato](./media/monitor-accounts/madocdb11.png)
4. Nella pagina **Aggiungi una regola di avviso** specificare:
   
   * Il nome della regola di avviso che si sta configurando.
   * Una descrizione della nuova regola di avviso.
   * La metrica per la regola di avviso.
   * Condizione, soglia e periodo per l'attivazione della regola. Ad esempio, un numero di errori del server maggiore di 5 negli ultimi 15 minuti.
   * Se deve essere inviato un messaggio di posta elettronica all'amministratore del servizio e ai coamministratori quando viene attivata la regola.
   * Indirizzi di posta elettronica aggiuntivi per le notifiche degli avvisi.  
     ![Schermata della pagina Aggiungi una regola di avviso](./media/monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programmatically"></a>Monitorare Azure Cosmos DB a livello di codice
Le metriche a livello di account disponibili nel portale, ad esempio l'uso delle risorse di archiviazione dell'account e il numero totale di richieste, non sono disponibili tramite le API SQL. È tuttavia possibile recuperare dati di utilizzo a livello di raccolta tramite le API SQL. Per recuperare i dati a livello di raccolta, eseguire le operazioni seguenti:

* Per usare l'API REST, [eseguire il comando GET sulla raccolta](https://msdn.microsoft.com/library/mt489073.aspx). Nelle intestazioni x-ms-resource-quota e x-ms-resource-usage della risposta verranno restituite le informazioni relative alla quota e all'utilizzo per la raccolta.
* Per usare .NET SDK, usare il metodo [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx), che restituisce un oggetto [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) contenente alcune proprietà d'uso, ad esempio **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** e altro ancora.

Per accedere a metriche aggiuntive, usare l'[SDK di Monitoraggio di Azure](https://www.nuget.org/packages/Microsoft.Azure.Insights). Le definizioni delle metriche disponibili possono essere recuperate chiamando:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Le query per recuperare le singole metriche usano il formato seguente:

    https://management.azure.com/subscriptions/{SubecriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Per altre informazioni, vedere [Retrieving Resource Metrics via the Azure Insights API](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/)(Recupero delle metriche delle risorse tramite l'API REST del Monitoraggio di Azure). Si noti che il nuovo nome di Azure Insights è Monitoraggio di Azure.  Nel blog si usa ancora il nome precedente.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla pianificazione della capacità di Azure Cosmos DB, vedere lo [strumento di calcolo Capacity Planner di Azure Cosmos DB](https://www.documentdb.com/capacityplanner).

