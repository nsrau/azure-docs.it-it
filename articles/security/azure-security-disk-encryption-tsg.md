---
title: Risoluzione dei problemi di Crittografia dischi di Azure | Microsoft Docs
description: Questo articolo offre suggerimenti per la risoluzione dei problemi di Crittografia dischi di Microsoft Azure per VM IaaS Windows e Linux.
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: ce0e23bd-07eb-43af-a56c-aa1a73bdb747
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: DevTiw;ejarvi;mayank88mahajan;vermashi;sudhakarareddyevuri;aravindthoram
ms.openlocfilehash: d49da52b3c45970c797150b7ff2bc6b699967977
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guida alla risoluzione dei problemi di Crittografia dischi di Azure

Questa guida è destinata a professionisti IT, analisti della sicurezza delle informazioni e amministratori cloud le cui organizzazioni usano Crittografia dischi di Azure e necessitano di indicazioni per risolvere i problemi relativi alla crittografia dei dischi.

## <a name="troubleshooting-linux-os-disk-encryption"></a>Risoluzione dei problemi relativi alla crittografia del disco del sistema operativo Linux

Per la crittografia del disco del sistema operativo Linux, prima di affrontare l'intero processo è necessario smontare l'unità del sistema operativo. In caso contrario, verrà probabilmente visualizzato un messaggio di errore di tipo "Impossibile smontare dopo...".

È molto probabile che questo errore si verifichi quando si tenta di eseguire la crittografia del disco del sistema operativo in un ambiente di VM di destinazione che è stato modificato rispetto all'immagine predefinita supportata della raccolta. Di seguito sono riportati alcuni esempi di deviazioni dall'immagine supportata che possono impedire all'estensione di smontare l'unità del sistema operativo:
- Immagini personalizzate che non corrispondono più al file system o allo schema di partizionamento supportato.
- Non sono supportate l'installazione e l'esecuzione nel sistema operativo di applicazioni di grandi dimensioni come SAP, MongoDB, Apache Cassandra e Docker prima della crittografia.  Crittografia dischi di Azure non è in grado di arrestare questi processi come richiesto durante la preparazione dell'unità del sistema operativo per la crittografia dei dischi.  Se sono presenti processi ancora attivi con handle di file aperti nell'unità del sistema operativo, tale unità non può essere smontata e ciò genera un errore di crittografia dell'unità del sistema operativo. 
- Esecuzione di script personalizzati a breve distanza di tempo dall'abilitazione della crittografia o esecuzione di qualsiasi altra modifica nella VM durante il processo di crittografia. Questo conflitto può verificarsi quando un modello di Azure Resource Manager definisce l'esecuzione simultanea di più estensioni o quando un'estensione di script personalizzati o un'altra azione viene eseguita contemporaneamente alla crittografia del disco. Il problema potrebbe essere risolto serializzando e isolando tali passaggi.
- Mancata disabilitazione di SELinux prima dell'abilitazione della crittografia, che causa l'esito negativo del passaggio di smontaggio. È possibile riabilitare SELinux al termine della crittografia.
- Il disco del sistema operativo usa uno schema di gestione dei volumi logici. Sebbene sia disponibile un supporto limitato per dischi dati LVM, non sono supportati dischi del sistema operativo LVM.
- Requisiti minimi di memoria non soddisfatti. Per la crittografia del disco del sistema operativo sono consigliati 7 GB.
- Montaggio ricorsivo delle unità dati nella directory /mnt/ o l'una nell'altra (ad esempio, /mnt/data1, /mnt/data2, /data3 + /data3/data4).
- Altri [prerequisiti](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) di Crittografia dischi di Azure per Linux non soddisfatti.

## <a name="unable-to-encrypt"></a>Impossibile eseguire la crittografia

In alcuni casi, la crittografia del disco Linux sembra bloccata nella fase "OS disk encryption started" e SSH è disabilitato. Il processo di crittografia in un'immagine di galleria della raccolta può richiedere da 3 a 16 ore. Se vengono aggiunti dischi dati di dimensioni da più TB, il processo può richiedere giorni.

La sequenza di crittografia del disco del sistema operativo Linux smonta temporaneamente l'unità del sistema operativo, per poi eseguire la crittografia blocco per blocco dell'intero disco del sistema operativo e riportarlo allo stato crittografato. A differenza di Crittografia dischi di Azure in Windows, la crittografia dei dischi Linux non consente di usare contemporaneamente la VM mentre è in corso la crittografia. Le caratteristiche delle prestazioni della macchina virtuale possono determinare una differenza significativa nel tempo necessario per completare la crittografia. Tali caratteristiche includono le dimensioni del disco e l'archiviazione standard o premium (SSD) dell'account di archiviazione.

Per controllare lo stato della crittografia è possibile eseguire il poll del campo **ProgressMessage** restituito dal comando [Get-AzureRmVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus). Mentre viene eseguita la crittografia dell'unità del sistema operativo, la macchina virtuale passa in stato di manutenzione e SSH viene disabilitato per impedire interruzioni del processo in corso. Mentre la crittografia è in corso, di norma viene visualizzato il messaggio **EncryptionInProgress**. Diverse ore più tardi, il messaggio **VMRestartPending** chiederà di riavviare la macchina virtuale. Ad esempio: 


