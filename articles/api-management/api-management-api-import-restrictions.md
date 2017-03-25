---
title: Limitazioni e problemi noti dell&quot;importazione dell&quot;API di Gestione API di Azure | Documentazione Microsoft
description: Dettagli di problemi noti e limitazioni relative all&quot;importazione in Gestione API di Azure con i formati Open API, WSDL o WADL.
services: api-management
documentationcenter: 
author: mattfarm
manager: vlvinogr
editor: 
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: apipm
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: f941f87019a667deba5ec3e5cd054d04318689db
ms.lasthandoff: 03/10/2017


---
# <a name="api-import-restrictions-and-known-issues"></a>Problemi noti e limitazioni dell'importazione dell'API
## <a name="about-this-list"></a>Informazioni sull'elenco
Mentre viene compiuto ogni sforzo per garantire che l'importazione dell'API in Gestione API di Azure sia la più semplice e priva di problemi possibile, talvolta si impongono limitazioni o si identificano problemi che dovranno essere risolti per poter eseguire correttamente l'importazione. L'articolo illustra questi aspetti, organizzati in base al formato di importazione dell'API.

## <a name="open-api"> </a>Aprire l'API/Swagger
In generale, se si ricevono errori durante l'importazione del documento Open API, assicurarsi che sia stato convalidato: usare la finestra di progettazione nel nuovo portale di Azure (Progettazione - Front End - Aprire l'editor della specifica API) o usare uno strumento di terze parti come <a href="http://www.swagger.io">Swagger Editor</a>.

* **Nome host** è necessario un attributo per il nome host.
* **Percorso base** è necessario un attributo per il percorso base.
* **Schemi** è necessaria una matrice di schemi. 

## <a name="wsdl"> </a>WSDL
I file WSDL vengono usati per generare le API SOAP pass-through o come back-end dell'API SOAP-REST.

* **WSDL:Import**: le API che usano questo attributo non sono attualmente supportate. I clienti devono unire gli elementi importati in un solo documento.
* **Messaggi con più parti**: non sono attualmente supportati.
* **WCF wsHttpBinding**: i servizi SOAP creati con Windows Communication Foundation devono usare basicHttpBinding - wsHttpBinding.
* **MTOM**: i servizi che usano MTOM <em>potrebbero</em> funzionare. Al momento il supporto ufficiale non è previsto.
* Non sono supportati i tipi **ricorsivi** che sono definiti in modo ricorsivo, ad esempio che fanno riferimento a una matrice di se stessi.

## <a name="wadl"> </a>WADL
Attualmente non sono noti problemi di importazione del formato WADL.


[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to cache operation results in Azure API Management]: api-management-howto-cache.md

