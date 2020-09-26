---
title: Connettere i computer non Azure al centro sicurezza di Azure
description: Informazioni su come connettere i computer non Azure al centro sicurezza
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 82b8161f92f337002e9d8bbdc45cd53d5921fc00
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280675"
---
#  <a name="connect-your-non-azure-machines-to-security-center"></a>Connetti i tuoi computer non Azure al centro sicurezza

Il Centro di sicurezza di Azure consente di monitorare le condizioni di sicurezza dei computer non Azure, ma è necessario prima caricare queste risorse. È possibile aggiungere computer non Azure **dalla pagina** introduttiva o dall' **inventario** , come descritto di seguito.

## <a name="add-non-azure-computers"></a>Aggiungere computer non Azure 

1. Dal menu del Centro sicurezza aprire la pagina **introduttiva** .
1. Selezionare la scheda **Introduzione**.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Scheda attività iniziali nella pagina riquadro attività introduttiva" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. Sotto **Aggiungi server non Azure**selezionare **Configura** .

    > [!TIP]
    > È anche possibile aprire Aggiungi computer dal pulsante **Aggiungi server non di Azure** della pagina di **inventario** .

    Viene visualizzato un elenco delle aree di lavoro di Log Analytics. L'elenco include, se applicabile, l'area di lavoro predefinita creata dal Centro sicurezza quando è stato abilitato il provisioning automatico. Selezionare l'area di lavoro che si desidera usare.

    È possibile aggiungere computer a un'area di lavoro esistente o creare una nuova area di lavoro. 

1. Facoltativamente, per creare una nuova area di lavoro, selezionare  **Crea nuova area di lavoro**.

