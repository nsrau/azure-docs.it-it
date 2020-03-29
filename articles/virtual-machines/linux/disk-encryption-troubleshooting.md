---
title: Risoluzione dei problemi di Crittografia disco di Azure per macchine virtuali LinuxTroubleshooting Azure Disk Encryption for Linux VMs
description: Questo articolo fornisce suggerimenti per la risoluzione dei problemi relativi a Crittografia disco di Microsoft Azure per macchine virtuali Linux.This article provides troubleshooting tips for Microsoft Azure Disk Encryption for Linux VMs.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: eeacea9e3305865881747801100dc17770b7df63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970457"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guida alla risoluzione dei problemi di Crittografia dischi di Azure

Questa guida è destinata ai professionisti IT, agli analisti della sicurezza delle informazioni e agli amministratori cloud le cui organizzazioni usano Crittografia dischi di Azure. Questo articolo contiene indicazioni per la risoluzione dei problemi relativi alla crittografia dei dischi.

Prima di eseguire una delle operazioni seguenti, verificare che le macchine virtuali che si sta tentando di crittografare siano tra le dimensioni delle [macchine virtuali e i sistemi operativi supportati](disk-encryption-overview.md#supported-vms-and-operating-systems)e che siano soddisfatti tutti i prerequisiti:

- [Requisiti aggiuntivi per le macchine virtualiAdditional requirements for VMs](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisiti di rete](disk-encryption-overview.md#networking-requirements)
- [Requisiti di archiviazione delle chiavi di crittografiaEncryption key storage requirements](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Risoluzione dei problemi relativi alla crittografia del disco del sistema operativo Linux

Per la crittografia del disco del sistema operativo Linux, prima di affrontare l'intero processo è necessario smontare l'unità del sistema operativo. Se non è possibile smontare l'unità, è probabile che si verifichi un errore del tipo "Impossibile smontare dopo...".

Questo errore può verificarsi quando viene tentata la crittografia del disco del sistema operativo in una macchina virtuale con un ambiente che è stato modificato dall'immagine della raccolta predefinita supportata. Eventuali deviazioni dall'immagine supportata possono impedire all'estensione di smontare l'unità del sistema operativo. Di seguito sono riportati alcuni esempi di deviazioni:
- Immagini personalizzate che non corrispondono più al file system o allo schema di partizionamento supportato.
- Le applicazioni di grandi dimensioni come SAP, MongoDB, Apache Cassandra e Docker non sono supportate quando sono installate e in esecuzione nel sistema operativo prima della crittografia. Crittografia dischi di Azure non è in grado di arrestare questi processi in modo sicuro come necessario durante la preparazione dell'unità del sistema operativo per la crittografia dei dischi. Se sono presenti processi ancora attivi con handle di file aperti nell'unità del sistema operativo, tale unità non può essere smontata e ciò genera un errore di crittografia dell'unità del sistema operativo. 
- Esecuzione di script personalizzati a breve distanza di tempo dall'abilitazione della crittografia o esecuzione di qualsiasi altra modifica nella VM durante il processo di crittografia. Questo conflitto può verificarsi quando un modello di Azure Resource Manager definisce l'esecuzione simultanea di più estensioni o quando un'estensione di script personalizzati o un'altra azione viene eseguita contemporaneamente alla crittografia del disco. Il problema potrebbe essere risolto serializzando e isolando tali passaggi.
- Security Enhanced Linux (SELinux) non è stato disabilitato prima dell'abilitazione della crittografia, con conseguente esito negativo dello smontaggio. È possibile riabilitare SELinux al termine della crittografia.
- Il disco del sistema operativo usa uno schema di gestione dei volumi logici. Anche se è disponibile un supporto limitato per dischi dati LVM, non sono supportati dischi del sistema operativo LVM.
- I requisiti minimi di memoria non sono soddisfatti. Per la crittografia del disco del sistema operativo si consigliano 7 GB.
- Montaggio ricorsivo delle unità dati nella directory /mnt/ o l'una nell'altra (ad esempio, /mnt/data1, /mnt/data2, /data3 + /data3/data4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a> Aggiornare il kernel predefinito per Ubuntu 14.04 LTS

L'immagine di Ubuntu 14.04 LTS viene fornita con la versione del kernel predefinito 4.4. Questa versione del kernel presenta un problema noto che consiste in un errore di memoria insufficiente killer, che termina in modo errato il comando dd durante il processo di crittografia del sistema operativo. Questo bug è stato risolto nel più recente kernel Linux ottimizzato di Azure. Per evitare tale errore, prima di abilitare la crittografia sull'immagine, aggiornare il [kernel ottimizzato 4.15 di Azure](https://packages.ubuntu.com/trusty/linux-azure) oppure usare i comandi seguenti in un secondo momento:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Dopo aver riavviato la macchina virtuale nel nuovo kernel, la nuova versione del kernel può essere confermata tramite:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Aggiornare le versioni dell'agente di macchine virtuali di AzureUpdate the Azure Virtual Machine Agent and extension versions

Azure Disk Encryption operations may fail on virtual machine images using unsupported versions of the Azure Virtual Machine Agent. Le immagini Linux con versioni dell'agente precedenti alla 2.2.38 devono essere aggiornate prima di abilitare la crittografia. Per altre informazioni, vedere [Come aggiornare l'agente Linux](../extensions/update-linux-agent.md) di Azure in una macchina virtuale e Supporto della versione minima per gli agenti di macchine virtuali in Azure.For more information, see How to update the Azure Linux Agent on a VM and [Minimum version support for virtual machine agents in Azure.](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

È inoltre necessaria la versione corretta dell'estensione dell'agente guest Microsoft.Azure.Security.AzureDiskEncryption o Microsoft.Azure.Security.AzureDiskEncryptionForLinux. Le versioni di estensione vengono gestite e aggiornate automaticamente dalla piattaforma quando vengono soddisfatti i prerequisiti dell'agente di Macchine virtuali di Azure e viene utilizzata una versione supportata dell'agente di macchina virtuale.

L'estensione Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux è deprecata e non è più supportata.  

## <a name="unable-to-encrypt-linux-disks"></a>Impossibile crittografare i dischi Linux

In alcuni casi, la crittografia del disco Linux sembra bloccata nella fase "OS disk encryption started" e SSH è disabilitato. Il processo di crittografia in un'immagine di galleria della raccolta può richiedere da 3 a 16 ore. Se vengono aggiunti dischi dati di dimensioni da più TB, il processo può richiedere giorni.

La sequenza di crittografia del disco del sistema operativo Linux smonta temporaneamente l'unità del sistema operativo, per poi eseguire la crittografia blocco per blocco dell'intero disco del sistema operativo e riportarlo allo stato crittografato. Crittografia disco Linux non consente l'utilizzo simultaneo della macchina virtuale mentre è in corso la crittografia. Le caratteristiche delle prestazioni della macchina virtuale possono determinare una differenza significativa nel tempo necessario per completare la crittografia. Tali caratteristiche includono le dimensioni del disco e l'archiviazione standard o premium (SSD) dell'account di archiviazione.

Per controllare lo stato della crittografia, eseguire il polling del campo **ProgressMessage** restituito dal comando [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) Mentre viene eseguita la crittografia dell'unità del sistema operativo, la macchina virtuale passa in stato di manutenzione e SSH viene disabilitato per impedire interruzioni del processo in corso. Mentre la crittografia è in corso, di norma viene visualizzato il messaggio **EncryptionInProgress**. Diverse ore più tardi, il messaggio **VMRestartPending** chiederà di riavviare la macchina virtuale. Ad esempio:


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Dopo la richiesta di riavviare la macchina virtuale e il riavvio della stessa, è necessario attendere 2-3 minuti perché nella destinazione siano completati il riavvio e i passaggi finali. Una volta completata la crittografia, il messaggio di stato cambia. Quando viene visualizzato tale messaggio, l'unità del sistema operativo crittografata sarà pronta per l'uso e la macchina virtuale sarà nuovamente utilizzabile.

Nei casi seguenti, è consigliabile ripristinare la macchina virtuale alla copia shadow o al backup eseguito immediatamente prima della crittografia:
   - Se la sequenza di riavvio descritta in precedenza non viene completata.
   - Se le informazioni di riavvio, il messaggio di stato di avanzamento o altri indicatori di errore indicano che la crittografia del sistema operativo all'interno di questo processo non è riuscita. Un esempio di messaggio è l'errore "Impossibile smontare" descritto in questa Guida.

Prima del tentativo successivo, valutare nuovamente le caratteristiche della macchina virtuale e verificare che siano soddisfatti tutti i prerequisiti.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Risoluzione dei problemi di Crittografia dischi di Azure dietro un firewall

Vedere Crittografia del disco in una [rete isolata](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Risoluzione dei problemi relativi allo stato della crittografia 

Il portale può visualizzare un disco come crittografato anche dopo che è stato decrittografato all'interno della macchina virtuale.  Ciò può verificarsi quando i comandi di basso livello vengono usati per decrittografare direttamente il disco dall'interno della macchina virtuale, anziché usare i comandi di gestione di Crittografia disco di Azure di livello superiore.  I comandi di livello superiore non solo decrittografano il disco dall'interno della macchina virtuale, ma al di fuori della macchina virtuale aggiornano anche importanti impostazioni di crittografia a livello di piattaforma e le impostazioni di estensione associate alla macchina virtuale.  Se questi non vengono mantenuti allineati, la piattaforma non sarà in grado di segnalare correttamente lo stato di crittografia o il provisioning della macchina virtuale.   

Per disabilitare Crittografia disco di Azure con PowerShell, usare [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) seguito da [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). L'esecuzione di Remove-AzVMDiskEncryptionExtension prima della disabilitazione della crittografia avrà esito negativo.

Per disabilitare Crittografia disco di Azure con l'interfaccia della riga di comando, usare [az vm encryption disable](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Passaggi successivi

In questo documento sono stati esaminati alcuni problemi comuni di Crittografia dischi di Azure ed è stato illustrato come risolverli. Per altre informazioni su questo servizio e sulle relative funzionalità, vedere gli articoli seguenti:

- [Applicare la crittografia del disco nel Centro sicurezza Azure](../../security-center/security-center-apply-disk-encryption.md)
- [Crittografia dei dati inattivi in Azure](../../security/fundamentals/encryption-atrest.md)
