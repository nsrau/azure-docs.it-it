---
title: Risoluzione dei problemi di crittografia dischi di Azure per macchine virtuali Linux
description: Questo articolo fornisce suggerimenti per la risoluzione dei problemi per la crittografia del disco Microsoft Azure per le macchine virtuali Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 5ca6431531f8cebf1205aa555c5545f4dc44abd3
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87372213"
---
# <a name="azure-disk-encryption-for-linux-vms-troubleshooting-guide"></a>Guida alla risoluzione dei problemi di crittografia dischi di Azure per macchine virtuali Linux

Questa guida è destinata ai professionisti IT, agli analisti della sicurezza delle informazioni e agli amministratori cloud le cui organizzazioni usano Crittografia dischi di Azure. Questo articolo contiene indicazioni per la risoluzione dei problemi relativi alla crittografia dei dischi.

Prima di eseguire le operazioni seguenti, assicurarsi che le macchine virtuali che si sta tentando di crittografare siano comprese tra le [macchine virtuali con dimensioni e sistemi operativi supportati](disk-encryption-overview.md#supported-vms-and-operating-systems) e che siano stati soddisfatti tutti i prerequisiti:

- [Requisiti aggiuntivi per le macchine virtuali](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisiti di rete](disk-encryption-overview.md#networking-requirements)
- [Requisiti di archiviazione delle chiavi di crittografia](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Risoluzione dei problemi relativi alla crittografia del disco del sistema operativo Linux

Per la crittografia del disco del sistema operativo Linux, prima di affrontare l'intero processo è necessario smontare l'unità del sistema operativo. Se non è possibile smontare l'unità, è probabile che si verifichi un errore del tipo "Impossibile smontare dopo...".

Questo errore può verificarsi quando si tenta di eseguire la crittografia del disco del sistema operativo in una macchina virtuale con un ambiente che è stato modificato rispetto all'immagine della raccolta di scorte supportata. Eventuali deviazioni dall'immagine supportata possono impedire all'estensione di smontare l'unità del sistema operativo. Di seguito sono riportati alcuni esempi di deviazioni:
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

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Aggiornare l'agente di macchine virtuali di Azure e le versioni dell'estensione

Le operazioni di crittografia dischi di Azure possono avere esito negativo sulle immagini di macchine virtuali usando versioni non supportate dell'agente di macchine virtuali di Azure. Prima di abilitare la crittografia, è necessario aggiornare le immagini Linux con le versioni di Agent precedenti a 2.2.38. Per altre informazioni, vedere [come aggiornare l'agente Linux di Azure in una](../extensions/update-linux-agent.md) macchina virtuale e il [supporto della versione minima per gli agenti di macchine virtuali in Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

È necessaria anche la versione corretta dell'estensione agente guest Microsoft. Azure. Security. AzureDiskEncryption o Microsoft. Azure. Security. AzureDiskEncryptionForLinux. Le versioni delle estensioni vengono gestite e aggiornate automaticamente dalla piattaforma quando sono soddisfatti i prerequisiti dell'agente di macchine virtuali di Azure e viene usata una versione supportata dell'agente di macchine virtuali.

L'estensione Microsoft. OSTCExtensions. AzureDiskEncryptionForLinux è stata deprecata e non è più supportata.  

## <a name="unable-to-encrypt-linux-disks"></a>Impossibile crittografare i dischi Linux

In alcuni casi, la crittografia del disco Linux sembra bloccata nella fase "OS disk encryption started" e SSH è disabilitato. Il processo di crittografia in un'immagine di galleria della raccolta può richiedere da 3 a 16 ore. Se vengono aggiunti dischi dati di dimensioni da più TB, il processo può richiedere giorni.

La sequenza di crittografia del disco del sistema operativo Linux smonta temporaneamente l'unità del sistema operativo, per poi eseguire la crittografia blocco per blocco dell'intero disco del sistema operativo e riportarlo allo stato crittografato. La crittografia del disco Linux non consente l'uso simultaneo della macchina virtuale mentre è in corso la crittografia. Le caratteristiche delle prestazioni della macchina virtuale possono determinare una differenza significativa nel tempo necessario per completare la crittografia. Tali caratteristiche includono le dimensioni del disco e l'archiviazione standard o premium (SSD) dell'account di archiviazione.

Mentre è in corso la crittografia dell'unità del sistema operativo, la macchina virtuale entra in uno stato di manutenzione e Disabilita SSH per impedire eventuali rotture al processo in corso.  Per verificare lo stato della crittografia, usare il comando Azure PowerShell [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) e controllare il campo **ProgressMessage** . **ProgressMessage** segnalerà una serie di stati durante la crittografia dei dati e dei dischi del sistema operativo:

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings :
ProgressMessage            : Transitioning

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for data volumes

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

Il **ProgressMessage** rimarrà nella **crittografia del disco del sistema operativo avviata** per la maggior parte del processo di crittografia.  Quando la crittografia è completata e ha esito positivo, **ProgressMessage** restituirà:

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for all volumes
```

Quando viene visualizzato tale messaggio, l'unità del sistema operativo crittografata sarà pronta per l'uso e la macchina virtuale sarà nuovamente utilizzabile.

Se le informazioni di avvio, il messaggio di stato o un errore segnala che la crittografia del sistema operativo ha avuto esito negativo durante il processo, ripristinare la macchina virtuale allo snapshot o al backup eseguito immediatamente prima della crittografia. Un esempio di messaggio è l'errore "Impossibile smontare" descritto in questa Guida.

Prima di ritentare la crittografia, rivalutare le caratteristiche della macchina virtuale e assicurarsi che tutti i prerequisiti siano soddisfatti.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Risoluzione dei problemi di Crittografia dischi di Azure dietro un firewall

Vedere [crittografia del disco in una rete isolata](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Risoluzione dei problemi relativi allo stato della crittografia 

È possibile che nel portale un disco venga visualizzato come crittografato anche dopo essere stato decrittografato nella macchina virtuale.  Questo problema può verificarsi quando si usano comandi di basso livello per decrittografare il disco direttamente dalla macchina virtuale, anziché usare i comandi di gestione di livello superiore di Crittografia dischi di Azure.  I comandi di livello superiore, infatti, non solo consentono di decrittografare il disco direttamente dalla macchina virtuale ma, all'esterno della macchina virtuale, consentono anche di aggiornare al livello di piattaforma le impostazioni di estensione e di crittografia associate alla macchina virtuale.  Se le impostazioni non sono allineate, la piattaforma non sarà in grado di segnalare correttamente lo stato della crittografia o di effettuare il provisioning della macchina virtuale.

Per disabilitare Crittografia dischi di Azure con PowerShell, usare il comando [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) seguito da [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). L'esecuzione del comando Remove-AzVMDiskEncryptionExtension prima di disabilitare la crittografia avrà, infatti, esito negativo.

Per disabilitare Crittografia dischi di Azure con l'interfaccia della riga di comando, usare [az vm encryption disable](/cli/azure/vm/encryption).

## <a name="next-steps"></a>Passaggi successivi

In questo documento sono stati esaminati alcuni problemi comuni di Crittografia dischi di Azure ed è stato illustrato come risolverli. Per altre informazioni su questo servizio e sulle relative funzionalità, vedere gli articoli seguenti:

- [Applicare la crittografia dei dischi nel Centro sicurezza di Azure](../../security-center/security-center-virtual-machine-protection.md)
- [Crittografia dei dati inattivi in Azure](../../security/fundamentals/encryption-atrest.md)
