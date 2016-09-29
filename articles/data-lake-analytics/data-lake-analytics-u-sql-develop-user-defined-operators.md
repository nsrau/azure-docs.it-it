<properties 
   pageTitle="Sviluppare operatori U-SQL definiti dall'utente per i processi di Analisi Azure Data Lake | Azure" 
   description="Informazioni su come sviluppare operatori definiti dall'utente da utilizzare e riutilizzare nei processi di Analisi Data Lake. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>


# Sviluppare operatori U-SQL definiti dall'utente per i processi di Analisi Azure Data Lake

Informazioni su come sviluppare operatori definiti dall'utente da utilizzare e riutilizzare nei processi di Analisi Data Lake. Si svilupperà un operatore personalizzato per convertire i nomi dei paesi.

##Prerequisiti

- Visual Studio 2015, Visual Studio 2013 Update 4 oppure Visual Studio 2012 con Visual C++ installato
- Microsoft Azure SDK per .NET versione 2.5 o successiva. Installare il prodotto usando il programma di installazione della piattaforma Web.
- Un account di Analisi Data Lake. Vedere [Introduzione ad Analisi Data Lake di Azure con il portale di Azure](data-lake-analytics-get-started-portal.md).
- Seguire i passaggi dell’esercitazione [Introduzione a U-SQL Studio di Analisi Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
- Connettersi ad Azure, vedere [Introduzione a U-SQL Studio di Analisi Azure Data Lake](data-lake-analytics-u-sql-get-started.md#connect-to-azure).
- Caricare i dati di origine, vedere [Introduzione a U-SQL Studio di Analisi Azure Data Lake](data-lake-analytics-u-sql-get-started.md#upload-source-data-files).

## Definire e utilizzare l'operatore definito dall'utente in U-SQL

**Per creare e inviare un processo di U-SQL**

1. Scegliere **Nuovo** dal menu **File** e quindi fare clic su **Progetto**.
2. Selezionare il tipo **Progetto U-SQL**.

	![Nuovo progetto Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Fare clic su **OK**. Visual Studio crea una soluzione con un file Script.usql.
4. In **Esplora soluzioni** espandere Script.usql e fare clic due volte su **Script.usql.cs**.
5. Incollare il seguente codice nel file:

		using Microsoft.Analytics.Interfaces;
		using System.Collections.Generic;
		
		namespace USQL_UDO
		{
			public class CountryName : IProcessor
			{
				private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
				{
					{
						"Deutschland", "Germany"
					},
					{
						"Schwiiz", "Switzerland"
					},
					{
						"UK", "United Kingdom"
					},
					{
						"USA", "United States of America"
					},
					{
						"中国", "PR China"
					}
				};
		
				public override IRow Process(IRow input, IUpdatableRow output)
				{
		
					string UserID = input.Get<string>("UserID");
					string Name = input.Get<string>("Name");
					string Address = input.Get<string>("Address");
					string City = input.Get<string>("City");
					string State = input.Get<string>("State");
					string PostalCode = input.Get<string>("PostalCode");
					string Country = input.Get<string>("Country");
					string Phone = input.Get<string>("Phone");
		
					if (CountryTranslation.Keys.Contains(Country))
					{
						Country = CountryTranslation[Country];
					}
					output.Set<string>(0, UserID);
					output.Set<string>(1, Name);
					output.Set<string>(2, Address);
					output.Set<string>(3, City);
					output.Set<string>(4, State);
					output.Set<string>(5, PostalCode);
					output.Set<string>(6, Country);
					output.Set<string>(7, Phone);
		
					return output.AsReadOnly();
				}
			}
		}

5. Aprire Script.usql e incollare lo script di U-SQL seguente:

		@drivers =
			EXTRACT UserID      string,
					Name        string,
					Address     string,
					City        string,
					State       string,
					PostalCode  string,
					Country     string,
					Phone       string
			FROM "/Samples/Data/AmbulanceData/Drivers.txt"
			USING Extractors.Tsv(Encoding.Unicode);
		
		@drivers_CountryName =
			PROCESS @drivers
			PRODUCE UserID string,
					Name string,
					Address string,
					City string,
					State string,
					PostalCode string,
					Country string,
					Phone string
			USING new USQL_UDO.CountryName();    
		
		OUTPUT @drivers_CountryName
			TO "/Samples/Outputs/Drivers.csv"
			USING Outputters.Csv(Encoding.Unicode);

6. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Script.usql** e quindi fare clic su **Compilazione dello script**.
6. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Script.usql** e quindi fare clic sul pulsante per l'**invio dello script**.
7. Se non ci si è ancora connessi alla sottoscrizione di Azure, verrà richiesto di immettere le credenziali dell'account Azure.
7. Fare clic su **Submit**. Al termine della procedura di invio, nella finestra dei risultati saranno disponibili i risultati dell'operazione di invio e il collegamento al processo.
8. Per visualizzare lo stato attuale del processo, è necessario fare clic sul pulsante Aggiorna per aggiornare la schermata.

**Per visualizzare l'output del processo**

1. Da **Esplora server** espandere **Azure**, quindi **Analisi Data Lake**, l'account di Analisi Data Lake personale e infine **Account di archiviazione**. Fare doppio clic sull'archiviazione predefinita e quindi fare clic su **Esplora**.
2. Espandere esempi, espandere gli output e quindi fare doppio clic su **Drivers.csv**.


##Vedere anche

- [Introduzione ad Analisi Data Lake di Azure mediante Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Introduzione ad Analisi Data Lake mediante il portale di Azure](data-lake-analytics-get-started-portal.md)
- [Utilizzare gli strumenti Data Lake per Visual Studio per lo sviluppo di applicazioni U-SQL](data-lake-analytics-data-lake-tools-get-started.md)

<!---HONumber=AcomDC_0914_2016-->