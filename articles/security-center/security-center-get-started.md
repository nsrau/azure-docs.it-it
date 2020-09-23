---
title: Eseguire l'aggiornamento ad Azure Defender - Centro sicurezza di Azure
description: Questo argomento di avvio rapido illustra come eseguire l'aggiornamento ad Azure Defender del Centro sicurezza per aumentare la sicurezza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: e51d0bfb79eab4db9bb571cc0f4ee70ada352d92
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90895779"
---
# <a name="quickstart-setting-up-azure-security-center"></a>Avvio rapido: Configurazione del Centro sicurezza di Azure

Il Centro sicurezza di Azure fornisce la gestione unificata della sicurezza e la protezione dalle minacce per carichi di lavoro cloud ibridi. Mentre le funzionalità gratuite offrono sicurezza limitata solo per le risorse di Azure, l'abilitazione di Azure Defender estende queste funzionalità all'ambiente locale e ad altri ambienti cloud. Azure Defender consente di individuare e risolvere le vulnerabilità di sicurezza, di applicare i controlli su applicazioni e accessi per bloccare le attività dannose, di rilevare le minacce usando funzioni di analisi e di intelligenza e di rispondere rapidamente in caso di attacco. È possibile provare Azure Defender gratuitamente. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).

Questo articolo descrive come eseguire l'aggiornamento ad Azure Defender per aumentare la sicurezza e come installare l'agente di Log Analytics nei computer per monitorare le vulnerabilità della sicurezza e le minacce.

## <a name="prerequisites"></a>Prerequisiti
Per iniziare a usare Centro sicurezza, è necessario avere una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

Per abilitare Azure Defender in una sottoscrizione, è necessario avere il ruolo di proprietario della sottoscrizione, collaboratore della sottoscrizione o amministratore della sicurezza.


## <a name="open-security-center-for-the-first-time"></a>Aprire il Centro sicurezza per la prima volta

1. Accedere al [portale di Azure](https://azure.microsoft.com/features/azure-portal/).

1. Nel menu del portale selezionare **Centro sicurezza**. 

    Si aprirà la pagina di panoramica del Centro sicurezza.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Dashboard di panoramica del Centro sicurezza" lightbox="./media/security-center-get-started/overview.png":::

**Centro di sicurezza - Panoramica** offre una visualizzazione unificata del comportamento di sicurezza dei carichi di lavoro cloud ibridi, consentendo all'utente di individuarne e valutarne la sicurezza e di identificare e ridurre i rischi. Il Centro sicurezza abilita automaticamente, senza alcun costo, una sottoscrizione di Azure di cui non sia stato eseguito l'onboarding in precedenza da uno degli utenti della sottoscrizione.

Per visualizzare e filtrare l'elenco delle sottoscrizioni, selezionare la voce di menu **Sottoscrizioni**. Il Centro sicurezza modificherà la visualizzazione in modo da riflettere il comportamento di sicurezza delle sottoscrizioni selezionate. 

Dopo pochi minuti dal primo avvio del Centro di sicurezza, è possibile visualizzare gli elementi seguenti:

- **Suggerimenti** per individuare i modi per migliorare la sicurezza delle risorse connesse.
- Un inventario delle risorse attualmente valutate dal Centro sicurezza, con il comportamento di sicurezza di ognuna.

Per sfruttare al meglio il Centro sicurezza, è necessario completare i passaggi seguenti per abilitare Azure Defender e installare l'agente di Log Analytics.


## <a name="enable-azure-defender"></a>Abilitare Azure Defender

Ai fini delle guide di avvio rapido e delle esercitazioni sul Centro sicurezza, è necessario abilitare Azure Defender. È disponibile una versione di valutazione gratuita valida 30 giorni. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/). 

1. Nella barra laterale del Centro sicurezza selezionare **Attività iniziali**.

    :::image type="content" source="./media/security-center-get-started/get-started-upgrade-tab.png" alt-text="Scheda Aggiorna della pagina introduttiva"::: 

    La scheda **Aggiorna** contiene un elenco di sottoscrizioni e aree di lavoro idonee per l'onboarding.

1. Nell'elenco **Selezionare le aree di lavoro in cui abilitare Azure Defender** selezionare le aree di lavoro da aggiornare.
   - Se si selezionano sottoscrizioni e aree di lavoro non idonee per la versione di valutazione, con il passaggio successivo verranno aggiornate e inizieranno a essere applicati gli addebiti.
   - Se si seleziona un'area di lavoro idonea per una versione di valutazione gratuita, il passaggio successivo inizierà una versione di valutazione.
1. Selezionare **Aggiorna** per abilitare Azure Defender.

## <a name="enable-automatic-data-collection"></a>Abilitare la raccolta dati automatica
Il Centro sicurezza raccoglie i dati dai computer per monitorare le minacce e le vulnerabilità della sicurezza. I dati vengono raccolti tramite l'agente di Log Analytics, che legge diverse configurazioni correlate alla sicurezza oltre ai log eventi del computer e copia i dati nell'area di lavoro per eseguire l'analisi. Per impostazione predefinita, il Centro sicurezza crea una nuova area di lavoro per l'utente.

Se è abilitato il provisioning automatico, il Centro sicurezza installa l'agente di Log Analytics in tutti i computer supportati e in quelli nuovi che vengono creati. È consigliabile abilitare il provisioning automatico.

Per abilitare il provisioning automatico dell'agente di Log Analytics:

1. Scegliere **Prezzi e impostazioni** dal menu del Centro sicurezza.
1. Selezionare la sottoscrizione pertinente.
1. Nella pagina **Raccolta dati** impostare **Provisioning automatico** su **Sì**.
1. Selezionare **Salva**.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Abilitare il provisioning automatico dell'agente di Log Analytics":::

>[!TIP]
> Se è necessario eseguire il provisioning di un'area di lavoro, l'installazione dell'agente potrebbe richiedere fino a 25 minuti.

Una volta distribuito l'agente nei computer, il Centro sicurezza può offrire altri suggerimenti correlati allo stato di aggiornamento del sistema, alle configurazioni di sicurezza del sistema operativo e alla protezione di endpoint, nonché generare avvisi di sicurezza aggiuntivi.

>[!NOTE]
> Se si imposta il provisioning automatico su **No**, l'agente di Log Analytics non viene rimosso dalle macchine virtuali di Azure in cui ne è già stato effettuato il provisioning. La disabilitazione automatica del provisioning limita il monitoraggio delle risorse.



## <a name="next-steps"></a>Passaggi successivi
In questo argomento di avvio rapido è stato abilitato Azure Defender ed è stato effettuato il provisioning dell'agente di Log Analytics per la gestione unificata della sicurezza e della protezione dalle minacce nei vari carichi di lavoro cloud ibridi. Per altre informazioni su come usare il Centro sicurezza, continuare con le indicazioni presenti nella Guida di avvio rapido per l'onboarding di computer Windows in locale o in altri ambienti cloud.

> [!div class="nextstepaction"]
> [Avvio rapido: Eseguire l'onboarding di computer non di Azure](quickstart-onboard-machines.md)

Si vuole ottimizzare e risparmiare sulla spesa per il cloud?

> [!div class="nextstepaction"]
> [Per iniziare, analizzare i costi con Gestione costi](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
