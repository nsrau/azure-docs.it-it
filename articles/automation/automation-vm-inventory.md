---
title: Gestire una macchina virtuale di Azure con la raccolta dell'inventario | Microsoft Docs
description: Gestire una macchina virtuale di Azure con la raccolta dell'inventario
services: automation
keywords: inventario, automazione, modifica, gestione
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.openlocfilehash: 275e4e9103808710e2a71168a05b3440bb288c6a
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Gestire una macchina virtuale di Azure con la raccolta dell'inventario

È possibile abilitare il monitoraggio dell'inventario per una macchina virtuale di Azure dalla pagina delle risorse della macchina virtuale. Questo metodo offre un'interfaccia utente basata sul browser per la configurazione della raccolta dell'inventario.

## <a name="before-you-begin"></a>Prima di iniziare
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/).
Se non si ha una macchina virtuale di Azure, [crearne una](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure
Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Abilitare la raccolta dell'inventario dalla pagina delle risorse della macchina virtuale

1. Nel riquadro sinistro del portale di Azure selezionare **Macchine virtuali**.
2. Nell'elenco delle macchine virtuali selezionare una macchina virtuale.
3. In **Operazioni** scegliere **Inventario (anteprima)** dal menu **Risorsa**.  
    Nella parte superiore della finestra viene visualizzato un banner che indica che la soluzione non è abilitata. 
4. Fare clic sul banner per abilitare la soluzione.
5. Selezionare un'area di lavoro di Log Analytics per archiviare i log di dati.  
    Se non sono disponibili aree di lavoro per l'area, viene chiesto se creare un'area di lavoro e un account di automazione predefiniti. 
6. Per avviare il caricamento del computer, selezionare **Abilita**.

   ![Visualizzare le opzioni di onboarding](./media/automation-vm-inventory/inventory-onboarding-options.png)  
    Una barra di stato notifica che si sta abilitando la soluzione. Il processo potrebbe richiedere fino a 15 minuti, durante i quali è possibile chiudere la finestra oppure tenerla aperta per visualizzare una notifica quando la soluzione viene abilitata. È possibile monitorare lo stato della distribuzione dal riquadro delle notifiche.

   ![Visualizzare la soluzione di inventario immediatamente dopo l'onboarding](./media/automation-vm-inventory/inventory-onboarded.png)

Al termine della distribuzione, la barra di stato scompare. Il sistema sta ancora raccogliendo i dati di inventario, che quindi potrebbero non essere ancora visibili. La raccolta completa dei dati può richiedere 24 ore.

## <a name="configure-your-inventory-settings"></a>Configurare le impostazioni di inventario

Per impostazione predefinita, sono configurati per la raccolta il software, i servizi di Windows e i daemon Linux. Per raccogliere i dati di inventario dei file e del Registro di sistema di Windows, configurare le impostazioni della raccolta dell'inventario.

1. Nella visualizzazione **Inventario (anteprima)** selezionare il pulsante **Modifica impostazioni** nella parte superiore della finestra.
2. Per aggiungere una nuova impostazione della raccolta, passare alla categoria delle impostazioni che si vuole aggiungere selezionando le schede **Registro di sistema di Windows**, **File Windows** e **File Linux**. 
3. Selezionare **Aggiungi** nella parte superiore della finestra.
4. Per visualizzare i dettagli e le descrizioni di ogni proprietà delle impostazioni, vedere la [pagina della documentazione dell'inventario](https://aka.ms/configinventorydocs).

## <a name="disconnect-your-virtual-machine-from-management"></a>Disconnettere la macchina virtuale dalla gestione

Per rimuovere la macchina virtuale dalla gestione dell'inventario:

1. Nel riquadro sinistro del portale di Azure selezionare **Log Analytics** e quindi selezionare l'area di lavoro usata durante il caricamento della macchina virtuale.
2. Nella categoria **Origini dati dell'area di lavoro** nella finestra **Log Analytics** scegliere **Macchine virtuali** dal menu **Risorsa**. 
3. Nell'elenco selezionare la macchina virtuale che si vuole disconnettere. La macchina virtuale avrà un segno di spunta verde accanto alla voce **Questa area di lavoro** nella colonna **Connessione OMS**. 
4. Nella parte superiore della pagina successiva selezionare **Disconnetti**.
5. Nella finestra di conferma fare clic su **Sì**.  
    Questa azione disconnette la macchina virtuale dalla gestione.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla gestione delle modifiche nelle impostazioni dei file e del Registro di sistema nelle macchine virtuali, vedere [Rilevare le modifiche software nell'ambiente in uso con la soluzione di rilevamento modifiche](../log-analytics/log-analytics-change-tracking.md).
* Per informazioni sulla gestione degli aggiornamenti di Windows e dei pacchetti nelle macchine virtuali, vedere [Soluzione Gestione aggiornamenti in OMS](../operations-management-suite/oms-solution-update-management.md).
