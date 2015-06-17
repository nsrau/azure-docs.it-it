<properties 
	pageTitle="Aggiungere i riferimenti di scalabilità elastica DB di SQL Azure a un progetto di Visual Studio" 
	description="Informazioni su come aggiungere riferimenti .NET per API Elastic Scale a progetti Visual Studio usando Nuget." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="stuartozer" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="stuartozer@microsoft.com"/>

# Procedura: Aggiungere i riferimenti di scalabilità elastica DB di SQL Azure a un progetto di Visual Studio 

### Prerequisiti: 

- Installare la [raccolta estensioni NuGet di Visual Studio](http://docs.nuget.org/docs/start-here/installing-nuget) per Visual Studio. 

### Per aggiungere un riferimento alla scalabilità elastica in Visual Studio 

1. Aprire un progetto esistente oppure creare un nuovo progetto mediante la finestra di dialogo Nuovo progetto, a cui si accede da **File** --> **Nuovo** --> **Progetto**. 
2. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti** e scegliere **Gestisci pacchetti NuGet**.
3. Scegliere **Online** e quindi **nuget.org** oppure "Tutto" dal menu sul lato sinistro della finestra Gestisci pacchetti NuGet. 
4. Nella finestra di dialogo di **ricerca online** digitare **Elastic Scale**, selezionare le **librerie client di scalabilità elastica** e fare clic su **Installa**.
4. Esaminare la licenza e fare clic su **Accetto**. 

I riferimenti di scalabilità elastica del database SQL di Azure sono stati ora aggiunti al progetto. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--HONumber=47-->
 