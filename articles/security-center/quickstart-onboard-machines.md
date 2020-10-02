---
title: Connettere macchine virtuali non di Azure al Centro sicurezza di Azure
description: Informazioni su come connettere macchine virtuali non di Azure al Centro sicurezza di Azure
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 6f2889c298f525e1babf80f86d4ae140ef2ce96f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448960"
---
#  <a name="connect-your-non-azure-machines-to-security-center"></a>Connettere macchine virtuali non di Azure al Centro sicurezza

Il Centro di sicurezza di Azure consente di monitorare le condizioni di sicurezza dei computer non Azure, ma è necessario prima caricare queste risorse. È possibile aggiungere computer non Azure nella pagina **Attività iniziali** o **Inventario**, come descritto di seguito.

## <a name="add-non-azure-computers"></a>Aggiungere computer non Azure 

1. Dal menu del Centro sicurezza aprire la pagina **Attività iniziali**.
1. Selezionare la scheda **Introduzione**.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Scheda Attività iniziali della pagina Attività iniziali" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. In **Aggiungi server non Azure** selezionare **Configura**.

    > [!TIP]
    > È anche possibile aprire Aggiungi computer tramite il pulsante **Aggiungi server non di Azure** della pagina **Inventario**.

    Viene visualizzato un elenco delle aree di lavoro di Log Analytics. L'elenco include, se applicabile, l'area di lavoro predefinita creata dal Centro sicurezza quando è stato abilitato il provisioning automatico. Selezionare l'area di lavoro che si desidera usare.

    È possibile aggiungere computer a un'area di lavoro esistente o creare una nuova area di lavoro. 

1. Facoltativamente, per creare una nuova area di lavoro, selezionare **Crea una nuova area di lavoro**.

