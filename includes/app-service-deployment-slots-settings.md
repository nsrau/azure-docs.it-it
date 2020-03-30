---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "71129686"
---
Quando si clona la configurazione da un altro slot di distribuzione, la configurazione clonata è modificabile. Alcuni elementi di configurazione seguono il contenuto in uno scambio (non specifico dello slot), mentre altri elementi di configurazione rimangono nello stesso slot dopo uno scambio (specifico dello slot). Negli elenchi seguenti sono riportate le impostazioni che vengono modificate durante lo scambio degli slot.

**Impostazioni che vengono scambiate**:

* Impostazioni generali, ad esempio la versione del framework, 32/64 bit, socket Web
* Impostazioni app (possono essere configurate per adattarsi a uno slot)
* Stringhe di connessione (possono essere configurate per adattarsi a uno slot)
* Mapping dei gestori
* Certificati pubblici
* Contenuto WebJobs
* Connessioni ibride
* Integrazione con la rete virtuale
* Endpoint di servizio
* Rete per la distribuzione di contenuti di Azure

È prevista l'unswapped delle feature contrassegnate con un asterisco. 

**Impostazioni che non vengono scambiate**:

* Endpoint di pubblicazione
* Nomi di dominio personalizzati
* Certificati non pubblici e impostazioni TLS/SSL
* Impostazioni di scalabilità
* Utilità di pianificazione WebJobs
* Restrizioni IP
* Always On
* Impostazioni del registro di diagnostica
* Condivisione delle risorse tra origini (CORS)

> [!NOTE]
> Anche alcune impostazioni dell'app che si applicano alle impostazioni non scambiate non vengono scambiate. Ad esempio, poiché le impostazioni del log `WEBSITE_HTTPLOGGING_RETENTION_DAYS` di `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` diagnostica non vengono scambiate, anche le impostazioni dell'app correlate, ad esempio e non vengono scambiate, anche se non vengono visualizzate come impostazioni dello slot.
>
