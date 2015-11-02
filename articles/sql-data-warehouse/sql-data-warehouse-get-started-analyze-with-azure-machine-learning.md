<properties
   pageTitle="Analizzare i dati con Azure Machine Learning | Microsoft Azure"
   description="Suggerimenti per l'uso di Azure Machine Learning con Azure SQL Data Warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/21/2015"
   ms.author="sahajs"/>

# Analizzare i dati con Azure Machine Learning
Questa esercitazione descrive come compilare un modello predittivo di apprendimento automatico con Azure Machine Learning usando i dati di Azure SQL Data Warehouse. In questa esercitazione si userà il database AdventureWorksDW e si classificheranno i clienti del negozio di biciclette di Adventure Works come probabili acquirenti di biciclette o meno, per creare una campagna di marketing mirata.


## Prerequisiti
Per eseguire questa esercitazione, sono necessari:

- SQL Data Warehouse con il database di esempio AdventureWorksDW.

[Creare un SQL Data Warehouse][] illustra come effettuare il provisioning di un database con dati di esempio. Se si ha già un database SQL Data Warehouse ma non i dati di esempio, è possibile [caricare manualmente i dati di esempio][].


## Passaggio 1: Ottenere i dati 
I dati saranno letti dalla visualizzazione dbo.vTargetMail nel database AdventureWorksDW.

1. Accedere ad [Azure Machine Learning Studio][] e fare clic sugli esperimenti personali.
2. Fare clic su **+NEW** e selezionare **Blank Experiment**.
3. Immettere un nome per l'esperimento: Marketing mirato.
4. Trascinare il modulo **Reader** dal riquadro dei moduli nell'area di disegno.
5. Nel riquadro Properties specificare i dettagli del database SQL Data Warehouse. 
6. Specificare la query di database per leggere i dati di interesse.

```
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
7. Eseguire l'esperimento facendo clic su **Run** sotto l'area di disegno dell'esperimento.
8. Per visualizzare i dati importati, fare clic sulla porta di output nella parte inferiore del modulo Reader e selezionare **Visualize**.



## Passaggio 2: Pulire i dati
Si elimineranno alcune colonne che non sono rilevanti per il modello.

1. Trascinare il modulo **Project Columns** nell'ara di disegno.
2. Fare clic su **Launch column selector** nel riquadro Properties per specificare le colonne da eliminare.
3. Escludere due colonne: CustomerAlternateKey e GeographyKey.



## Passaggio 3: Compilare il modello
Si suddivideranno i dati 80-20: 80% per il training di un modello di Machine Learning e 20% per testare il modello. Per questo problema di classificazione binaria si useranno gli algoritmi "Two-Class".

1. Trascinare il modulo **Split** nell'area di disegno.
2. Immettere 0,8 per Fraction of rows nel primo set di dati di output nel riquadro Properties.
3. Trascinare il modulo **Two-Class Boosted Decision Tree** nell'area di disegno.
4. Trascinare il modulo **Train Model** nell'area di disegno e specificare: Primo input: algoritmo ML. Secondo input: dati su cui eseguire il training dell'algoritmo.
5. Fare clic su **Launch column selector** nel riquadro Properties per specificare la colonna del modello che dovrebbe essere predittiva: BikeBuyer.


## Passaggio 4: Modello di punteggio
A questo punto si verificheranno le prestazioni del modello sui dati di test. L'algoritmo scelto verrà confrontato con un algoritmo diverso per verificare quale offre prestazioni migliori.

1. Trascinare il modulo **Score Model** nell'area di disegno. Primo input: modello con training Secondo input: dati di test
2. Trascinare il **Two-Class Bayes Point Machine** nell'area di disegno dell'esperimento. Si confronteranno le prestazioni di questo algoritmo rispetto a Two-Class Boosted Decision Tree.
3. Copiare e incollare i moduli Train Model e Score Model nell'area di disegno.
4. Trascinare il modulo **Evaluate Model** nell'area di disegno per confrontare i due algoritmi.
5. Scegliere **Run** per eseguire l'esperimento.
6. Fare clic con il pulsante destro del mouse sulla porta di output del modulo Evaluate Model e scegliere Visualize.


La metrica fornita include curva ROC, curva di precisione/recupero e curva di accuratezza. Esaminando la metrica, si noterà che il primo modello fornisce prestazioni migliori rispetto al secondo. Per vedere i quali sono state le previsioni del primo modello, fare clic sulla porta di output di Score Model e scegliere Visualize.


Verranno visualizzate altre due colonne aggiunte al set di dati di test.

- Scored Probabilities: la probabilità che un cliente sia un acquirente di biciclette.
- Scored Labels: la classificazione eseguita dal modello – acquirente di biciclette (1) o non acquirente (0). La soglia di probabilità per le etichette è impostata su 50% e può essere modificata.

Confrontando la colonna BikeBuyer (effettivo) con Scored Labels (stima), è possibile vedere il livello di prestazioni del modello. Come passaggi successivi è possibile usare questo modello per eseguire stime per i nuovi clienti e pubblicare il modello come un servizio Web o scrivere i risultati in SQL Data Warehouse.

Per altre informazioni, vedere [Introduzione all'apprendimento automatico in Azure][].



<!--Article references-->
[Azure Machine Learning studio]: https://studio.azureml.net/
[Introduzione all'apprendimento automatico in Azure]: ../machine-learning/machine-learning-what-is-machine-learning.md
[caricare manualmente i dati di esempio]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Creare un SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!---HONumber=Oct15_HO4-->