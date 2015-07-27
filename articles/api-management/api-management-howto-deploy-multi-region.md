<properties
	pageTitle="Come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure"
	description="Informazioni su come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure." 
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="sdanie"/>

# Come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure

Gestione API supporta la distribuzione in più aree che consente agli autori di API di distribuire un solo servizio Gestione API in qualsiasi numero di aree di Azure. Ciò consente di ridurre la latenza delle richieste percepita dagli utenti dell'API distribuiti geograficamente, oltre a migliorare la disponibilità del servizio se un'area viene portata offline.

Quando un servizio di gestione API viene creato, inizialmente contiene una sola [unità][] e si trova in una sola area di Azure, designata come area primaria. È possibile aggiungere facilmente altre aree tramite il portale di Azure. Il server proxy di gestione API viene distribuito in ogni area il traffico delle chiamate verrà indirizzato al proxy più vicino. Quando un'area passa offline, il traffico viene automaticamente reindirizzato al proxy successivo più vicino.

> [AZURE.IMPORTANT]La distribuzione in più aree è disponibile solo nel livello **[Premium][]**.

## <a name="add-region"></a>Distribuire un'istanza del servizio Gestione API in una nuova area

Per iniziare, fare clic su **Gestisci** nel portale di Azure per il servizio Gestione API. Verrà visualizzato il portale di pubblicazione di Gestione API.

![Portale di pubblicazione][api-management-management-console]

>Se non è stato creata un'istanza del servizio Gestione API, vedere [Creare un'istanza del servizio Gestione API][] nell'esercitazione [Introduzione a Gestione API di Azure][].

Passare alla scheda **Scalabilità** nel portale di Azure per l'istanza del servizio di Gestione API.

![Scheda Scalabilità][api-management-scale-service]

Per la distribuzione in una nuova area, fare clic sull'elenco a discesa sotto l'area primaria e selezionare un'area nell'elenco.

![Aggiungere un'area][api-management-add-region]

Una volta selezionata l'area, selezionare il numero di unità per la nuova area nell'elenco a discesa delle unità.

![Specificare le unità][api-management-select-units]

Dopo avere configurato le aree e le unità desiderate, fare clic su **Salva**.

## <a name="remove-region"> </a>Eliminare un'istanza di Gestione API da un'area

Per rimuovere un'istanza del servizio Gestione API da un'area, passare alla scheda **Scalabilità** nel portale di Azure per l'istanza del servizio Gestione API.

![Scheda Scalabilità][api-management-scale-service]

Fare clic su **X** a destra dell'area da rimuovere.

![Rimuovere un'area][api-management-remove-region]

Dopo aver rimosso le aree desiderate, fare clic su **Salva**.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Creare un'istanza del servizio Gestione API]: api-management-get-started.md#create-service-instance
[Introduzione a Gestione API di Azure]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unità]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

<!---HONumber=July15_HO3-->