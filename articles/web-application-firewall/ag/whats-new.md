---
title: Novità di Web Application Firewall di Azure
description: Informazioni sulle novità di Web Application Firewall di Azure, tra cui note sulla versione aggiornate, problemi noti, correzioni di bug, funzionalità deprecate e modifiche imminenti.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 11212c931e38b1c6319c7c3554155d9d17a1080c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745156"
---
# <a name="whats-new-in-azure-web-application-firewall"></a>Novità di Web Application Firewall di Azure

Web Application Firewall di Azure viene aggiornato regolarmente. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate

## <a name="new-features"></a>Nuove funzionalità

|Funzionalità  |Descrizione  |Data di aggiunta  |
|---------|---------|---------|
|Set di regole di mitigazione dei bot (anteprima)|È possibile abilitare un set di regole di mitigazione dei bot, insieme al set di regole CRS scelto. | Novembre 2019 |
|Integrazione di GeoDB (anteprima)|Ora è possibile creare regole personalizzate per limitare il traffico in base al paese/area geografica di origine. | Novembre 2019 |
|Criteri di WAF per sito/per URI (anteprima)|WAF-v2 supporta ora l'applicazione di un criterio ai listener, nonché le regole basate sul percorso. Vedere [Creare criteri di WAF](create-waf-policy-ag.md). | Novembre 2019 |
|Regole personalizzate di WAF |Il gateway applicazione WAF_v2 supporta ora la creazione di regole personalizzate. Vedere [Regole personalizzate del gateway applicazione](custom-waf-rules-overview.md). |Giugno 2019 |
|Configurazione di WAF ed elenco di esclusione     |Sono state aggiunte altre opzioni per configurare la soluzione WAF e ridurre i falsi positivi. Per altre informazioni, vedere [Limiti delle dimensioni di richiesta di Web application firewall ed elenchi di esclusione](application-gateway-waf-configuration.md).|Dicembre 2018|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Web Application Firewall nel gateway applicazione, vedere [Web Application Firewall di Azure nel gateway di applicazione di Azure](ag-overview.md).
