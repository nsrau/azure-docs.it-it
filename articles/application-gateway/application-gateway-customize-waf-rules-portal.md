---
title: Personalizzare regole del Web application firewall nel gateway applicazione di Azure - portale | Documentazione Microsoft
description: Questa pagina descrive come personalizzare le regole del Web application firewall nel gateway applicazione con il portale.
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
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: c3e93614f012eecff0e88f5f2ad13db199406f4a
ms.contentlocale: it-it
ms.lasthandoff: 08/04/2017

---

# <a name="customize-web-application-firewall-rules-through-the-portal"></a>Personalizzare le regole del Web application firewall tramite il portale

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Interfaccia della riga di comando di Azure 2.0](application-gateway-customize-waf-rules-cli.md)

Il Web application firewall del gateway applicazione fornisce la protezione per le Applicazioni Web. Queste protezioni vengono fornite dai set di regole CRS dell'OWASP. Alcune regole possono generare falsi positivi e bloccare il traffico reale.  Per questo motivo il gateway applicazione offre la possibilità di personalizzare regole e gruppi di regole in un gateway applicazione abilitato per il web application firewall. Per altre informazioni su regole e gruppi di regole specifici, visitare la pagina relativa a [regole e gruppi di regole CRS del Web application firewall](application-gateway-crs-rulegroups-rules.md)

>[!NOTE]
> Se il gateway applicazione non sta usando il livello WAF, viene visualizzata l'opzione per aggiornare il gateway applicazione al livello WAF, come illustrato nella figura seguente:

![abilitare WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Visualizzare le regole e i gruppi di regole

Passare a un gateway applicazione e selezionare **Web application firewall**.  Fare clic su **Advanced rule configuration** (Configurazione regole avanzata).  Questa visualizzazione mostra una tabella nella pagina di tutti i gruppi di regole forniti con il set di regole selezionato.

![configurare regole disabilitate][1]

## <a name="search-for-rules-to-disable"></a>Ricerca di regole da disabilitare

Il pannello delle impostazioni del Web application firewall offre la possibilità di filtrare le regole in base a una ricerca di testo. Il risultato visualizza solo le regole e i gruppi di regole contenenti il testo ricercato.

![cercare le regole][2]

## <a name="disable-rule-groups-and-rules"></a>Disabilitare le regole e i gruppi di regole

Quando si disabilitano le regole, è possibile disabilitare un intero gruppo di regole o regole specifiche in uno o più gruppi di regole.  Dopo avere deselezionato le regole da disabilitare, fare clic su **Salva**.  Questo passaggio salva le modifiche nel gateway applicazione.

![salvare le modifiche][3]

## <a name="next-steps"></a>Passaggi successivi

Una volta configurate le regole disabilitate, informazioni su come visualizzare i log del WAF sono disponibili nella sezione relativa alla [diagnostica del gateway applicazione](application-gateway-diagnostics.md#diagnostic-logging)

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

