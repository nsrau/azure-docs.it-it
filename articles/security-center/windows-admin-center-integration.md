---
title: Come integrare l'interfaccia di amministrazione di Windows con il Centro sicurezza di Azure | Microsoft Docs
description: Questo articolo illustra come integrare il Centro sicurezza di Azure con l'interfaccia di amministrazione di Windows
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 842c7c81e3bf9615eb56d50ee2d6fce794845b6e
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960702"
---
# <a name="integrate-azure-security-center-with-windows-admin-center-preview"></a>Integrare il Centro sicurezza di Azure con l'interfaccia di amministrazione di Windows (anteprima)

Centro di amministrazione di Windows è uno strumento di gestione per i server Windows. Si tratta di un'unica posizione per gli amministratori di sistema per accedere alla maggior parte degli strumenti di amministrazione usati più di frequente. Nell'interfaccia di amministrazione di Windows è possibile caricare direttamente i server locali nel centro sicurezza di Azure. È quindi possibile visualizzare un riepilogo delle raccomandazioni e degli avvisi di sicurezza direttamente nell'esperienza dell'interfaccia di amministrazione di Windows.

> [!NOTE]
> Per abilitare l'integrazione dell'interfaccia di amministrazione di Windows, è necessario che sia abilitata la sottoscrizione di Azure e l'area di lavoro Log Analytics associata per il livello standard del Centro sicurezza.
> Il livello standard è gratuito per i primi 30 giorni, se non è stato usato in precedenza nella sottoscrizione e nell'area di lavoro. Per ulteriori informazioni, vedere [la pagina relativa alle informazioni sui prezzi](security-center-pricing.md).
>

Dopo aver caricato un server dall'interfaccia di amministrazione di Windows al centro sicurezza di Azure, è possibile:

* Visualizzare gli avvisi di sicurezza e le raccomandazioni nell'estensione del Centro sicurezza nell'interfaccia di amministrazione di Windows
* Visualizzare il comportamento di sicurezza e recuperare informazioni dettagliate aggiuntive dei server gestiti dall'interfaccia di amministrazione di Windows nel centro sicurezza all'interno del portale di Azure (o tramite un'API)

Combinando questi due strumenti, il Centro sicurezza diventa il singolo riquadro di vetro per visualizzare tutte le informazioni di sicurezza, indipendentemente dalla risorsa: la protezione dei server locali gestiti dall'interfaccia di amministrazione di Windows, delle macchine virtuali e di eventuali carichi di lavoro PaaS aggiuntivi.

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>Caricamento dei server gestiti dal centro di amministrazione di Windows nel centro sicurezza

1. Dall'interfaccia di amministrazione di Windows selezionare uno dei server e nel riquadro **strumenti** selezionare l'estensione del Centro sicurezza di Azure:

    ![Estensione del Centro sicurezza di Azure nell'interfaccia di amministrazione di Windows](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Se il server è già caricato nel centro sicurezza, la finestra di configurazione non verrà visualizzata.

1. Fare clic su **Accedi ad Azure e configurare**.
    ![l'onboarding dell'estensione dell'interfaccia di amministrazione di Windows al centro sicurezza di Azure](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Seguire le istruzioni per connettere il server al centro sicurezza. Dopo aver immesso i dettagli necessari e confermato, il Centro sicurezza apporta le modifiche di configurazione necessarie per assicurarsi che siano soddisfatte tutte le condizioni seguenti:
    * Un gateway di Azure è registrato.
    * Il server dispone di un'area di lavoro a cui eseguire il report e di una sottoscrizione associata.
    * Il livello standard del Centro sicurezza Log Analytics soluzione è abilitato nell'area di lavoro. Questa soluzione fornisce le funzionalità del livello standard del Centro sicurezza per *tutti i* server e le macchine virtuali che inviano report a questa area di lavoro.
    * I prezzi del livello standard del Centro sicurezza per la macchina virtuale sono abilitati nella sottoscrizione.
    * Il Microsoft Monitoring Agent (MMA) viene installato nel server e configurato per il report nell'area di lavoro selezionata. Se il server già segnala a un'altra area di lavoro, è configurato in modo da creare un report anche per l'area di lavoro selezionata.

    > [!NOTE]
    > La visualizzazione delle indicazioni potrebbe richiedere del tempo dopo l'onboarding. Infatti, a seconda dell'attività del server, è possibile che non si riceva *alcun* avviso. Per generare avvisi di test per verificare che gli avvisi funzionino correttamente, seguire le istruzioni riportate nella [procedura di convalida degli](security-center-alert-validation.md)avvisi.


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>Visualizzazione di avvisi e raccomandazioni sulla sicurezza nell'interfaccia di amministrazione di Windows

Al termine dell'onboarding, è possibile visualizzare gli avvisi e le raccomandazioni direttamente nell'area del Centro sicurezza di Azure dell'interfaccia di amministrazione di Windows. Fare clic su una raccomandazione o un avviso per visualizzarli nel portale di Azure. In questa pagina sono disponibili informazioni aggiuntive e viene illustrato come correggere i problemi.

[![raccomandazioni e avvisi del Centro sicurezza come visualizzato nell'interfaccia di amministrazione di Windows](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Visualizzazione di avvisi e raccomandazioni sulla sicurezza per i server gestiti dall'interfaccia di amministrazione di Windows nel centro sicurezza
Dal centro sicurezza di Azure:

* Per visualizzare le raccomandazioni sulla sicurezza per tutti i server dell'interfaccia di amministrazione di Windows, aprire **calcolo & app** e fare clic sulla scheda **VM e computer** . filtrare l'elenco in base alla risorsa "Server", come illustrato di seguito:

    [![visualizzare le raccomandazioni sulla sicurezza per i server gestiti dall'interfaccia di amministrazione di Windows](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* Per visualizzare gli avvisi di sicurezza per tutti i server dell'interfaccia di amministrazione di Windows, aprire **avvisi di sicurezza**. Fare clic su **filtro** e verificare che sia selezionato **solo** "non Azure":

    ![Filtrare gli avvisi di sicurezza per i server gestiti dall'interfaccia di amministrazione di Windows](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![visualizzare gli avvisi di sicurezza per i server gestiti dall'interfaccia di amministrazione di Windows](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)