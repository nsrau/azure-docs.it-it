<properties
   pageTitle="Analizzare i dati con Azure Machine Learning | Microsoft Azure"
   description="Usare Azure Machine Learning per creare un modello predittivo di apprendimento automatico basato sui dati archiviati in Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="shivaniguptamsft"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="shigu;barbkess;sonyama"/>

# Analizzare i dati con Azure Machine Learning

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Questa esercitazione usa Azure Machine Learning per creare un modello predittivo di apprendimento automatico basato sui dati archiviati in Azure SQL Data Warehouse. Nello specifico, verrà compilata una campagna di marketing mirata di Adventure Works, il negozio di biciclette, per stimare la probabilità che un cliente acquisti una bicicletta o meno.

> [AZURE.VIDEO integrating-azure-machine-learning-with-azure-sql-data-warehouse]


## Prerequisiti
Per eseguire questa esercitazione, è necessario:

- Un'istanza di SQL Data Warehouse in cui sia precaricato il database di esempio AdventureWorksDW. Per effettuarne il provisioning, vedere [Creare un Azure SQL Data Warehouse][] e scegliere di caricare i dati di esempio. Se si ha già un data warehouse ma non i dati di esempio, è possibile [caricare manualmente i dati di esempio][].

## 1\. Ottenere i dati
I dati sono disponibili nella visualizzazione dbo.vTargetMail nel database AdventureWorksDW. Per leggere i dati:

1. Accedere ad [Azure Machine Learning Studio][] e fare clic sugli esperimenti personali.
2. Fare clic su **+NEW** e selezionare **Blank Experiment**.
3. Immettere un nome per l'esperimento: Marketing mirato.
4. Trascinare il modulo **Reader** dal riquadro dei moduli nell'area di disegno.
5. Nel riquadro Properties specificare i dettagli del database SQL Data Warehouse.
6. Specificare la **query** di database per leggere i dati di interesse.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Eseguire l'esperimento facendo clic su **Run** sotto l'area di disegno dell'esperimento.![Eseguire l'esperimento][1]


Una volta terminato con successo l’esperimento, per visualizzare i dati importati ![Visualizzare i dati importati][3], fare clic sulla porta di output nella parte inferiore del modulo Reader e selezionare **Visualize**.


## 2\. Pulire i dati
Per pulire i dati, eliminare alcune colonne non rilevanti per il modello. A tale scopo, effettuare l'operazione seguente:

1. Trascinare il modulo **Project Columns** nell'area di disegno.
2. Fare clic su **Launch column selector** nel riquadro Properties per specificare le colonne da eliminare.![Selezione delle colonne][4]

3. Escludere due colonne: CustomerAlternateKey e GeographyKey.![Rimuovere le colonne non necessarie][5]


## 3\. Compilare il modello
Si suddivideranno i dati 80-20: 80% per il training di un modello di Machine Learning e 20% per testare il modello. Per questo problema di classificazione binaria si useranno gli algoritmi "Two-Class".

1. Trascinare il modulo **Split** nell'area di disegno.
2. Immettere 0,8 per Fraction of rows nel primo set di dati di output nel riquadro Properties.![Dividere i dati in set di traning e di test.][6]
3. Trascinare il modulo **Two-Class Boosted Decision Tree** nell'area di disegno.
4. Trascinare il modulo **Train Model** nell'area di disegno e specificare gli input. Fare clic su **Launch column selector** nel riquadro Properties.
      - Primo input: algoritmo ML.
      - Secondo input: dati su cui eseguire il training dell'algoritmo.![Connettere il modulo Train Model][7]
5. Selezionare la colonna **BikeBuyer** come colonna da stimare. ![Selezionare una colonna da stimare][8]


## 4\. Assegnare un punteggio al modello
A questo punto si verificheranno le prestazioni del modello sui dati di test. L'algoritmo scelto verrà confrontato con un algoritmo diverso per verificare quale offre prestazioni migliori.

1. Trascinare il modulo **Score Model** nell'area di disegno. Primo input: Trained Model Secondo input: Test data![Assegnare un punteggio al modello][9]
2. Trascinare il **Two-Class Bayes Point Machine** nell'area di disegno dell'esperimento. Si confronteranno le prestazioni di questo algoritmo rispetto a Two-Class Boosted Decision Tree.
3. Copiare e incollare i moduli Train Model e Score Model nell'area di disegno.
4. Trascinare il modulo **Evaluate Model** nell'area di disegno per confrontare i due algoritmi.
5. Scegliere **Run** per eseguire l'esperimento.![Eseguire l'esperimento][10]
6. Fare clic con il pulsante destro del mouse sulla porta di output del modulo Evaluate Model e scegliere Visualize.![Visualizzare i risultati della valutazione][11]

La metrica fornita include curva ROC, curva di precisione/recupero e curva di accuratezza. Esaminando la metrica, si noterà che il primo modello fornisce prestazioni migliori rispetto al secondo. Per vedere i quali sono state le previsioni del primo modello, fare clic sulla porta di output di Score Model e scegliere Visualize.![Visualizzare i risultati di punteggio][12]

Verranno visualizzate altre due colonne aggiunte al set di dati di test.

- Scored Probabilities: la probabilità che un cliente sia un acquirente di biciclette.
- Scored Labels: la classificazione eseguita dal modello – acquirente di biciclette (1) o non acquirente (0). La soglia di probabilità per le etichette è impostata su 50% e può essere modificata.

Confrontando la colonna BikeBuyer (effettivo) con Scored Labels (stima), è possibile vedere il livello di prestazioni del modello. Come passaggi successivi è possibile usare questo modello per eseguire stime per i nuovi clienti e pubblicare il modello come un servizio Web o scrivere i risultati in SQL Data Warehouse.

## Passaggi successivi

Per ulteriori informazioni sulla creazione di modelli di apprendimento automatico predittivi, fare riferimento a [Introduzione a Machine Learning in Azure][].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure Machine Learning studio]: https://studio.azureml.net/
[Introduzione a Machine Learning in Azure]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[caricare manualmente i dati di esempio]: sql-data-warehouse-get-started-load-sample-databases.md
[Creare un Azure SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!---HONumber=AcomDC_0622_2016-->