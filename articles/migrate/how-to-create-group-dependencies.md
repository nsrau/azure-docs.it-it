---
title: Ridefinire un gruppo di valutazione con il mapping delle dipendenze del gruppo in Azure Migrate | Microsoft Docs
description: Descrive come ridefinire una valutazione usando il mapping delle dipendenze del gruppo nel servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/22/2017
ms.author: raynew
ms.openlocfilehash: 3b10765894501791004e3a9221363f196cc0c91d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Ridefinire un gruppo usando il mapping delle dipendenze del gruppo

In questo articolo viene descritto come migliorare un gruppo, la visualizzazione delle dipendenze di tutti i computer nel gruppo. È in genere possibile utilizzare questo metodo quando si desidera ridefinire il controllo incrociato delle dipendenze del gruppo, l'appartenenza di un gruppo esistente, prima di eseguire una valutazione. Modifica di un gruppo utilizzando la visualizzazione delle dipendenze può consentono di pianificare in modo efficace la migrazione a Azure.You può individuare tutti i sistemi interdipendenti necessari per eseguire la migrazione insieme. Consente di verificare che non vengono ignorate e sorpresa non si verificano interruzioni quando si esegue la migrazione in Azure. 


> [!NOTE]
> Gruppi per il quale si desidera visualizzare le dipendenze non devono contenere più di 10 macchine. Se si dispone più di 10 computer nel gruppo, è consigliabile suddividerlo in gruppi più piccoli per sfruttare la funzionalità di visualizzazione delle dipendenze.


# <a name="prepare-the-group-for-dependency-visualization"></a>Preparare il gruppo per la visualizzazione di dipendenza
Per visualizzare le dipendenze di un gruppo, è necessario scaricare e installare gli agenti in ogni computer locale che fa parte del gruppo. Inoltre, se si dispone di computer senza accesso a Internet, è necessario scaricare e installare il [gateway OMS](../log-analytics/log-analytics-oms-gateway.md).

### <a name="download-and-install-the-vm-agents"></a>Scaricare e installare gli agenti di macchine virtuali
1. In **Panoramica**, fare clic su **Gestisci** > **gruppi**, passare al gruppo obbligatorio.
2. Nell'elenco dei computer, nel **Dependency agent** colonna, fare clic su **richiede l'installazione** per visualizzare istruzioni su come scaricare e installare gli agenti.
3. Nel **dipendenze** pagina, scaricare e installare Microsoft Monitoring Agent (MMA) e l'agente di dipendenza in ogni macchina virtuale che fa parte del gruppo.
4. Copiare l'ID e la chiave dell'area di lavoro. Quando si installa il MMA nei computer locali, è necessario questi.

### <a name="install-the-mma"></a>Installare MMA

Per installare l'agente in un computer Windows:

1. Fare doppio clic sull'agente scaricato.
2. Nella pagina di **benvenuto** fare clic su **Avanti**. Nella pagina **Condizioni di licenza** fare clic su **Accetto** per accettare la licenza.
3. In **Cartella di destinazione** mantenere o modificare la cartella di installazione predefinita e quindi fare clic su **Avanti**. 
4. In **Opzioni di installazione dell'agente** selezionare **Azure Log Analytics (OMS)** > **Avanti**. 
5. Fare clic su **Aggiungi** per aggiungere una nuova area di lavoro OMS. Incollare l'ID e la chiave dell'area di lavoro copiati dal portale. Fare clic su **Avanti**.


Per installare l'agente in un computer Linux:

1. Trasferire il bundle appropriato (x86 o x64) nel computer Linux mediante scp/sftp.
2. Installare il bundle usando l'argomento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>Installare Dependency Agent
1. Per installare Dependency Agent in un computer Windows, fare doppio clic sul file di installazione e seguire la procedura guidata.
2. Per installare Dependency Agent in un computer Linux, procedere all'installazione come utente ROOT usando il comando seguente:

    ```sh InstallDependencyAgent-Linux64.bin```

Sono disponibili [altre informazioni](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems) sui sistemi operativi supportati da Dependency Agent. 

## <a name="refine-the-group-based-on-dependency-visualization"></a>Perfezionare il gruppo basato sulla visualizzazione di dipendenza
Dopo aver installato gli agenti in tutti i computer del gruppo, è possibile visualizzare le dipendenze del gruppo e ottimizzarli seguendo i passaggi seguenti.

1. Nel progetto di Azure Migrate, in **Gestisci**, fare clic su  **Gruppi** e selezionare il gruppo.
2. Nella pagina relativa al gruppo fare clic su  **Visualizza dipendenze** per aprire la mappa delle dipendenze del gruppo.
3. La mappa di dipendenza per il gruppo Mostra i dettagli seguenti:
    - In ingresso (client) e le connessioni TCP (server) in uscita a/da tutti i computer che fanno parte del gruppo
        - Le macchine di dipendenti che non sono installato l'agente MMA e delle dipendenze verranno raggruppate i numeri di porta
        - Le macchine dependenct che dispongono di MMA e installato l'agente di dipendenza vengono visualizzate come finestre separate 
    - Processi in esecuzione all'interno della macchina, è possibile espandere ogni casella macchina per visualizzare i processi
    - Proprietà come nome di dominio completo, sistema operativo, e così via. indirizzo MAC di ogni computer, è possibile fare clic su ogni casella macchina per visualizzare i dettagli

     ![Visualizzazione delle dipendenze del gruppo](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Per visualizzare le dipendenze a un livello più dettagliato, fare clic sull'intervallo di tempo per modificarlo. Per impostazione predefinita, l'intervallo è un'ora. È possibile modificare l'intervallo di tempo oppure specificare le date di inizio e fine e la durata.
4. Verificare che le macchine di dipendenti, il processo in esecuzione all'interno di ogni macchina e identificare i computer che devono essere aggiunto o rimosso dal gruppo.
5. Utilizzare Ctrl + clic per selezionare le macchine virtuali sulla mappa per aggiungere o rimuovere dal gruppo.
    - È possibile aggiungere solo i computer che sono stati individuati.
    - L'aggiunta e la rimozione dei computer di un gruppo invalidano le precedenti valutazioni eseguite per il gruppo.
    - Quando si modifica il gruppo, è possibile creare facoltativamente una nuova valutazione.
5. Fare clic su **OK** per salvare il gruppo.

    ![Aggiungere o rimuovere computer](./media/how-to-create-group-dependencies/add-remove.png)

Se si vogliono verificare le dipendenze di un computer specifico che viene visualizzato nella mappa delle dipendenze del gruppo, [configurare il mapping delle dipendenze del computer](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
