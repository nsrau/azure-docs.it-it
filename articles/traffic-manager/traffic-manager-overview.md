---
title: Gestione traffico di Azure | Microsoft Docs
description: Questo articolo offre una panoramica di Gestione traffico di Azure. È possibile scoprire se si tratta della scelta giusta per il bilanciamento del carico del traffico utente per l'applicazione specifica.
services: traffic-manager
author: asudbring
manager: twooley
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: allensu
ms.openlocfilehash: 95ef493ed44da8fd8372c11f15c3ab712961783e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071016"
---
# <a name="what-is-traffic-manager"></a>Gestione traffico di Azure
Gestione traffico di Azure è un servizio di bilanciamento del carico basato su DNS che consente di distribuire il traffico in modo ottimale ai servizi nelle aree globali di Azure, offrendo al tempo stesso disponibilità e velocità di risposta elevate.

Gestione traffico usa DNS per indirizzare le richieste client all'endpoint di servizio più appropriato, in base a un metodo di routing del traffico e all'integrità degli endpoint. Un endpoint è un servizio con connessione Internet ospitato all'interno o all'esterno di Azure. Gestione traffico offre diversi [metodi di routing del traffico](traffic-manager-routing-methods.md) e [opzioni di monitoraggio degli endpoint](traffic-manager-monitoring.md) per soddisfare le diverse esigenze delle applicazioni e i modelli di failover automatico. Gestione traffico è resiliente agli errori, incluso l'errore di un'intera area di Azure.

>[!NOTE]
> Azure offre una suite di soluzioni di bilanciamento del carico completamente gestite per tutti gli scenari. Per la terminazione del protocollo Transport Layer Security (TLS) ("offload SSL"), la richiesta per HTTP/HTTPS o l'elaborazione a livello di applicazione, vedere [Gateway di applicazione](../application-gateway/application-gateway-introduction.md). Se si vuole ottenere il bilanciamento a livello di area, vedere [Load Balancer](../load-balancer/load-balancer-overview.md). Gli scenari end-to-end potrebbero trarre vantaggio dalla combinazione di queste soluzioni in base alle esigenze.

Gestione traffico offre le funzionalità seguenti:

## <a name="increase-application-availability"></a>Migliorare la disponibilità dell'applicazione

Gestione traffico assicura la disponibilità elevata delle applicazioni essenziali attraverso il monitoraggio degli endpoint e il failover automatico in caso di inattività di un endpoint.
    
## <a name="improve-application-performance"></a>Migliorare le prestazioni dell'applicazione

Azure consente di eseguire servizi cloud o siti Web in datacenter di tutto il mondo. Gestione traffico può migliorare la velocità di risposta delle applicazioni indirizzando il traffico all'endpoint con la minore latenza di rete per il client.

## <a name="perform-service-maintenance-without-downtime"></a>Eseguire la manutenzione dei servizi senza tempi di inattività

È possibile eseguire le operazioni pianificate di manutenzione delle applicazioni senza tempi di inattività. Durante l'esecuzione delle operazioni di manutenzione, Gestione traffico può indirizzare il traffico verso endpoint alternativi.

## <a name="combine-hybrid-applications"></a>Combinare applicazioni ibride

Gestione traffico supporta anche endpoint esterni, non di Azure, e può quindi essere usato con distribuzioni cloud ibride e locali, inclusi gli scenari di tipo "[burst nel cloud](https://azure.microsoft.com/overview/what-is-cloud-bursting/)," "migrazione nel cloud" e "failover nel cloud".

## <a name="distribute-traffic-for-complex-deployments"></a>Distribuire il traffico per distribuzioni complesse

Usando i [profili di Gestione traffico annidati](traffic-manager-nested-profiles.md) è possibile combinare più metodi di routing del traffico per creare regole sofisticare e flessibili in grado di adattarsi alle esigenze di distribuzioni più complesse e di maggiori dimensioni.

## <a name="pricing"></a>Prezzi

Per informazioni sui prezzi, vedere [Prezzi per Gestione traffico](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un profilo di Gestione traffico](traffic-manager-create-profile.md).
- Informazioni sul [funzionamento di Gestione traffico](traffic-manager-how-it-works.md).
- Visualizzare le [domande frequenti](traffic-manager-FAQs.md) su Gestione traffico.




