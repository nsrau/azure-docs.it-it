---
title: Clonare da un backup dell&quot;array virtuale Microsoft Azure StorSimple | Documentazione Microsoft
description: Informazioni su come clonare un backup e ripristinare un file dall&quot;array virtuale StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: ae076d52b532f5181868b2ab24ee8f2ffa887af7
ms.openlocfilehash: f1c050c499484b612d2f9dcae395f36524397008

---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Clonare da un backup dell'array virtuale StorSimple

## <a name="overview"></a>Panoramica

Questo articolo illustra passo per passo come clonare un set di backup di condivisioni o volumi sull'array virtuale Microsoft Azure StorSimple. Il backup clonato viene usato per ripristinare un file perso o eliminato. L'articolo include anche la procedura dettagliata per il ripristino a livello di elemento sull'array virtuale StorSimple configurato come file server.

## <a name="clone-shares-from-a-backup-set"></a>Clonare condivisioni da un set di backup

**Prima di tentare di clonare le condivisioni, assicurarsi di avere spazio sufficiente sul dispositivo per completare questa operazione.** Per clonare da un backup, nel [portale di Azure](https://portal.azure.com/), eseguire la procedura seguente.

#### <a name="to-clone-a-share"></a>Per clonare una condivisione

1. Passare al pannello **Dispositivi**. Selezionare e fare clic sul dispositivo, quindi fare clic su **Condivisioni**. Selezionare la condivisione che si desidera clonare, fare clic con il pulsante destro del mouse sulla condivisione per richiamare il menu di scelta rapida. Selezionare **Clona**.
   
   ![Clonare un backup](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. Nel pannello **Clona** fare clic su **Backup > Seleziona** e quindi eseguire le operazioni seguenti: 
   
   a.    Filtrare un backup su questo dispositivo in base all'intervallo di tempo. È possibile scegliere tra **Ultimi 7 giorni**, **Ultimi 30 giorni** e **Ultimo anno**.
   
   b.    Nell'elenco di backup filtrati visualizzato selezionare un backup da cui eseguire la clonazione.
   
   c.    Fare clic su **OK**.
   
   ![Clonare un backup](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. Nel pannello **Clona** fare clic su **Impostazioni di destinazione** e quindi eseguire le operazioni seguenti:
   
   a.    Fornire un nome per la condivisione. Il nome della condivisione deve contenere da 3 a 127 caratteri.
   
   b.    Fornire una descrizione per la condivisione clonata (facoltativo).
   
   c.    Non è possibile modificare il tipo della condivisione di cui si sta eseguendo il ripristino. Una condivisione a livelli viene clonata come condivisione a livelli e una condivisione aggiunta in locale come una condivisione aggiunta in locale.
   
   d.    La capacità viene impostata alla stessa dimensione della condivisione che si sta clonando.
   
   e.    Assegnare gli amministratori per la condivisione. Sarà possibile modificare le proprietà della condivisione tramite Esplora file dopo il completamento della clonazione.
   
   f.    Fare clic su **OK**.
   
   ![Clonare un backup](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Fare clic su **Clona** per avviare un processo di clonazione. Al termine del processo, viene avviata l'operazione di clonazione e si riceve una notifica. Per monitorare lo stato di avanzamento della clonazione, passare al pannello **Processi** e fare clic sul processo per visualizzarne i dettagli.
5. Dopo aver creato correttamente il clone, tornare al pannello **Condivisioni** sul dispositivo.
6. È ora possibile visualizzare la nuova condivisione clonata nell'elenco delle condivisioni nel dispositivo. Una condivisione a livelli viene clonata come condivisione a livelli e una condivisione aggiunta in locale come una condivisione aggiunta in locale.
   
   ![Clonare un backup](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Clonare volumi da un set di backup

Per clonare da un backup, nel portale di Azure, è necessario seguire una procedura simile a quella della clonazione di una condivisione. L'operazione di clonazione consente di clonare il backup in un nuovo volume nello stesso dispositivo virtuale. Non è possibile clonare in un dispositivo diverso.

#### <a name="to-clone-a-volume"></a>Per clonare un volume

1. Passare al pannello **Dispositivi**. Selezionare e fare clic sul dispositivo e quindi fare clic su **Volumi**. Selezionare il volume che si desidera clonare, fare clic con il pulsante destro del mouse sul volume per richiamare il menu di scelta rapida. Selezionare **Clona**.
   
   ![Clonare un volume](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. Nel pannello **Clona** fare clic su **Backup** e quindi eseguire le operazioni seguenti: 
   
   a.    Filtrare un backup su questo dispositivo in base all'intervallo di tempo. È possibile scegliere tra **Ultimi 7 giorni**, **Ultimi 30 giorni** e **Ultimo anno**. 
   
   b.    Nell'elenco di backup filtrati visualizzato selezionare un backup da cui eseguire la clonazione.
   
   c.    Fare clic su **OK**.
   
   ![Clonare un backup](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. Nel pannello **Clona** fare clic su **Impostazioni di destinazione del volume** e quindi eseguire le operazioni seguenti:
   
   a. Il nome del dispositivo viene inserito automaticamente.
   
   b. Specificare un nome di volume per il **volume clonato**. Il nome del volume deve contenere da 3 a 127 caratteri.
   
   c. Il tipo di volume viene automaticamente impostato sul volume originale. Un volume a livelli viene clonato come volume a livelli e un volume aggiunto in locale come un volume aggiunto in locale.
   
   d. Per gli **Host connessi** fare clic su **Seleziona**.
   
   ![Clonare un backup](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. Nel pannello **Host connessi** scegliere un record di controllo di accesso esistente o aggiungerne uno nuovo. Per aggiungere un nuovo record di controllo di accesso, sarà necessario fornire il relativo nome e il nome IQN dell'host. Fare clic su **Seleziona**.
   
   ![Clonare un backup](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Fare clic su **Clona** per avviare un processo di clonazione.
   
   ![Clonare un backup](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. La clonazione viene avviata dopo aver creato il relativo processo. Dopo aver creato la clonazione, questa viene visualizzata nel pannello Volumi nel dispositivo. Si noti che un volume a livelli viene clonato come volume a livelli e un volume aggiunto in locale come un volume aggiunto in locale.
   
   ![Clonare un backup](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Quando viene visualizzato nell'elenco dei volumi online, il volume è disponibile per l'uso. Aggiornare l'elenco delle destinazioni nella finestra Proprietà iniziatore iSCSI nell'host dell'iniziatore iSCSI. Una nuova destinazione contenente il nome del volume clonato verrà visualizzata come "inattiva" nella colonna dello stato.
8. Selezionare la destinazione e fare clic su **Connetti**. Dopo avere connesso il dispositivo, lo stato deve essere modificato in **Connesso**.
9. Nella finestra **Gestione disco** i volumi montati sono visualizzati come indicato nella figura seguente. Fare clic con il pulsante destro del mouse sul volume individuato (fare clic sul nome del disco) e fare clic su **Online**.

> [!IMPORTANT]
> Quando si tenta di clonare un volume o una condivisione da un set di backup, anche se il processo di clonazione non viene completato correttamente, è comunque possibile creare un volume o una condivisione di destinazione nel portale. È importante eliminare questo volume o questa condivisione di destinazione nel portale per ridurre al minimo eventuali problemi futuri causati da questo elemento.
> 
> 

## <a name="item-level-recovery-ilr"></a>Ripristino a livello di elemento (ILR)

Questa versione introduce il ripristino a livello di elemento (ILR) su un array virtuale StorSimple configurato come file server. La funzionalità consente di eseguire il ripristino granulare di file e cartelle da un backup cloud di tutte le condivisioni nel dispositivo StorSimple. Gli utenti possono recuperare i file eliminati dai backup recenti con un modello self-service.

Ogni condivisione dispone di una cartella *.backups* contenente i backup più recenti. L'utente può passare al backup desiderato, copiare i file e le cartelle rilevanti dal backup, quindi ripristinarli. In questo modo si eliminano le chiamate agli amministratori per il ripristino di file dai backup.

1. Quando si esegue un ILR, è possibile visualizzare i backup con Esplora file. Fare clic sulla condivisione specifica per la quale si desidera visualizzare il backup. Viene visualizzata una cartella *.backups* creata sotto la condivisione che archivia tutti i backup. Espandere la cartella *.backups* per visualizzare i backup. La cartella mostra così in dettaglio l'intera gerarchia di backup. Questa visualizzazione viene creata su richiesta e generalmente bastano pochi secondi per completarne la creazione.
   
   Le ultime cinque copie di backup vengono visualizzate in questo modo e possono essere usate per eseguire un ripristino a livello di elemento. Le cinque copie di backup più recenti includono sia i backup pianificati predefiniti sia i backup manuali.
   
   * **Backup pianificati** denominati &lt;Device name&gt;DailySchedule-YYYYMMDD-HHMMSS-UTC.
   * **Backup manuali** denominati Ad-hoc-YYYYMMDD-HHMMSS-UTC.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. Identificare il backup contenente la versione più recente del file eliminato. Anche se il nome della cartella contiene un timestamp UTC in ognuno dei casi precedenti, l'ora in cui è stata creata la cartella è l'ora effettiva del dispositivo all'avvio del backup. Usare il timestamp della cartella per individuare e identificare i backup.

3. Individuare la cartella o il file che si desidera ripristinare nel backup identificato nel passaggio precedente. Notare che è possibile visualizzare solo i file o le cartelle per cui si dispone delle autorizzazioni necessarie. Se non è possibile accedere a determinati file o cartelle, contattare un amministratore della condivisione. L'amministratore può usare Esplora file per modificare le autorizzazioni di condivisione e garantire l'accesso a file o cartelli specifici. Preferibilmente, l'amministratore della condivisione deve corrispondere a un gruppo utenti anziché a un utente singolo.

4. Copiare il file o la cartella nella condivisione appropriata nel file server StorSimple.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [Usare l'interfaccia utente Web per amministrare StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).




<!--HONumber=Nov16_HO4-->


