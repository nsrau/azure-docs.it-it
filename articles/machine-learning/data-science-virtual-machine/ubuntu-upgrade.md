---
title: Come aggiornare il Data Science Virtual Machine a Ubuntu 18,04
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni su come eseguire l'aggiornamento da CentOS e Ubuntu 16,04 alla versione più recente di Ubuntu 18,04 Data Science Virtual Machine.
keywords: Deep Learning, intelligenza artificiale, strumenti di data science, data science macchina virtuale, processo team data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 5b98384d4d735f4c124c6af40d6edbff896900ce
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320982"
---
# <a name="upgrade-your-data-science-virtual-machine-to-ubuntu-1804"></a>Aggiornare la Data Science Virtual Machine a Ubuntu 18.04

Se si dispone di un Data Science Virtual Machine che esegue una versione precedente, ad esempio Ubuntu 16,04 o CentOS, è necessario eseguire la migrazione della DSVM a Ubuntu 18,04. La migrazione assicurerà di ottenere le patch, i driver, il software preinstallato e le versioni di libreria più recenti del sistema operativo. Questo documento descrive come eseguire la migrazione da versioni precedenti di Ubuntu o da CentOS. 

## <a name="prerequisites"></a>Prerequisiti

- Familiarità con SSH e la riga di comando di Linux

## <a name="overview"></a>Panoramica

Esistono due possibili modi per eseguire la migrazione:

- Migrazione sul posto, denominata anche migrazione "stessa server". Questa migrazione aggiorna la VM esistente senza creare una nuova macchina virtuale. La migrazione sul posto è il modo più semplice per eseguire la migrazione da Ubuntu 16,04 a Ubuntu 18,04.
- Migrazione side-by-Side, denominata anche migrazione tra server. Questa migrazione trasferisce i dati dalla macchina virtuale esistente a una VM appena creata. La migrazione side-by-Side consente di eseguire la migrazione da CentOS a Ubuntu 18,04. Potrebbe essere preferibile eseguire la migrazione side-by-side per l'aggiornamento tra le versioni di Ubuntu se si ritiene che l'installazione precedente sia stata inutilmente ingombrata.

## <a name="snapshot-your-vm-in-case-you-need-to-roll-back"></a>Snapshot della macchina virtuale nel caso in cui sia necessario eseguire il rollback

Nella portale di Azure usare la barra di ricerca per trovare la funzionalità **snapshot** . 

:::image type="content" source="media/ubuntu_upgrade/azure-portal-search-bar.png" alt-text="Screenshot che Mostra portale di Azure e barra di ricerca con * * snapshot * * evidenziato":::

1. Selezionare **Aggiungi** , che consente di passare alla pagina **Crea snapshot** . Selezionare la sottoscrizione e il gruppo di risorse della macchina virtuale. Per **area** selezionare la stessa area in cui è presente l'archivio di destinazione. Selezionare il disco di archiviazione DSVM e altre opzioni di backup. **HDD standard** è un tipo di archiviazione appropriato per questo scenario di backup.

:::image type="content" source="media/ubuntu_upgrade/create-snapshot-options.png" alt-text="Screenshot che mostra le opzioni di ' Crea snapshot '":::

2. Una volta completati tutti i dettagli e vengono superate le convalide, selezionare **Verifica + crea** per convalidare e creare lo snapshot. Quando lo snapshot viene completato correttamente, viene visualizzato un messaggio che informa che la distribuzione è stata completata.

## <a name="in-place-migration"></a>Migrazione sul posto

