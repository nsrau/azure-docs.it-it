---
title: Raggruppare i computer usando le dipendenze dei computer con Azure Migrate | Microsoft Docs
description: Descrive come creare una valutazione usando le dipendenze dei computer con il servizio Azure Migrate.
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0527e34e-a078-405e-aeb9-c91a5808112a
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 5ed49f3dc71af6a8c1c7b6c9e38fd84452505aec
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2017
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Raggruppare i computer usando il mapping delle dipendenze dei computer

Questo articolo descrive come creare un gruppo di computer per eseguire la valutazione con [Azure Migrate](migrate-overview.md) usando il mapping delle dipendenze dei computer. Questo metodo viene in genere usato quando si vogliono valutare gruppi di macchine virtuali con livelli di attendibilità più elevati controllando in modo incrociato le dipendenze dei computer prima di eseguire una valutazione.



## <a name="prepare-machines-for-dependency-mapping"></a>Preparare i computer per il mapping delle dipendenze
Per includere i computer nel mapping delle dipendenze, è necessario scaricare e installare gli agenti in ogni computer locale che si vuole valutare. Inoltre, se si dispone di computer senza accesso a Internet, è necessario scaricare e installare il [gateway OMS](../log-analytics/log-analytics-oms-gateway.md).

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
2. Nella colonna **Dipendenze** viene ora visualizzata la dicitura **Visualizza dipendenze**. Fare clic sulla colonna per visualizzare le dipendenze.
3. Per ogni computer è possibile verificare:
    - Se MMA e Dependency Agent sono installati e se il computer è stato individuato.
    - Il sistema operativo guest in esecuzione nel computer.
    - Le porte e le connessioni IP in ingresso e in uscita.
    - I processi in esecuzione nei computer.
    - Le dipendenze tra i computer.

4. Per visualizzare le dipendenze a un livello più dettagliato, fare clic sull'intervallo di tempo per modificarlo. Per impostazione predefinita, l'intervallo è un'ora. È possibile modificare l'intervallo di tempo oppure specificare le date di inizio e fine e la durata.
5. Dopo aver identificato i computer dipendenti che si vogliono raggruppare, selezionare i computer nella mappa e fare clic su **Raggruppa macchine virtuali**.
6. Specificare un nome di gruppo. Verificare che il computer sia stato individuato da Azure Migrate. In caso contrario, eseguire nuovamente il processo di individuazione a livello locale. Se si vuole, è possibile eseguire una valutazione immediatamente.
7. Fare clic su **OK** per salvare il gruppo.

    ![Creazione di un gruppo con le dipendenze dei computer](./media/how-to-create-group-machine-dependencies/create-group.png)

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni](how-to-create-group-dependencies.md) su come ridefinire il gruppo selezionando le dipendenze del gruppo
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
