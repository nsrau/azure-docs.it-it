---
title: Modello di onboarding del servizio peering di Azure
description: Informazioni su come eseguire l'onboarding del servizio peering di Azure
services: peering-service
documentationcenter: na
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 7a257b4c2bea3bd3384a55c0a6b85d7fdd2ca583
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871401"
---
# <a name="onboarding-peering-service-model"></a>Modello del servizio di peering di onboarding

Il processo di onboarding del servizio di peering è costituito da due modelli, come indicato di seguito:

 - Connessione del servizio di peering di onboarding

 - Telemetria di connessione al servizio di peering di onboarding

I piani di azione per i modelli elencati in precedenza sono descritti di seguito:

| **Step** | **Azione**| **Componenti**|
|-----------|---------|---------|---------|
|1|Cliente per effettuare il provisioning della connettività da un partner di connettività (nessuna interazione con Microsoft). |Un provider Internet che è ben connesso a Microsoft e soddisfa i requisiti tecnici per una connettività efficiente e affidabile a Microsoft.  |
|2 (facoltativo)|Il cliente registra le località nel portale di Azure. Un percorso è definito da: nome ISP/IXP, posizione fisica del sito del cliente (livello di stato), prefisso IP assegnato al percorso dal provider di servizi o dall'azienda.  |Telemetria: monitoraggio della Route Internet, assegnazione di priorità al traffico da Microsoft alla località POP Edge più vicina dell'utente. |



## <a name="onboarding-peering-service-connection"></a>Connessione del servizio di peering di onboarding

Per caricare la connessione al servizio di peering, eseguire le operazioni seguenti:

Collaborare con il provider di servizi Internet (ISP) o il partner Internet Exchange (IX) per ottenere il servizio di peering per connettere la rete alla rete Microsoft.

Assicurarsi che i [provider di connettività](location-partners.md) collaborino con Microsoft per il servizio di peering. 

## <a name="onboarding-peering-service-connection-telemetry"></a>Telemetria di connessione al servizio di peering di onboarding

I clienti possono optare per la telemetria, ad esempio l'analisi della route BGP, per monitorare le prestazioni e la latenza di rete durante l'accesso alla rete Microsoft. Questa operazione può essere eseguita registrando la connessione nel portale di Azure.

Per caricare i dati di telemetria delle connessioni al servizio di peering, il cliente deve registrare la connessione del servizio di peering nel portale di Azure. Per informazioni sulla procedura, fare riferimento al [servizio di peering di registrazione-portale di Azure](azure-portal.md) .

In seguito, è possibile misurare la telemetria facendo riferimento [qui](measure-connection-telemetry.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sul processo di registrazione della connessione al servizio di peering, vedere [registrare il servizio di peering-portale di Azure](azure-portal.md).

Per informazioni sui dati di telemetria della connessione di misura, vedere [misurare la telemetria della connessione](measure-connection-telemetry.md).
