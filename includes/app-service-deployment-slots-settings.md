---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: 0691b1a531ffebbb2c368bdb37dd4d8025fb4a4e
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623683"
---
Quando si clona la configurazione da un altro slot di distribuzione, la configurazione clonata è modificabile. Alcuni elementi di configurazione seguono il contenuto in uno scambio (non specifico dello slot), mentre altri elementi di configurazione si mantengono nello stesso slot dopo uno scambio (specifico dello slot). Negli elenchi seguenti sono riportate le impostazioni che vengono modificate durante lo scambio degli slot.

**Impostazioni che vengono scambiate**:

* Impostazioni generali, ad esempio versione del Framework, 32/64 bit, Web socket
* Impostazioni app (possono essere configurate per adattarsi a uno slot)
* Stringhe di connessione (possono essere configurate per adattarsi a uno slot)
* Mapping dei gestori
* Impostazioni di monitoraggio e diagnostica
* Certificati pubblici
* Contenuto WebJobs
* Connessioni ibride *
* Integrazione della rete virtuale *
* Endpoint di servizio *
* Rete per la distribuzione di contenuti di Azure *

Le funzionalità contrassegnate con un asterisco (*) sono pianificate per essere rese permanenti nello slot. 

**Impostazioni che non vengono scambiate**:

* Endpoint di pubblicazione
* Nomi di dominio personalizzati
* Certificati privati e associazioni SSL
* Impostazioni scalabilità
* Utilità di pianificazione WebJobs
* Restrizioni IP
* Sempre online
* Impostazioni protocollo (HTTPS, versione TLS, certificati client)
* Impostazioni del log di diagnostica
* Condivisione di risorse tra le origini (CORS)

<!-- VNET and hybrid connections not yet sticky to slot -->