```
PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Dopo la richiesta di riavviare la macchina virtuale e il riavvio della stessa, è necessario attendere 2-3 minuti perché nella destinazione siano completati il reboot e i passaggi finali. Una volta completata la crittografia, il messaggio di stato cambia. Quando viene visualizzato tale messaggio, l'unità del sistema operativo crittografata sarà pronta per l'uso e la macchina virtuale sarà nuovamente utilizzabile.

Nei casi seguenti, è consigliabile ripristinare la macchina virtuale alla copia shadow o al backup eseguito immediatamente prima della crittografia:
   - Se la sequenza di riavvio descritta in precedenza non viene completata.
   - Se le informazioni di riavvio, il messaggio di stato di avanzamento o altri indicatori di errore indicano che la crittografia del sistema operativo all'interno di questo processo non è riuscita. Un esempio di messaggio è l'errore "Impossibile smontare" descritto in questa Guida.

Prima del tentativo successivo, valutare di nuovo le caratteristiche della macchina virtuale e verificare che siano soddisfatti tutti i prerequisiti.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Risoluzione dei problemi di Crittografia dischi di Azure dietro un firewall
Quando la connettività è limitata da un firewall, da un requisito del proxy o dalle impostazioni di gruppi di sicurezza di rete (NSG), l'estensione potrebbe non essere più in grado di eseguire le attività necessarie. È quindi possibile che vengano visualizzati messaggi del tipo "Stato estensione non disponibile nella macchina virtuale". In scenari previsti, la crittografia non viene completata. Le sezioni che seguono illustrano alcuni problemi comuni relativi ai firewall che è possibile esaminare.

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete
Tutte le impostazioni dei gruppi di sicurezza di rete devono consentire all'endpoint di soddisfare i [prerequisiti](https://docs.microsoft.com/azure/security/azure-security-disk-encryption#prerequisites) di configurazione di rete documentati per la crittografia dei dischi.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault protetto da firewall
La macchina virtuale deve poter accedere all'insieme di credenziali delle chiavi. Vedere il materiale sussidiario sull'accesso a un insieme di credenziali delle chiavi protetto da firewall e gestito dal team di [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall).

### <a name="linux-package-management-behind-a-firewall"></a>Gestione pacchetti Linux protetto da firewall

In fase di esecuzione, Crittografia dischi di Azure per Linux usa il sistema di gestione pacchetti della distribuzione di destinazione per installare i componenti che costituiscono prerequisiti necessari prima di abilitare la crittografia. Se le impostazioni del firewall impediscono alla VM di scaricare e installare tali componenti, si verificheranno errori successivi. I passaggi per configurare questo sistema di gestione pacchetti possono variare in base alla distribuzione. In Red Hat, quando è necessario un proxy è essenziale verificare che subscription-manager e yum siano configurati correttamente. Per altre informazioni, vedere [Risoluzione dei problemi relativi a subscription-manager e yum](https://access.redhat.com/solutions/189533).  

## <a name="troubleshooting-windows-server-2016-server-core"></a>Risoluzione dei problemi di Server Core di Windows Server 2016

In Server Core di Windows Server 2016, la componente bdehdcfg non è disponibile per impostazione predefinita. Questa componente è necessaria per la Crittografia dischi di Azure. Viene usato per dividere il volume di sistema dal volume del sistema operativo, operazione che viene eseguita solo una volta per la durata della macchina virtuale. Questi file binari non sono necessari durante le operazioni di crittografia successive.

Per risolvere questo problema, copiare i 4 file seguenti da una macchina virtuale Windows Server 2016 Data Center nello stesso percorso in Server Core:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

   2. Immettere il comando seguente:

   ```
   bdehdcfg.exe -target default
   ```

   3. Questo comando crea una partizione di sistema da 550 MB. Riavviare il sistema.

   4. Usare DiskPart per verificare i volumi, quindi procedere.  

Ad esempio: 

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
## <a name="troubleshooting-encryption-status"></a>Risoluzione dei problemi relativi allo stato della crittografia

Se lo stato della crittografia previsto non corrisponde a quello segnalato nel portale, vedere l'articolo relativo alla [visualizzazione errata dello stato della crittografia nel portale di gestione di Azure](https://support.microsoft.com/en-us/help/4058377/encryption-status-is-displayed-incorrectly-on-the-azure-management-por)

## <a name="next-steps"></a>Passaggi successivi

In questo documento sono stati esaminati alcuni problemi comuni di Crittografia dischi di Azure ed è stato illustrato come risolverli. Per altre informazioni su questo servizio e sulle relative funzionalità, vedere gli articoli seguenti:

- [Applicare la crittografia dei dischi nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Crittografare una macchina virtuale di Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Crittografia dei dati inattivi in Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
