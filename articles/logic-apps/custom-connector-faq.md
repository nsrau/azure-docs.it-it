---
title: Domande frequenti sui connettori personalizzati - App per la logica di Azure | Microsoft Docs
description: Domande frequenti su requisiti, trigger e altri aspetti della creazione di connettori personalizzati
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 225e56de3985acae871ddec447b763e7de61cb80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="faq-custom-connectors"></a>Domande frequenti: connettori personalizzati

## <a name="requirements"></a>Requisiti

**D:** è possibile creare un connettore senza le API REST? </br>
**R:** no, per creare un connettore, è necessario supportare API REST HTTP stabili per il servizio. 

**D:** quali strumenti è possibile usare per creare un connettore? </br>
**R:** Azure offre funzionalità e servizi che è possibile usare per l'esposizione di qualsiasi servizio come API, ad esempio il Servizio app di Azure per l'hosting, Gestione API e così via.

**D:** quali tipi di autenticazione sono supportati? </br>
**R:** è possibile usare questi standard di autenticazione supportati:

* [OAuth 2.0](https://oauth.net/2/), inclusi [Azure Active Directory](https://azure.microsoft.com/develop/identity/) o servizi specifici, ad esempio Dropbox, GitHub e a SalesForce
* OAuth 2.0 generica
* [Autenticazione di base](https://swagger.io/docs/specification/authentication/basic-authentication/)
* [Chiave API](https://swagger.io/docs/specification/authentication/api-keys/)

## <a name="triggers"></a>Trigger

**D:** è possibile creare trigger senza webhook? </br>
**R:** no, i connettori personalizzati per App per la logica di Azure e Microsoft Flow supportano solo trigger basati su webhook. Se si vuole richiedere altri modelli per l'implementazione, contattare [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com) con altri dettagli sull'API.

## <a name="certification"></a>Certificazione

**D**: i connettori possono essere creati anche da sviluppatori non partner Microsoft o non fornitori di software indipendenti? </br>
**Oggetto**: sì, è possibile registrare questi connettori per l'uso all'interno dell'organizzazione, ma se si vuole certificare e rilasciare pubblicamente un connettore, è necessario essere il proprietario del servizio sottostante o presentare diritti espliciti per l'uso dell'API.

## <a name="other"></a>Altre

**D:** come si possono implementare API che usano un host dinamico con OpenAPI? </br>
**R:** i connettori personalizzati non supportano host dinamici. Usare invece un host statico per scopi di sviluppo e test. Se si vuole certificare il connettore, richiedere informazioni sull'implementazione dinamica al contatto Microsoft di riferimento.

**D:** è supportato Postman Collection V2? </br>
**R:** no, è attualmente supportata solo la versione 1 di Postman Collection.

**D:** è supportato OpenAPI 3.0? </br>
**R:** no, è attualmente supportato solo OpenAPI 2.0.