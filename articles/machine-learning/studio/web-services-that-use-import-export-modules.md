---
title: Utilizzo di Import Data ed Export Data nei servizi Web Azure Machine Learning | Documentazione Microsoft
description: Informazioni su come usare i moduli Import Data ed Export Data per inviare e ricevere dati da un servizio Web.
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondlaghaeian
editor: 
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: raymondl
ms.openlocfilehash: 626b730bf7f012ee64f51e3aee0970a1a1ac5f66
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>Distribuzione di servizi di Web Azure ML che usano i moduli Import Data ed Export Data

Quando si crea un esperimento predittivo, si aggiunge in genere un input e un output del servizio Web. Quando si distribuisce l'esperimento, i consumer possono inviare e ricevere dati dal servizio Web tramite gli input e gli output. Per alcune applicazioni, i dati del consumer possono essere disponibili da un feed di dati o risiedere già in un'origine dati esterna, ad esempio archiviazione BLOB di Azure. In questi casi non è necessario leggere e scrivere dati usando gli input e gli output del servizio Web . Gli utenti possono invece usare il servizio di esecuzione batch (BES) per leggere i dati dall'origine dati mediante un modulo Import Data e scrivere i risultati di assegnazione dei punteggi in una posizione dati diversa mediante un modulo Export Data.

I moduli Import Data ed Export Data possono leggere e scrivere in numerose posizioni, ad esempio un URL Web tramite HTTP, una query Hive, un database SQL di Azure, l'archiviazione tabelle di Azure, l'Archiviazione BLOB di Azure, un provider di feed di dati o un database SQL locale.

Questo argomento usa l'esempio "Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset" e presuppone che il set di dati sia già stato caricato nella tabella SQL di Azure denominata censusdata.

## <a name="create-the-training-experiment"></a>Creare l'esperimento di training
Quando si apre l'esempio "Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset", si usa il set di dati Adult Census Income Binary Classification di esempio. L'esperimento nell'area di disegno sarà simile all'immagine seguente:

