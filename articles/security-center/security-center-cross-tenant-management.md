---
title: Gestione cross-tenant nel Centro sicurezza di Azure Documenti Microsoft
description: Informazioni su come configurare la gestione tra tenant per gestire il livello di sicurezza di più tenant nel Centro sicurezza usando la gestione delle risorse delegate di Azure.Learn how to set up cross-tenant management to manage the security posture of multiple tenants in Security Center using Azure delegated resource management.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: 2aeb2ab4cfb4ed5e8652638aaced320cc7119d3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919482"
---
# <a name="cross-tenant-management-in-security-center"></a>Gestione cross-tenant nel Centro sicurezza

La gestione intertenant consente di visualizzare e gestire il livello di sicurezza di più tenant nel Centro sicurezza sfruttando la gestione delle risorse delegate di [Azure.](../lighthouse/concepts/azure-delegated-resource-management.md) Gestire più tenant in modo efficiente, da un'unica visualizzazione, senza dover accedere alla directory di ogni tenant.

- I fornitori di servizi possono gestire la sicurezza delle risorse, per più clienti, dall'interno del proprio tenant.

- I team di sicurezza delle organizzazioni con più tenant possono visualizzare e gestire la propria sicurezza da un'unica posizione.

## <a name="set-up-cross-tenant-management"></a>Configurare la gestione cross-tenant

Configurare la gestione tra tenant delegando l'accesso alle risorse dei tenant gestiti al proprio tenant usando la gestione delle risorse delegate di [Azure.](../lighthouse/concepts/azure-delegated-resource-management.md)

> [!NOTE]
> La gestione risorse delegate di Azure è uno dei componenti principali di Azure Lighthouse.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Funzionamento della gestione tra tenant nel Centro sicurezza

È possibile esaminare e gestire le sottoscrizioni tra più tenant nello stesso modo in cui si gestiscono più sottoscrizioni in un singolo tenant.

Nella barra dei menu superiore fare clic sull'icona del filtro e selezionare le sottoscrizioni, dalla directory di ogni tenant, che si desidera visualizzare.

  ![Filtrare i tenant](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

Le opinioni e le azioni sono fondamentalmente le stesse. Di seguito sono riportati alcuni esempi:

- **Gestire i criteri**di sicurezza : Da un'unica vista, gestire il livello di sicurezza di molte risorse con [i criteri,](tutorial-security-policy.md)eseguire azioni con consigli di sicurezza e raccogliere e gestire i dati relativi alla sicurezza.
- **Migliorare la sicurezza e**la postura di conformità: la visibilità cross-tenant consente di visualizzare la posizione di sicurezza complessiva di tutti i tenant e dove e come migliorare al meglio il [punteggio sicuro](security-center-secure-score.md) e la [postura](security-center-compliance-dashboard.md) di conformità per ognuno di essi.
- **Correggere i suggerimenti:** monitorare e correggere un [suggerimento](security-center-recommendations.md) per molte risorse da vari tenant contemporaneamente. È quindi possibile affrontare immediatamente le vulnerabilità che presentano il rischio più elevato in tutti gli inquilini.
- **Gestisci avvisi:** consente di rilevare [gli avvisi](security-center-alerts-overview.md) tra i diversi tenant. Intervenire sulle risorse non conformi alle procedure di [correzione utilizzabili.](security-center-managing-and-responding-alerts.md)

- Gestire le funzionalità avanzate di **difesa cloud e altro ancora:** gestire i vari servizi di protezione dalle minacce, ad esempio [l'accesso JIT (Just-In-Time VM),](security-center-just-in-time.md)l'Hardening [della rete adattiva,](security-center-adaptive-network-hardening.md)i [controlli adattivi delle applicazioni](security-center-adaptive-application.md)e altro ancora.
 
## <a name="next-steps"></a>Passaggi successivi
Questo articolo illustra il funzionamento della gestione tra tenant nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Rafforzare la posizione di sicurezza con](security-center-monitoring.md) il Centro sicurezza di Azure: informazioni su come monitorare l'integrità delle risorse di Azure.Microsoft your security posture with Azure Security Center - Learn how to monitor the health of your Azure resources.
* [Domande frequenti sul Centro sicurezza di Azure](faq-general.md) : domande frequenti sull'uso del servizio.
* [Informazioni su Azure Lighthouse negli scenari aziendali:](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise) scopri come Azure Lighthouse può semplificare la gestione cross-tenant all'interno di un'azienda che usa più tenant di Azure AD.