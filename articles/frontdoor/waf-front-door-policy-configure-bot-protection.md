---
title: Configurare la protezione di bot per il firewall applicazione web con Azure porta d'ingresso (anteprima)
description: Informazioni su firewall applicazione web (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: af92f3b9049862fc19c69965f979b7dfe8c62526
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481625"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Configurare la protezione di bot per web application firewall (anteprima)
Questo articolo illustra come configurare la regola di protezione bot in firewall applicazione web di Azure (WAF) per la porta Front con modello di comando di Azure, Azure PowerShell o Azure Resource Manager.

Un set di regole di protezione Bot gestito può essere abilitato per il firewall WAF intraprendere azioni personalizzate nelle richieste da indirizzi IP dannosi noti. Gli indirizzi IP sono originati da Microsoft Threat Intelligence feed. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) alla base di intelligence sulle minacce di Microsoft ed è usato da più servizi, tra cui Centro sicurezza di Azure.

> [!IMPORTANT]
> Set di regole di protezione Bot è attualmente in anteprima pubblica e viene fornito con un contratto di servizio di anteprima. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.  Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Creare un criterio di WAF base per la porta d'ingresso seguendo le istruzioni descritte nel [creare un criterio di Web Application firewall per l'ingresso principale di Azure usando il portale di Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Abilita set di regole di protezione di bot

1. Nella pagina dei criteri di base creato nella sezione precedente, nella sezione **le impostazioni**, fare clic su **regole**.
2. Nella pagina dei dettagli sotto il **gestire le regole** sezione, dal menu elenco a discesa, selezionare la casella di controllo davanti la regola **BotProtection-preview-0,1**e quindi selezionare **salvare**sopra.
    
   ![Regola di protezione di BOT](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [monitorare WAF](waf-front-door-monitor.md).
