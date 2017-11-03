---
title: Connessione di Azure Active Directory Identity Protection al Centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come il Centro sicurezza di Azure si integra con Azure Active Directory Identity Protection.
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: 0d4b77c2-dba4-4e46-8f55-ab04ddd92496
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 7562dd5e1c303a6cb97d25bda5aa080bb5643583
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2017
---
# <a name="connecting-azure-active-directory-identity-protection-to-azure-security-center"></a>Connessione di Azure Active Directory Identity Protection al Centro sicurezza di Azure
Questo documento consente di configurare l'integrazione tra Azure Active Directory (AD) Identity Protection e il Centro sicurezza di Azure.

## <a name="why-connect-azure-ad-identity-protection"></a>Perché connettere Azure AD Identity Protection?
[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) consente di rilevare le potenziali vulnerabilità per le identità dell'organizzazione. Quando si è connessi, si è in grado di visualizzare gli avvisi di Azure AD Identity Protection nel Centro sicurezza. Questa integrazione consente di visualizzare, correlare e analizzare tutti gli avvisi di sicurezza relativi a carichi di lavoro di cloud ibrido nel Centro sicurezza. 

## <a name="how-do-i-configure-this-integration"></a>Come si configura l'integrazione?
Se l'organizzazione sta già usando Azure AD Identity Protection, seguire questa procedura per configurare l'integrazione:

1. Aprire il dashboard del **Centro sicurezza**.
2. Nel riquadro sinistro fare clic su **Soluzioni di sicurezza**. Il Centro sicurezza individua automaticamente se Azure AD Identity Protection è abilitato per l'organizzazione.

    ![AADIP](./media/security-center-aadip-integration/security-center-aadip-integration-fig1.png)

3. Fare clic su **CONNETTI**.
4. Nella pagina **Integra Azure AD Identity Protection** scorrere verso il basso e selezionare l'area di lavoro appropriata:

    ![area di lavoro](./media/security-center-aadip-integration/security-center-aadip-integration-fig2.png)

5. Fare clic su **Connetti**.

Dopo aver completato la configurazione, la soluzione Azure AD Identity Protection viene visualizzata nella pagina **Soluzioni di sicurezza** in **Soluzioni connesse**. 

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Azure AD Identity Protection al Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Connecting Microsoft Advanced Threat Analytics to Azure Security Center (Connessione di Microsoft Advanced Threat Analytics al Centro sicurezza di Azure)](security-center-ata-integration.md)
* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni facilitano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare l'integrità delle soluzioni dei partner.
- [Sicurezza dei dati nel Centro sicurezza di Azure](security-center-data-security.md): informazioni sulla gestione e la protezione dei dati nel Centro sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : informazioni e notizie aggiornate sulla sicurezza di Azure.


