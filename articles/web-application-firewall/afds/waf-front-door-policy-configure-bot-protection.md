---
title: Configurare la protezione di bot per il Web Application Firewall con lo sportello anteriore di Azure (anteprima)
description: Informazioni sul Web Application Firewall (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 9a8901e3763b10b7ee00c10f4eec08a8325d7dec
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512470"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Configurare la protezione di bot per il Web Application Firewall (anteprima)
Questo articolo illustra come configurare la regola di protezione bot in Web Application Firewall (WAF) di Azure per lo sportello anteriore usando l'interfaccia della riga di comando di Azure, Azure PowerShell o Azure Resource Manager modello.

È possibile abilitare un set di regole di protezione bot gestito per WAF per eseguire azioni personalizzate sulle richieste provenienti da indirizzi IP dannosi noti. Gli indirizzi IP vengono originati dal feed di intelligence per le minacce di Microsoft. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) Power Intelligence per le minacce di Microsoft e viene usato da più servizi, incluso il Centro sicurezza di Azure.

> [!IMPORTANT]
> Il set di regole di protezione da bot è attualmente disponibile in anteprima pubblica e viene fornito con un contratto di servizio in anteprima. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.  Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Creare un criterio WAF di base per la porta anteriore seguendo le istruzioni descritte in [creare un criterio WAF per la porta anteriore di Azure usando il portale di Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Abilita set di regole di protezione bot

1. Nella pagina Criteri di base creata nella sezione precedente, in **Impostazioni**, fare clic su **regole**.
2. Nella pagina dettagli, nella sezione **Gestisci regole** , dal menu a discesa selezionare la casella di controllo davanti alla regola **BotProtection-Preview-0,1**, quindi selezionare **Salva** sopra.
    
   ![Regola di protezione bot](.././media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [monitorare WAF](waf-front-door-monitor.md).
