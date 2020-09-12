---
title: Gestire gli snapshot tramite Azure NetApp Files | Microsoft Docs
description: Viene descritto come creare e gestire gli snapshot utilizzando Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/04/2020
ms.author: b-juche
ms.openlocfilehash: 405d872c178a3172454943b7d40ea276ea5c017e
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89459087"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Gestire gli snapshot tramite Azure NetApp Files

Azure NetApp Files supporta la creazione di snapshot su richiesta e l'uso di criteri snapshot per pianificare la creazione automatica di snapshot.  È anche possibile ripristinare uno snapshot in un nuovo volume o ripristinare un singolo file usando un client.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Creare uno snapshot su richiesta per un volume

È possibile creare snapshot del volume su richiesta. 

1.  Passare al volume per il quale si desidera creare uno snapshot. Fare clic su **snapshot**.

    ![Passare agli snapshot](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Fare clic su **+ Aggiungi snapshot** per creare uno snapshot su richiesta per un volume.

    ![Aggiungi snapshot](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Nella finestra Nuovo snapshot, specificare un nome per il nuovo snapshot che si sta creando.   

    ![Nuovo snapshot](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Fare clic su **OK**. 

## <a name="manage-snapshot-policies"></a>Gestire i criteri di snapshot

È possibile pianificare l'uso automatico degli snapshot del volume usando i criteri di snapshot. È anche possibile modificare i criteri di snapshot in base alle esigenze o eliminare un criterio di snapshot non più necessario.  

### <a name="register-the-feature"></a>Registrare la funzionalità

La funzionalità per i **criteri di snapshot** è attualmente in anteprima. Se si utilizza questa funzionalità per la prima volta, è necessario prima registrare la funzionalità. 

1. Registrare la funzionalità: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. Verificare lo stato della registrazione della funzionalità: 

    > [!NOTE]
    > Il **RegistrationState** potrebbe trovarsi nello `Registering` stato per un massimo di 60 minuti prima di modificare in `Registered` . Prima di continuare, attendere che lo stato sia **registrato** .

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```
È anche possibile usare i comandi dell'interfaccia [`az feature register`](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-register) della riga di comando di Azure e [`az feature show`](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-show) registrare la funzionalità e visualizzare lo stato della registrazione. 

### <a name="create-a-snapshot-policy"></a>Creare un criterio di snapshot 

Un criterio di snapshot consente di specificare la frequenza di creazione dello snapshot nei cicli orari, giornalieri, settimanali o mensili. È anche necessario specificare il numero massimo di snapshot da conservare per il volume.  

1.  Dalla visualizzazione dell'account NetApp fare clic su **criteri snapshot**.

    ![Esplorazione dei criteri snapshot](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  Nella finestra criteri snapshot impostare stato criterio su **abilitato**. 

3.  Fare clic sulla scheda **oraria**, **giornaliera**, **settimanale**o **mensile** per creare criteri di snapshot orari, giornalieri, settimanali o mensili. Consente di specificare il **numero di snapshot da memorizzare**.  

    Vedere [limiti delle risorse per Azure NetApp files](azure-netapp-files-resource-limits.md) sul numero massimo di snapshot consentiti per un volume. 

    Nell'esempio seguente viene illustrata la configurazione dei criteri snapshot orari. 

    ![Criteri snapshot ogni ora](../media/azure-netapp-files/snapshot-policy-hourly.png)

    Nell'esempio seguente viene illustrata la configurazione dei criteri snapshot giornalieri.

    ![Criteri di snapshot giornalieri](../media/azure-netapp-files/snapshot-policy-daily.png)

    Nell'esempio seguente viene illustrata la configurazione dei criteri snapshot settimanali.

    ![Criteri di snapshot settimanalmente](../media/azure-netapp-files/snapshot-policy-weekly.png)

    Nell'esempio seguente viene illustrata la configurazione dei criteri snapshot mensili.

    ![Criteri snapshot mensili](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Fare clic su **Salva**.  

Se è necessario creare criteri di snapshot aggiuntivi, ripetere il passaggio 3.
I criteri creati vengono visualizzati nella pagina Criteri snapshot.

Se si vuole che un volume usi i criteri di snapshot, è necessario [applicare il criterio al volume](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume). 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>Applicare un criterio snapshot a un volume

Se si vuole che un volume usi un criterio snapshot creato, è necessario applicare il criterio al volume. 

1.  Andare alla pagina **volumi** , fare clic con il pulsante destro del mouse sul volume a cui si desidera applicare i criteri di snapshot e scegliere **modifica**.

    ![Menu di scelta rapida dei volumi](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  Nella finestra modifica, in **criteri snapshot**, selezionare i criteri da usare per il volume.  Fare clic su **OK** per applicare i criteri.  

    ![Modifica dei criteri di snapshot](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>Modificare un criterio di snapshot 

È possibile modificare un criterio snapshot esistente per modificare lo stato dei criteri, la frequenza degli snapshot (oraria, giornaliera, settimanale o mensile) o il numero di snapshot da contenere.  
 
1.  Dalla visualizzazione dell'account NetApp fare clic su **criteri snapshot**.

2.  Fare clic con il pulsante destro del mouse sui criteri di snapshot che si desidera modificare, quindi scegliere **modifica**.

    ![Menu di scelta rapida per i criteri snapshot](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Apportare le modifiche nella finestra dei criteri di snapshot visualizzata, quindi fare clic su **Salva**. 

### <a name="delete-a-snapshot-policy"></a>Eliminare un criterio di snapshot 

È possibile eliminare un criterio snapshot che non si desidera più memorizzare.   

1.  Dalla visualizzazione dell'account NetApp fare clic su **criteri snapshot**.

2.  Fare clic con il pulsante destro del mouse sui criteri di snapshot da modificare, quindi selezionare **Elimina**.

    ![Menu di scelta rapida per i criteri snapshot](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Fare clic su **Sì** per confermare che si desidera eliminare i criteri di snapshot.   

    ![Conferma eliminazione criteri snapshot](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Ripristinare uno snapshot in un nuovo volume

Attualmente, è possibile ripristinare uno snapshot solo in un nuovo volume. 
1. Selezionare **snapshot** dal pannello volume per visualizzare l'elenco di snapshot. 
2. Fare clic con il pulsante destro del mouse sullo snapshot da ripristinare e selezionare **Ripristina in nuovo volume** dall'opzione di menu.  

    ![Ripristinare snapshot in un nuovo volume](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. Nella finestra Crea un volume specificare le informazioni per il nuovo volume:  
    * **Nome**   
        Specificare il nome per il volume che si sta creando.  
        
        Il nome deve essere univoco all'interno di un gruppo di risorse. Deve essere composto da almeno tre caratteri.  È possibile usare qualsiasi carattere alfanumerico.

    * **Quota**  
        Specificare la quantità di archiviazione logica che si desidera allocare al volume.  

    ![Ripristino in un nuovo volume](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Fare clic su **Verifica + crea**.  Fare clic su **Crea**.   
    Il nuovo volume usa lo stesso protocollo usato dallo snapshot.   
    Il nuovo volume in cui viene ripristinato lo snapshot viene visualizzato nel pannello Volumi.

## <a name="restore-a-file-from-a-snapshot-using-a-client"></a>Ripristinare un file da uno snapshot usando un client

Se non si vuole [ripristinare l'intero snapshot in un volume](#restore-a-snapshot-to-a-new-volume), è possibile scegliere di ripristinare un file da uno snapshot usando un client in cui è installato il volume.  

Il volume montato contiene una directory snapshot denominata  `.snapshot` (nei client NFS) o `~snapshot` (nei client SMB) accessibile al client. La directory snapshot contiene sottodirectory corrispondenti agli snapshot del volume. Ogni sottodirectory contiene i file dello snapshot. Se si elimina o sovrascrive accidentalmente un file, è possibile ripristinare il file nella directory padre di lettura/scrittura copiando il file da una sottodirectory snapshot alla directory di lettura/scrittura. 

Se è stata selezionata la casella di controllo Nascondi percorso snapshot al momento della creazione del volume, la directory snapshot viene nascosta. È possibile visualizzare lo stato Nascondi percorso snapshot del volume selezionando il volume. È possibile modificare l'opzione Nascondi percorso snapshot facendo clic su **modifica** nella pagina del volume.  

![Modifica opzioni snapshot del volume](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

### <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Ripristinare un file usando un client NFS Linux 

1. Usare il `ls` comando Linux per elencare il file che si vuole ripristinare dalla `.snapshot` Directory. 

    Ad esempio:

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. Usare il `cp` comando per copiare il file nella directory padre.  

    Ad esempio: 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

### <a name="restore-a-file-by-using-a-windows-client"></a>Ripristinare un file usando un client Windows 

1. Se la `~snapshot` directory del volume è nascosta, [visualizzare gli elementi nascosti](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10) nella directory padre da visualizzare `~snapshot` .

    ![Mostra elementi nascosti](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. Passare alla sottodirectory in `~snapshot` per trovare il file che si desidera ripristinare.  Fare clic con il pulsante destro del mouse sul file. Selezionare **Copia**.  

    ![Copia file da ripristinare](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. Tornare alla directory padre. Fare clic con il pulsante destro del mouse nella directory padre e scegliere `Paste` di incollare il file nella directory.

    ![Incolla il file da ripristinare](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. È anche possibile fare clic con il pulsante destro del mouse sulla directory padre, scegliere **Proprietà**, fare clic sulla scheda **versioni precedenti** per visualizzare l'elenco degli snapshot e selezionare **Ripristina** per ripristinare un file.  

    ![Proprietà versioni precedenti](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Limiti delle risorse per Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Video snapshot di Azure NetApp Files 101](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
