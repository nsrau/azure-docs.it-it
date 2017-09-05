---
title: Risoluzione dei problemi di Crittografia dischi di Azure | Microsoft Docs
description: Questo articolo offre suggerimenti per la risoluzione dei problemi di Crittografia dischi di Microsoft Azure per VM IaaS Windows e Linux.
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: ce0e23bd-07eb-43af-a56c-aa1a73bdb747
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: devtiw
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 5f482a92b8fcd71a1b767fcc5741bc57605997ea
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guida alla risoluzione dei problemi di Crittografia dischi di Azure

Questa guida è destinata a professionisti IT, analisti della sicurezza delle informazioni e amministratori cloud le cui organizzazioni usano Crittografia dischi di Azure e necessitano di indicazioni per risolvere i problemi relativi alla crittografia dei dischi.

## <a name="troubleshooting-linux-os-disk-encryption"></a>Risoluzione dei problemi relativi alla crittografia del disco del sistema operativo Linux

Per la crittografia del disco del sistema operativo Linux, prima di affrontare l'intero processo è necessario smontare l'unità del sistema operativo.   In caso contrario, verrà probabilmente visualizzato un messaggio di errore di tipo "Impossibile smontare dopo…".

È molto probabile che ciò si verifichi quando si tenta di eseguire la crittografia del disco del sistema operativo in un ambiente di VM di destinazione che è stato modificato rispetto all'immagine predefinita supportata della raccolta.  Di seguito sono riportati esempi di deviazioni dall'immagine supportata che possono impedire all'estensione di smontare l'unità del sistema operativo:
- Immagini personalizzate che non corrispondono più al file system e/o allo schema di partizionamento supportato.
- Immagini personalizzate con applicazioni quali antivirus, Docker, SAP, MongoDB o Apache Cassandra in esecuzione nel sistema operativo prima della crittografia.  Queste applicazioni sono difficili da terminare e, quando hanno mantenuto handle di file aperti nell'unità del sistema operativo, l'unità non può essere disinstallata, causando un errore.
- Gli script personalizzati che vengono eseguiti in prossimità del tempo di chiusura al passaggio di crittografia possono interferire e causare questo errore. Questa situazione può verificarsi quando un modello di Resource Manager definisce l'esecuzione simultanea di più estensioni o quando un'estensione di script personalizzati o un'altra azione viene eseguita contemporaneamente alla crittografia del disco.   Il problema potrebbe essere risolto serializzando e isolando tali passaggi.
- Mancata disabilitazione di SELinux prima dell'abilitazione della crittografia, che causa l'esito negativo del passaggio di smontaggio.  È possibile riabilitare SELinux al termine della crittografia.
- Uso nel disco del sistema operativo di uno schema LVM. Anche è disponibile un supporto limitato per dischi dati LVM, non sono supportati dischi del sistema operativo LVM.
- Requisiti minimi di memoria non soddisfatti. Per la crittografia del disco del sistema operativo sono consigliati 7 GB.
- Montaggio ricorsivo delle unità dati nella directory /mnt/ o l'una nell'altra (ad esempio, /mnt/data1, /mnt/data2, /data3 + /data3/data4 e così via).
- Altri [prerequisiti](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption) di Crittografia dischi di Azure per Linux non soddisfatti.

## <a name="unable-to-encrypt"></a>Impossibile eseguire la crittografia

In alcuni casi, la crittografia del disco Linux sembra bloccata nella fase "OS disk encryption started" e SSH è disabilitato. Il completamento del processo in un immagine di galleria della raccolta può richiedere da 3 a 16 ore.  Se vengono aggiunti dischi dati di dimensioni da più TB, il processo può richiedere giorni. La sequenza della crittografia del disco del sistema operativo Linux prevede lo smontaggio temporaneo dell'unità del sistema operativo, la crittografia blocco per blocco dell'intero disco del sistema operativo e quindi il relativo rimontaggio in stato crittografato.   A differenza di Crittografia dischi di Azure in Windows, la crittografia dei dischi Linux non consente di usare contemporaneamente la VM mentre è in corso la crittografia.  Le caratteristiche di prestazioni della VM, ad esempio le dimensioni del disco e il fatto che l'account di archiviazione sia supportato da Archiviazione Standard o Premium (SSD), può influire notevolmente sul tempo necessario per completare la crittografia.

