---
title: Reimpostare una password di Windows locale senza l&quot;agente di Azure | Documentazione Microsoft
description: "Come reimpostare la password di un account utente di Windows locale quando l&quot;agente guest di Azure non è installato o funzionante in una VM"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/07/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: 880f5e5967298401fc2522124af3746d9906ffa8
ms.lasthandoff: 04/10/2017


---
# <a name="how-to-reset-local-windows-password-for-azure-vm"></a>Come reimpostare una password di Windows locale per una VM di Azure
È possibile reimpostare la password di Windows locale di una VM in Azure tramite il [portale di Azure o Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a condizione che l'agente guest di Azure sia installato. Questo è il metodo principale per reimpostare una password per una VM di Azure. In mancanza di risposta da parte dell'agente guest di Azure, o in caso di errore di installazione dopo il caricamento di un'immagine personalizzata, è possibile reimpostare la password di Windows manualmente. Questo articolo illustra come reimpostare la password di un account locale collegando il disco virtuale del sistema operativo di origine a un'altra VM. 

> [!WARNING]
> Questo processo viene usato solo come ultima risorsa. Provare sempre a reimpostare la password usando prima il [portale di Azure o Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> 
> 

## <a name="overview-of-the-process"></a>Panoramica della procedura
Di seguito sono elencati i passaggi fondamentali della reimpostazione di una password locale per una VM Windows in Azure quando non è possibile accedere all'agente guest di Azure:

* Eliminare la VM di origine. I dischi virtuali vengono mantenuti.
* Collegare il disco del sistema operativo della VM di origine a un'altra VM nella stessa posizione nella sottoscrizione di Azure. Questa VM viene considerata come la VM per la risoluzione dei problemi.
* Con l'ausilio di questa VM, creare dei file di configurazione sul disco del sistema operativo della VM di origine.
* Scollegare il disco del sistema operativo della VM dalla VM per la risoluzione dei problemi.
* Usare un modello di Resource Manager per creare una VM tramite il disco virtuale originale.
* All'avvio della nuova VM, i file di configurazione creati aggiornano la password dell'utente richiesto.

## <a name="detailed-steps"></a>Procedura dettagliata
Provare sempre a reimpostare la password usando il [portale di Azure o Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) prima di procedere ai passaggi seguenti. Assicurarsi di disporre di un backup della VM prima di iniziare. 

1. Eliminare la VM interessata nel portale di Azure. Questa operazione determina l'eliminazione solo dei metadati, il riferimento della VM in Azure. Quando la VM viene eliminata, i dischi virtuali vengono mantenuti:
   
   * Selezionare la VM nel portale di Azure, fare clic su *Elimina*:
     
     ![Eliminare una VM esistente](./media/reset-local-password-without-agent/delete_vm.png)
2. Collegare il disco del sistema operativo della VM di origine alla VM per la risoluzione dei problemi. La VM per la risoluzione dei problemi deve essere nella stessa area del disco del sistema operativo della VM di origine (ad esempio `West US`):
   
   * Selezionare la VM per la risoluzione dei problemi nel portale di Azure. Fare clic su *Dischi* | *Collega esistente*:
     
     ![Collegare un disco esistente](./media/reset-local-password-without-agent/disks_attach_existing.png)
     
     Selezionare *File VHD*, quindi scegliere l'account di archiviazione contenente la VM di origine:
     
     ![Selezionare l'account di archiviazione](./media/reset-local-password-without-agent/disks_select_storageaccount.PNG)
     
     Selezionare il contenitore di origine. Il contenitore di origine è in genere *vhd*:
     
     ![Selezionare il contenitore di archiviazione](./media/reset-local-password-without-agent/disks_select_container.png)
     
     Selezionare il disco rigido virtuale del sistema operativo da collegare. Fare clic su *Seleziona* per completare il processo:
     
     ![Selezionare il disco virtuale di origine](./media/reset-local-password-without-agent/disks_select_source_vhd.png)
3. Connettersi alla VM per la risoluzione dei problemi tramite Desktop remoto e assicurarsi che il disco del sistema operativo della VM di origine sia visibile:
   
   * Selezionare la VM per la risoluzione dei problemi nel portale di Azure e fare clic su *Connetti*.
   * Aprire il file RDP scaricato. Immettere il nome utente e la password della VM per la risoluzione dei problemi.
   * In Esplora file cercare il disco dati collegato. Se il disco rigido virtuale della VM di origine è l'unico disco dati collegato alla VM per la risoluzione dei problemi, questo deve corrispondere all'unità F:
     
     ![Visualizzare il disco dati collegato](./media/reset-local-password-without-agent/troubleshooting_vm_fileexplorer.png)
4. Creare `gpt.ini` in `\Windows\System32\GroupPolicy` sull'unità della VM di origine (in presenza del file gpt.ini, rinominarlo in gpt.ini.bak):
   
   > [!WARNING]
   > Assicurarsi di non creare accidentalmente i seguenti file in C:\Windows, l'unità del sistema operativo per la VM per la risoluzione dei problemi. Creare i seguenti file nell'unità del sistema operativo per la VM di origine collegata come disco dati.
   > 
   > 
   
   * Aggiungere le righe seguenti al file `gpt.ini` creato:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Creare gpt.ini](./media/reset-local-password-without-agent/create_gpt_ini.png)
5. Creare `scripts.ini` in `\Windows\System32\GroupPolicy\Machine\Scripts`. Accertarsi che vengano visualizzate le cartelle nascoste. Se necessario, creare le cartelle `Machine` o `Scripts`.
   
   * Aggiungere le righe seguenti al file `scripts.ini` creato:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Creare scripts.ini](./media/reset-local-password-without-agent/create_scripts_ini.png)
6. Creare `FixAzureVM.cmd` in `\Windows\System32` con i contenuti seguenti, sostituendo `<username>` e `<newpassword>` con i propri valori:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add

    ```

    ![Creare FixAzureVM.cmd](./media/reset-local-password-without-agent/create_fixazure_cmd.png)
   
    Quando si definisce la nuova password, è necessario soddisfare i requisiti di complessità delle password configurate per la VM.
7. Nel portale di Azure scollegare il disco dalla VM per la risoluzione dei problemi:
   
   * Selezionare la VM per la risoluzione dei problemi nel portale di Azure, fare clic su *Dischi*.
   * Selezionare il disco dati collegato nel passaggio 2, fare clic su *Scollega*:
     
     ![Scollegare il disco](./media/reset-local-password-without-agent/detach_disk.png)
8. Prima di creare una VM, è necessario ottenere l'URI per il disco del sistema operativo di origine:
   
   * Selezionare l'account di archiviazione nel portale di Azure, fare clic su *BLOB*.
   * Selezionare il contenitore. Il contenitore di origine è in genere *vhd*:
     
     ![Selezionare il BLOB dell'account di archiviazione](./media/reset-local-password-without-agent/select_storage_details.png)
     
     Selezionare il disco rigido virtuale del sistema operativo della VM di origine e fare clic sul pulsante *Copia* accanto al nome *URL*:
     
     ![Copiare l'URI del disco](./media/reset-local-password-without-agent/copy_source_vhd_uri.png)
9. Creare una VM dal disco del sistema operativo della VM di origine:
   
   * Usare [questo modello di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) per creare una VM da un disco rigido virtuale specializzato. Fare clic sul pulsante `Deploy to Azure` per aprire il portale di Azure con i dettagli basati su modelli compilati automaticamente.
   * Per mantenere tutte le impostazioni precedenti per la VM, selezionare *Modifica modello* per fornire rete virtuale, subnet, scheda di rete o IP pubblico.
   * Nella casella di testo del parametro `OSDISKVHDURI` incollare l'URI del disco rigido virtuale di origine ottenuto nel passaggio precedente:
     
     ![Creare una VM dal modello](./media/reset-local-password-without-agent/create_new_vm_from_template.png)
10. Quando è in esecuzione, connettersi alla nuova VM tramite Desktop remoto con la nuova password specificata nello script `FixAzureVM.cmd`.
11. Dalla sessione remota per la nuova VM, rimuovere i file seguenti per pulire l'ambiente:
    
    * Da %windir%\System32
      * rimuovere FixAzureVM.cmd
    * Da %windir%\System32\GroupPolicy\Machine\
      * rimuovere scripts.ini
    * Da %windir%\System32\GroupPolicy
      * rimuovere il file gpt.ini (se gpt.ini era presente in precedenza, ed era stato rinominato in gpt.ini.bak, modificare il nome del file con estensione bak nuovamente in gpt.ini)

## <a name="next-steps"></a>Passaggi successivi
Se non è ancora possibile connettersi tramite Desktop remoto, vedere la [guida alla risoluzione dei problemi di RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). La [guida alla risoluzione dei problemi di RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) esamina i metodi di risoluzione dei problemi anziché i passaggi specifici. È anche possibile [aprire una richiesta di supporto tecnico di Azure](https://azure.microsoft.com/support/options/) per ricevere un supporto pratico.