Se si sta eseguendo la migrazione di una versione precedente di Ubuntu, è possibile scegliere di eseguire una migrazione sul posto. Questa migrazione non crea una nuova macchina virtuale e prevede un minor numero di passaggi rispetto a una migrazione side-by-side.  Se si desidera eseguire una migrazione side-by-side perché si desidera un maggiore controllo o perché si sta eseguendo la migrazione da una distribuzione diversa, ad esempio CentOS, passare alla sezione relativa alla [migrazione side-by-side](#side-by-side-migration) . 

1. Dal portale di Azure, avviare il DSVM e accedere con SSH. A tale scopo, selezionare **Connetti** e **SSH** e seguire le istruzioni per la connessione. 

1. Una volta stabilita la connessione a una sessione terminal in DSVM, eseguire il comando di aggiornamento seguente:

    ```bash
    sudo do-release-upgrade
    ```

Il completamento del processo di aggiornamento potrebbe richiedere del tempo. Al termine, il programma richiederà l'autorizzazione per riavviare la macchina virtuale. Risposta **Sì**. L'utente verrà disconnesso dalla sessione SSH durante il riavvio del sistema.

### <a name="if-necessary-regenerate-ssh-keys"></a>Se necessario, rigenerare le chiavi SSH

> [!IMPORTANT] 
> Dopo l'aggiornamento e il riavvio, potrebbe essere necessario rigenerare le chiavi SSH.

Dopo che la macchina virtuale è stata aggiornata e riavviata, provare ad accedere di nuovo tramite SSH. È possibile che l'indirizzo IP sia stato modificato durante il riavvio, quindi confermarlo prima di provare a connettersi.

Se viene visualizzato l'errore l' **identificazione dell'host remoto è cambiata** , sarà necessario rigenerare le credenziali SSH.

:::image type="content" source="media/ubuntu_upgrade/remote-host-warning.png" alt-text="Schermata di PowerShell che mostra l'avviso di identificazione dell'host remoto modificato":::

A tale scopo, nel computer locale eseguire il comando:

```bash
ssh-keygen -R "your server hostname or ip"
```

A questo punto dovrebbe essere possibile connettersi con SSH. Se si verificano ancora problemi, nella pagina **Connetti** seguire il collegamento per **risolvere i problemi di connettività SSH**.

## <a name="side-by-side-migration"></a>Migrazione affiancata

Se si esegue la migrazione da CentOS o si desidera un'installazione pulita del sistema operativo, è possibile eseguire una migrazione side-by-side. Questo tipo di migrazione prevede più passaggi, ma consente di controllare esattamente quali file vengono trasportati.

Le migrazioni da altri sistemi basati sullo stesso set di pacchetti di origine upstream dovrebbero essere relativamente semplici, ad esempio [domande frequenti/CentOS3](https://wiki.centos.org/FAQ/CentOS3).

È possibile scegliere di aggiornare le parti del sistema operativo del file System e lasciare le directory utente, ad esempio `/home` sul posto. Se si lasciano invariate le Home directory degli utenti precedenti, è possibile che si verifichino problemi con i menu GNOME/KDE e altri elementi del desktop. Potrebbe essere più semplice creare nuovi account utente e montare le directory precedenti in un'altra posizione nel file System per riferimento, copia o collegamento del materiale degli utenti dopo la migrazione.

### <a name="migration-at-a-glance"></a>Panoramica della migrazione

1.  Creare uno snapshot della macchina virtuale esistente come descritto in precedenza

1.  Creare un disco da tale snapshot

1.  Crea una nuova Data Science Virtual Machine Ubuntu

1.  Ricreare gli account utente nella nuova macchina virtuale

1.  Montare il disco della VM snapshotted come disco dati nella nuova Data Science Virtual Machine

1.  Copiare manualmente i dati desiderati

### <a name="create-a-disk-from-your-vm-snapshot"></a>Creare un disco dallo snapshot della macchina virtuale

Se non è già stato creato uno snapshot della macchina virtuale come descritto in precedenza, eseguire questa operazione. 

1. Nella portale di Azure cercare **dischi** e selezionare **Aggiungi** per aprire la pagina **disco** .

:::image type="content" source="media/ubuntu_upgrade/portal-disks-search.png" alt-text="Screenshot della portale di Azure visualizzazione della pagina Cerca dischi e del pulsante Aggiungi":::

2. Impostare la **sottoscrizione** , il **gruppo di risorse** e l' **area** sui valori dello snapshot della VM. Scegliere un **nome** per il disco da creare.

3. Selezionare il **tipo di origine** come **snapshot** e selezionare lo snapshot della macchina virtuale come snapshot di **origine**. Esaminare e creare il disco. 

:::image type="content" source="media/ubuntu_upgrade/disk-create-options.png" alt-text="Screenshot della finestra di dialogo di creazione del disco con opzioni":::

### <a name="create-a-new-ubuntu-data-science-virtual-machine"></a>Crea una nuova Data Science Virtual Machine Ubuntu

Creare una nuova Data Science Virtual Machine di Ubuntu usando il [portale di Azure](https://portal.azure.com) o un [modello ARM](./dsvm-tutorial-resource-manager.md). 

### <a name="recreate-user-accounts-on-your-new-data-science-virtual-machine"></a>Ricreare gli account utente nella nuova Data Science Virtual Machine

Poiché verranno copiati solo i dati dal computer precedente, sarà necessario ricreare gli account utente e gli ambienti software che si desidera utilizzare nel nuovo computer.

Linux è sufficientemente flessibile da consentire di personalizzare le directory e i percorsi nella nuova installazione per seguire il vecchio computer. In generale, tuttavia, è più semplice usare il layout preferito di Ubuntu moderno e modificare l'ambiente utente e gli script per adattarsi.

Per altre informazioni, vedere [Guida introduttiva: configurare la Data Science Virtual Machine per Linux (Ubuntu)](./dsvm-ubuntu-intro.md).

### <a name="mount-the-disk-of-the-snapshotted-vm-as-a-data-disk-on-your-new-data-science-virtual-machine"></a>Montare il disco della VM snapshotted come disco dati nella nuova Data Science Virtual Machine

1. Nella portale di Azure verificare che il Data Science Virtual Machine sia in esecuzione.

2. Nella portale di Azure andare alla pagina della Data Science Virtual Machine. Scegliere il pannello **dischi** sul binario sinistro. Scegliere **Connetti dischi esistenti**.

3. Nell'elenco a discesa **nome disco** selezionare il disco creato dallo snapshot precedente della macchina virtuale.

:::image type="content" source="media/ubuntu_upgrade/attach-data-disk.png" alt-text="Screenshot della pagina delle opzioni di DSVM che mostra le opzioni di collegamento del disco":::

4. Selezionare **Save (Salva** ) per aggiornare la macchina virtuale.

> [!Important]
> La macchina virtuale deve essere in esecuzione nel momento in cui si connette il disco dati. Se la macchina virtuale non è in esecuzione, i dischi possono essere aggiunti in un ordine non corretto, causando un sistema confuso e potenzialmente non avviabile. Se si aggiunge il disco dati con la macchina virtuale disattivata, scegliere la **X** accanto al disco dati, avviare la macchina virtuale e collegarla nuovamente.

### <a name="manually-copy-the-wanted-data"></a>Copiare manualmente i dati desiderati 

1. Accedere alla macchina virtuale in esecuzione tramite SSH.

2. Verificare di aver collegato il disco creato dallo snapshot della macchina virtuale precedente eseguendo il comando seguente:

    ```bash
    lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i 'sd'
    ```
    
    I risultati dovrebbero avere un aspetto simile all'immagine seguente. Nell'immagine, il disco `sda1` viene montato alla radice ed `sdb2` è il `/mnt` disco scratch. Il disco dati creato dallo snapshot della macchina virtuale precedente viene identificato come `sdc1` , ma non è ancora disponibile, come evidenziato dalla mancanza di un percorso di montaggio. I risultati potrebbero avere identificatori diversi, ma verrà visualizzato un modello simile.
    
    :::image type="content" source="media/ubuntu_upgrade/lsblk-results.png" alt-text="Screenshot dell'output di lsblk, che mostra l'unità dati non montata":::
    
3. Per accedere all'unità dati, crearne un percorso e montarlo. Sostituire `/dev/sdc1` con il valore appropriato restituito da `lsblk` :

    ```bash
    sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
    ```
    
4. Contiene ora `/datadrive` le directory e i file del Data Science Virtual Machine precedente. Spostare o copiare le directory o i file desiderati dall'unità dati alla nuova VM.

Per altre informazioni, vedere [usare il portale per aggiungere un disco dati a una VM Linux](../../virtual-machines/linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk).

## <a name="connect-and-confirm-version-upgrade"></a>Connetti e conferma aggiornamento versione

Se è stata eseguita una migrazione sul posto o affiancata, verificare che l'aggiornamento sia stato completato correttamente. Da una sessione terminal eseguire: 

```bash
cat /etc/os-release
```

Si noterà che si sta eseguendo Ubuntu 18,04.

:::image type="content" source="media/ubuntu_upgrade/ssh-os-release.png" alt-text="Screenshot del terminale Ubuntu che mostra i dati sulla versione del sistema operativo":::

La modifica della versione viene visualizzata anche nella portale di Azure.

:::image type="content" source="media/ubuntu_upgrade/portal-showing-os-version.png" alt-text="Screenshot del portale che mostra le proprietà di DSVM, inclusa la versione del sistema operativo":::

## <a name="next-steps"></a>Passaggi successivi

- [Data Science con un computer Ubuntu Data Science in Azure](./linux-dsvm-walkthrough.md)
- [Quali strumenti sono inclusi in Azure Data Science Virtual Machine?](./tools-included.md)