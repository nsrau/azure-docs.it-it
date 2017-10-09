---
title: Gestire una macchina virtuale di Azure con la raccolta dell'inventario | Microsoft Docs
description: Gestire una macchina virtuale con la raccolta dell'inventario
services: automation
keywords: inventario, automazione, modifica, gestione
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5291d112c2cdf157543fe301d5a5c80f3fe561ae
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Gestire una macchina virtuale di Azure con la raccolta dell'inventario

La gestione dell'inventario può essere abilitata per una macchina virtuale di Azure dalla pagina delle risorse della macchina. Questo metodo offre un'interfaccia utente basata sul browser per la configurazione della raccolta dell'inventario.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.
Se non si ha una macchina virtuale di Azure, crearne [una ](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal) prima di iniziare.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure.

Accedere al [Portale di Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Abilitare la raccolta dell'inventario dalla pagina delle risorse della macchina virtuale

1. Nel schermata a sinistra selezionare **Macchine virtuali**.
1. Selezionare una macchina virtuale dall'elenco.
1. Selezionare **Inventario (anteprima)** dal menu delle risorse sotto **Operazioni**.
1. Nella parte superiore della pagina viene visualizzato un banner che notifica che la soluzione non è abilitata. Fare clic sul banner per abilitare la soluzione.
1. Selezionare un'area di lavoro di Log Analytics per archiviare i log di dati. Se non sono disponibili aree di lavoro per tale area, verrà chiesto di creare un'area di lavoro e un account di Automazione predefiniti. Fare clic su **Abilita** per avviare l'onboarding del computer.

   ![Visualizzare le opzioni di onboarding](./media/automation-vm-inventory/inventory-onboarding-options.png)  

1. Una barra di stato notifica che si sta abilitando la soluzione. Il processo potrebbe richiedere fino a 15 minuti, durante i quali è possibile chiudere il pannello oppure tenerlo aperto per visualizzare una notifica quando la soluzione viene abilitata. È possibile monitorare lo stato della distribuzione dal riquadro delle notifiche.

   ![Visualizzare la soluzione di inventario immediatamente dopo l'onboarding](./media/automation-vm-inventory/inventory-onboarded.png)

1. Al termine della distribuzione, la barra di stato scompare. In questo momento il sistema sta ancora raccogliendo i dati di inventario, che quindi potrebbero non essere ancora visibili. La raccolta completa dei dati può richiedere 24 ore.

## <a name="configure-your-inventory-settings"></a>Configurare le impostazioni di inventario

Per impostazione predefinita, sono configurati per la raccolta il software, i servizi di Windows e i daemon Linux. Per raccogliere l'inventario dei file e del Registro di sistema di Windows, configurare le impostazioni della raccolta dell'inventario.

1. Dalla visualizzazione **Inventario (anteprima)** selezionare il pulsante **Modifica impostazioni** nella parte superiore della pagina.
2. Per aggiungere una nuova impostazione di raccolta, passare alla categoria di impostazioni da aggiungere usando le schede **Registro di sistema di Windows**, **File Windows** e **File Linux**. Nella parte superiore della pagina fare clic su **Aggiungi**.
3. Per visualizzare i dettagli e le descrizioni di ogni proprietà delle impostazioni, vedere la [pagina della documentazione dell'inventario](https://aka.ms/configinventorydocs).

## <a name="disconnecting-your-virtual-machine-from-management"></a>Disconnessione della macchina virtuale dalla gestione

Per rimuovere la macchina dalla gestione dell'inventario:

1. Dal menu a sinistra nel portale di Azure fare clic su **Log Analytics** e quindi selezionare l'area di lavoro usata durante l'onboarding della macchina virtuale.
1. Nella pagina Log Analytics selezionare **Macchine virtuali** nella categoria **Origini dati dell'area di lavoro** nel menu delle risorse. 
1. Selezionare la macchina virtuale da disconnettere. La macchina avrà un segno di spunta verde accanto al testo "Questa area di lavoro" nella colonna **Connessione OMS**. Fare clic su **Disconnetti** nella parte superiore della pagina successiva e su **Sì** nella finestra di dialogo di conferma per disconnettere la macchina dalla gestione.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla gestione delle modifiche nelle impostazioni dei file e del registro di sistema nelle macchine virtuali, vedere [Rilevamento modifiche](../log-analytics/log-analytics-change-tracking.md).
* Per informazioni sulla gestione degli aggiornamenti di Windows e dei pacchetti nelle macchine virtuali, vedere [Gestione aggiornamenti](../operations-management-suite/oms-solution-update-management.md)

