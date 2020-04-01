---
title: Come integrare l'interfaccia di amministrazione di Windows con il Centro sicurezza di Azure Documenti Microsoft
description: Questo articolo spiega come integrare l'interfaccia di amministrazione di Azure con l'interfaccia di amministrazione di Windows
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: cbbdf724b9d7fe4948553e7526410b994f491b49
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435253"
---
# <a name="integrate-azure-security-center-with-windows-admin-center"></a>Integrazione dell'Interfaccia di sicurezza di Azure con l'interfaccia di amministrazione di WindowsIntegrate Azure Security Center with Windows Admin Center

Interfaccia di amministrazione di Windows è uno strumento di gestione per i server Windows.Windows Admin Center is a management tool for your Windows servers. Si tratta di un'unica posizione in cui gli amministratori di sistema possono accedere alla maggior parte degli strumenti di amministrazione più comunemente utilizzati. Dall'interfaccia di amministrazione di Windows è possibile eseguire l'onboarding dei server locali direttamente nel Centro sicurezza di Azure.From within Windows Admin Center, you can directly onboard your on-prem servers into Azure Security Center. È quindi possibile visualizzare un riepilogo dei consigli e degli avvisi per la sicurezza direttamente nell'esperienza dell'interfaccia di amministrazione di Windows.You can then view a summary of your security recommendations and alerts directly in the Windows Admin Center experience.

> [!NOTE]
> La sottoscrizione di Azure e l'area di lavoro di Log Analytics associata devono entrambi avere il livello standard del Centro sicurezza abilitato per abilitare l'integrazione dell'interfaccia di amministrazione di Windows.Your Azure subscription and the associated Log Analytics workspace need both have Security Center's standard tier enabled in order to enable the Windows Admin Center integration.
> Il livello standard è gratuito per i primi 30 giorni se non è stato usato in precedenza nella sottoscrizione e nell'area di lavoro. Per ulteriori informazioni, vedere la pagina delle [informazioni sui prezzi](security-center-pricing.md).
>

Dopo aver eseguito correttamente l'onboarding di un server dall'interfaccia di amministrazione di Windows all'interfaccia di sicurezza di Azure, è possibile:When you've successfully onboarded a server from Windows Admin Center to Azure Security Center, you can:

* Visualizzare avvisi e suggerimenti per la sicurezza all'interno dell'estensione Centro sicurezza nell'interfaccia di amministrazione di WindowsView security alerts and recommendations inside the Security Center extension in Windows Admin Center
* Visualizzare il livello di sicurezza e recuperare informazioni dettagliate aggiuntive dei server gestiti dell'interfaccia di amministrazione di Windows nel Centro sicurezza all'interno del portale di Azure (o tramite un'API)

Combinando questi due strumenti, il Centro sicurezza diventa il tuo unico riquadro di vetro per visualizzare tutte le informazioni di sicurezza, indipendentemente dalla risorsa: protezione dei server gestiti dall'interfaccia di amministrazione di Windows, delle macchine virtuali e di eventuali carichi di lavoro PaaS aggiuntivi.

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>Onboarding dei server gestiti dell'interfaccia di amministrazione di Windows nel Centro sicurezza

1. Nell'interfaccia di amministrazione di Windows selezionare uno dei server e nel riquadro **Strumenti** selezionare l'estensione Centro sicurezza di Azure:

    ![Azure Security Center extension in Windows Admin Center](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Se il server è già collegato al Centro sicurezza, la finestra di configurazione non verrà visualizzata.

1. Fare clic su **Accedi ad Azure e configurare**.
    ![Onboarding Windows Admin Center extension to Azure Security Center](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Seguire le istruzioni per connettere il server al Centro sicurezza. Dopo aver immesso i dettagli necessari e confermato, il Centro sicurezza inoltre apporta le modifiche di configurazione necessarie per garantire che tutte le condizioni seguenti siano vere:
    * È stato registrato un gateway di Azure.An Azure Gateway is registered.
    * Il server dispone di un'area di lavoro da segnalare e di una sottoscrizione associata.
    * La soluzione Log Analytics di livello standard del Centro sicurezza è abilitata nell'area di lavoro. Questa soluzione offre le funzionalità standard del Centro sicurezza per *tutti i* server e le macchine virtuali che segnalano questa area di lavoro.
    * I prezzi del livello standard del Centro sicurezza per la macchina virtuale sono abilitati nella sottoscrizione.
    * L'agente di Log Analytics viene installato nel server e configurato per il report nell'area di lavoro selezionata. Se il server segnala già in un'altra area di lavoro, è configurato per segnalare anche l'area di lavoro appena selezionata.

    > [!NOTE]
    > La visualizzazione dei suggerimenti potrebbe richiedere del tempo dopo l'onboarding. Infatti, a seconda dell'attività del server, è possibile che non si riceva *alcun* avviso. Per generare avvisi di test per testare il corretto funzionamento degli avvisi, seguire le istruzioni della procedura di [convalida degli avvisi.](security-center-alert-validation.md)


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>Visualizzazione di avvisi e avvisi relativi alla sicurezza nell'interfaccia di amministrazione di Windows

Dopo l'installazione, è possibile visualizzare gli avvisi e i suggerimenti direttamente nell'area Centro sicurezza di Azure dell'interfaccia di amministrazione di Windows.Once onboarded, you can view your alerts and recommendations directly in the Azure Security Center area of Windows Admin Center. Fare clic su un suggerimento o un avviso per visualizzarli nel portale di Azure.Click a recommendation or an alert to view them in the Azure portal. In questo caso, si otterranno ulteriori informazioni e si apprenderà come risolvere i problemi.

[![Consigli e avvisi del Centro sicurezza visualizzati nell'interfaccia di amministrazione di Windows](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Visualizzazione di avvisi e avvisi per la sicurezza per i server gestiti dell'interfaccia di amministrazione di Windows nel Centro sicurezza
Dal Centro sicurezza di Azure:From Azure Security Center:

* Per visualizzare i consigli per la sicurezza per tutti i server dell'interfaccia di amministrazione di Windows, aprire **Calcola & app** e fare clic sulla scheda **Macchine virtuali e computer.** Filtrare l'elenco in base alla risorsa "Server" come illustrato di seguito:To view security recommendations for all your Windows Admin Center servers, open Compute & Apps and click the VMs and Computers tab.

    [![Visualizzare i consigli per la sicurezza per i server gestiti dell'interfaccia di amministrazione di WindowsView security recommendations for Windows Admin Center managed servers](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* Per visualizzare gli avvisi di sicurezza per tutti i server dell'interfaccia di amministrazione di Windows, aprire **Avvisi di sicurezza**. Fare clic su Filtro e verificare che sia selezionato solo "Non Azure":Click **Filter** and ensure **only** "Non-Azure" is selected:

    ![Filtrare gli avvisi di sicurezza per i server gestiti dell'interfaccia di amministrazione di WindowsFilter security alerts for Windows Admin Center managed servers](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![Visualizzare gli avvisi di sicurezza per i server gestiti dell'interfaccia di amministrazione di WindowsView security alerts for Windows Admin Center managed servers](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)