---
title: Terminologia di Gestione API di Azure | Microsoft Docs
description: Questo articolo contiene le definizioni dei termini specifici di Gestione API.
services: api-management
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: 9391b65a5aade4c050ca964354bfea2d3a2338d8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="terminology"></a>Terminologia

Questo articolo contiene le definizioni dei termini specifici di Gestione API.

## <a name="term-definitions"></a>Definizioni dei termini

* **API back-end**: servizio HTTP che implementa l'API e le operazioni. 
* **API front-end**/**API di Gestione API**: un'API di Gestione API non ospita le API, ma crea le facciate per le API per personalizzare la facciata in base alle esigenze senza modificare l'API back-end. Per altre informazioni, vedere [Importare e pubblicare un'API](import-and-publish.md).
* **Prodotto Gestione API**: un prodotto contiene una o più API, oltre a una quota di utilizzo e le condizioni per l'utilizzo. È possibile includere diverse API e proporle agli sviluppatori tramite il portale per sviluppatori. Per altre informazioni, vedere [Creare e pubblicare un prodotto](api-management-howto-add-products.md).
* **Operazione API di Gestione API**: ogni API di Gestione API rappresenta un set di operazioni a disposizione degli sviluppatori. Ogni API di Gestione API contiene un riferimento al servizio back-end che implementa l'API e delle relative operazioni viene eseguito il mapping alle operazioni implementate dal servizio back-end. Per altre informazioni, vedere [Simulare le risposte di un'API](mock-api-responses.md).
* **Versione**: a volte si vogliono pubblicare funzionalità dell'API nuove o diverse destinate ad alcuni utenti, mentre altri preferiscono continuare a usare l'API in uso e funzionante. Per altre informazioni, vedere [Pubblicare più versioni dell'API](api-management-get-started-publish-versions.md).
* **Revisione**: se l'API è pronta e inizia a essere usata dagli sviluppatori, è in genere necessario fare attenzione alle modifiche apportate a tale API e contemporaneamente non interrompere i chiamanti dell'API. È anche utile far conoscere agli sviluppatori le modifiche apportate. Per altre informazioni, vedere [Usare le revisioni](api-management-get-started-revise-api.md).
* **Portale per sviluppatori**: i clienti (sviluppatori) devono usare il portale per sviluppatori per accedere alle API. È possibile personalizzare il portale per sviluppatori. Per altre informazioni, vedere [Personalizzare il portale per sviluppatori](api-management-customize-styles.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'istanza](get-started-create-service-instance.md)

