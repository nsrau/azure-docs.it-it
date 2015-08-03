<properties
   pageTitle="Strumenti di gestione per SQL Data Warehouse | Microsoft Azure"
   description="Introduzione agli strumenti di gestione per SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="HappyNicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/24/2015"
   ms.author="mausher;nicw;barbkess;JRJ@BigBangData.co.uk;"/>

# Strumenti di gestione per SQL Data Warehouse
Questo argomento presenta e confronta gli strumenti e le opzioni per la gestione di SQL Data Warehouse per facilitare la scelta dello strumento più adatto per le proprie esigenze. La scelta dello strumento appropriato dipende dal numero di database gestiti, dall'attività e dalla frequenza con cui viene eseguita un'attività.

## Portale di Azure
Il [portale di Azure][] è un portale di gestione basato sul Web in cui è possibile creare, aggiornare ed eliminare i database e monitorare le risorse di database. Si tratta dello strumento ideale se si sta appena iniziando a usare Azure, se si gestisce un numero ridotto di database del data warehouse o se è necessario operare rapidamente.

Il portale include metriche relative alle impostazioni DWU per le prestazioni correnti e cronologiche, alla quantità di spazio di archiviazione in uso e alle connessioni SQL riuscite o non riuscite, oltre a un set di visualizzazioni e dati che consentono di comprendere le query in esecuzione nell'istanza e i dettagli di tali query.

## SQL Server Data Tools in Visual Studio	
[SQL Server Data Tools][] (SSDT) in Visual Studio è uno strumento client che viene eseguito nel computer locale e consente di connettersi, gestire e sviluppare il database nel cloud. È opportuno che gli sviluppatori di applicazioni con una certa familiarità con Visual Studio o altri ambienti di sviluppo integrato (IDE), provino a usare SSDT in Visual Studio.

SSDT include SQL Server Explorer, che consente di visualizzare, connettersi ed eseguire gli script su database di SQL Data Warehouse. Per connettersi rapidamente a SQL Data Warehouse, è sufficiente fare clic sul pulsate **Apri in Visual Studio** sulla barra dei comandi quando i dettagli del database sono visualizzati nel portale di Azure.

È possibile scaricare la versione più recente di [SQL Server Data Tools][] (SSDT), che include il supporto per SQL Data Warehouse.

## Strumenti da riga di comando
Un'opzione possibile consiste nell'usare gli strumenti da riga di comando sqlcmd o PowerShell per gestire SQL Data Warehouse e automatizzare le distribuzioni di risorse di Azure. È consigliabile servirsi di questi strumenti per gestire un numero elevato di server logici e distribuire le modifiche relative alle risorse in un ambiente di produzione, in quanto è possibile scrivere gli script per le attività necessarie e quindi automatizzare tali attività.

## Passaggi successivi
Per iniziare a usare questi strumenti, vedere l'argomento relativo alla [connessione][].

<!--Image references-->

<!--Article references-->
[connessione]: sql-data-warehouse-develop-connections.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/it-it/library/mt204009.aspx

<!--Other web references-->
[portale di Azure]: http://portal.azure.com/

<!---HONumber=July15_HO4-->