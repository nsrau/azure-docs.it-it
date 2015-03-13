<properties 
	pageTitle="Pubblicare il servizio Web di Azure Machine Learning in Azure Marketplace | Azure" 
	description="Informazioni su come pubblicare il servizio Web di Azure Machine Learning in Azure Marketplace." 
	services="machine-learning" 
	documentationCenter="" 
	authors="luisca" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/03/2014" 
	ms.author="luisca"/>

# Pubblicare servizi Web di Azure ML in Azure Marketplace 

Contenuto del documento

- [Introduzione]
- [Panoramica della procedura di pubblicazione]
- [Linee guida per la pubblicazione in Azure Marketplace]
- [Opzioni specifiche di Machine Learning] 

<!--Anchors-->
[Introduzione]: #introduction
[Panoramica della procedura di pubblicazione]: #overview-of-the-publishing-process
[Linee guida per la pubblicazione in Azure Marketplace]: #guidelines-for-publishing-to-azure-marketplace
[Opzioni specifiche di Machine Learning]: #machine-learning-specific-options 

## Introduzione

Azure Marketplace consente di pubblicare servizi Web di Machine Learning di Azure sotto forma di servizi a pagamento o gratuiti utilizzabili da clienti esterni. In questo documento viene offerta una panoramica di questa procedura e vengono forniti i collegamenti alle linee guida per iniziare. In questo modo sarà possibile rendere disponibili i servizi Web ad altri sviluppatori che potranno usarli nelle proprie applicazioni.

## Panoramica della procedura di pubblicazione 

I passaggi seguenti consentono di pubblicare un servizio Web Azure ML in Azure Marketplace:

1.	Creare e pubblicare un servizio Web di richiesta/risposta di Azure ML.
2.	Distribuire il servizio in produzione dal portale di gestione di Azure.
3.	Usare l'URL del servizio Web pubblicato per la pubblicazione in Azure Marketplace.
4.	Panoramica della procedura di pubblicazione: http://msdn.microsoft.com/library/azure/hh580725.aspx 
5.	Una volta inviata, l'offerta viene esaminata e deve essere approvata prima che i clienti possano iniziare ad acquistarla. La procedura di pubblicazione può richiedere alcuni giorni lavorativi. Microsoft sta cercando di ridurre il più possibile i tempi di attesa e comunicherà a breve gli eventuali aggiornamenti in merito.

## Linee guida per la pubblicazione in Azure Marketplace

1.	È necessario effettuare la registrazione come editore. Per informazioni dettagliate, vedere: <http://msdn.microsoft.com/library/azure/hh563872.aspx>
2.	È necessario fornire informazioni sull'offerta, incluso un piano tariffario. Decidere se offrire un servizio gratuito o a pagamento. Per informazioni dettagliate, vedere: <http://msdn.microsoft.com/library/azure/hh563873.aspx> 
3.	Nel caso di servizi a pagamento, è necessario fornire i dati per il pagamento, come le coordinate bancarie e le informazioni fiscali. Per informazioni dettagliate, vedere: <http://msdn.microsoft.com/library/azure/hh563873.aspx>

## Opzioni specifiche di Machine Learning


1.	Quando si crea una nuova offerta, selezionare **Servizi dati**, quindi fare clic su **Crea un nuovo servizio dati**. 
 
	![Azure Marketplace][image1]

	<br />

2. Nella scheda **Servizio dati** fare clic su **Servizio Web** come origine dati.

	![Azure Marketplace][image2]

3.	Ottenere l'URL del servizio Web e la chiave API dal portale di gestione di Azure:
	1.	In una finestra o una scheda separata del browser accedere al portale di gestione di Azure ([https://manage.windowsazure.com](https://manage.windowsazure.com)) 
	2.	Selezionare **Machine Learning** nel menu a sinistra.
	3.	Fare clic su **Servizi Web**, quindi selezionare il servizio Web da pubblicare.
	4.	Copiare la **chiave API** in una posizione temporanea, ad esempio nel Blocco note.
	5.	Fare clic sulla **pagina della Guida per l'API** per il servizio di richiesta/risposta.
	6.	Copiare l'**indirizzo dell'endpoint OData** nella posizione temporanea.

	<br />

3.	Nella finestra di dialogo di configurazione del servizio dati del Marketplace incollare l'indirizzo dell'ednpoint OData in **URL servizio**.

	<br />

4. Per l'autenticazione scegliere **Intestazione** come **Schema di autenticazione**.

	- Immettere "Authorization" per **Nome intestazione**.
	- Nel campo **Valore intestazione** immettere "Bearer" (senza le virgolette), seguito da uno spazio, quindi incollare la chiave API.
	- Selezionare la casella di controllo **Questo servizio è OData**.
	- Fare clic su **Test connessione** per testare la connessione.

	<br />

5.	In Categorie:
	- Assicurarsi che l'opzione **Machine Learning** sia selezionata.



[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png

<!--HONumber=46--> 
