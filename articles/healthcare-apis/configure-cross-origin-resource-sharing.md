---
title: Configurare la condivisione delle risorse tra le origini nell'API di Azure per FHIR
description: Questo articolo descrive come configurare la condivisione delle risorse tra le origini in API di Azure per FHIR.
author: matjazl
ms.author: matjazl
ms.date: 3/11/2019
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: dc4c034b0821f1fe5ecb940591fca77d61edc3ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84870941"
---
# <a name="configure-cross-origin-resource-sharing-in-azure-api-for-fhir"></a>Configurare la condivisione delle risorse tra le origini nell'API di Azure per FHIR

API di Azure per le risorse di interoperabilità sanitaria veloce (FHIR) supporta la [condivisione di risorse tra le origini (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing). CORS consente di configurare le impostazioni in modo che le applicazioni di un dominio (Origin) possano accedere alle risorse da un dominio diverso, noto come richiesta tra domini.

CORS viene spesso usato in un'app a singola pagina che deve chiamare un'API RESTful in un dominio diverso.

Per configurare un'impostazione CORS nell'API di Azure per FHIR, specificare le impostazioni seguenti:

- **Origins (Access-Control-Allow-Origin)**. Elenco di domini autorizzati a eseguire richieste tra le origini per l'API di Azure per FHIR. Ogni dominio (origine) deve essere immesso in una riga separata. È possibile immettere un asterisco (*) per consentire le chiamate da qualsiasi dominio, ma non è consigliabile perché si tratta di un rischio per la sicurezza.

- **Intestazioni (Access-Control-Allow-Headers)**. Elenco di intestazioni che la richiesta di origine conterrà. Per consentire tutte le intestazioni, immettere un asterisco (*).

- **Metodi (Access-Control-Allow-Methods)**. Metodi consentiti (PUT, GET, POST e così via) in una chiamata API. Scegliere **Seleziona tutto** per tutti i metodi.

- **Validità massima (Access-Control-max-age)**. Il valore in secondi per memorizzare nella cache i risultati della richiesta preliminare per Access-Control-Allow-Headers e Access-Control-Allow-methods.

- **Consenti le credenziali (Access-Control-Allow-Credentials)**. Le richieste CORS normalmente non includono cookie per evitare attacchi di [richiesta intersito falsificazione (CSRF)](https://en.wikipedia.org/wiki/Cross-site_request_forgery) . Se si seleziona questa impostazione, la richiesta può includere le credenziali, ad esempio i cookie. Non è possibile configurare questa impostazione se sono già state impostate le origini con un asterisco (*).

![Impostazioni di condivisione risorse tra le origini (CORS)](media/cors/cors.png)

>[!NOTE]
>Non è possibile specificare impostazioni diverse per origini di dominio diverse. Tutte le impostazioni (**intestazioni**, **Metodi**, **validità massima**e **Consenti credenziali**) si applicano a tutte le origini specificate nell'impostazione Origins.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come configurare la condivisione tra le origini in API di Azure per FHIR. Distribuire quindi un'API di Azure completamente gestita per FHIR:
 
>[!div class="nextstepaction"]
>[Distribuire l'API di Azure per FHIR](fhir-paas-portal-quickstart.md)