![Configurazione iniziale dell'esperimento.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Per leggere i dati dalla tabella SQL di Azure:

1. Eliminare il modulo del set di dati.
2. Digitare import nella casella di ricerca dei componenti.
3. Nell'elenco dei risultati aggiungere un modulo *Import data* nell'area di disegno dell'esperimento.
4. Connettere l'output del modulo *Import Data* (Importa dati) e l'input del modulo *Clean Missing Data* (Pulisci dati mancanti).
5. Nel riquadro delle proprietà selezionare **Azure SQL Database** in the **Data Source** (Origine dati).
6. Immettere le informazioni appropriate per il database nei campi **Database server name** (Nome server database), **Database name** (Nome database), **User name** (Nome utente) e **Password**.
7. Immettere la query seguente nel campo Database query (Query database).
   
     select [age],
   
        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     from dbo.censusdata;
8. Fare clic su **Run**(Esegui) nella parte inferiore dell'area di disegno dell'esperimento.

## <a name="create-the-predictive-experiment"></a>Creare l'esperimento predittivo
Configurare quindi l'esperimento predittivo da cui distribuire il servizio Web.

1. Nella parte inferiore dell'area di disegno dell'esperimento fare clic su **Set Up Web Service** (Configura servizio Web) e selezionare **Predictive Web Service [Recommended]** (Servizio Web predittivo - Scelta consigliata).
2. Rimuovere i moduli *Web Service Input* e *Web Service Output modules* dall'esperimento predittivo. 
3. Digitare export nella casella di ricerca di componenti.
4. Nell'elenco dei risultati aggiungere un modulo *Export Data* nell'area di disegno dell'esperimento.
5. Connettere l'output del modulo *Score Model* (Modello di punteggio) e l'input del modulo *Export Data* (Esporta dati). 
6. Nel riquadro delle proprietà selezionare **Azure SQL Database** (Database SQL Azure) come destinazione dei dati.
7. Immettere le informazioni appropriate per il database nei campi **Database server name** (Nome server database), **Database name** (Nome database), **Server user account name** (Nome account utente server) e **Server user account password** (Password account utente server).
8. Nel campo **Comma separated list of columns to be saved** (Elenco di colonne da salvare delimitato da virgole) digitare Scored Labels.
9. Nel campo **Data table name**(Nome tabella dati) digitare dbo.ScoredLabels. Se non esiste, la tabella viene creata quando viene eseguito l'esperimento o viene chiamato il servizio Web.
10. Nel campo **Comma separated list of datatable columns** (Elenco di colonne di tabella di database delimitato da virgole) digitare ScoredLabels.

Quando si scrive un'applicazione che chiama il servizio Web finale, è possibile specificare una tabella di destinazione o una query di input diversa in fase di esecuzione. Per configurare questi input e output, usare la funzionalità Web Service Parameters (Parametri del servizio Web) per impostare la proprietà *Data source* (Origine dati) del modulo *Import Data* (Importa dati) e la proprietà di destinazione dei dati del modulo *Export Data* (Esporta dati).  Per altre informazioni sui parametri del servizio Web, vedere [AzureML Web Service Parameters](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) su Cortana Intelligence and Machine Learning Blog.

Per configurare i parametri del servizio Web per la query di importazione e la tabella di destinazione:

1. Nel riquadro delle proprietà per il modulo *Import Data* (Importa dati) fare clic sull'icona nella parte superiore destra del campo **Database query** (Query database) e selezionare **Set as web service parameter** (Imposta come parametro di servizio Web).
2. Nel riquadro delle proprietà per il modulo *Export Data* (Esporta dati) fare clic sull'icona nella parte superiore destra del campo **Data table name** (Nome tabella dati) e selezionare **Set as web service parameter** (Imposta come parametro di servizio Web).
3. Nella parte inferiore del riquadro delle proprietà del modulo *Export Data* nella sezione **Web Service Parameters** (Parametri del servizio Web), fare clic su Database query (Query database) e rinominare in Query.
4. Fare clic su **Data table name** (Nome tabella dati) e rinominare in **Table** (Tabella).

Al termine l'esperimento dovrebbe essere simile all'immagine seguente:

![Risultato finale dell'esperimento.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Ora è possibile distribuire l'esperimento come servizio Web.

## <a name="deploy-the-web-service"></a>Distribuire il servizio web
È possibile eseguire la distribuzione come servizio Web nuovo o classico.

### <a name="deploy-a-classic-web-service"></a>Distribuire un servizio Web classico
Per eseguire la distribuzione come servizio Web classico e creare un'applicazione per usare il servizio:

1. Nella parte inferiore dell'area di disegno dell'esperimento fare clic su Run (Esegui).
2. Al termine dell'esecuzione fare clic su **Deploy Web Service** (Distribuisci servizio Web) e selezionare **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]).
3. Nel dashboard del servizio Web individuare la chiave API. Copiarla e salvarla per usarla in un secondo momento.
4. Nella tabella **Default Endpoint** (Endpoint predefinito) fare clic sul collegamento **Esecuzione batch** per aprire la pagina della Guida dell'API.
5. In Visual Studio creare un'applicazione console C#. A tale scopo, selezionare **Nuovo** > **Progetto** > **Visual C#** > **Desktop classico di Windows** > **Applicazione console (.NET Framework)**.
6. Nella pagina della Guida di API individuare la sezione **Sample Code** (Codice di esempio) nella parte inferiore della pagina.
7. Copiare e incollare il codice di esempio in C# nel file Program.cs e rimuovere tutti i riferimenti nell'archiviazione BLOB.
8. Aggiornare il valore della variabile *apiKey* con la chiave API salvata in precedenza.
9. Individuare la dichiarazione di richiesta e aggiornare i valori del servizio Web passati ai moduli *Import Data* (Importa dati) e *Export Data* (Esporta dati). In questo caso, usare la query originale, ma definire un nome per la nuova tabella.
   
        var request = new BatchExecutionRequest() 
        {           
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Eseguire l'applicazione. 

Al termine dell'esecuzione verrà aggiunta una nuova tabella al database contenente i risultati di punteggio.

### <a name="deploy-a-new-web-service"></a>Distribuire un servizio Web nuovo

> [!NOTE] 
> Per distribuire un nuovo servizio Web è necessario disporre delle autorizzazioni sufficienti nella sottoscrizione a cui si sta distribuendo il servizio Web. Per altre informazioni, vedere [Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning](manage-new-webservice.md). 

Per eseguire la distribuzione come servizio Web nuovo e creare un'applicazione per usare il servizio:

1. Fare clic su **Run**(Esegui) nella parte inferiore dell'area di disegno dell'esperimento.
2. Al termine dell'esecuzione fare clic su **Deploy Web Service** (Distribuisci servizio Web) e selezionare **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]).
3. Nella pagina Deploy Experiment (Sperimentazione distribuzione) immettere un nome per il servizio Web e selezionare un piano tariffario, quindi fare clic su **Deploy**(Distribuzione).
4. Nella pagina **Quickstart** (Avvio rapido) fare clic su **Consume** (Utilizzo).
5. Nella sezione **Sample Code** (Codice di esempio) fare clic su **Batch**.
6. In Visual Studio creare un'applicazione console C#. A tale scopo, selezionare **Nuovo** > **Progetto** > **Visual C#** > **Desktop classico di Windows** > **Applicazione console (.NET Framework)**.
7. Copiare e incollare il codice di esempio in C# nel file Program.cs.
8. Aggiornare il valore della variabile *apiKey* con la **chiave primaria** presente nella sezione **Basic consumption info** (Informazioni di base sul consumo).
9. Individuare la dichiarazione *scoreRequest* e aggiornare i valori dei parametri del servizio Web passati ai moduli *Import Data* (Importa dati) e *Export Data* (Esporta dati). In questo caso, usare la query originale, ma definire un nome per la nuova tabella.
   
        var scoreRequest = new
        {       
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };
10. Eseguire l'applicazione. 

