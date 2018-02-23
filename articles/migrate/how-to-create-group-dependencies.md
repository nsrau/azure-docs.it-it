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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Ridefinire un gruppo usando il mapping delle dipendenze del gruppo

In questo articolo viene descritto come ridefinire un gruppo visualizzando le dipendenze di tutti i computer nel gruppo. In genere si usa questo metodo quando si vogliono ridefinire le appartenenze di un gruppo esistente, controllando in modo incrociato le dipendenze del gruppo, prima di eseguire una valutazione. La ridefinizione di un gruppo mediante la visualizzazione delle dipendenze può aiutare a pianificare la migrazione a Azure in modo efficace, in quanto consente di individuare tutti i sistemi interdipendenti che dovranno essere migrati. Garantisce inoltre una migrazione completa e senza interruzioni a sorpresa. 


> [!NOTE]
> I gruppi per i quali si vuole visualizzare le dipendenze non devono includere più di 10 computer. Se il gruppo contiene più di 10 computer, è consigliabile suddividerlo in gruppi più piccoli per sfruttare la funzionalità di visualizzazione delle dipendenze.


# <a name="prepare-the-group-for-dependency-visualization"></a>Preparare il gruppo per la visualizzazione delle dipendenze
Per visualizzare le dipendenze di un gruppo, è necessario scaricare e installare gli agenti in ogni computer locale appartenente al gruppo. Inoltre, se si dispone di computer senza accesso a Internet, è necessario scaricare e installare il [gateway OMS](../log-analytics/log-analytics-oms-gateway.md).

### <a name="download-and-install-the-vm-agents"></a>Scaricare e installare gli agenti di macchine virtuali
1. In **Panoramica**, fare clic su **Gestisci** > **gruppi** e andare al gruppo desiderato.
2. Nell'elenco di computer nella colonna **Dependency agent**, fare clic su **Richiede l'installazione** per visualizzare le istruzioni su come scaricare e installare gli agenti.
3. Nella pagina **Dipendenze**, scaricare e installare Microsoft Monitoring Agent (MMA) e Dependency Agent in ogni macchina virtuale del gruppo.
4. Copiare l'ID e la chiave dell'area di lavoro. Questi dati sono necessari quando si installa MMA nei computer locali.

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

## <a name="refine-the-group-based-on-dependency-visualization"></a>Ridefinire il gruppo in base alla visualizzazione delle dipendenze
Dopo aver installato gli agenti in tutti i computer del gruppo, è possibile visualizzare le dipendenze e ridefinirle seguendo i passaggi seguenti.

1. Nel progetto di Azure Migrate, in **Gestisci**, fare clic su  **Gruppi** e selezionare il gruppo.
2. Nella pagina relativa al gruppo fare clic su  **Visualizza dipendenze** per aprire la mappa delle dipendenze del gruppo.
3. La mappa delle dipendenze del gruppo mostra i dettagli seguenti:
    - Connessioni TCP in entrata (client) e in uscita (server) a/da tutti i computer che fanno parte del gruppo
        - Le macchine dipendenti su cui non sono installati l'agente MMA e l'agente Dependency Agent verranno raggruppate in base ai numeri di porta
        - Le macchine dipendenti su cui sono installati l'agente MMA e l'agente Dependency Agent verranno visualizzate in caselle separate 
    - Processi in esecuzione all'interno della macchina: è possibile espandere ogni casella macchina per visualizzare i processi
    - Proprietà quali: nome di dominio completo, sistema operativo, indirizzo MAC di ogni computer e così via. Fare clic su ogni casella macchina per visualizzare questi dettagli

     ![Visualizzazione delle dipendenze del gruppo](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Per visualizzare le dipendenze a un livello più dettagliato, fare clic sull'intervallo di tempo per modificarlo. Per impostazione predefinita, l'intervallo è un'ora. È possibile modificare l'intervallo di tempo oppure specificare le date di inizio e fine e la durata.
4. Verificare i computer dipendenti, i processi in esecuzione in ciascun computer e identificare i computer da aggiungere o rimuovere dal gruppo.
5. Premere CTRL+clic per selezionare contemporaneamente più computer sulla mappa e aggiungerle o rimuoverle dal gruppo.
    - È possibile aggiungere solo i computer che sono stati individuati.
    - L'aggiunta e la rimozione dei computer di un gruppo invalidano le precedenti valutazioni eseguite per il gruppo.
    - Quando si modifica il gruppo, è possibile creare facoltativamente una nuova valutazione.
5. Fare clic su **OK** per salvare il gruppo.

    ![Aggiungere o rimuovere computer](./media/how-to-create-group-dependencies/add-remove.png)

Se si vogliono verificare le dipendenze di un computer specifico che viene visualizzato nella mappa delle dipendenze del gruppo, [configurare il mapping delle dipendenze del computer](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
