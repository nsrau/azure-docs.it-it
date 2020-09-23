---
title: Come proteggere i server dell'interfaccia di amministrazione di Windows con il Centro sicurezza di Azure
description: Questo articolo illustra come integrare il Centro sicurezza di Azure con l'interfaccia di amministrazione di Windows
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: memildin
ms.openlocfilehash: 36f519ce41ccfbfb48ca696ed2a61c6131a75998
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906324"
---
# <a name="protect-windows-admin-center-resources-with-security-center"></a>Proteggi le risorse dell'interfaccia di amministrazione di Windows con il Centro sicurezza

Centro di amministrazione di Windows è uno strumento di gestione per i server Windows. Si tratta di un'unica posizione per gli amministratori di sistema per accedere alla maggior parte degli strumenti di amministrazione usati più di frequente. Nell'interfaccia di amministrazione di Windows è possibile caricare direttamente i server locali nel centro sicurezza di Azure. È quindi possibile visualizzare un riepilogo delle raccomandazioni e degli avvisi di sicurezza direttamente nell'esperienza dell'interfaccia di amministrazione di Windows.

> [!NOTE]
> Per abilitare l'integrazione dell'interfaccia di amministrazione di Windows, è necessario che Azure Defender sia abilitato per la sottoscrizione di Azure e per l'area di lavoro Log Analytics associata.
> Azure Defender è gratuito per i primi 30 giorni, se non è stato usato in precedenza nella sottoscrizione e nell'area di lavoro. Per ulteriori informazioni, vedere [la pagina relativa alle informazioni sui prezzi](security-center-pricing.md).
>

Dopo aver caricato un server dall'interfaccia di amministrazione di Windows al centro sicurezza di Azure, è possibile:

* Visualizzare gli avvisi di sicurezza e le raccomandazioni nell'estensione del Centro sicurezza nell'interfaccia di amministrazione di Windows
* Visualizzare il comportamento di sicurezza e recuperare informazioni dettagliate aggiuntive dei server gestiti dall'interfaccia di amministrazione di Windows nel centro sicurezza all'interno del portale di Azure (o tramite un'API)

Combinando questi due strumenti, il Centro sicurezza diventa il singolo riquadro di vetro per visualizzare tutte le informazioni di sicurezza, indipendentemente dalla risorsa: la protezione dei server locali gestiti dall'interfaccia di amministrazione di Windows, delle macchine virtuali e di eventuali carichi di lavoro PaaS aggiuntivi.

## <a name="onboard-windows-admin-center-managed-servers-into-security-center"></a>Caricare i server gestiti dall'interfaccia di amministrazione di Windows nel centro sicurezza

1. Dall'interfaccia di amministrazione di Windows selezionare uno dei server e nel riquadro **strumenti** selezionare l'estensione del Centro sicurezza di Azure:

    ![Estensione del Centro sicurezza di Azure nell'interfaccia di amministrazione di Windows](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Se il server è già caricato nel centro sicurezza, la finestra di configurazione non verrà visualizzata.

1. Fare clic su **Accedi ad Azure e configurare**.
    ![Onboarding dell'estensione dell'interfaccia di amministrazione di Windows al centro sicurezza di Azure](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Seguire le istruzioni per connettere il server al centro sicurezza. Dopo aver immesso i dettagli necessari e confermato, il Centro sicurezza apporta le modifiche di configurazione necessarie per assicurarsi che siano soddisfatte tutte le condizioni seguenti:
    * Un gateway di Azure è registrato.
    * Il server dispone di un'area di lavoro a cui eseguire il report e di una sottoscrizione associata.
    * La soluzione Log Analytics del Centro sicurezza è abilitata nell'area di lavoro. Questa soluzione fornisce le funzionalità di Azure Defender per *tutti i* server e le macchine virtuali che inviano report a questa area di lavoro.
    * Azure Defender per i server è abilitato nella sottoscrizione.
    * L'agente di Log Analytics viene installato nel server e configurato per il report nell'area di lavoro selezionata. Se il server già segnala a un'altra area di lavoro, è configurato in modo da creare un report anche per l'area di lavoro selezionata.

    > [!NOTE]
    > La visualizzazione delle indicazioni potrebbe richiedere del tempo dopo l'onboarding. Infatti, a seconda dell'attività del server, è possibile che non si riceva *alcun* avviso. Per generare avvisi di test per verificare che gli avvisi funzionino correttamente, seguire le istruzioni riportate nella [procedura di convalida degli](security-center-alert-validation.md)avvisi.


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Visualizzare le raccomandazioni e gli avvisi relativi alla sicurezza nell'interfaccia di amministrazione di Windows

Al termine dell'onboarding, è possibile visualizzare gli avvisi e le raccomandazioni direttamente nell'area del Centro sicurezza di Azure dell'interfaccia di amministrazione di Windows. Fare clic su una raccomandazione o un avviso per visualizzarli nel portale di Azure. In questa pagina sono disponibili informazioni aggiuntive e viene illustrato come correggere i problemi.

[![Raccomandazioni e avvisi del Centro sicurezza come visualizzato nell'interfaccia di amministrazione di Windows](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Visualizzare le raccomandazioni e gli avvisi relativi alla sicurezza per i server gestiti dall'interfaccia di amministrazione di Windows nel centro sicurezza
Dal centro sicurezza di Azure:

* Per visualizzare le raccomandazioni sulla sicurezza per tutti i server dell'interfaccia di amministrazione di Windows, aprire [Asset Inventory](asset-inventory.md) e filtrare per il tipo di computer che si desidera analizzare. Selezionare la scheda **VM e computer** .

* Per visualizzare gli avvisi di sicurezza per tutti i server dell'interfaccia di amministrazione di Windows, aprire **avvisi di sicurezza**. Fare clic su **filtro** e verificare che sia selezionato **solo** "non Azure":

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="Filtrare gli avvisi di sicurezza per i server gestiti dall'interfaccia di amministrazione di Windows" lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::