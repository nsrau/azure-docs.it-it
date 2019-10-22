---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "71129686"
---
Quando si clona la configurazione da un altro slot di distribuzione, la configurazione clonata è modificabile. Alcuni elementi di configurazione seguono il contenuto in uno scambio (non specifico dello slot), mentre altri elementi di configurazione si mantengono nello stesso slot dopo uno scambio (specifico dello slot). Negli elenchi seguenti sono riportate le impostazioni che vengono modificate durante lo scambio degli slot.

**Impostazioni che vengono scambiate**:

* Impostazioni generali, ad esempio versione del Framework, 32/64 bit, Web socket
* Impostazioni app (possono essere configurate per adattarsi a uno slot)
* Stringhe di connessione (possono essere configurate per adattarsi a uno slot)
* Mapping dei gestori
* Certificati pubblici
* Contenuto WebJobs
* Connessioni ibride *
* Integrazione della rete virtuale *
* Endpoint di servizio *
* Rete per la distribuzione di contenuti di Azure *

Le funzionalità contrassegnate con un asterisco (*) sono pianificate per lo scambio. 

**Impostazioni che non vengono scambiate**:

* Endpoint di pubblicazione
* Nomi di dominio personalizzati
* Certificati non pubblici e impostazioni TLS/SSL
* Impostazioni di scalabilità
* Utilità di pianificazione WebJobs
* Restrizioni IP
* Always On
* Impostazioni del log di diagnostica
* Condivisione di risorse tra le origini (CORS)

> [!NOTE]
> Anche alcune impostazioni di app che si applicano alle impostazioni non scambiate non vengono scambiate. Ad esempio, poiché le impostazioni del log di diagnostica non vengono scambiate, le impostazioni dell'app correlate come `WEBSITE_HTTPLOGGING_RETENTION_DAYS` e `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` non vengono scambiate anche se non vengono visualizzate come impostazioni dello slot.
>
