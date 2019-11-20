---
title: Configurare la protezione di bot per WAF con lo sportello anteriore di Azure (anteprima)
description: Informazioni sul Web Application Firewall (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 95660f764d28172ecb55a4952b785fea5f2aa4bb
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186704"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Configurare la protezione di bot per il Web Application Firewall (anteprima)
Questo articolo illustra come configurare la regola di protezione bot in Web Application Firewall (WAF) di Azure per lo sportello anteriore usando l'interfaccia della riga di comando di Azure, Azure PowerShell o Azure Resource Manager modello.

Per WAF è possibile abilitare un set di regole gestito di protezione dai bot per eseguire azioni personalizzate sulle richieste provenienti da indirizzi IP noti come dannosi. Gli indirizzi IP hanno origine dal feed di Microsoft Threat Intelligence. La soluzione Microsoft Threat Intelligence, basata su [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence), viene usata da più servizi, tra cui Centro sicurezza di Azure.

> [!IMPORTANT]
> Il set di regole di protezione dai bot è attualmente disponibile in anteprima pubblica e viene fornito con un contratto di servizio di anteprima. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.  Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>prerequisiti

Creare un criterio WAF di base per la porta anteriore seguendo le istruzioni descritte in [creare un criterio WAF per la porta anteriore di Azure usando il portale di Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Abilita set di regole di protezione bot

1. Nella pagina Criteri di base creata nella sezione precedente, in **Impostazioni**, fare clic su **regole**.
2. Nella pagina dettagli, nella sezione **Gestisci regole** , dal menu a discesa selezionare la casella di controllo davanti alla regola **BotProtection-Preview-0,1**, quindi selezionare **Salva** sopra.
    
   ![Regola di protezione bot](.././media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [monitorare WAF](waf-front-door-monitor.md).