Per controllare lo stato, è possibile eseguire il poll del campo ProgressMessage restituito dal comando [Get-AzureRmVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus).   Mentre viene eseguita la crittografia dell'unità del sistema operativo, la VM passa in stato di manutenzione e SSH viene disabilitato per impedire interruzioni del processo in corso.  Mentre è in corso la crittografia, per la maggior parte del tempo verrà segnalato lo stato EncryptionInProgress, seguito diverse ore dopo da un messaggio VMRestartPending con cui viene richiesto di riavviare la VM.  Ad esempio:


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

Dopo tale richiesta, il riavvio della VM e 2-3 minuti per l'esecuzione del riavvio e dei passaggi finali nella destinazione, il messaggio di stato indicherà che la crittografia è completata.   Quando viene visualizzato tale messaggio, l'unità del sistema operativo crittografata sarà pronta per l'uso e la VM sarà nuovamente utilizzabile.

Se questa sequenza non è stata visualizzata o le informazioni di avvio, il messaggio di stato o altri indicatori di errore segnalano che la crittografia del sistema operativo ha avuto esito negativo nel corso di questo processo (ad esempio, se viene visualizzato il messaggio di tipo "Impossibile smontare" descritto in questa guida), è consigliabile ripristinare lo snapshot o il backup della VM eseguiti subito prima della crittografia.  Prima del tentativo successivo, è consigliabile valutare di nuovo le caratteristiche della VM e verificare che siano soddisfatti tutti i prerequisiti.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Risoluzione dei problemi di Crittografia dischi di Azure dietro un firewall
Quando la connettività è limitata da un firewall, da un requisito del proxy o dalle impostazioni di gruppi di sicurezza di rete (NSG), l'estensione potrebbe non essere più in grado di eseguire le attività necessarie.   È quindi possibile che vengano visualizzati messaggi di tipo "Stato estensione non disponibile nella VM" e che gli scenari previsti non vengano completati.  Le sezioni che seguono illustrano alcuni problemi comuni relativi ai firewall che è possibile esaminare.

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete
Tutte le impostazioni dei gruppi di sicurezza di rete devono consentire all'endpoint di soddisfare i [prerequisiti](https://docs.microsoft.com/azure/security/azure-security-disk-encryption#prerequisites) di configurazione di rete documentati per la crittografia dei dischi.

### <a name="azure-keyvault-behind-firewall"></a>Azure Key Vault dietro un firewall
La VM deve poter accedere all'insieme di credenziali delle chiavi. Vedere le indicazioni sull'accesso a un insieme di credenziali delle chiavi protetto da un firewall gestito dal team di [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall).

### <a name="linux-package-management-behind-firewall"></a>Gestione pacchetti Linux dietro un firewall
In fase di esecuzione, Crittografia dischi di Azure per Linux usa il sistema di gestione pacchetti della distribuzione di destinazione per installare i componenti che costituiscono prerequisiti necessari prima di abilitare la crittografia.  Se le impostazioni del firewall impediscono alla VM di scaricare e installare tali componenti, si verificheranno errori successivi.    I passaggi per eseguire questa configurazione possono variare in base alla distribuzione.  In Red Hat, quando è necessario un proxy è essenziale verificare che subscription-manager e yum siano configurati correttamente.  Vedere [questo](https://access.redhat.com/solutions/189533) articolo del supporto Red Hat sull'argomento.  

## <a name="troubleshooting-windows-server-2016-server-core"></a>Risoluzione dei problemi di Server Core di Windows Server 2016

In Server Core di Windows Server 2016, la componente bdehdcfg non è disponibile per impostazione predefinita. Questa componente è necessaria per la Crittografia dischi di Azure.

Per risolvere questo problema, copiare i seguenti 4 file da una macchina virtuale del data center di Windows Server 2016 nella cartella c:\windows\system32 dell'immagine di Server Core:

```
bdehdcfg.exe
bdehdcfglib.dll
bdehdcfglib.dll.mui
bdehdcfg.exe.mui
```

Quindi, eseguire il comando seguente:

```
bdehdcfg.exe -target default
```

Verrà creata una partizione del sistema di 550MB e quindi, dopo un riavvio, è possibile usare Diskpart per verificare i volumi e continuare.  

ad esempio:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
## <a name="see-also"></a>Vedere anche
In questo documento sono stati esaminati alcuni problemi comuni di Crittografia dischi di Azure ed è stato illustrato come risolverli. Per altre informazioni su questo servizio e sulle relative funzionalità, vedere:

- [Applicare la crittografia dei dischi nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Crittografare una macchina virtuale di Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Crittografia dei dati inattivi di Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

