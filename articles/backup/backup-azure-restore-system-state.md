---
title: 'Backup di Azure: Ripristinare lo stato del sistema per Windows Server | Microsoft Docs'
description: Procedura dettagliata per il ripristino dello stato del sistema di Windows Server da un backup in Azure.
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/18/2017
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: 971f7979ca27bce5d147a9d479248c808dc658e3
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2017
---
# <a name="restore-system-state-to-windows-server"></a>Ripristinare lo stato del sistema per Windows Server

Questo articolo illustra come ripristinare i backup dello stato del sistema di Windows Server da un insieme di credenziali di Servizi di ripristino di Azure. Per ripristinare lo stato del sistema, è necessario avere un backup dello stato del sistema, creato usando le istruzioni disponibili in [Eseguire il backup dello stato del sistema](backup-azure-system-state.md#back-up-windows-server-system-state), e assicurarsi di avere installato la [versione più recente dell'agente dei Servizi di ripristino di Microsoft Azure](http://aka.ms/azurebackup_agent). Il ripristino dei dati dello stato del sistema di Windows Server da un insieme di credenziali di Servizi di ripristino di Azure è un processo in due passaggi:

1. Ripristinare lo stato del sistema sotto forma di file da Backup di Azure. Quando si ripristina lo stato del sistema sotto forma di file da Backup di Azure, è possibile eseguire una di queste operazioni:
  * Ripristinare lo stato del sistema nello stesso server in cui è stato creato il backup.
  * Ripristinare il file dello stato del sistema in un server alternativo.

2. Applicare i file dello stato del sistema ripristinato in un'istanza di Windows Server.


## <a name="recover-system-state-files-to-the-same-server"></a>Ripristinare i file dello stato del sistema nello stesso server
La procedura seguente illustra come eseguire il rollback della configurazione di Windows Server a uno stato precedente. Il rollback della configurazione del server a uno stato noto e stabile può essere estremamente utile. La procedura seguente consente di ripristinare lo stato del sistema del server da un insieme di credenziali dei Servizi di ripristino. 

1. Aprire lo snap-in di **Backup di Microsoft Azure** . Se non si sa dove è stato installato lo snap-in, cercare **Backup di Microsoft Azure** nel computer o nel server.

    L'applicazione desktop dovrebbe essere visualizzata nei risultati della ricerca.

2. Fare clic su **Ripristina dati** per avviare la procedura guidata.

    ![Ripristina dati](./media/backup-azure-restore-windows-server/recover.png)

3. Nel riquadro **Guida introduttiva** selezionare l'opzione **This server (Questo server) (`<server name>`)** e fare clic su **Avanti** per ripristinare i dati nello stesso server o computer.

    ![Scegliere l'opzione This server (Questo server) per ripristinare i dati nello stesso computer](./media/backup-azure-restore-system-state/samemachine.png)

4. Nel riquadro **Seleziona modalità di ripristino** scegliere **Stato del sistema** e quindi fare clic su **Avanti**.

    ![Ricerca dei file](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. Nel calendario del riquadro **Seleziona volume e data** selezionare un punto di ripristino. 

    È possibile ripristinare da qualsiasi punto di ripristino. Le date in **grassetto** indicano la disponibilità di almeno un punto di ripristino. Dopo aver selezionato una data, se sono disponibili più punti di ripristino, scegliere quello appropriato dall'elenco a discesa **Ora**.

    ![Volume e dati](./media/backup-azure-restore-system-state/select-date.png)

6. Dopo aver selezionato il punto di ripristino, fare clic su **Avanti**.

    Backup di Azure monta il punto di ripristino locale e lo usa come volume di ripristino.

7. Nel riquadro successivo specificare la destinazione per i file ripristinati dello stato del sistema e quindi fare clic su **Sfoglia** per aprire Esplora risorse e trovare i file e le cartelle necessari. L'opzione **Crea copie in modo da mantenere entrambe le versioni** crea copie dei singoli file in un archivio di file dello stato del sistema esistente invece di creare la copia dell'intero archivio dello stato del sistema.

    ![Opzioni di ripristino](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Verificare i dettagli relativi al ripristino nel riquadro **Conferma** e fare clic su **Ripristina**.

   ![Fare clic su Ripristina per confermare l'azione di ripristino](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Copiare la directory *WindowsImageBackup* nella destinazione di ripristino in un volume non critico del server. Il volume del sistema operativo Windows è in genere il volume critico.

10. Al termine del ripristino, seguire la procedura illustrata nella sezione [Applicare i file ripristinati dello stato del sistema a Windows Server](backup-azure-restore-system-state.md#apply-restored-system-state-files-to-the-windows-server) per completare il processo di ripristino dello stato del sistema.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Ripristinare il file dello stato del sistema in un server alternativo

Se l'istanza di Windows Server è danneggiata o inaccessibile e si vuole ripristinarne uno stato stabile eseguendo il ripristino dello stato del sistema di Windows Server, è possibile ripristinare lo stato del sistema del server danneggiato da un altro server. Seguire questa procedura per ripristinare lo stato del sistema in un server separato.  

Include la terminologia utilizzata in questi passaggi:

- *Computer di origine* : il computer di origine da cui è stato eseguito il backup e che non è attualmente disponibile.
- *Computer di destinazione* : il computer in cui i dati vengono ripristinati.
- *Insieme di credenziali di esempio*: l'insieme di credenziali dei servizi di ripristino in cui il *computer di origine* e il *computer di destinazione* sono registrati. <br/>

> [!NOTE]
> I backup eseguiti da un computer non possono essere ripristinati in un computer che esegue una versione precedente del sistema operativo. Ad esempio, i backup eseguiti da un computer con Windows Server 2016 non possono essere ripristinati in Windows Server 2012 R2. È tuttavia possibile eseguire l'operazione inversa. È possibile usare i backup da Windows Server 2012 R2 per ripristinare Windows Server 2016.
>

1. Aprire lo snap-in di **Backup di Microsoft Azure** nel *Computer di destinazione*.
2. Assicurarsi che il *computer di destinazione* e il *computer di origine* siano registrati nello stesso insieme di credenziali dei servizi di ripristino.
3. Fare clic su **Ripristina dati** per avviare il flusso di lavoro.

    ![Ripristina dati](./media/backup-azure-restore-windows-server-classic/recover.png)

4. Selezionare **Un altro server**

    ![Un altro server](./media/backup-azure-restore-system-state/anotherserver.png)

5. Specificare il file dell'insieme di credenziali che corrisponde all' *Insieme di credenziali di esempio*. Se il file dell'insieme di credenziali non è valido (o è scaduto), è necessario scaricarne uno nuovo dall'*insieme di credenziali di esempio* nel Portale di Azure. Dopo aver specificato il file dell'insieme di credenziali, viene visualizzato l'insieme di credenziali di Servizi di ripristino associato al file di credenziali dell'insieme di credenziali.

6. Nel riquadro Seleziona server di backup selezionare il *computer di origine* dall'elenco di computer visualizzati.

    ![Elenco di computer](./media/backup-azure-restore-windows-server-classic/machinelist.png)

7. Nel riquadro Seleziona modalità di ripristino scegliere **Stato del sistema** e quindi fare clic su **Avanti**. 

    ![Search](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. Nel riquadro **Seleziona volume e data** del Calendario selezionare un punto di ripristino. È possibile ripristinare da qualsiasi punto di ripristino. Le date in **grassetto** indicano la disponibilità di almeno un punto di ripristino. Dopo aver selezionato una data, se sono disponibili più punti di ripristino, scegliere quello appropriato dal menu a discesa **Ora**. 

    ![Ricerca di elementi](./media/backup-azure-restore-system-state/select-date.png)

9. Dopo aver selezionato il punto di ripristino, fare clic su **Avanti**.

10. Nel riquadro **Selezionare la modalità di ripristino dello stato del sistema** specificare la destinazione per il ripristino dei file dello stato del sistema, quindi fare clic su **Avanti**.

    ![Crittografia](./media/backup-azure-restore-system-state/recover-as-files.png)

    L'opzione **Crea copie in modo da mantenere entrambe le versioni** crea copie dei singoli file in un archivio di file dello stato del sistema esistente invece di creare la copia dell'intero archivio dello stato del sistema.

11. Verificare i dettagli relativi al ripristino nel riquadro Conferma e fare clic su **Ripristina**. 

    ![Fare clic sul pulsante Ripristina per confermare il processo di ripristino](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Copiare la directory *WindowsImageBackup* in un volume non critico del server, ad esempio D:\). Il volume del sistema operativo Windows è in genere il volume critico.

13. Per completare il processo di ripristino, usare la sezione seguente per [applicare i file ripristinati dello stato del sistema a un'istanza di Windows Server](backup-azure-restore-system-state.md#apply-restored-system-state-on-a-windows-server).




## <a name="apply-restored-system-state-on-a-windows-server"></a>Applicare lo stato del sistema ripristinato a un'istanza di Windows Server

Dopo avere ripristinato lo stato del sistema sotto forma di file tramite l'agente dei Servizi di ripristino di Microsoft Azure, usare l'utilità Windows Server Backup per applicare lo stato del sistema ripristinato a un'istanza di Windows Server. L'utilità Windows Server Backup è già disponibile nel server. La procedura seguente illustra come applicare lo stato del sistema ripristinato.

1. Usare i comandi seguenti per riavviare il server in *Modalità di ripristino dei servizi directory*. In un prompt dei comandi con privilegi elevati:

    ```
    PS C:\> Bcdedit /set safeboot dsrepair
    PS C:\> Shutdown /r /t 0
    ```

2. Dopo il riavvio aprire lo snap-in Windows Server Backup. Se non si conosce il percorso di installazione dello snap-in, cercare **Windows Server Backup** nel computer o nel server.

    L'applicazione desktop viene visualizzata nei risultati della ricerca.

3. Nello snap-in selezionare **Backup locale**.

    ![Selezionare Backup locale per eseguire il ripristino da tale posizione](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. Nel **riquadro Azioni** della console Backup locale fare clic su **Ripristina** per aprire il Ripristino guidato.

5. Selezionare l'opzione **Backup archiviato in un altro percorso** e fare clic su **Avanti**.

   ![Scegliere il ripristino in un server diverso](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Quando si specifica il tipo di posizione, selezionare **Cartella condivisa remota** se il backup dello stato del sistema è stato ripristinato in un altro server. Se lo stato del sistema è stato ripristinato localmente, selezionare **Unità locali**. 

    ![Specificare se eseguire il ripristino da un server locale o da un altro server](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Immettere il percorso della directory *WindowsImageBackup* oppure scegliere l'unità locale contenente questa directory, ad esempio D:\WindowsImageBackup, ripristinata come parte del ripristino dei file dello stato del sistema tramite l'agente dei Servizi di ripristino di Azure Recovery e quindi fare clic su **Avanti**.

    ![Percorso del file condiviso](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Selezionare la versione dello stato del sistema da ripristinare, quindi fare clic su **Avanti**.

9. Nel riquadro Seleziona tipo di ripristino selezionare**Stato del sistema** e quindi fare clic su **Avanti**.

10. Come percorso del ripristino dello stato del sistema selezionare **Percorso originale** e fare clic su **Avanti**.

11. Verificare i dettagli della conferma e le impostazioni di riavvio, quindi fare clic su **Ripristina** per applicare i file ripristinati dello stato del sistema.

    ![Avviare il ripristino dei file dello stato del sistema](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Considerazioni speciali per il ripristino dello stato del sistema nel server di Active Directory

Il backup dello stato del sistema include i dati di Active Directory. Seguire questa procedura per ripristinare Active Directory Domain Service (AD DS) dallo stato corrente a uno stato precedente.

1. Riavviare il controller di dominio in Modalità ripristino servizi directory.
2. Seguire [questa procedura](https://technet.microsoft.com/en-us/library/cc794755(v=ws.10).aspx) per usare i cmdlet di Windows Server Backup per ripristinare AD DS.


## <a name="troubleshoot-failed-system-state-restore"></a>Risolvere i problemi relativi a un ripristino non riuscito dello stato del sistema

Se il processo precedente di applicazione dello stato del sistema non viene completato correttamente, usare Ambiente ripristino Windows per ripristinare l'istanza di Windows Server. La procedura seguente illustra come eseguire il ripristino con Ambiente ripristino Windows. Usare questa opzione solo se Windows Server non viene avviato normalmente dopo un ripristino dello stato del sistema. Il processo seguente cancella i dati non di sistema, quindi occorre eseguirlo con cautela. 

1. Avviare l'istanza di Windows Server in Ambiente ripristino Windows.

2. Selezionare una delle opzioni disponibili per la risoluzione dei problemi.

    ![Menu iniziale](./media/backup-azure-restore-system-state/winre-1.png)

3. Dalla schermata **Opzioni avanzate** selezionare **Prompt dei comandi** e specificare il nome utente e la password dell'amministratore del server.

   ![Menu iniziale](./media/backup-azure-restore-system-state/winre-2.png)

4. Specificare il nome utente e la password dell'amministratore del server.

    ![Menu iniziale](./media/backup-azure-restore-system-state/winre-3.png)

5. Quando si apre il prompt dei comandi in modalità Amministratore, eseguire questo comando per ottenere le versioni del backup dello stato del sistema.

    ```
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```
    ![Ottenere le versioni del backup dello stato del sistema](./media/backup-azure-restore-system-state/winre-4.png)

6. Eseguire il comando seguente per ottenere tutti i volumi disponibili nel backup.

    ```
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Ottenere le versioni del backup dello stato del sistema](./media/backup-azure-restore-system-state/winre-5.png)

7. Il comando seguente ripristina tutti i volumi che fanno parte del backup dello stato del sistema. Si noti che questo passaggio ripristina solo i volumi critici che fanno parte dello stato del sistema. Tutti i dati non di sistema vengono cancellati.

    ```
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```
     ![Ottenere le versioni del backup dello stato del sistema](./media/backup-azure-restore-system-state/winre-6.png)



## <a name="next-steps"></a>Passaggi successivi
* Dopo aver ripristinato i file e le cartelle, è possibile [gestire i backup](backup-azure-manage-windows-server.md).
