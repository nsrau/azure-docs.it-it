---
title: Configurare la protezione dei bot per il firewall dell'applicazione Web con lo sportello anteriore di Azure (anteprima)Configure bot protection for Web Application Firewall with Azure Front Door (Preview)
description: Imparare Web Application Firewall (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934672"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Configurare la protezione dei bot per Web Application Firewall (anteprima)
Questo articolo illustra come configurare la regola di protezione dei bot in WaF (Web Application Firewall) di Azure per Front Door tramite il portale di Azure.This article shows you how to configure bot protection rule in Azure Web Application Firewall (WAF) for Front Door by using Azure portal. La regola di protezione dei bot può essere configurata anche usando l'interfaccia della riga di comando, Azure PowerShell o il modello di Azure Resource Manager.Bot protection rule can also be configured using CLI, Azure PowerShell, or Azure Resource Manager template.

> [!IMPORTANT]
> Il set di regole di protezione dai bot è attualmente disponibile in anteprima pubblica e viene fornito con un contratto di servizio di anteprima. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.  Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Creare un criterio WAF di base per Front Door seguendo le istruzioni descritte in [Creare un criterio WAF per Lo sportello frontale](waf-front-door-create-portal.md)di Azure usando il portale di Azure.

## <a name="enable-bot-protection-rule-set"></a>Abilitare il set di regole di protezione del botEnable bot protection rule set

Nella pagina **Regole gestite** durante la creazione di un criterio Firewall applicazione Web, prima trovare la sezione Set di regole **gestite,** selezionare la casella di controllo davanti alla regola **Microsoft_BotManager_1.0** dal menu a discesa, quindi selezionare **Revisione e creazione**.

   ![Regola di protezione dei bot](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>Passaggi successivi

- Scopri come [monitorare WAF](waf-front-door-monitor.md).
