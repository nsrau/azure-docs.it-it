---
title: Avvisi di sicurezza per gli ambienti in Azure DevTest Labs
description: In questo articolo viene illustrato come visualizzare gli avvisi di sicurezza per un ambiente in DevTest Labs ed eseguire un'azione appropriata.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588706"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Avvisi di sicurezza per gli ambienti in Azure DevTest Labs
Come utente del lab è ora possibile visualizzare gli avvisi del Centro sicurezza di Azure per gli ambienti lab. Il Centro sicurezza raccoglie, analizza e integra automaticamente i dati di log delle risorse di Azure, della rete e delle soluzioni dei partner connesse, come soluzioni di protezione endpoint e firewall, per rilevare le minacce reali e ridurre i falsi positivi. Il Centro sicurezza visualizza un elenco degli avvisi di sicurezza in ordine di priorità, nonché le informazioni necessarie per analizzare rapidamente il problema e indicazioni per risolvere un attacco. Altre informazioni sugli avvisi di sicurezza nel Centro sicurezza di [Azure.Learn more about security alerts in Azure Security Center](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Prerequisiti
Attualmente, è possibile visualizzare gli avvisi di sicurezza solo per gli ambienti di piattaforma come servizio (PaaS) distribuiti nel lab. Per testare o utilizzare questa funzionalità, [distribuire un ambiente nel lab.](devtest-lab-create-environment-from-arm.md) 

## <a name="view-security-alerts-for-an-environment"></a>Visualizzare gli avvisi di sicurezza per un ambienteView security alerts for an environment

1. Nella home page del lab selezionare Avvisi di **sicurezza** nel menu a sinistra. Verrà visualizzato il numero di avvisi di sicurezza (alto, medio e basso). Ulteriori informazioni su [come vengono classificati gli avvisi.](../security-center/security-center-alerts-overview.md#how-are-alerts-classified)

    ![Avvisi di sicurezza - Panoramica](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Fare clic con il pulsante destro del mouse su tre puntini (...) nell'ultima colonna e selezionare **Visualizza avvisi**di sicurezza . 

    ![Visualizzare avvisi di sicurezza](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Vengono visualizzati ulteriori dettagli sugli avvisi e sui consigli per gli advisor. Altre informazioni sulla [gestione e la risposta agli avvisi](../security-center/security-center-managing-and-responding-alerts.md)di sicurezza nel Centro sicurezza di Azure.Learn more on managing and responding to security alerts in Azure Security Center .

    ![Visualizzare avvisi di sicurezza](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sugli ambienti, vedere gli articoli seguenti:To learn more about environments, see the following articles:

- [Creare ambienti multi-vm e risorse PaaS con i modelli di Azure Resource ManagerCreate multi-vm environments and PaaS resources with Azure Resource Manager templates](devtest-lab-create-environment-from-arm.md)
- [Configurare e usare ambienti pubblici](devtest-lab-configure-use-public-environments.md)