1. Nell'elenco delle aree di lavoro selezionare **Aggiungi server** per l'area di lavoro pertinente.
    Verrà visualizzata la pagina di **gestione degli agenti** .

    Da qui scegliere la procedura pertinente riportata di seguito, a seconda del tipo di computer che si sta caricando:

    - [Caricare le macchine virtuali Azure Stack](#onboard-your-azure-stack-vms)
    - [Caricare i computer Linux](#onboard-your-linux-machines)
    - [Caricare i computer Windows](#onboard-your-windows-machines)


### <a name="onboard-your-azure-stack-vms"></a>Caricare le macchine virtuali Azure Stack
Per aggiungere Azure Stack macchine virtuali, sono necessarie le informazioni nella pagina di **gestione degli agenti** e per configurare l'estensione della macchina virtuale di **monitoraggio, aggiornamento e gestione della configurazione di Azure** nelle macchine virtuali in esecuzione nel Azure stack.
1. Dalla pagina di **gestione degli agenti** , copiare l'ID dell'area di **lavoro** e la **chiave primaria** nel blocco note.
1. Accedere al portale di **Azure stack** e aprire la pagina **macchine virtuali** .
1. Selezionare la macchina virtuale che si vuole proteggere con il Centro sicurezza.
    >[!TIP]
    > Per informazioni su come creare una macchina virtuale in Azure Stack, vedere [questo argomento di avvio rapido per le macchine virtuali Windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) oppure [per le macchine virtuali Linux](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
1. Selezionare **Estensioni**. Viene visualizzato l'elenco delle estensioni macchina virtuale installate in questa macchina virtuale.
1. Selezionare la scheda **Aggiungi** . Nel menu **nuova risorsa** viene visualizzato l'elenco delle estensioni delle macchine virtuali disponibili.
1. Selezionare l'estensione **monitoraggio di Azure, aggiornamento e gestione della configurazione** e selezionare **Crea**. Verrà visualizzata la pagina Installa configurazione dell' **estensione** .
    >[!NOTE]
    > Se l'estensione **Monitoraggio di Azure, Gestione aggiornamenti e configurazione** non è elencata nel marketplace, contattare un operatore di Azure Stack per ottenerla.
1. Nella pagina **Installa estensione** configurazione incollare l' **ID area di lavoro** e la **chiave dell'area di lavoro (chiave primaria)** copiati nel blocco note nel passaggio precedente.
1. Al termine della configurazione, fare clic su **OK**. Lo stato dell'estensione viene visualizzato come **provisioning riuscito**. La visualizzazione della macchina virtuale nel centro sicurezza potrebbe richiedere fino a un'ora.


### <a name="onboard-your-linux-machines"></a>Caricare i computer Linux
Per aggiungere computer Linux, è necessario il comando WGET dalla pagina di **gestione degli agenti** .
1. Dalla pagina di **gestione degli agenti** , copiare il comando **wget** nel blocco note. Salvare questo file in un percorso accessibile dal computer Linux.
1. Nel computer Linux aprire il file con il comando WGET. Selezionare tutto il contenuto e copiarlo e incollarlo in una console terminale.
1. Al termine dell'installazione, è possibile verificare che *omsagent* sia installato eseguendo il comando *pgrep* . Il comando restituirà il PID *omsagent* .
    I log per l'agente sono disponibili in: */var/opt/Microsoft/omsagent/ \<workspace id> /log/* . potrebbero essere necessari fino a 30 minuti per la visualizzazione del nuovo computer Linux nel centro sicurezza.


### <a name="onboard-your-windows-machines"></a>Caricare i computer Windows
Per aggiungere computer Windows, è necessario disporre delle informazioni nella pagina di **gestione degli agenti** e scaricare il file dell'agente appropriato (32/64 bit).
1. Selezionare il collegamento **Scarica agente Windows** applicabile al tipo di processore del computer per scaricare il file di installazione.
1. Dalla pagina di **gestione degli agenti** , copiare l'ID dell'area di **lavoro** e la **chiave primaria** nel blocco note.
1. Copiare il file di installazione scaricato nel computer di destinazione ed eseguirlo.
1. Seguire l'installazione guidata (**Avanti**, **Accetto**, **Avanti**, **Avanti**).
    1. Nella pagina **log Analytics di Azure** incollare l' **ID area** di lavoro e la **chiave dell'area di lavoro (chiave primaria)** copiati nel blocco note.
    1. Se il computer deve segnalare un'area di lavoro Log Analytics nel cloud di Azure per enti pubblici, selezionare **Azure Government per enti pubblici** nell'elenco a discesa **cloud di Azure** .
    1. Se il computer deve comunicare tramite un server proxy con il servizio Log Analytics, scegliere **Avanzate** e specificare l'URL e il numero di porta del server proxy.
    1. Dopo aver immesso tutte le impostazioni di configurazione, fare clic su **Avanti**.
    1. Nella pagina **pronto per l'installazione** rivedere le impostazioni da applicare e selezionare **Installa**.
    1. Nella pagina **Configurazione completata** fare clic su **Fine**.

Al termine, l'**agente di Log Analytics** verrà visualizzato nel **Pannello di controllo**. È possibile rivedere la configurazione e verificare che l'agente sia connesso.

Per ulteriori informazioni sull'installazione e sulla configurazione dell'agente, vedere [Connect Windows computers](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).


## <a name="verifying"></a>Verifica
Congratulazioni! A questo punto è possibile visualizzare i computer Azure e non Azure in un'unica posizione. Aprire la [pagina inventario asset](asset-inventory.md) e filtrare per i tipi di risorsa pertinenti. Queste due icone distinguono i tipi:

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Computer non Azure

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Macchina virtuale di Azure


## <a name="next-steps"></a>Passaggi successivi

Questa pagina ha illustrato come aggiungere computer non Azure al centro sicurezza di Azure. Per monitorarne lo stato, usare gli strumenti di inventario come illustrato nella pagina seguente:

- [Esplorare e gestire le risorse con gli strumenti di inventario e gestione degli asset](asset-inventory.md)