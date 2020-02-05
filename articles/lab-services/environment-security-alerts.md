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
ms.openlocfilehash: 4ca17bece33107de756eb221e14eaab851660a99
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992235"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Avvisi di sicurezza per gli ambienti in Azure DevTest Labs
Questo articolo illustra come visualizzare gli avvisi di sicurezza per gli ambienti in Azure DevTest Labs. 

## <a name="prerequisites"></a>Prerequisiti
Attualmente, è possibile visualizzare gli avvisi di sicurezza solo per gli ambienti distribuiti nel Lab. Per testare o usare questa funzionalità, distribuire un ambiente nel Lab. 

## <a name="view-security-alerts-for-an-environment"></a>Visualizzare gli avvisi di sicurezza per un ambiente

1. Nella home page per il Lab selezionare avvisi di **sicurezza** nel menu a sinistra. Verrà visualizzato il numero di avvisi di sicurezza (alto, medio e basso).

    ![Avvisi di sicurezza-Panoramica](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Fare clic con il pulsante destro del mouse su tre punti (...) nell'ultima colonna e selezionare **Visualizza avvisi di sicurezza**. 

    ![Visualizzare avvisi di sicurezza](./media/environment-security-alerts/view-security-alerts-menu.png)
3. Vengono visualizzati altri dettagli sugli avvisi e le raccomandazioni di Advisor. 

    ![Visualizzare avvisi di sicurezza](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sugli ambienti, vedere gli articoli seguenti:

- [Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurare e usare ambienti pubblici](devtest-lab-configure-use-public-environments.md)
