---
title: Personalizzare le regole usando il portale-Web Application Firewall di Azure
description: Questo articolo fornisce informazioni su come personalizzare le regole del Web Application Firewall nel gateway applicazione con il portale di Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048373"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Personalizzare le regole del Web Application Firewall usando il portale di Azure

Il firewall applicazione Web del gateway applicazione Azure (WAF) fornisce la protezione per le applicazioni Web. Queste protezioni vengono fornite dal Set di regole principali (CRS) di Open Web Application Security Project (OWASP). Alcune regole possono generare falsi positivi e bloccare il traffico reale. Per questo motivo, il gateway applicazione offre la possibilità di personalizzare regole e gruppi di regole. Per ulteriori informazioni sulle regole e sui gruppi di regole specifici, vedere l' [elenco delle regole e dei gruppi di regole CRS del Web Application Firewall](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Se il gateway applicazione non sta usando il livello WAF, l'opzione per aggiornare il gateway applicazione al livello WAF, come visualizzato nel riquadro a destra. 

![Abilitare WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Visualizzare le regole e i gruppi di regole

**Visualizzare le regole e i gruppi di regole**
1. Esplorare il gateway applicazione, e quindi selezionare **Web application firewall**.  
2. Selezionare i **criteri di WAF**.
2. Selezionare **regole gestite**.

   Questa visualizzazione mostra una tabella nella pagina di tutti i gruppi di regole forniti con il set di regole selezionato. Vengono selezionate tutte le caselle di controllo della regola.

## <a name="disable-rule-groups-and-rules"></a>Disabilitare le regole e i gruppi di regole

> [!IMPORTANT]
> Prestare attenzione quando si disabilitano le regole o i gruppi di regole. Questo potrebbe esporre un aumento dei rischi per la sicurezza.

Quando si disabilitano le regole, è possibile disabilitare un intero gruppo di regole o regole specifiche in uno o più gruppi di regole. 

**Disabilitare gruppi di regole o regole specifiche**

   1. Cercare le regole o i gruppi di regole che si desidera disabilitare.
   2. Selezionare le caselle di controllo relative alle regole che si desidera disabilitare. 
   3. Selezionare l'azione nella parte superiore della pagina (Abilita/Disabilita) per le regole selezionate.
   2. Selezionare **Salva**. 

![Salvare le modifiche][3]

## <a name="mandatory-rules"></a>Regole obbligatorie

L'elenco seguente contiene le condizioni che determinano il blocco della richiesta da parte di WAF in modalità di prevenzione. In modalità di rilevamento vengono registrate come eccezioni.

Non possono essere configurati o disabilitati:

* Se non si analizza il corpo della richiesta, la richiesta viene bloccata, a meno che l'ispezione del corpo non sia spenta (XML, JSON, dati del modulo)
* La lunghezza dei dati del corpo della richiesta senza file è superiore al limite configurato
* Il corpo della richiesta (inclusi i file) è più grande del limite
* Si è verificato un errore interno nel motore WAF

Specifico CRS 3. x:

* Il Punteggio di anomalie in ingresso ha superato la soglia

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le regole disattivate, viene descritto come visualizzare i log WAF. Per altre informazioni, vedere [Diagnostica del gateway applicazione](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
