---
title: Usare Azure Machine Learning con SQL Data Warehouse | Documentazione Microsoft
description: Suggerimenti per l'uso di Azure Machine Learning con Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: barbkess
editor: 
ms.assetid: ac6bc731-6add-47a9-b3fe-68996e656f4d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: c19860c6b5b1c15d1e29ddc67f9cf9ad4618725b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="use-azure-machine-learning-with-sql-data-warehouse"></a>Usare Azure Machine Learning con SQL Data Warehouse
Azure Machine Learning è un servizio di analisi predittive completamente gestito, che può essere usato per creare modelli predittivi dei dati in SQL Data Warehouse e pubblicarli come servizi pronti all'uso. Per le nozioni di base sull'analisi predittiva e su Machine Learning, vedere [Introduzione a Machine Learning in Azure][Introduction to Machine Learning on Azure].  Sarà quindi possibile imparare a creare, eseguire il training, assegnare punteggi e testare un modello di Machine Learning usando l'[esercitazione per la creazione di esperimenti][Create experiment tutorial].

Questo articolo illustra come effettuare le operazioni seguenti usando [Azure Machine Learning Studio][Azure Machine Learning Studio]:

* Leggere i dati dal database da creare, eseguire il training e il punteggio di un modello di stima
* Scrivere dati nel database

## <a name="read-data-from-sql-data-warehouse"></a>Leggere dati da SQL Data Warehouse
Si leggeranno i dati dalla tabella Product nel database AdventureWorksDW.

### <a name="step-1"></a>Passaggio 1
Avviare un nuovo esperimento facendo clic su +NEW nella parte inferiore della finestra di Machine Learning Studio, selezionare EXPERIMENT, quindi Blank Experiment. Selezionare il nome dell'esperimento predefinito nella parte superiore dell'area di disegno e denominarlo in modo significativo, ad esempio Bicycle price prediction.

### <a name="step-2"></a>Passaggio 2
Cercare il modulo Reader nella tavolozza dei set di dati e moduli a sinistra dell'area di disegno dell'esperimento. Trascinare il set di dati nell'area di disegno dell'esperimento.
![][drag_reader]

### <a name="step-3"></a>Passaggio 3
Selezionare il modulo Reader e compilare il riquadro delle proprietà.

1. Selezionare il database SQL di Azure in Data Source.
2. Database server name: digitare il nome del server. Per trovarlo, è possibile usare il [portale di Azure][Azure portal].

![][server_name]

1. Database name: digitare il nome del database nel server specificato.
2. Server user account name: digitare il nome utente di un account con autorizzazioni di accesso al database.
3. Server user account password: fornire la password per l'account utente specificato.
4. Accept any server certificate: usare questa opzione (meno sicura) se si vuole evitare di rivedere il certificato del sito prima di leggere i dati.
5. Database query: immettere un'istruzione SQL che descriva i dati da leggere. In questo caso si leggeranno i dati dalla tabella Product usando la query seguente.

```SQL
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### <a name="step-4"></a>Passaggio 4
1. Eseguire l'esperimento facendo clic su Run sotto l'area di disegno dell'esperimento.
2. Al termine dell'esperimento, il modulo Reader sarà contraddistinto da un segno di spunta verde per indicarne il corretto completamento. Si noti anche lo stato Esecuzione terminata nell'angolo in alto a destra.

![][run]

1. Per visualizzare tutti i dati importati, fare clic sulla porta di output nella parte inferiore del set di dati relativo alle automobili e selezionare Visualize.

## <a name="create-train-and-score-a-model"></a>Creare, eseguire il training e assegnare un punteggio a un modello
A questo punto è possibile utilizzare questo set di dati per:

* Creare un modello: elaborare i dati e definire le funzionalità
* Il training del modello: selezionare e applicare un algoritmo di apprendimento
* Assegnare un punteggio e testare il modello: stima di un nuovo prezzo di biciclette

![][model]

Per altre informazioni su come creare, eseguire il training, assegnare punteggi e testare un modello di Machine Learning, usare l'[esercitazione per la creazione di esperimenti][Create experiment tutorial].

## <a name="write-data-to-azure-sql-data-warehouse"></a>Scrivere dati in Azure SQL Data Warehouse
Si scriverà il set di risultati nella tabella ProductPriceForecast del database AdventureWorksDW.

### <a name="step-1"></a>Passaggio 1
Cercare il modulo Writer nella tavolozza dei set di dati e moduli a sinistra dell'area di disegno dell'esperimento. Trascinare il set di dati nell'area di disegno dell'esperimento.

![][drag_writer]

### <a name="step-2"></a>Passaggio 2
Selezionare il modulo Writer e compilare il riquadro delle proprietà.

1. Selezionare il database SQL di Azure in Data Destination.
2. Database server name: digitare il nome del server. Per trovarlo, è possibile usare il [portale di Azure][Azure portal].
3. Database name: digitare il nome del database nel server specificato.
4. Server user account name: digitare il nome utente di un account con autorizzazioni di scrittura nel database.
5. Server user account password: fornire la password per l'account utente specificato.
6. Accept any server certificate (insecure): selezionare questa opzione se non si vuole visualizzare il certificato.
7. Comma-separated list of columns to be saved: fornire un elenco del set di dati o delle colonne dei risultati da generare.
8. Data table name: specificare il nome della tabella dati.
9. Comma-separated list of datatable columns: specificare i nomi delle colonne da usare nella nuova tabella. I nomi di colonna possono essere diversi da quelli del set di dati di origine, ma è necessario elencare lo stesso numero di colonne definite per la tabella di output.
10. Number of rows written per SQL Azure operation: è possibile configurare il numero di righe scritte in un database SQL in un'unica operazione.

![][writer_properties]

### <a name="step-3"></a>Passaggio 3
1. Eseguire l'esperimento facendo clic su Run sotto l'area di disegno dell'esperimento.
2. Al termine dell'esperimento, tutti i moduli saranno contraddistinti da un segno di spunta verde per indicarne il corretto completamento.

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Create experiment tutorial]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[Introduction to machine learning on Azure]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[Azure Machine Learning Studio]: https://studio.azureml.net/Home
[Azure portal]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/
