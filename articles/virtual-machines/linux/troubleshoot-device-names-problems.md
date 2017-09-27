---
title: Nomi di dispositivo della macchina virtuale Linux modificati in Azure | Microsoft Docs
description: Viene spiegato il motivo per cui i nomi dei dispositivi sono cambiati e si offre una soluzione a questo problema.
services: virtual-machines-linux
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: 
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 07/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 789f4580901a22dc3aaae9599c7205c76f268403
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="troubleshooting-linux-vm-device-names-are-changed"></a>Risoluzione dei problemi: nomi dei dispositivi della macchina virtuale Linux modificati

L'articolo spiega i motivi per cui i nomi dei dispositivi sono diversi dopo il riavvio di una macchina virtuale Linux (VM) o dopo aver ricollegato i dischi. offre la soluzione a questo problema.

## <a name="symptom"></a>Sintomo

È possibile che l'utente debba affrontare i problemi seguenti durante l'esecuzione di macchine virtuali Linux in Microsoft Azure.

- La macchina virtuale non si avvia dopo il riavvio.

- Se i dischi di dati vengono scollegati e poi ricollegati, i nomi dei dispositivi per i dischi cambiano.

- Impossibile eseguire un'applicazione o uno script che fa riferimento a un disco con il nome del dispositivo. Ci si accorge che il nome del dispositivo del disco è cambiato.

## <a name="cause"></a>Causa

La coerenza dei percorsi del dispositivo in Linux non è garantita tra i vari riavvi. I nomi del dispositivo sono costituiti da numeri principali, ovvero lettere, e numeri secondari.  Quando il driver del dispositivo di archiviazione di Linux rileva un nuovo dispositivo, assegna un numero di dispositivo principale e secondario dall'intervallo disponibile. Quando un dispositivo viene rimosso, il numero di dispositivi può essere riusato in un secondo momento.

Il problema si verifica perché l'analisi del dispositivo in Linux pianificata dal sottosistema SCSI avviene in modo asincrono. La denominazione del percorso del dispositivo finale può variare tra un riavvio e l'altro. 

## <a name="solution"></a>Soluzione

Per risolvere questo problema, usare la denominazione permanente. Esistono quattro metodi per la denominazione permanente: per etichetta file system, per UUID, per ID e per percorso. Per le macchine virtuali Linux di Azure, è consigliabile usare i metodi per etichetta file system e per UUID. 

La maggior parte delle distribuzioni specifica anche le opzioni fstab **nofail** o **nobootwait**. Queste opzioni consentono l'avvio di un sistema anche se il montaggio del disco non riesce in fase di avvio. Per altre informazioni su questi parametri, consultare la documentazione della distribuzione. Per altre informazioni su come configurare una macchina virtuale Linux per l'uso di un UUID quando si aggiunge un disco dati, vedere [Connettersi alla VM Linux per montare il nuovo disco](add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk). 

Quando l'agente Linux di Azure viene installato in una macchina virtuale, questo usa le regole Udev per costruire un set di collegamenti simbolici in **/dev/disk/azure**. Queste regole Udev possono essere usate dalle applicazioni e dagli script per identificare i dischi collegati alla macchina virtuale, il tipo e il LUN.

## <a name="more-information"></a>Altre informazioni

### <a name="identify-disk-luns"></a>Identificare i LUN del disco

Un'applicazione può usare i LUN per trovare tutti i dischi collegati e creare i collegamenti simbolici. L'agente Linux di Azure include ora le regole udev che configurano i collegamenti simbolici da un LUN ai dispositivi, come indicato di seguito:

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3                                    
                                 

È possibile recuperare le informazioni sui LUN dal guest Linux con lsscsi o uno strumento simile, come indicato di seguito.

       $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

Le informazioni LUN guest possono essere usate con i metadati della sottoscrizione di Azure per identificare la posizione del disco virtuale rigido nell'archivio di Azure che archivia i dati della partizione. Ad esempio, usare az cli:

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks                                        
    [                                                                                                                                                                  
    {                                                                                                                                                                  
    "caching": "None",                                                                                                                                              
      "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 1023,                                                                                                                                             
      "image": null,                                                                                                                                                   
    "lun": 0,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-114353",                                                                                                                    
    "vhd": {                                                                                                                                                          
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"                                                       
    }                                                                                                                                                              
    },                                                                                                                                                                
    {                                                                                                                                                                   
    "caching": "None",                                                                                                                                               
    "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 512,                                                                                                                                              
    "image": null,                                                                                                                                                   
    "lun": 1,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-121516",                                                                                                                    
    "vhd": {                                                                                                                                                           
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"                                                       
      }                                                                                                                                                             
      }                                                                                                                                                             
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Individuare l'UUID del file system usando blkid

Uno script o un'applicazione possono leggere l'output di blkid oppure fonti di informazioni simili e creare collegamenti simbolici in **/dev**. L'output mostra gli UUID di tutti i dischi collegati alla macchina virtuale e il file del dispositivo a cui sono associati:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

La regola udev waagent crea un set di collegamenti simbolici in **/dev/disk/azure**:


    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1


L'applicazione può usare queste informazioni identificano il dispositivo del disco di avvio e il disco della risorsa, temporaneo. In Azure le applicazioni devono fare riferimento a **/dev/disk/azure/root-part1** o **/dev/disk/azure-resource-part1** per individuare le partizioni.

Se sono presenti partizioni aggiuntive nell'elenco blkid, si trovano in un disco dati. Le applicazioni possono mantenere l'UUID per tali partizioni e usare un percorso come il seguente per individuare il nome del dispositivo in fase di esecuzione:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1

    
### <a name="get-the-latest-azure-storage-rules"></a>Ottenere le regole di archiviazione di Azure più recenti

Per ottenere le regole di archiviazione di Azure più recenti, eseguire i comandi seguenti:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block


Per altre informazioni, vedere gli articoli seguenti:

- [Ubuntu: uso di UUID](https://help.ubuntu.com/community/UsingUUID)

- [Red Hat: denominazione permanente](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)

- [Linux: operazioni eseguibili con le UUID](https://www.linux.com/news/what-uuids-can-do-you)

- [Udev: Introduction to Device Management In Modern Linux System](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system) (Udev: introduzione alla gestione dei dispositivi nel sistema Linux moderno)


