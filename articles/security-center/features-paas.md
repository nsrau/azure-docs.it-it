---
title: Funzionalità del Centro sicurezza di Azure per le risorse PaaS di Azure supportate.
description: Questa pagina mostra la disponibilità delle funzionalità del Centro sicurezza di Azure per le risorse PaaS di Azure supportate.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 77bf0f4cd60ba6e85763334a4d864975945ec2c3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894776"
---
# <a name="feature-coverage-for-azure-paas-services"></a>Copertura delle funzionalità per i servizi PaaS di Azure <a name="paas-services"></a>

La tabella seguente illustra la disponibilità delle funzionalità del Centro sicurezza di Azure per le risorse PaaS di Azure supportate.

|Servizio|Raccomandazioni (gratuito)|Avvisi di sicurezza (Azure Defender)|Valutazione della vulnerabilità (Azure Defender)|
|:----|:----:|:----:|:----:|
|Servizio app di Azure|✔|✔|-|
|Account di automazione di Azure|✔|-|-|
|Account Azure Batch|✔|-|-|
|Archiviazione BLOB di Azure|✔|✔|-|
|Cache Redis di Azure|✔|-|-|
|Servizi cloud di Azure|✔|-|-|
|Ricerca cognitiva di Azure|✔|-|-|
|Registro Azure Container|-|-|✔|
|Azure Cosmos DB*|-|✔|-|
|Azure Data Lake Analytics.|✔|-|-|
|Azure Data Lake Storage|✔|-|-|
|Database di Azure per MySQL *|✔|✔|-|
|Database di Azure per PostgreSQL *|✔|✔|-|
|Spazio dei nomi di hub eventi di Azure|✔|-|-|
|App Funzioni di Azure|✔|-|-|
|Insieme di credenziali chiave di Azure|✔|✔|-|
|Servizio Azure Kubernetes|✔|✔|-|
|Azure Load Balancer|✔|-|-|
|App per la logica di Azure|✔|-|-|
|Database SQL di Azure|✔|✔|✔|
|Istanza gestita di SQL di Azure|✔|✔|✔|
|Spazio dei nomi del bus di servizio di Azure|✔|-|-|
|Account Service Fabric di Azure|✔|-|-|
|Account di archiviazione di Azure|✔|-|-|
|Analisi di flusso di Azure|✔|-|-|
|Sottoscrizione di Azure|✔ **|✔|-|
|Rete virtuale di Azure</br> (incl. subnet, nic e gruppi di sicurezza di rete)|✔|-|-|

\* Queste funzionalità sono attualmente supportate in anteprima.

\*\* Le raccomandazioni di Azure Active Directory (Azure AD) sono disponibili solo per le sottoscrizioni con Azure Defender abilitato.