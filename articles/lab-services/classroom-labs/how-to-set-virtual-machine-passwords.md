---
title: Impostare le password per le macchine virtuali in Azure Lab Services . Documenti Microsoft
description: Informazioni su come impostare e reimpostare le password per le macchine virtuali (VM) nei laboratori in classe di Azure Lab Services.Learn how to set and reset passwords for virtual machines (VMs) in classroom labs of Azure Lab Services.
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 0ff464936025a20cb6925adc7ef6eb44c2fe1f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933813"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Configurare e gestire il pool di macchine virtuali 
In questo articolo viene illustrato come eseguire le attività seguenti:This article shows you how to do the following tasks:

- Aumentare il numero di macchine virtuali (VM) nel lab
- Avviare tutte le macchine virtuali o le macchine virtuali selezionateStart all VMs or selected VMs 
- Reimpostare le macchine virtuali

## <a name="update-the-lab-capacity"></a>Aggiornare la capacità del laboratorio
Per aumentare o ridurre la capacità lab (numero di macchine virtuali in un lab), eseguire la procedura seguente:To increase or decrease the lab capacity (number of virtual machines in a lab), do the following steps:

1. Nella pagina **Pool di macchine virtuali** selezionare Capacità **lab: &lt;numero&gt; di computer**.
2. Immettere il nuovo **numero di macchine virtuali** desiderato nel lab. Questo numero deve essere maggiore o uguale al numero di utenti registrati nel lab. 
3. Selezionare quindi **Salva**. 

    ![Pulsante Avvia tutto](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Se si aumenta la capacità, è possibile visualizzare la macchina virtuale o le macchine virtuali in fase di creazione. Se la nuova macchina virtuale non è presente nell'elenco, aggiornare la pagina. 

    ![VM in fase di creazione](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Avviare le macchine virtuali

### <a name="start-ot-stop-all-vms"></a>Avviare l'arresto di tutte le macchine virtualiStart ot stop all VMs
1. Passare alla pagina **Pool di macchine virtuali.** 
2. Selezionare **Avvia tutto** dalla barra degli strumenti. 

    ![Pulsante Avvia tutto](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Dopo aver avviato tutte le macchine virtuali, è possibile arrestare tutte le macchine virtuali selezionando il pulsante **Arresta tutto** sulla barra degli strumenti. 

    ![Pulsante Interrompi tutto](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Avviare le macchine virtuali selezionateStart selected VMs
Esistono due modi per avviare le macchine virtuali selezionate (una o più). Il primo modo consiste nel selezionare la macchina virtuale o le macchine virtuali nell'elenco e quindi selezionare **Avvia** sulla barra degli strumenti. 

Il secondo modo consiste nel selezionare una o più macchine virtuali nell'elenco e attivare o disattivare il pulsante nella colonna **Stato.The** second way is to select one or more VMs in the list, and toggle the button in the State column. 

![Avviare le macchine virtuali selezionateStart selected VMs](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Analogamente, è possibile arrestare una o più macchine virtuali alternando il pulsante nella colonna **Stato** o selezionando **Interrompi** sulla barra degli strumenti. 

> [!NOTE]
> Quando un docente attiva una macchina virtuale per studenti, la quota per lo studente non viene influenzata. La quota per un utente specifica il numero di ore di laboratorio disponibili per l'utente al di fuori dell'orario pianificato. Per ulteriori informazioni sulle quote, vedere [Impostare le quote per gli utenti.](how-to-configure-student-usage.md?#set-quotas-for-users)

## <a name="reset-vms"></a>Reimpostare le macchine virtuali
Per reimpostare una o più macchine virtuali, selezionarle nell'elenco e quindi selezionare **Reimposta** sulla barra degli strumenti. 

![Reimpostare le macchine virtuali selezionate](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

Nella finestra di dialogo **Reimposta macchine virtuali** selezionare **Reimposta**. 

![Finestra di dialogo Reimposta macchina virtuale](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>Impostare la password per le macchine virtualiSet password for VMs
Il proprietario di un lab (insegnante) può impostare/reimpostare la password per le macchine virtuali al momento della creazione del lab (creazione guidata lab) o dopo aver creato il lab nella pagina **Modello.** 

### <a name="set-password-at-the-time-of-lab-creation"></a>Impostare la password al momento della creazione del lab
Il proprietario di un lab (insegnante) può impostare una password per le macchine virtuali nel lab nella pagina **Credenziali macchina virtuale** della procedura guidata di creazione del lab.

![Finestra New lab](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Abilitando/disabilitando l'opzione **Usa la stessa password per tutte le macchine virtuali** in questa pagina, un insegnante può scegliere di usare la stessa password per tutte le macchine virtuali nel lab o consentire agli studenti di impostare password per le macchine virtuali. Per impostazione predefinita, questa impostazione è abilitata per tutte le immagini del sistema operativo Windows e Linux ad eccezione di Ubuntu. Quando questa impostazione è disabilitata, agli studenti verrà richiesto di impostare una password quando tentano di connettersi alla macchina virtuale per la prima volta. 

### <a name="reset-password-later"></a>Reimposta password in un secondo momento

1. Nella pagina **Modello** del lab selezionare **Reimposta password** sulla barra degli strumenti. 
1. Nella finestra di dialogo **Reimposta password** immettere una password e selezionare **Reimposta password**.
    
    ![Finestra di dialogo Imposta password](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Connettersi alle macchine virtuali degli studentiConnect to student VMs
L'autore del lab (istruttore/professore) può connettersi a una macchina virtuale studente se vengono soddisfatte le condizioni seguenti:The lab creator (instructor/professor) can connect to a student VM if the following conditions are met: 

- L'opzione **Usa la stessa password per tutte le macchine virtuali** è stata selezionata durante la creazione del lab
- La macchina virtuale è in esecuzione 

 Per connettersi alla macchina virtuale studente, passare il mouse sulla macchina virtuale nell'elenco e selezionare il pulsante del computer.  

![Pulsante Connetti a VM studente](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Quando il professore avvia la macchina virtuale e si connette a essa, la quota dello studente non viene influenzata. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>Esportare l'elenco delle macchine virtuali in un file CSVExport list of virtual machines to a CSV file

1. Passare alla scheda **Pool di macchine virtuali.**
2. Selezionare **...** (i lipsiane) sulla barra degli strumenti e quindi selezionare **Esporta CSV**. 

    ![Esportare l'elenco delle macchine virtualiExport list of virtual machines](../media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle altre opzioni di utilizzo degli studenti, è possibile configurare (come proprietario del lab), vedere il seguente articolo: [Configure student usage](how-to-configure-student-usage.md).

Per informazioni su come gli studenti possono reimpostare le password per le macchine virtuali, vedere [Impostare o reimpostare la password per le macchine virtuali nei laboratori della classe (studenti).](how-to-set-virtual-machine-passwords-student.md)