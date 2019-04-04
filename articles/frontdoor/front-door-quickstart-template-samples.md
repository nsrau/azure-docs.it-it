---
title: Esempi di modelli di Azure Resource Manager - Servizio Frontdoor di Azure | Microsoft Docs
description: Esempi di modelli di Azure Resource Manager per il servizio Frontdoor di Azure
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2018
ms.author: sharadag
ms.openlocfilehash: 1d1f7eeb6f55fa19b94a1eb4d05520c76352a414
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756552"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Modello di distribuzione Azure Resource Manager per Frontdoor

La tabella seguente contiene collegamenti ai modelli di distribuzione di Azure Resource Manager per il servizio Frontdoor di Azure. 

| | |
| ---| ---|
| [Creare una frontodoor di base](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic)| Consente di creare una configurazione Frontdoor di base con un singolo back-end. |
| [Creare una fontdoor con più back-end e pool back-end e routing basato su URL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends)| Crea una frontdoor con bilanciamento del carico configurato per più back-end in pool back-end, inclusi i pool back-end basati su percorso URL. |
| [Eseguire l'onboarding di un dominio personalizzato tramite HTTPS (certificato gestito Frontdoor) con Frontdoor](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain)| Aggiungere un dominio personalizzato alla fontdoor e abilitare il traffico HTTPS relativo con un certificato gestito Frontdoor generato tramite DigiCert. |
| [Creare Frontdoor con filtro geografico ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| Creare una frontdoor che consente o blocca il traffico proveniente da determinati paesi. |
| [Controllare probe di integrità per il back-end in Frontdoor](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes)| Aggiornare Frontdoor per modificare le impostazioni di probe di integrità, aggiornando il percorso probe, nonché gli intervalli in cui verranno inviati i probe. |
| [Creare una frontdoor con configurazione back-end attiva e in standby](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb)| Consente di creare una frontdoor che verifica il routing basato su priorità per la topologia dell'applicazione attiva/standby: ciò significa che invia per impostazione predefinita tutto il traffico al back-end primario (con maggiore priorità) fino a quando non diventa disponibile. |
| [Creare una frontdoor con la memorizzazione nella cache abilitata per determinati route](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching)| Crea una frontdoor con memorizzazione nella cache abilitata per la configurazione del routing, memorizzando quindi nella cache qualsiasi risorsa statica per il carico di lavoro. |
| [Configurare l'affinità di sessione per i nomi host di Frontdoor](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) | Aggiorna una frontdoor per abilitare l'affinità di sessione per l'host di front-end; in tal modo, il traffico successivo proveniente dalla stessa sessione utente verrà inviato nello stesso back-end. |
| [Configurare Frontdoor per l'inserimento in whitelist o blacklist di IP client](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)| Configura una frontdoor per limitare il traffico a determinati client IP, usando un controllo di accesso personalizzato tramite IP client. |
| [Configurare Frontdoor per eseguire un'azione con parametri HTTP specifici ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| Configura una frontdoor per consentire o bloccare il traffico in base a parametri http nella richiesta in ingresso tramite regole personalizzate per il controllo dell'accesso con parametri http. |
| [Configurare la limitazione della frequenza di Frontdoor](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| Consente di configurare una frontdoor per limitare la frequenza del traffico in ingresso per un host di front-end specifico. |
| | |

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).