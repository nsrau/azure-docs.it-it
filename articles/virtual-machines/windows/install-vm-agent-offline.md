---
title: "Installare l'agente di macchine virtuali di Azure in modalità offline | Microsoft Docs"
description: "Informazioni su come installare l'agente di macchine virtuali di Azure in modalità offline."
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: genli
ms.openlocfilehash: 3770cc3338c89a9bf88e2cf9ec3faa37e2ff109b
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2018
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Installare l'agente di macchine virtuali di Azure in modalità offline 

L'agente di macchine virtuali di Azure offre funzionalità utili, ad esempio la reimpostazione della password dell'amministratore locale e il push di script. Questo articolo illustra come installare l'agente di macchine virtuali per una macchina virtuale di Windows offline. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Quando usare l'agente di macchine virtuali in modalità offline

Installare l'agente di macchine virtuali in modalità offline negli scenari seguenti:

- Nella macchina virtuale di Azure distribuita non è installato l'agente oppure l'agente non funziona.
- Si è dimenticata la password dell'amministratore per la macchina virtuale o non è possibile accedere alla macchina virtuale.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Come installare l'agente di macchine virtuali in modalità offline

Usare la procedura seguente per installare l'agente di macchine virtuali in modalità offline.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Passaggio 1: Collegare il disco del sistema operativo della macchina virtuale a un'altra macchina virtuale come disco dati

1.  Eliminare la macchina virtuale. Assicurarsi di aver selezionato l'opzione **Keep the disks** (Mantieni i dischi) quando si esegue questa operazione.

2.  Collegare il disco del sistema operativo come disco dati a un'altra macchina virtuale (nota come _macchina virtuale per la risoluzione dei problemi_). Per altre informazioni, vedere [Collegare un disco dati a una macchina virtuale Windows nel portale di Azure](attach-managed-disk-portal.md).

3.  Connettersi alla macchina virtuale per la risoluzione dei problemi. Aprire **Gestione computer** > **Gestione disco**. Verificare che il disco del sistema operativo sia online e che siano assegnate lettere di unità alle partizioni del disco.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Passaggio 2: Modificare il disco del sistema operativo per installare l'agente di macchine virtuali di Azure

1.  Attivare una connessione Desktop remoto alla macchina virtuale per la risoluzione dei problemi.

2.  Nel disco del sistema operativo collegato passare alla cartella \windows\system32\config. Copiare tutti i file in questa cartella come backup, nel caso risulti necessario un ripristino dello stato precedente.

3.  Avviare l'**Editor del Registro di sistema** (regedit.exe).

4.  Selezionare la chiave **HKEY_LOCAL_MACHINE**. Scegliere **File** > **Carica hive** dal menu:

    ![Caricare l'hive](./media/install-vm-agent-offline/load-hive.png)

5.  Passare alla cartella \windows\system32\config\SYSTEM nel disco del sistema operativo collegato. Immettere il nome di hive **BROKENSYSTEM**. Il nuovo hive del Registro di sistema viene visualizzato per la chiave **HKEY_LOCAL_MACHINE**.

6.  Passare alla cartella \windows\system32\config\SOFTWARE nel disco del sistema operativo collegato. Immettere il nome di hive **BROKENSOFTWARE**.

7.  Se l'agente di macchine virtuali non funziona, eseguire il backup della configurazione corrente.

    >[!NOTE]
    >Se l'agente non è installato nella macchina virtuale, procedere al passaggio 8. 
      
    1. Rinominare la cartella \windowsazure in \windowsazure.old.

    2. Esportare le voci del Registro di sistema seguenti:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Usare i file esistenti nella macchina virtuale per la risoluzione dei problemi come repository per l'installazione dell'agente di macchine virtuali. Completare questi passaggi:

    1. Dalla macchina virtuale per la risoluzione dei problemi esportare le sottochiavi seguenti nel formato del Registro di sistema (con estensione reg): 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

        ![Esportare le sottochiavi del Registro di sistema](./media/install-vm-agent-offline/backup-reg.png)

    2. Modificare i file del Registro di sistema. In ogni file, modificare il valore della voce **SYSTEM** in **BROKENSYSTEM** (come illustrato nelle immagini seguenti) e salvare il file.

        ![Modificare i valori della sottochiave del Registro di sistema](./media/install-vm-agent-offline/change-reg.png)

    3. Importare i file del Registro di sistema nel repository facendo doppio clic su ogni file del Registro di sistema.

    4. Verificare che le tre sottochiavi seguenti vengano importate correttamente nell'hive **BROKENSYSTEM**:
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

9.  Copiare la cartella dell'agente di macchine virtuali da C:\windowsazure\packages a &lt;disco del sistema operativo collegato&gt;:\windowsazure\packages.

    ![Copiare i file dell'agente di macchine virtuali nel disco del sistema operativo](./media/install-vm-agent-offline/copy-package.png)
      
    >[!NOTE]
    >Non copiare la cartella **logs**. Dopo l'avvio del servizio, vengono generati nuovi log.

10.  Selezionare **BROKENSYSTEM**. Scegliere **File** > **Scarica hive** dal menu.

11.  Selezionare **BROKENSOFTWARE**. Scegliere **File** > **Scarica hive** dal menu.

12.  Scollegare il disco del sistema operativo e quindi ricreare la macchina virtuale usando il disco del sistema operativo.

13.  Accedere alla macchina virtuale. Si noti che RdAgent è in esecuzione e i log vengono generati.

Se la macchina virtuale è stata creata tramite il modello di distribuzione classica, la procedura è completa.


### <a name="use-the-provisionguestagent-property-for-vms-created-with-azure-resource-manager"></a>Usare la proprietà ProvisionGuestAgent per le macchine virtuali create con Azure Resource Manager

Se la macchina virtuale è stata creata con il modello di distribuzione di Resource Manager, usare il modulo di Azure PowerShell per aggiornare la proprietà **ProvisionGuestAgent**. La proprietà informa Azure che nella macchina virtuale è installato l'agente di macchine virtuali.

Per impostare la proprietà **ProvisionGuestAgent** eseguire i comandi seguenti in Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Eseguire quindi il comando `Get-AzureVM` . Si noti che la proprietà **GuestAgentStatus** è ora compilata:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'agente di macchine virtuali di Azure](agent-user-guide.md)
- [Estensioni e funzionalità della macchina virtuale per Windows](extensions-features.md)
