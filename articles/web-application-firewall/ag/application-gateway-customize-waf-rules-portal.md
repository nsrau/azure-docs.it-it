---
title: Personalizzare le regole usando il portale - Firewall applicazione Web di AzureCustomize rules using portal - Azure Web Application Firewall
description: Questo articolo fornisce informazioni su come personalizzare le regole di Web Application Firewall nel gateway applicazione con il portale di Azure.This article provides information on how to customize Web Application Firewall rules in Application Gateway with the Azure portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048373"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Personalizzare le regole di Web Application Firewall tramite il portale di AzureCustomize Web Application Firewall rules using the Azure portal

Il firewall dell'applicazione Web del gateway applicazione di Azure (WAF) fornisce protezione per le applicazioni Web. Queste protezioni vengono fornite dal Set di regole principali (CRS) di Open Web Application Security Project (OWASP). Alcune regole possono generare falsi positivi e bloccare il traffico reale. Per questo motivo, il gateway applicazione offre la possibilità di personalizzare regole e gruppi di regole. Per ulteriori informazioni sui gruppi di regole e sulle regole specifiche, vedere [List of Web Application Firewall CRS rule groups and rules](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Se il gateway applicazione non sta usando il livello WAF, l'opzione per aggiornare il gateway applicazione al livello WAF, come visualizzato nel riquadro a destra. 

![Abilitare WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Visualizzare le regole e i gruppi di regole

**Visualizzare le regole e i gruppi di regole**
1. Esplorare il gateway applicazione, e quindi selezionare **Web application firewall**.  
2. Selezionare i **criteri WAF**.
2. Selezionare **Regole gestite**.

   Questa visualizzazione mostra una tabella nella pagina di tutti i gruppi di regole forniti con il set di regole selezionato. Vengono selezionate tutte le caselle di controllo della regola.

## <a name="disable-rule-groups-and-rules"></a>Disabilitare le regole e i gruppi di regole

> [!IMPORTANT]
> Prestare attenzione quando si disabilitano i gruppi di regole o le regole. Ciò potrebbe esporre l'utente a maggiori rischi per la sicurezza.

Quando si disabilitano le regole, è possibile disabilitare un intero gruppo di regole o regole specifiche in uno o più gruppi di regole. 

**Disabilitare gruppi di regole o regole specifiche**

   1. Cercare le regole o i gruppi di regole che si desidera disabilitare.
   2. Selezionare le caselle di controllo relative alle regole che si desidera disabilitare. 
   3. Selezionare l'azione nella parte superiore della pagina (abilitazione/disabilitazione) per le regole selezionate.
   2. Selezionare **Salva**. 

![Salvare le modifiche][3]

## <a name="mandatory-rules"></a>Regole obbligatorie

L'elenco seguente contiene le condizioni che causano il WAF bloccare la richiesta in modalità Prevenzione. In modalità di rilevamento, vengono registrati come eccezioni.

Questi non possono essere configurati o disabilitati:

* La mancata analisi del corpo della richiesta comporta il blocco della richiesta, a meno che l'ispezione del corpo non sia disattivata (XML, JSON, dati del modulo)
* La lunghezza dei dati del corpo della richiesta (senza file) è maggiore del limite configuratoRequest body data length is larger than the configured limit
* Il corpo della richiesta (inclusi i file) è maggiore del limite
* Si è verificato un errore interno nel motore WAF

Specifico di CRS 3.x:

* Punteggio anomaly in entrata superato soglia

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le regole disattivate, viene descritto come visualizzare i log WAF. Per ulteriori informazioni, vedere [Diagnostica gateway applicazione](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
