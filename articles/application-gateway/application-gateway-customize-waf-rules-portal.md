---
title: Personalizzare le regole di web application firewall nel Gateway applicazione di Azure - portale di Azure
description: Questo articolo descrive come personalizzare le regole del web application firewall nel gateway applicazione con il portale di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: f4af52907ab2e950636dea0874b49500f3a6b587
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613447"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Personalizzare le regole del web application firewall con il portale di Azure

Il Web application firewall del gateway applicazione di Azure (WAF) fornisce la protezione per le Applicazioni Web. Queste protezioni vengono fornite dal Set di regole principali (CRS) di Open Web Application Security Project (OWASP). Alcune regole possono generare falsi positivi e bloccare il traffico reale. Per questo motivo, il gateway applicazione offre la possibilità di personalizzare regole e gruppi di regole. Per altre informazioni su regole e gruppi di regole specifici, vedere l'[Elenco di regole e gruppi di regole CRS del Web application firewall](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Se il gateway applicazione non sta usando il livello WAF, l'opzione per aggiornare il gateway applicazione al livello WAF, come visualizzato nel riquadro a destra. 

![Abilitare WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Visualizzare le regole e i gruppi di regole

**Visualizzare le regole e i gruppi di regole**
   1. Esplorare il gateway applicazione, e quindi selezionare **Web application firewall**.  
   2. Selezionare **Advanced rule configuration** (Configurazione regole avanzata).  
   Questa visualizzazione mostra una tabella nella pagina di tutti i gruppi di regole forniti con il set di regole selezionato. Vengono selezionate tutte le caselle di controllo della regola.

![Configurare regole disabilitate][1]

## <a name="search-for-rules-to-disable"></a>Ricerca di regole da disabilitare

Il **impostazioni firewall applicazione Web** pagina offre la possibilità di filtrare le regole tramite una ricerca di testo. Il risultato visualizza solo le regole e i gruppi di regole contenenti il testo ricercato.

![Cercare le regole][2]

## <a name="disable-rule-groups-and-rules"></a>Disabilitare le regole e i gruppi di regole

> [!IMPORTANT]
> Prestare attenzione quando si disabilita tutti i gruppi di regole o regole. Ciò potrebbe esporre è un aumento dei rischi di sicurezza.

Quando si sta disattivando le regole, è possibile disabilitare un intero gruppo di regole o regole specifiche in uno o più gruppi di regole. 

**Disabilitare gruppi di regole o regole specifiche**

   1. Cercare le regole o i gruppi di regole che si desidera disabilitare.
   2. Deselezionare le caselle di controllo per le regole che si desidera disabilitare. 
   2. Selezionare **Salva**. 

![Salvare le modifiche][3]

## <a name="mandatory-rules"></a>Regole obbligatorie

Nell'elenco seguente contiene le condizioni che causano il firewall WAF bloccare la richiesta in modalità di prevenzione. In modalità di rilevamento, si è connessi come eccezioni.

Questi non può essere configurati o disabilitati:

* Impossibile analizzare il corpo della richiesta comporta la richiesta viene bloccata, a meno che non ispezione del corpo è disattivata (XML, JSON, i dati del modulo)
* Lunghezza dei dati del corpo (con nessun file) della richiesta è supera al limite configurato
* Request body (inclusi i file) è superiore al limite
* Si è verificato un errore interno nel motore di Web Application firewall

CRS 3.x specifico:

* Connessioni in entrata soglia superata punteggio delle anomalie

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le regole disattivate, viene descritto come visualizzare i log WAF. Per altre informazioni, vedere [Diagnostica del gateway applicazione](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
