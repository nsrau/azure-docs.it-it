---
title: Personalizzare regole del Web application firewall nel gateway applicazione di Azure - Portale di Azure | Microsoft Docs
description: Questo articolo descrive come personalizzare le regole del web application firewall nel gateway applicazione con il portale di Azure.
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: cdcbadbc3765dfc583c26e1b1453863d421c9a72
ms.contentlocale: it-it
ms.lasthandoff: 08/23/2017

---

# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Personalizzare le regole del web application firewall con il portale di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Interfaccia della riga di comando di Azure 2.0](application-gateway-customize-waf-rules-cli.md)

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

Il pannello delle **Impostazioni del Web application firewall** offre la possibilità di filtrare le regole tramite una ricerca di testo. Il risultato visualizza solo le regole e i gruppi di regole contenenti il testo ricercato.

![Cercare le regole][2]

## <a name="disable-rule-groups-and-rules"></a>Disabilitare le regole e i gruppi di regole

Quando si disabilitano le regole, è possibile disabilitare un intero gruppo di regole o regole specifiche in uno o più gruppi di regole. 

**Disabilitare gruppi di regole o regole specifiche**

   1. Cercare le regole o i gruppi di regole che si desidera disabilitare.
   2. Deselezionare le caselle di controllo per le regole che si desidera disabilitare. 
   2. Selezionare **Salva**. 

![Salvare le modifiche][3]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le regole disattivate, viene descritto come visualizzare i log WAF. Per altre informazioni, vedere [Diagnostica del gateway applicazione](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

