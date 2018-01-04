---
title: Raggruppare i computer usando le dipendenze dei computer con Azure Migrate | Microsoft Docs
description: Descrive come creare una valutazione usando le dipendenze dei computer con il servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/25/2017
ms.author: raynew
ms.openlocfilehash: 720380fd14d9eaf4856ad75269a80f2b63a4725f
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Raggruppare i computer usando il mapping delle dipendenze dei computer

In questo articolo viene descritto come creare un gruppo di computer per [eseguire la migrazione di Azure](migrate-overview.md) valutazione visualizzando le dipendenze dei computer. Questo metodo viene in genere usato quando si vogliono valutare gruppi di macchine virtuali con livelli di attendibilità più elevati controllando in modo incrociato le dipendenze dei computer prima di eseguire una valutazione. Visualizzazione di dipendenza consente di pianificare in modo efficace la migrazione a Azure. Consente di verificare che non vengono ignorate e sorpresa non si verificano interruzioni quando si esegue la migrazione in Azure. È possibile individuare tutti i sistemi interdipendenti necessari per eseguire la migrazione tra loro e identificare se un sistema in esecuzione è ancora per gli utenti o è un candidato per la rimozione delle autorizzazioni invece di migrazione. 


## <a name="prepare-machines-for-dependency-mapping"></a>Preparare i computer per il mapping delle dipendenze
Per visualizzare le dipendenze dei computer, è necessario scaricare e installare gli agenti in ogni computer locale che si desidera valutare. Inoltre, se si dispone di computer senza accesso a Internet, è necessario scaricare e installare il [gateway OMS](../log-analytics/log-analytics-oms-gateway.md).

### <a name="download-and-install-the-vm-agents"></a>Scaricare e installare gli agenti di macchine virtuali
1. In **Panoramica** fare clic su **Gestisci** > **Computer** e selezionare il computer necessario.
2. Nella colonna **Dipendenze** fare clic su **Installa agenti**. 
3. Nella pagina **Dipendenze** scaricare e installare Microsoft Monitoring Agent (MMA) e Dependency Agent in ogni macchina virtuale che si vuole valutare.
4. Copiare l'ID e la chiave dell'area di lavoro. Questi dati sono necessari quando si installa MMA nel computer locale.

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

## <a name="create-a-group"></a>Creare un gruppo

1. Dopo aver installato gli agenti, accedere al portale e fare clic su **Gestisci** > **Computer**.
2. Ricerca per il computer in cui è installato gli agenti.
3. Il **dipendenze** colonna per il computer ora dovrebbe essere visualizzato come **Visualizza dipendenze**. Fare clic sulla colonna per visualizzare le dipendenze della macchina.
4. La mappa di dipendenza per il computer visualizza i dettagli seguenti:
    - (Client) in entrata e in uscita da e verso la macchina di connessioni TCP (server)
        - Le macchine di dipendenti che non sono installato l'agente MMA e delle dipendenze verranno raggruppate i numeri di porta
        - Le macchine dependenct che dispongono di MMA e installato l'agente di dipendenza vengono visualizzate come finestre separate 
    - Processi in esecuzione all'interno della macchina, è possibile espandere ogni casella macchina per visualizzare i processi
    - Proprietà come nome di dominio completo, sistema operativo, e così via. indirizzo MAC di ogni computer, è possibile fare clic su ogni casella macchina per visualizzare i dettagli

 ![Visualizzare le dipendenze di computer](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. È possibile esaminare le dipendenze per intervalli di tempo diversi facendo clic su intervallo di tempo in etichetta dell'intervallo di tempo. Per impostazione predefinita, l'intervallo è un'ora. È possibile modificare l'intervallo di tempo oppure specificare le date di inizio e fine e la durata.
5. Dopo avere identificato macchine dipendenti che si desidera raggruppare, utilizzare Ctrl + clic per selezionare più computer nella mappa, quindi scegliere **raggruppare macchine**.
6. Specificare un nome di gruppo. Verificare che le macchine dipendenti vengono individuate da eseguire la migrazione di Azure. 

    > [!NOTE]
    > Se una macchina dipendenti non viene individuata da eseguire la migrazione di Azure, non è possibile aggiungerlo al gruppo. Per aggiungere tali macchine al gruppo, è necessario eseguire nuovamente il processo di individuazione con l'ambito corretto in vCenter Server e verificare che il computer viene individuato da eseguire la migrazione di Azure.  

7. Se si desidera creare una valutazione per questo gruppo, selezionare la casella di controllo per creare una nuova valutazione per il gruppo.
8. Fare clic su **OK** per salvare il gruppo.

Una volta creato il gruppo, è consigliabile installare gli agenti in tutti i computer del gruppo e perfezionare il gruppo visualizzando la dipendenza dell'intero gruppo.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come](how-to-create-group-dependencies.md) per perfezionare il gruppo dalla visualizzazione delle dipendenze del gruppo
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
