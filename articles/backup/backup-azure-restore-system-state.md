---
title: Ripristinare lo stato del sistema per Windows Server
description: Spiegazione dettagliata del ripristino dello stato del sistema di Windows Server da un backup in Azure.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 5212e5ea0ed3a8c0e0a8e9d4fa45f1eb6c901bf5
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86184465"
---
# <a name="restore-system-state-to-windows-server"></a>Ripristinare lo stato del sistema per Windows Server

Questo articolo illustra come ripristinare i backup dello stato del sistema di Windows Server da un insieme di credenziali di Servizi di ripristino di Azure. Per ripristinare lo stato del sistema, è necessario disporre di un backup dello stato del sistema, creato usando le istruzioni disponibili in eseguire il backup [dello stato del sistema](backup-azure-system-state.md#back-up-windows-server-system-state), e assicurarsi di aver installato la [versione più recente dell'agente di servizi di ripristino di Microsoft Azure (Mars)](https://aka.ms/azurebackup_agent). Il ripristino dei dati dello stato del sistema di Windows Server da un insieme di credenziali di Servizi di ripristino di Azure è un processo in due passaggi:

1. Ripristinare lo stato del sistema sotto forma di file da Backup di Azure. Quando si ripristina lo stato del sistema sotto forma di file da Backup di Azure, è possibile eseguire una di queste operazioni:
   * Ripristinare lo stato del sistema nello stesso server in cui è stato creato il backup.
   * Ripristinare il file dello stato del sistema in un server alternativo.

2. Applicare i file di stato del sistema ripristinati a un server Windows utilizzando l'utilità Windows Server Backup.

## <a name="recover-system-state-files-to-the-same-server"></a>Ripristinare i file dello stato del sistema nello stesso server

La procedura seguente illustra come eseguire il rollback della configurazione di Windows Server a uno stato precedente. Il rollback della configurazione del server a uno stato noto e stabile può essere estremamente utile. La procedura seguente consente di ripristinare lo stato del sistema del server da un insieme di credenziali dei Servizi di ripristino.

1. Aprire lo snap-in di **Backup di Microsoft Azure** . Se non si sa dove è stato installato lo snap-in, cercare **Backup di Microsoft Azure** nel computer o nel server.

    L'applicazione desktop dovrebbe essere visualizzata nei risultati della ricerca.

2. Fare clic su **Ripristina dati** per avviare la procedura guidata.

    ![Ripristina dati](./media/backup-azure-restore-windows-server/recover.png)

3. Nel riquadro **Guida introduttiva** selezionare l'opzione **This server (Questo server) (`<server name>`)** e fare clic su **Avanti** per ripristinare i dati nello stesso server o computer.

    ![Scegliere questa opzione del server per ripristinare i dati nello stesso computer](./media/backup-azure-restore-system-state/samemachine.png)

4. Nel riquadro **Seleziona modalità di ripristino** scegliere **Stato del sistema** e quindi fare clic su **Avanti**.

    ![Ricerca dei file](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. Nel calendario del riquadro **Seleziona volume e data** selezionare un punto di ripristino.

    È possibile ripristinare da qualsiasi punto di ripristino. Le date in **grassetto** indicano la disponibilità di almeno un punto di ripristino. Dopo aver selezionato una data, se sono disponibili più punti di ripristino, scegliere quello appropriato dall'elenco a discesa **Ora**.

    ![Volume e dati](./media/backup-azure-restore-system-state/select-date.png)

6. Dopo aver selezionato il punto di ripristino, fare clic su **Avanti**.

    Backup di Azure monta il punto di ripristino locale e lo usa come volume di ripristino.

7. Nel riquadro successivo specificare la destinazione per i file di stato del sistema ripristinati. Quindi fare clic su **Sfoglia** per aprire Esplora risorse e individuare i file e le cartelle desiderati. L'opzione **Crea copie in modo da mantenere entrambe le versioni** crea copie dei singoli file in un archivio di file dello stato del sistema esistente invece di creare la copia dell'intero archivio dello stato del sistema.

    ![Opzioni di ripristino](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Verificare i dettagli relativi al ripristino nel riquadro **Conferma** e fare clic su **Ripristina**.

   ![Fare clic su Ripristina per confermare l'azione di ripristino](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Copiare la directory *WindowsImageBackup* nella destinazione di ripristino in un volume non critico del server. Il volume del sistema operativo Windows è in genere il volume critico.

10. Una volta completato il ripristino, attenersi alla procedura riportata nella sezione [applicare lo stato del sistema ripristinato in un server Windows](#apply-restored-system-state-on-a-windows-server)per completare il processo di ripristino dello stato del sistema.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Ripristinare il file dello stato del sistema in un server alternativo

Se l'istanza di Windows Server è danneggiata o inaccessibile e si vuole ripristinarne uno stato stabile eseguendo il ripristino dello stato del sistema di Windows Server, è possibile ripristinare lo stato del sistema del server danneggiato da un altro server. Seguire questa procedura per ripristinare lo stato del sistema in un server separato.  

Include la terminologia utilizzata in questi passaggi:

* *Computer di origine* : il computer originale dal quale è stato effettuato il backup e che non è al momento disponibile.
* *Computer di destinazione* : il computer in cui i dati vengono ripristinati.
* Insieme di credenziali di *esempio* : l'insieme di credenziali dei servizi di ripristino in cui il *computer di origine* e quello di *destinazione* sono registrati.

> [!NOTE]
> I backup eseguiti da un computer non possono essere ripristinati in un computer che esegue una versione precedente del sistema operativo. Ad esempio, i backup eseguiti da un computer con Windows Server 2016 non possono essere ripristinati in Windows Server 2012 R2. È tuttavia possibile eseguire l'operazione inversa. È possibile usare i backup da Windows Server 2012 R2 per ripristinare Windows Server 2016.
>

1. Aprire lo snap-in **backup di Microsoft Azure** nel *computer di destinazione*.
2. Verificare che il *computer di destinazione* e il *computer di origine* siano registrati nello stesso insieme di credenziali di servizi di ripristino.
3. Fare clic su **Ripristina dati** per avviare il flusso di lavoro.
4. Selezionare **Un altro server**

    ![Un altro server](./media/backup-azure-restore-system-state/anotherserver.png)

5. Specificare il file dell'insieme di credenziali che corrisponde all' *Insieme di credenziali di esempio*. Se il file dell'insieme di credenziali non è valido (o è scaduto), scaricare un nuovo file di archivio delle credenziali dall'insieme di credenziali di *esempio* nel portale di Azure. Dopo aver specificato il file dell'insieme di credenziali, viene visualizzato l'insieme di credenziali di Servizi di ripristino associato al file di credenziali dell'insieme di credenziali.

6. Nel riquadro Seleziona server di backup selezionare il *computer di origine* dall'elenco di computer visualizzati.
7. Nel riquadro Seleziona modalità di ripristino scegliere **Stato del sistema** e quindi fare clic su **Avanti**.

    ![Cerca](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. Nel riquadro **Seleziona volume e data** del Calendario selezionare un punto di ripristino. È possibile ripristinare da qualsiasi punto di ripristino. Le date in **grassetto** indicano la disponibilità di almeno un punto di ripristino. Dopo aver selezionato una data, se sono disponibili più punti di ripristino, scegliere il punto di ripristino specifico dal menu a discesa **ora** .

    ![Ricerca di elementi](./media/backup-azure-restore-system-state/select-date.png)

9. Dopo aver selezionato il punto di ripristino, fare clic su **Avanti**.

10. Nel riquadro **Selezionare la modalità di ripristino dello stato del sistema** specificare la destinazione per il ripristino dei file dello stato del sistema, quindi fare clic su **Avanti**.

    ![Crittografia](./media/backup-azure-restore-system-state/recover-as-files.png)

    L'opzione **Crea copie in modo da mantenere entrambe le versioni** crea copie dei singoli file in un archivio di file dello stato del sistema esistente invece di creare la copia dell'intero archivio dello stato del sistema.

11. Verificare i dettagli del ripristino nel riquadro conferma e fare clic su **Ripristina**.

    ![Fare clic sul pulsante Ripristina per confermare il processo di ripristino](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Copiare la directory *WindowsImageBackup* in un volume non critico del server, ad esempio D:\). Il volume del sistema operativo Windows è in genere il volume critico.

13. Per completare il processo di ripristino, usare la sezione seguente per [applicare i file ripristinati dello stato del sistema a un'istanza di Windows Server](#apply-restored-system-state-on-a-windows-server).

## <a name="apply-restored-system-state-on-a-windows-server"></a>Applicare lo stato del sistema ripristinato a un'istanza di Windows Server

Dopo avere ripristinato lo stato del sistema sotto forma di file tramite l'agente dei Servizi di ripristino di Microsoft Azure, usare l'utilità Windows Server Backup per applicare lo stato del sistema ripristinato a un'istanza di Windows Server. L'utilità Windows Server Backup è già disponibile nel server. La procedura seguente illustra come applicare lo stato del sistema ripristinato.

1. Aprire lo snap-in Windows Server Backup. Se non si conosce il percorso di installazione dello snap-in, cercare **Windows Server Backup** nel computer o nel server.

    L'applicazione desktop viene visualizzata nei risultati della ricerca. Se non viene visualizzato o si verificano errori quando si apre l'applicazione, è necessario installare le **funzionalità di Windows Server Backup**e i componenti dipendenti sottostanti, che sono disponibili nell' **Aggiunta guidata funzionalità** di **Server Manager**.

1. Nello snap-in selezionare **Backup locale**.

    ![Selezionare Backup locale per eseguire il ripristino da tale posizione](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

1. Nel **riquadro Azioni** della console Backup locale fare clic su **Ripristina** per aprire il Ripristino guidato.

1. Selezionare l'opzione **Backup archiviato in un altro percorso** e fare clic su **Avanti**.

   ![Scegliere il ripristino in un server diverso](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

1. Quando si specifica il tipo di posizione, selezionare **Cartella condivisa remota** se il backup dello stato del sistema è stato ripristinato in un altro server. Se lo stato del sistema è stato ripristinato localmente, selezionare **Unità locali**.

    ![Specificare se eseguire il ripristino da un server locale o da un altro server](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

1. Immettere il percorso della directory *WindowsImageBackup* oppure scegliere l'unità locale contenente questa directory, ad esempio D:\WindowsImageBackup, ripristinata come parte del ripristino dei file dello stato del sistema tramite l'agente dei Servizi di ripristino di Azure Recovery e quindi fare clic su **Avanti**.

    ![Percorso del file condiviso](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

1. Selezionare la versione dello stato del sistema da ripristinare, quindi fare clic su **Avanti**.

1. Nel riquadro Seleziona tipo di ripristino selezionare**Stato del sistema** e quindi fare clic su **Avanti**.

1. Come percorso del ripristino dello stato del sistema selezionare **Percorso originale** e fare clic su **Avanti**.

    Se si esegue il ripristino di un controller di dominio, verrà visualizzata l'opzione aggiuntiva seguente:

    ![Percorso per il ripristino dello stato del sistema](./media/backup-azure-restore-system-state/location-for-system-state-recovery.png)

    >[!NOTE]
    >Selezionare "Esegui un ripristino autorevole dei file di Active Directory" solo se si intende eseguire un ripristino autorevole di tutti i dati Active Directory in modo esplicito.

1. Verificare i dettagli della conferma e le impostazioni di riavvio, quindi fare clic su **Ripristina** per applicare i file ripristinati dello stato del sistema.

    ![Avviare il ripristino dei file dello stato del sistema](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

    >[!NOTE]
    >Non selezionare l'opzione **Riavvia automaticamente il server** se si esegue il ripristino in modalità ripristino servizi directory.

1. Dopo aver completato correttamente il ripristino, è necessario riavviare il server in modalità normale. Aprire un prompt dei comandi e digitare quanto segue:`bcdedit /deletevalue safeboot`
1. Riavviare il server.

## <a name="special-considerations-for-system-state-recovery-on-a-domain-controller"></a>Considerazioni speciali per il ripristino dello stato del sistema in un controller di dominio

Il backup dello stato del sistema include i dati di Active Directory. Seguire questa procedura per ripristinare Active Directory Domain Service (AD DS) dallo stato corrente a uno stato precedente. Questo tipo di ripristino può essere eseguito in due scenari:

* Ripristino di tutti i dati di Active Directory quando nell'insieme di strutture non sono rimasti controller di dominio funzionanti
* Ripristino di una parte dei dati di Active Directory quando tali oggetti sono stati eliminati o danneggiati

In questo articolo verrà illustrato solo il primo scenario, che richiede un ripristino nonauthorative di servizi di dominio Active Directory e un ripristino autorevole della cartella SYSVOL.  Se è necessario eseguire il secondo scenario, in cui i controller di dominio sono ancora funzionali ma è necessario ripristinare determinati oggetti di Active Directory, vedere [queste istruzioni](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac).

1. Per [ripristinare i file di stato del sistema in un server alternativo](#recover-system-state-files-to-an-alternate-server), attenersi alla procedura riportata di seguito.
1. Usare i comandi seguenti per riavviare il server in *Modalità di ripristino dei servizi directory*. In un prompt dei comandi con privilegi elevati:

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

1. Per ripristinare Active Directory come parte del ripristino dello stato del sistema, è possibile scegliere uno dei due metodi seguenti:

    * Seguire le istruzioni riportate sopra per [applicare lo stato del sistema ripristinato in un server Windows](#apply-restored-system-state-on-a-windows-server) con l'utilità Windows Server backup.

        >[!NOTE]
        >Se si ripristinano tutti i dati Active Directory (e non sono presenti controller di dominio che funzionano nella foresta), nel passaggio 9 precedente assicurarsi di selezionare **Esegui un ripristino autorevole di file di Active Directory**.

    * Utilizzare l'utilità [Wbadmin](https://docs.microsoft.com/windows-server/administration/windows-commands/wbadmin-start-systemstaterecovery) per eseguire il ripristino dalla riga di comando.

        È necessario l'identificatore di versione del backup che si vuole usare. È possibile ottenere un elenco di identificatori di versione eseguendo questo comando:

        ```cmd
        wbadmin get versions -backuptarget <servername\sharename>
        ```

        Usare quindi tale identificatore di versione per eseguire il ripristino.

        Ad esempio, per eseguire un [ripristino nonauthorative di servizi di dominio Active Directory e un ripristino autorevole della cartella SYSVOL](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-nonauthoritative-restore) usando il backup da 04/30/2020 alle 9:00 AM, archiviato nella risorsa condivisa `\\servername\share` per `server01` , digitare:

        ```cmd
        wbadmin start systemstaterecovery -version:04/30/2020-09:00 -backupTarget:\\servername\share -machine:server01 -authsysvol
        ```

1. Dopo aver completato correttamente il ripristino, è necessario riavviare il server in modalità normale. Aprire un prompt dei comandi e digitare quanto segue:`bcdedit /deletevalue safeboot`
1. Riavviare il server.

## <a name="troubleshoot-failed-system-state-restore"></a>Risolvere i problemi relativi a un ripristino non riuscito dello stato del sistema

Se il processo precedente di applicazione dello stato del sistema non viene completato correttamente, utilizzare ambiente ripristino Windows (Win RE) per ripristinare Windows Server. La procedura seguente illustra come eseguire il ripristino con Ambiente ripristino Windows. Usare questa opzione solo se Windows Server non viene avviato normalmente dopo un ripristino dello stato del sistema. Il processo seguente cancella i dati non di sistema, quindi occorre eseguirlo con cautela.

1. Avviare l'istanza di Windows Server in Ambiente ripristino Windows.

2. Selezionare una delle opzioni disponibili per la risoluzione dei problemi.

    ![Menu iniziale](./media/backup-azure-restore-system-state/winre-1.png)

3. Dalla schermata **Opzioni avanzate** selezionare **Prompt dei comandi** e specificare il nome utente e la password dell'amministratore del server.

   ![Menu iniziale](./media/backup-azure-restore-system-state/winre-2.png)

4. Specificare il nome utente e la password dell'amministratore del server.

    ![Menu iniziale](./media/backup-azure-restore-system-state/winre-3.png)

5. Quando si apre il prompt dei comandi in modalità Amministratore, eseguire questo comando per ottenere le versioni del backup dello stato del sistema.

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![Ottenere le versioni del backup dello stato del sistema](./media/backup-azure-restore-system-state/winre-4.png)

6. Eseguire il comando seguente per ottenere tutti i volumi disponibili nel backup.

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Ottenere le versioni del backup dello stato del sistema](./media/backup-azure-restore-system-state/winre-5.png)

7. Il comando seguente ripristina tutti i volumi che fanno parte del backup dello stato del sistema. Si noti che questo passaggio ripristina solo i volumi critici che fanno parte dello stato del sistema. Tutti i dati non di sistema vengono cancellati.

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![Ottenere le versioni del backup dello stato del sistema](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>Passaggi successivi

* Dopo aver ripristinato i file e le cartelle, è possibile [gestire i backup](backup-azure-manage-windows-server.md).
