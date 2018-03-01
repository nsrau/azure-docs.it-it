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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Raggruppare i computer usando il mapping delle dipendenze dei computer

Questo articolo descrive come creare un gruppo di computer per eseguire la valutazione con [Azure Migrate](migrate-overview.md) visualizzando le dipendenze dei computer. Questo metodo viene in genere usato quando si vogliono valutare gruppi di macchine virtuali con livelli di attendibilità più elevati controllando in modo incrociato le dipendenze dei computer prima di eseguire una valutazione. La visualizzazione delle dipendenze è utile per pianificare in modo efficace la migrazione a Azure. Garantisce inoltre una migrazione completa e senza interruzioni a sorpresa. È possibile individuare tutti i sistemi interdipendenti di cui è necessario eseguire contemporaneamente la migrazione e determinare se un sistema in esecuzione è ancora utile o se è un candidato alla rimozione anziché alla migrazione. 


## <a name="prepare-machines-for-dependency-mapping"></a>Preparare i computer per il mapping delle dipendenze
Per visualizzare le dipendenze dei computer, è necessario scaricare e installare gli agenti in ogni computer locale che si vuole valutare. Inoltre, se si dispone di computer senza accesso a Internet, è necessario scaricare e installare il [gateway OMS](../log-analytics/log-analytics-oms-gateway.md).

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
2. Cercare il computer in cui sono stati installati gli agenti.
3. Nella colonna **Dipendenze** relativa al computer viene ora visualizzata la dicitura **Visualizza dipendenze**. Fare clic sulla colonna per visualizzare le dipendenze del computer.
4. La mappa delle dipendenze del computer mostra i dettagli seguenti:
    - Le connessioni TCP in ingresso (client) e in uscita (server) verso o dal computer
        - I computer dipendenti in cui non sono installati l'agente MMA e l'agente Dependency Agent sono raggruppati in base ai numeri di porta
        - I computer dipendenti in cui sono installati l'agente MMA e l'agente Dependency Agent sono visualizzati in caselle separate 
    - I processi in esecuzione sul computer: è possibile espandere ogni casella di computer per visualizzare i processi
    - Le proprietà di ogni computer, come il nome di dominio completo, il sistema operativo, l'indirizzo MAC e così via: fare clic su ogni casella di computer per visualizzare questi dettagli

 ![Visualizzare le dipendenze dei computer](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. È possibile esaminare le dipendenze per intervalli di tempo diversi facendo clic sulla durata nell'etichetta dell'intervallo di tempo. Per impostazione predefinita, l'intervallo è un'ora. È possibile modificare l'intervallo di tempo oppure specificare le date di inizio e fine e la durata.
5. Dopo aver identificato i computer dipendenti da raggruppare, fare clic tenendo premuto CTRL per selezionare più computer nella mappa e quindi fare clic su **Raggruppa macchine virtuali**.
6. Specificare un nome di gruppo. Verificare che i computer dipendenti siano stati individuati da Azure Migrate. 

    > [!NOTE]
    > Se un computer dipendente non viene individuato da Azure Migrate, non è possibile aggiungerlo al gruppo. Per aggiungere tali computer al gruppo, è necessario eseguire nuovamente il processo di individuazione con l'ambito corretto nel server vCenter e verificare che il computer venga individuato da Azure Migrate.  

7. Se si vuole creare una valutazione per questo gruppo, selezionare la casella di controllo per creare una nuova valutazione per il gruppo.
8. Fare clic su **OK** per salvare il gruppo.

Una volta creato il gruppo, è consigliabile installare gli agenti in tutti i computer del gruppo e perfezionare il gruppo visualizzandone tutte le dipendenze.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni](how-to-create-group-dependencies.md) su come perfezionare il gruppo visualizzandone le dipendenze
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