1. Nell'elenco di aree di lavoro selezionare **Aggiungi server** per l'area di lavoro pertinente.
    Viene visualizzata la pagina **Gestione agenti**.

    Da qui scegliere la procedura appropriata tra quelle riportate di seguito, in base al tipo di computer di cui eseguire l'onboarding:

    - [Eseguire l'onboarding di VM di Azure Stack](#onboard-your-azure-stack-vms)
    - [Eseguire l'onboarding di computer Linux](#onboard-your-linux-machines)
    - [Eseguire l'onboarding di computer Windows](#onboard-your-windows-machines)


### <a name="onboard-your-azure-stack-vms"></a>Eseguire l'onboarding di VM di Azure Stack
Per aggiungere VM di Azure Stack, è necessario fare riferimento alle informazioni riportate nella pagina **Gestione agenti** e configurare l'estensione **Azure Monitor, Update and Configuration Management** (Monitoraggio di Azure, Gestione aggiornamenti e configurazione) nelle macchine virtuali che eseguono Azure Stack.
1. Nella pagina **Gestione agenti** copiare i valori di **ID area di lavoro** e **Chiave primaria** nel Blocco note.
1. Accedere al portale di **Azure Stack** e aprire la pagina **Macchine virtuali**.
1. Selezionare la macchina virtuale da proteggere con il Centro sicurezza.
    >[!TIP]
    > Per informazioni su come creare una macchina virtuale in Azure Stack, vedere [questo argomento di avvio rapido per le macchine virtuali Windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) oppure [per le macchine virtuali Linux](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
1. Selezionare **Estensioni**. Viene visualizzato l'elenco delle estensioni macchina virtuale installate in questa macchina virtuale.
1. Selezionare la scheda **Aggiungi**. Il menu **Nuova risorsa** contiene un elenco delle estensioni macchina virtuale disponibili.
1. Selezionare l'estensione **Azure Monitor, Update and Configuration Management** (Monitoraggio di Azure, Gestione aggiornamenti e configurazione) e fare clic su **Crea**. Viene visualizzata la pagina di configurazione **Installa estensione**.
    >[!NOTE]
    > Se l'estensione **Monitoraggio di Azure, Gestione aggiornamenti e configurazione** non è elencata nel marketplace, contattare un operatore di Azure Stack per ottenerla.
1. Nella pagina di configurazione **Installa estensione** incollare i valori di **ID area di lavoro** e **Chiave dell'area di lavoro (chiave primaria)** copiati nel Blocco note nella procedura precedente.
1. Dopo aver completato la configurazione, selezionare **OK**. Lo stato dell'estensione viene visualizzato come **Provisioning completato**. Può essere necessaria fino a un'ora prima che la macchina virtuale venga visualizzata nel Centro sicurezza.


### <a name="onboard-your-linux-machines"></a>Eseguire l'onboarding di computer Linux
Per aggiungere computer Linux, è necessario il comando WGET della pagina **Gestione agenti**.
1. Nella pagina **Gestione agenti** copiare il comando **WGET** nel Blocco note. Salvare questo file in un percorso accessibile dal computer Linux.
1. Nel computer Linux aprire il file con il comando WGET. Selezionare l'intero contenuto, quindi copiarlo e incollarlo in una console di terminale.
1. Al termine dell'installazione, è possibile verificare che *omsagent* sia installato eseguendo il comando *pgrep*. Il comando restituirà il PID *omsagent*.
    I log per l'agente sono reperibili in: */var/opt/microsoft/omsagent/\<workspace id>/log/* . Possono essere necessari fino a 30 minuti prima che il computer Linux venga visualizzato nel Centro sicurezza.


### <a name="onboard-your-windows-machines"></a>Eseguire l'onboarding di computer Windows
Per aggiungere computer Windows, è necessario avere le informazioni riportate nella pagina **Gestione agenti** e scaricare il file dell'agente appropriato (32/64 bit).
1. Selezionare il collegamento **Scarica agente Windows** applicabile al tipo di processore del computer per scaricare il file di installazione.
1. Nella pagina **Gestione agenti** copiare i valori di **ID area di lavoro** e **Chiave primaria** nel Blocco note.
1. Copiare nel computer di destinazione il file di configurazione scaricato ed eseguirlo.
1. Seguire l'installazione guidata (**Avanti**, **Accetto**, **Avanti**, **Avanti**).
    1. Nella pagina **Azure Log Analytics** incollare i valori di **ID area di lavoro** e **Chiave dell'area di lavoro (chiave primaria)** copiati nel Blocco note.
    1. Se il computer deve fare riferimento a un'area di lavoro Log Analytics nel cloud di Azure per enti pubblici, selezionare **Azure US Government** nell'elenco a discesa **Cloud di Azure**.
    1. Se il computer deve comunicare tramite un server proxy con il servizio Log Analytics, scegliere **Avanzate** e specificare l'URL e il numero di porta del server proxy.
    1. Dopo aver immesso tutte le impostazioni di configurazione, selezionare **Avanti**.
    1. Nella pagina **Pronto per l'installazione** esaminare le impostazioni da applicare e selezionare **Installa**.
    1. Nella pagina **Configurazione completata** fare clic su **Fine**.

Al termine, l'**agente di Log Analytics** verrà visualizzato nel **Pannello di controllo**. È possibile rivedere la configurazione e verificare che l'agente sia connesso.

Per altre informazioni sull'installazione e la configurazione dell'agente, vedere [Connettere computer Windows](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).


## <a name="verifying"></a>Verifica
Congratulazioni! A questo punto è possibile visualizzare i computer Azure e non Azure in un'unica posizione. Aprire la pagina inventario dell'[inventario degli asset](asset-inventory.md) e filtrare in base ai tipi di risorsa appropriati. Queste due icone distinguono i tipi:

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Computer non Azure

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Macchina virtuale di Azure


## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come aggiungere computer non Azure al Centro sicurezza di Azure. Per monitorarne lo stato, usare gli strumenti di inventario come illustrato nella pagina seguente:

- [Esplorare e gestire le risorse con gli strumenti di inventario e gestione delle risorse](asset-inventory.md)