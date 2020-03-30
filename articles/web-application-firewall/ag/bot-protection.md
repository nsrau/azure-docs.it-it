---
title: Configurare la protezione dei bot per Il firewall delle applicazioni Web di AzureConfigure bot protection for Azure Web Application Firewall (WAF)
description: Informazioni su come configurare la protezione dei bot per Web Application Firewall (WAF) nel gateway applicazione di Azure.Learn how to configure bot protection for Web Application Firewall (WAF) on Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73516864"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Configurare la protezione dei bot per Il firewall applicazione Web nel gateway applicazione di Azure (anteprima)Configure bot protection for Web Application Firewall on Azure Application Gateway (Preview)

Questo articolo illustra come configurare una regola di protezione bot in Firewall applicazione Web di Azure per Il gateway applicazione tramite il portale di Azure.This article shows you how to configure a bot protection rule in Azure Web Application Firewall (WAF) for Application Gateway using the Azure portal. 

È possibile abilitare un set di regole di protezione bot gestito per il WAF per bloccare o registrare le richieste provenienti da indirizzi IP dannosi noti. Gli indirizzi IP hanno origine dal feed di Microsoft Threat Intelligence. La soluzione Microsoft Threat Intelligence, basata su Intelligent Security Graph, viene usata da più servizi, tra cui Centro sicurezza di Azure.

> [!NOTE]
> Il set di regole di protezione del bot è attualmente in anteprima pubblica e viene fornito con un contratto di servizio di anteprima. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per informazioni dettagliate, vedere [Le Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure.See the Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for details.

## <a name="prerequisites"></a>Prerequisiti

Creare criteri WAF di base per il gateway applicazione seguendo le istruzioni descritte in [Create Web Application Firewall policies for Application Gateway](create-waf-policy-ag.md).

## <a name="enable-bot-protection-rule-set"></a>Abilitare il set di regole di protezione del botEnable bot protection rule set

1. Nella pagina **Criteri di base** creata in precedenza, in **Impostazioni**, selezionare **Regole**.  

2. Nella sezione **Gestisci regole** della pagina dei dettagli selezionare la casella di controllo relativa alla regola di protezione del bot dal menu a discesa e quindi scegliere **Salva.**

> [!div class="mx-imgBorder"]
> ![Protezione dei bot](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle regole personalizzate, vedere [Regole personalizzate per Web Application Firewall v2 nel gateway applicazione di Azure.For](custom-waf-rules-overview.md)more information about custom rules, see Custom rules for Web Application Firewall v2 on Azure Application Gateway .