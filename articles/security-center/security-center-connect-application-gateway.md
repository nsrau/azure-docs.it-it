---
title: Connessione del gateway applicazione di Microsoft Azure al Centro sicurezza di Azure| Microsoft Docs
description: Informazioni su come integrare il gateway applicazione e il Centro sicurezza di Azure per migliorare la sicurezza complessiva delle risorse.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2018
ms.author: terrylan
ms.openlocfilehash: 7c15e5a86df7ff2a374aa9b62d2775b1eb035fc6
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="connecting-microsoft-azure-application-gateway-to-azure-security-center"></a>Connessione del gateway applicazione di Microsoft Azure al Centro sicurezza di Azure
Questo documento illustra come configurare l'integrazione con il web application firewall (WAF) del gateway applicazione e con il Centro sicurezza.

## <a name="why-connect-application-gateway"></a>Perché connettere il gateway applicazione?
Il WAF nel gateway applicazione protegge le applicazioni Web dai comuni attacchi basati sul Web, come gli attacchi SQL injection, gli attacchi di scripting intersito e il controllo delle sessioni. Il Centro sicurezza si integra con il gateway applicazione per prevenire e rilevare le minacce ad applicazioni Web non protette nell'ambiente.

## <a name="how-do-i-configure-this-integration"></a>Come si configura l'integrazione?
Il Centro sicurezza individua istanze WAF distribuite in precedenza nella sottoscrizione. Connettere queste soluzioni al Centro sicurezza per consentire l'integrazione degli avvisi.

> [!NOTE]
> Il provisioning del WAF del gateway applicazione può essere eseguito anche dalle **Raccomandazioni** del Centro sicurezza, come descritto in [Aggiungere un Web application firewall](security-center-add-web-application-firewall.md).
>
>

1. Accedere al [portale di Azure](https://azure.microsoft.com/features/azure-portal/).

2. Scegliere **Centro sicurezza** dal menu **Microsoft Azure**. Viene visualizzato **Centro sicurezza - Panoramica**.

3. In **Panoramica** selezionare **Soluzioni di sicurezza**.

  ![Panoramica del Centro sicurezza](./media/security-center-connect-application-gateway/overview.png)

4. In **Discovered solutions** (Soluzioni individuate) individuare Microsoft SaaS-based Web Application Firewall e selezionare **CONNECT** (CONNETTI).

  ![Discovered solutions (Soluzioni individuate)](./media/security-center-connect-application-gateway/connect.png)

5. Si aprirà **Connetti la soluzione WAF**.  Selezionare **Connect** (Connetti) per integrare il WAF e il Centro sicurezza.

  ![Connetti la soluzione WAF](./media/security-center-connect-application-gateway/waf-solution.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come integrare il WAF del gateway applicazione nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Integrare soluzioni di sicurezza nel Centro sicurezza](security-center-partner-integration.md)
* [Connessione di Microsoft Advanced Threat Analytics al Centro sicurezza](security-center-ata-integration.md)
* [Connessione di Azure Active Directory Identity Protection al Centro sicurezza](security-center-aadip-integration.md)
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md).
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md).
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md).
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/).
