---
title: Sviluppare operatori U-SQL definiti dall&quot;utente (UDO) | Microsoft Docs
description: 'Informazioni su come sviluppare operatori definiti dall&quot;utente da usare e riutilizzare nei processi di Data Lake Analytics. '
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: ef310a094667f390addd5d0df3dc68d67100d2f4
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017


---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Sviluppare operatori U-SQL definiti dall'utente (UDO)
Informazioni su come sviluppare operatori definiti dall'utente per elaborare i dati in un processo U-SQL.

Per le istruzioni per lo sviluppo di assembly per utilizzo generico per U-SQL, vedere [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md) (Sviluppare assembly U-SQL per i processi di Azure Data Lake Analytics).

## <a name="define-and-use-user-defined-operator-in-u-sql"></a>Definire e usare l'operatore definito dall'utente in U-SQL
**Per creare e inviare un processo di U-SQL**

1. Scegliere **Nuovo** dal menu **File** e quindi fare clic su **Progetto**.
2. Selezionare il tipo **Progetto U-SQL** .

    ![Nuovo progetto Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Fare clic su **OK**. Visual Studio crea una soluzione con un file Script.usql.
4. In **Esplora soluzioni** espandere Script.usql e fare doppio clic su **Script.usql.cs**.
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
6. Aprire **Script.usql** e incollare lo script U-SQL seguente:

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
7. Specificare l'account di Analisi Data Lake, il database e lo schema.
8. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Script.usql** e quindi scegliere **Build Script** (Compila script).
9. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Script.usql** e quindi scegliere **Submit Script** (Invia script).
10. Se non ci si è ancora connessi alla sottoscrizione di Azure, verrà richiesto di immettere le credenziali dell'account Azure.
11. Fare clic su **Submit**. Al termine della procedura di invio, nella finestra dei risultati saranno disponibili i risultati dell'operazione di invio e il collegamento al processo.
12. Per visualizzare lo stato attuale del processo, fare clic sul pulsante **Aggiorna** per aggiornare la schermata.

**Per visualizzare l'output**

1. Da **Esplora server** espandere **Azure**, quindi **Data Lake Analytics**, l'account Data Lake Analytics e infine **Account di archiviazione**. Fare clic con il pulsante destro del mouse su Archivio predefinito e quindi scegliere **Esplora**.
2. Espandere esempi, espandere gli output e quindi fare doppio clic su **Drivers.csv**.

## <a name="see-also"></a>Vedere anche
* [Introduzione ad Analisi Data Lake di Azure mediante Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introduzione ad Analisi Data Lake mediante il portale di Azure](data-lake-analytics-get-started-portal.md)
* [Utilizzare gli strumenti Data Lake per Visual Studio per lo sviluppo di applicazioni U-SQL](data-lake-analytics-data-lake-tools-get-started.md)

