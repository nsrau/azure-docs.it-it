<properties title="Add Azure SQL DB Elastic Scale References to a Visual Studio Project" pageTitle="Aggiungere i riferimenti di scalabilità elastica DB di SQL Azure a un progetto di Visual Studio" description="Informazioni su come aggiungere riferimenti .NET per API Elastic Scale a progetti Visual Studio usando Nuget." metaKeywords="Azure SQL Database, elastic scale, Nuget references" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Procedura: Aggiungere i riferimenti di scalabilità elastica DB di SQL Azure a un progetto di Visual Studio 

###Prerequisiti: 

- Installare la [Raccolta estensioni NuGet di Visual Studio](http://docs.nuget.org/docs/start-here/installing-nuget) per Visual Studio 

###Per aggiungere un riferimento alla scalabilità elastica in Visual Studio 

1. Creare un nuovo progetto nella finestra di dialogo Nuovo progetto disponibile in**File** --> **Nuovo** --> **Progetto** 
2. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti**, quindi fare clic sull'opzione **Gestisci pacchetti NuGet**
3. Nel menu a sinistra della finestra di dialogo Gestisci pacchetti NuGet selezionare **Online** e quindi **nuget.org** oppure "Tutti" 
4. Nella finestra di dialogo **Cerca Online** digitare **Scalabilità elastica**, selezionare le **librerie client della scalabilità elastica** e fare clic su **Installa**.
4. Esaminare la licenza e fare clic su **I Accetto**. 

I riferimenti di scalabilità elastica del database SQL di Azure sono stati ora aggiunti al progetto. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
