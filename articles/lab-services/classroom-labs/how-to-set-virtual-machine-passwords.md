---
title: Impostare le password per le macchine virtuali in Azure Lab Services | Microsoft Docs
description: Informazioni su come impostare e reimpostare le password per le macchine virtuali (VM) in laboratori della classe di Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: d89d506d8912706bbdb802801b16d01036ecb8e2
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73583607"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Configurare e gestire il pool di macchine virtuali 
Questo articolo illustra come eseguire le attività seguenti:

- Aumentare il numero di macchine virtuali (VM) nel Lab
- Avvia tutte le macchine virtuali o le VM selezionate 
- Reimposta macchine virtuali

## <a name="update-the-lab-capacity"></a>Aggiornare la capacità del Lab
Per aumentare o diminuire la capacità del Lab (numero di macchine virtuali in un Lab), seguire questa procedura:

1. Nella pagina **pool di macchine virtuali** selezionare **capacità Lab: &lt;numero&gt; computer**.
2. Immettere il nuovo **numero di macchine virtuali** desiderate nel Lab. Questo numero deve essere maggiore o uguale al numero di utenti registrati nel Lab. 
3. Selezionare quindi **Salva**. 

    ![Pulsante Avvia tutto](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Se è stata aumentata la capacità, è possibile visualizzare la macchina virtuale o le VM in fase di creazione. Se la nuova macchina virtuale non viene visualizzata nell'elenco, aggiornare la pagina. 

    ![VM in fase di creazione](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Avviare le macchine virtuali

### <a name="start-ot-stop-all-vms"></a>Avviare OT arrestare tutte le macchine virtuali
1. Passare alla pagina del **pool di macchine virtuali** . 
2. Selezionare **Avvia tutto** dalla barra degli strumenti. 

    ![Pulsante Avvia tutto](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Una volta avviate tutte le VM, è possibile arrestare tutte le VM selezionando il pulsante **Interrompi tutto** sulla barra degli strumenti. 

    ![Pulsante Interrompi tutto](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Avvia macchine virtuali selezionate
Esistono due modi per avviare le VM selezionate (una o più). Il primo consiste nel selezionare la VM o le macchine virtuali nell'elenco, quindi selezionare **Avvia** sulla barra degli strumenti. 

Il secondo modo consiste nel selezionare una o più macchine virtuali nell'elenco e impostare il pulsante nella colonna **stato** . 

![Avvia macchine virtuali selezionate](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Analogamente, è possibile arrestare una o più macchine virtuali attivando il pulsante nella colonna **stato** o selezionando **Interrompi** sulla barra degli strumenti. 

## <a name="reset-vms"></a>Reimposta macchine virtuali
Per reimpostare una o più macchine virtuali, selezionarle nell'elenco e quindi fare clic su **Reimposta** sulla barra degli strumenti. 

![Reimposta le macchine virtuali selezionate](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

Nella finestra di dialogo **Reimposta macchine virtuali** selezionare **Reimposta**. 

![Finestra di dialogo Reimposta macchina virtuale](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>Impostare la password per le macchine virtuali
Un proprietario del Lab (insegnante) può impostare o reimpostare la password per le macchine virtuali al momento della creazione del Lab (creazione guidata Lab) o dopo la creazione del Lab nella pagina **modello** . 

### <a name="set-password-at-the-time-of-lab-creation"></a>Impostare la password al momento della creazione del Lab
Un proprietario del Lab (insegnante) può impostare una password per le macchine virtuali nel Lab nella pagina **credenziali macchina virtuale** della procedura guidata di creazione Lab.

![Nuova finestra del Lab](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Abilitando/disabilitando l'opzione **Usa la stessa password per tutte le macchine virtuali** in questa pagina, un insegnante può scegliere di usare la stessa password per tutte le macchine virtuali nel Lab o consentire agli studenti di impostare le password per le proprie macchine virtuali. Per impostazione predefinita, questa impostazione è abilitata per tutte le immagini del sistema operativo Windows e Linux, ad eccezione di Ubuntu. Quando questa impostazione è disabilitata, agli studenti verrà richiesto di impostare una password quando tenteranno di connettersi alla macchina virtuale per la prima volta. 

### <a name="reset-password-later"></a>Reimposta la password in un secondo momento

1. Nella pagina **modello** del Lab selezionare **Reimposta password** sulla barra degli strumenti. 
1. Nella finestra di dialogo **Reimposta password** immettere una password e selezionare **Reimposta password**.
    
    ![Finestra di dialogo Imposta password](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Connettersi alle macchine virtuali degli studenti
Il creatore del Lab (insegnante/professore) può connettersi a una macchina virtuale per studenti se vengono soddisfatte le condizioni seguenti: 

- Quando si crea il Lab, è stata selezionata l'opzione **Usa la stessa password per tutte le macchine virtuali**
- La macchina virtuale è in esecuzione 

 Per connettersi alla macchina virtuale studente, posizionare il mouse sulla macchina virtuale nell'elenco e selezionare il pulsante computer.  

![Pulsante Connetti a macchina virtuale studente](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle altre opzioni di utilizzo degli studenti che possono essere configurate da un proprietario del Lab, vedere l'articolo relativo alla configurazione dell'utilizzo da parte [degli studenti](how-to-configure-student-usage.md).

Per informazioni sul modo in cui gli studenti possono reimpostare le password per le proprie macchine virtuali, vedere [impostare o reimpostare la password per le macchine virtuali in laboratori della classe (studenti)](how-to-set-virtual-machine-passwords-student.md).