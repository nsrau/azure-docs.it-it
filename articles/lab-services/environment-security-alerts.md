---
title: Avvisi di sicurezza per gli ambienti in Azure DevTest Labs
description: Questo articolo illustra come visualizzare gli avvisi di sicurezza per un ambiente in DevTest Labs e intraprendere un'azione appropriata.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2020
ms.author: spelluru
ms.openlocfilehash: fbac5a2fab91cdac8ebf626e324f12f209cfade5
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588706"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Avvisi di sicurezza per gli ambienti in Azure DevTest Labs
Gli utenti del Lab possono ora visualizzare gli avvisi del Centro sicurezza di Azure per gli ambienti Lab. Il Centro sicurezza raccoglie, analizza e integra automaticamente i dati di log delle risorse di Azure, della rete e delle soluzioni dei partner connesse, come soluzioni di protezione endpoint e firewall, per rilevare le minacce reali e ridurre i falsi positivi. Il Centro sicurezza visualizza un elenco degli avvisi di sicurezza in ordine di priorità, nonché le informazioni necessarie per analizzare rapidamente il problema e indicazioni per risolvere un attacco. [Altre informazioni sugli avvisi di sicurezza nel centro sicurezza di Azure](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Prerequisites
Attualmente, è possibile visualizzare gli avvisi di sicurezza solo per gli ambienti di piattaforma distribuita come servizio (PaaS) distribuiti nel Lab. Per testare o usare questa funzionalità, [distribuire un ambiente nel Lab](devtest-lab-create-environment-from-arm.md). 

## <a name="view-security-alerts-for-an-environment"></a>Visualizzare gli avvisi di sicurezza per un ambiente

1. Nella home page per il Lab selezionare avvisi di **sicurezza** nel menu a sinistra. Verrà visualizzato il numero di avvisi di sicurezza (alto, medio e basso). Altre informazioni sul [modo in cui vengono classificati gli avvisi](../security-center/security-center-alerts-overview.md#how-are-alerts-classified).

    ![Avvisi di sicurezza-Panoramica](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Fare clic con il pulsante destro del mouse su tre punti (...) nell'ultima colonna e selezionare **Visualizza avvisi di sicurezza**. 

    ![Visualizzare avvisi di sicurezza](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Vengono visualizzati altri dettagli sugli avvisi e le raccomandazioni di Advisor. Scopri di più sulla [gestione e sulla risposta agli avvisi di sicurezza nel centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md).

    ![Visualizzare avvisi di sicurezza](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sugli ambienti, vedere gli articoli seguenti:

- [Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurare e usare ambienti pubblici](devtest-lab-configure-use-public-environments.md)
