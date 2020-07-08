---
title: Impostare le password per le macchine virtuali in Azure Lab Services | Microsoft Docs
description: Informazioni su come impostare e reimpostare le password per le macchine virtuali (VM) in lab per le classi di Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6012123b729bd1be6b4ff0fdb00d71aca56cfa09
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85443775"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Configurare e gestire il pool di macchine virtuali 
Questo articolo illustra come eseguire le attività seguenti:

- Aumentare il numero di macchine virtuali (VM) nel lab
- Avviare tutte le macchine virtuali o le macchine virtuali selezionate 
- Reimpostare le macchine virtuali

## <a name="update-the-lab-capacity"></a>Aggiornare la capacità del lab
Per aumentare o diminuire la capacità del lab (numero di macchine virtuali in un lab), seguire questa procedura:

1. Nella pagina **Pool di macchine virtuali** selezionare **Capacità lab: &lt;numero&gt; macchine**.
2. Immettere il nuovo **numero di macchine virtuali** desiderate nel lab. Questo numero deve essere maggiore o uguale al numero di utenti registrati nel lab. 
3. Selezionare quindi **Salva**. 

    ![Pulsante Avvia tutte](./media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Se è stata aumentata la capacità, è possibile visualizzare la macchina virtuale o le macchine virtuali create. Se la nuova macchina virtuale non viene visualizzata nell'elenco, aggiornare la pagina. 

    ![Macchina virtuale creata](./media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Avviare le macchine virtuali

### <a name="start-ot-stop-all-vms"></a>Avviare o arrestare tutte le macchine virtuali
1. Passare alla pagina **Pool di macchine virtuali**. 
2. Selezionare **Avvia tutte** nella barra degli strumenti. 

    ![Pulsante Avvia tutte](./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Una volta avviate tutte le macchine virtuali, è possibile arrestarle selezionando il pulsante **Arresta tutte** nella barra degli strumenti. 

    ![Pulsante Arresta tutte](./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Avviare le macchine virtuali selezionate
Esistono due modi per avviare le macchine virtuali selezionate (una o più). Il primo consiste nel selezionare la macchina virtuale o le macchine virtuali nell'elenco e quindi selezionare **Avvia** nella barra degli strumenti. 

Il secondo consiste nel selezionare una o più macchine virtuali nell'elenco e attivare il pulsante nello colonna **Stato**. 

![Avviare le macchine virtuali selezionate](./media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Analogamente, è possibile arrestare una o più macchine virtuali attivando il pulsante nella colonna **Stato** o selezionando **Arresta** nella barra degli strumenti. 

> [!NOTE]
> Quando un docente accende una macchina virtuale per studenti, la quota per lo studente rimane invariata. La quota per un utente specifica il numero di ore lab disponibili per l'utente al di fuori dell'orario delle lezioni pianificato. Per altre informazioni sulle quote, vedere [Impostare quote per gli utenti](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="reset-vms"></a>Reimpostare le macchine virtuali
Per reimpostare una o più macchine virtuali, selezionarle nell'elenco e quindi selezionare **Reimposta** nella barra degli strumenti. 

![Reimpostare le macchine virtuali selezionate](./media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

Nella finestra di dialogo **Reimposta macchine virtuali** selezionare **Reimposta**. 

![Finestra di dialogo Reimposta macchine virtuali](./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>Impostare la password per le macchine virtuali
Un proprietario del lab (docente) può impostare o reimpostare la password per le macchine virtuali al momento della creazione del lab (creazione guidata del lab) o dopo la creazione del lab nella pagina **Modello**. 

### <a name="set-password-at-the-time-of-lab-creation"></a>Impostare la password al momento della creazione del lab
Un proprietario del lab (docente) può impostare una password per le macchine virtuali nel lab nella pagina **Credenziali della macchina virtuale** della creazione guidata del lab.

![Finestra New lab](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Abilitando/disabilitando l'opzione **Usa la stessa password per tutte le macchine virtuali** in questa pagina, un docente può scegliere di usare la stessa password per tutte le macchine virtuali nel lab o consentire agli studenti di impostare le password per le proprie macchine virtuali. Per impostazione predefinita, questa impostazione è abilitata per tutte le immagini del sistema operativo Windows e Linux, ad eccezione di Ubuntu. Quando questa impostazione è disabilitata, agli studenti verrà richiesto di impostare una password quando proveranno a connettersi alla macchina virtuale per la prima volta. 

### <a name="reset-password-later"></a>Reimpostar la password in un secondo momento

1. Nella pagina **Modello** del lab selezionare **Reimposta password** nella barra degli strumenti. 
1. Nella finestra di dialogo **Reimposta password** immettere una password e selezionare **Reimposta password**.
    
    ![Finestra di dialogo Imposta password](./media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Connettersi alle macchine virtuali degli studenti
L'autore del lab (docente) può connettersi alla macchina virtuale di uno studente se vengono soddisfatte le condizioni seguenti: 

- L'opzione **Usa la stessa password per tutte le macchine virtuali** è stata selezionata durante la creazione del lab
- La macchina virtuale è in esecuzione 

 Per connettersi alla macchina virtuale dello studente, posizionare il mouse sulla macchina virtuale nell'elenco e selezionare il pulsante del computer.  

![Pulsante per connettersi alla macchina virtuale di uno studente](./media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Quando il docente avvia la macchina virtuale e vi si connette, la quota dello studente non è interessata. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>Esportare l'elenco di macchine virtuali in un file CSV

1. Passare alla scheda **Pool di macchine virtuali**.
2. Selezionare **...** (puntini di sospensione) nella barra degli strumenti e quindi selezionare **Esporta CSV**. 

    ![Esportare l'elenco di macchine virtuali](./media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle altre opzioni di utilizzo degli studenti che possono essere configurate dal proprietario del lab, vedere l'articolo seguente: [Configurare l'utilizzo degli studenti](how-to-configure-student-usage.md).

Per informazioni sul modo in cui gli studenti possono reimpostare le password per le proprie macchine virtuali, vedere [Impostare o reimpostare la password per le macchine virtuali nei lab per le classi (studenti)](how-to-set-virtual-machine-passwords-student.md).