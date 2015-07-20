<properties 
	pageTitle="Aggiungere i riferimenti di scalabilità elastica DB di SQL Azure a un progetto di Visual Studio" 
	description="Informazioni su come aggiungere riferimenti .NET per API Elastic Scale a progetti Visual Studio usando Nuget." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="sidneyh"/>

# Procedura: aggiungere riferimenti alla libreria client di database elastici in un progetto di Visual Studio 

### Prerequisiti: 

- Installare la [raccolta estensioni NuGet di Visual Studio](http://docs.nuget.org/docs/start-here/installing-nuget) per Visual Studio. 

### Per aggiungere un riferimento alla libreria client dei database elastici in Visual Studio 

1. Aprire un progetto esistente oppure creare un nuovo progetto mediante la finestra di dialogo Nuovo progetto, a cui si accede da **File** --> **Nuovo** --> **Progetto**. 
2. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti** e scegliere **Gestisci pacchetti NuGet**.
3. Scegliere **Online** e quindi **nuget.org** oppure "Tutto" dal menu sul lato sinistro della finestra Gestisci pacchetti NuGet. 
4. Nella finestra di dialogo di **ricerca online** digitare **Elastic Database**, selezionare la **libreria client dei database elastici** e fare clic su **Installa**.

	![Ricerca in linea][1]
4. Esaminare la licenza e fare clic su **Accetto**. 

Il riferimento della libreria client ora è stato aggiunto al progetto.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-add-references-visual-studio/search-online.png
<!--anchors-->

<!---HONumber=July15_HO2-->