---
title: Risolvere il problema dei nomi di dispositivo nelle macchine virtuali Linux in Azure | Microsoft Docs
description: "Spiega perché i nomi di dispositivo delle macchine virtuali Linux cambiano e come risolvere il problema."
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
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 9b1c9a283e1439c0db5fa37b6601868a7a830ed3
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Risolvere il problema dei nomi di dispositivo nelle macchine virtuali Linux

Questo articolo spiega i motivi per cui i nomi dei dispositivi cambiano dopo il riavvio di una macchina virtuale Linux o quando i dischi vengono ricollegati. L'articolo offre anche alcune soluzioni per questo problema.

## <a name="symptoms"></a>Sintomi
Durante l'esecuzione di macchine virtuali Linux in Microsoft Azure possono verificarsi i problemi seguenti:

- La macchina virtuale non si avvia dopo il riavvio.
- Se i dischi dati vengono scollegati e quindi ricollegati, i nomi dei dispositivi disco cambiano.
- Dato che il nome dei dispositivi è cambiato, le applicazioni e gli script che fanno riferimento a un disco tramite il nome del dispositivo non funzionano più.

## <a name="cause"></a>Causa

La coerenza dei percorsi dei dispositivi in Linux tra un riavvio e l'altro non è garantita. I nomi dei dispositivi sono costituiti da numeri principali (lettere) e numeri secondari. Quando il driver dei dispositivi di archiviazione Linux rileva un nuovo dispositivo, assegna a quest'ultimo un numero di dispositivo principale e uno secondario, ricavandoli dall'intervallo disponibile. Quando un dispositivo viene rimosso, i numeri del dispositivo vengono liberati e possono essere usati di nuovo.

Il problema si verifica perché l'analisi dei dispositivi in Linux, programmata dal sottosistema SCSI, viene eseguita in modo asincrono. Di conseguenza, il nome di percorso di un dispositivo può variare da un riavvio a un altro. 

## <a name="solution"></a>Soluzione

Per risolvere questo problema, usare la denominazione permanente. Per consentire la denominazione persistente sono disponibili quattro metodi: per etichetta file system, per UUID, per ID e per percorso. Per le macchine virtuali Linux di Azure è consigliabile usare il metodo per etichetta file system o per UUID. 

La maggior parte delle distribuzioni dispone dei parametri `fstab` **nofail** o **nobootwait**. Questi parametri consentono l'avvio di un sistema quando il montaggio del disco in fase di avvio non riesce. Per altre informazioni su questi parametri, vedere la documentazione della distribuzione in uso. Per informazioni su come configurare una macchina virtuale Linux per l'uso di un UUID quando si aggiunge un disco dati, vedere [Connettersi alla VM Linux per montare il nuovo disco](add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk). 

Quando l'agente Linux di Azure viene installato in una macchina virtuale, usa le regole Udev per creare un set di collegamenti simbolici nel percorso /dev/disk/azure. Le applicazioni e gli script usano regole Udev per identificare i dischi collegati alla macchina virtuale, oltre al tipo di disco e ai LUN del disco stesso.

### <a name="identify-disk-luns"></a>Identificare i LUN del disco

Le applicazioni usano i LUN per trovare tutti i dischi collegati e creare i collegamenti simbolici. L'agente Linux di Azure include le regole Udev che configurano i collegamenti simbolici da un LUN ai dispositivi:

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
                                 
Le informazioni LUN dall'account guest Linux possono essere recuperate tramite `lsscsi` o con uno strumento analogo:

       $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

Le informazioni LUN guest vengono usate con i metadati di sottoscrizione di Azure per individuare il disco rigido virtuale nell'Archiviazione di Azure che contiene i dati della partizione. È ad esempio possibile usare l'interfaccia della riga di comando `az`:

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

Le applicazioni e gli script leggono l'output di `blkid` o fonti di informazioni simili per creare collegamenti simbolici nel percorso /dev. L'output visualizza l'UUID di tutti i dischi collegati alla macchina virtuale e i file di dispositivo associati:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Le regole Udev dell'agente Linux di Azure creano un set di collegamenti simbolici nel percorso /dev/disk/azure:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Le applicazioni usano i collegamenti per identificare il dispositivo del disco di avvio e il disco della risorsa (temporaneo). Per individuare le partizioni in Azure, le applicazioni devono fare riferimento al percorso /dev/disk/azure/root-part1 o /dev/disk/azure-resource-part1.

Eventuali partizioni aggiuntive dell'elenco `blkid` si trovano in un disco dati. Le applicazioni mantengono l'UUID per tali partizioni e usano un percorso simile al seguente per individuare il nome del dispositivo in runtime:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1

    
### <a name="get-the-latest-azure-storage-rules"></a>Ottenere le regole di Archiviazione di Azure più recenti

Per ottenere le regole di Archiviazione di Azure più recenti, eseguire i comandi seguenti:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Vedere anche

Per altre informazioni, vedere gli articoli seguenti:

- [Ubuntu: uso di UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Persistent naming](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html) (Red Hat: denominazione persistente)
- [Linux: operazioni eseguibili con le UUID](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Introduction to device management in a modern Linux system](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system) (Udev: introduzione alla gestione dei dispositivi in un sistema Linux moderno)

