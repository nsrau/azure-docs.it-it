---
title: Configurare la protezione di bot per il Web Application Firewall con lo sportello anteriore di Azure (anteprima)
description: Informazioni sul Web Application Firewall (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76934672"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Configurare la protezione di bot per il Web Application Firewall (anteprima)
Questo articolo illustra come configurare la regola di protezione bot in Web Application Firewall (WAF) di Azure per lo sportello anteriore usando portale di Azure. È anche possibile configurare la regola di protezione bot usando l'interfaccia della riga di comando, Azure PowerShell o Azure Resource Manager modello.

> [!IMPORTANT]
> Il set di regole di protezione dai bot è attualmente disponibile in anteprima pubblica e viene fornito con un contratto di servizio di anteprima. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.  Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Creare un criterio WAF di base per la porta anteriore seguendo le istruzioni descritte in [creare un criterio WAF per la porta anteriore di Azure usando il portale di Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Abilita set di regole di protezione bot

Nella pagina **regole gestite** quando si crea un criterio di Web Application Firewall, prima di tutto trova la sezione **set di regole gestite** , selezionare la casella di controllo davanti alla regola **Microsoft_BotManager_1.0** dal menu a discesa e quindi selezionare **Verifica + crea**.

   ![Regola di protezione bot](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [monitorare WAF](waf-front-door-monitor.md).
