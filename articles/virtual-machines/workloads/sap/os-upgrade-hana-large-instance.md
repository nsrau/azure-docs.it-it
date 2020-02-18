---
title: Aggiornamento del sistema operativo per SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Eseguire l'aggiornamento del sistema operativo per SAP HANA in Azure (istanze Large)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0275d30f2314073af07eced224ade47c49e580c1
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425377"
---
# <a name="operating-system-upgrade"></a>Aggiornamento del sistema operativo
Questo documento descrive i dettagli sugli aggiornamenti del sistema operativo nelle istanze HANA Large.

>[!NOTE]
>L'aggiornamento del sistema operativo è responsabilità del cliente, il supporto per le operazioni Microsoft può guidare l'utente nelle aree principali da tenere in considerazione durante l'aggiornamento. Prima di pianificare un aggiornamento, è consigliabile consultare anche il fornitore del sistema operativo.

Durante il provisioning unità HLI, il team operativo Microsoft installa il sistema operativo.
È necessario mantenere il sistema operativo nel tempo (ad esempio applicando patch, eseguendo ottimizzazioni, applicando aggiornamenti e così via) nell'unità HLI.

Prima di apportare modifiche importanti al sistema operativo (ad esempio, aggiornare SP1 a SP2), è necessario contattare il team operativo Microsoft aprendo un ticket di supporto per consultarlo.

Includi nel ticket:

* L'ID di sottoscrizione HLI.
* Il nome del server.
* Il livello di patch che si prevede di applicare.
* La data in cui si prevede di effettuare la modifica. 

È consigliabile aprire il ticket almeno una settimana prima della data di aggiornamento desiderata per permettere al team di Microsoft Operations di verificare se è necessario un aggiornamento del firmware nel pannello del server.


Per la matrice di supporto delle diverse versioni di SAP HANA con le varie versioni di Linux, vedere la [nota SAP 2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Problemi noti

Ecco alcuni problemi comuni noti che possono presentarsi durante l'aggiornamento:
- Nello SKU classe di tipo II, il Software Foundation Server (SFS) viene rimosso dopo l'aggiornamento del sistema operativo. È necessario reinstallare il FS compatibile dopo l'aggiornamento del sistema operativo.
- I driver di scheda Ethernet (ENIC e FNIC) di cui è stato eseguito il rollback a una versione precedente. È necessario reinstallare la versione compatibile dei driver dopo l'aggiornamento.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA configurazione consigliata per istanze Large (tipo I)

La configurazione del sistema operativo può derivare dalle impostazioni consigliate nel tempo a causa dell'applicazione di patch, degli aggiornamenti del sistema e delle modifiche apportate dai clienti. Microsoft identifica inoltre gli aggiornamenti necessari per i sistemi esistenti per assicurarsi che siano configurati in modo ottimale per ottenere prestazioni ottimali e resilienza. Le istruzioni seguenti illustrano le raccomandazioni che riguardano le prestazioni di rete, la stabilità del sistema e le prestazioni ottimali di HANA.

### <a name="compatible-enicfnic-driver-versions"></a>Versioni del driver eNIC/fNIC compatibili
  Per garantire una corretta stabilità del sistema e delle prestazioni di rete, è consigliabile assicurarsi che sia installata la versione appropriata specifica del sistema operativo dei driver eNIC e fNIC, come illustrato nella tabella di compatibilità riportata di seguito. I server vengono distribuiti ai clienti con versioni compatibili. Si noti che in alcuni casi, durante l'applicazione di patch del sistema operativo/kernel, è possibile eseguire il rollback dei driver alle versioni predefinite del driver. Assicurarsi che la versione del driver appropriata esegua le operazioni di applicazione di patch del sistema operativo/kernel.
       
      
  |  Fornitore del sistema operativo    |  Versione del pacchetto del sistema operativo     |  Driver eNIC  |  Driver fNIC |
  |---------------|-------------------------|---------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   2.3.0.44    |   1.6.0.36   |
  |   Red Hat     |  RHEL 7.2               |   2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Comandi per l'aggiornamento dei driver e per pulire i pacchetti RPM precedenti
```
rpm -U driverpackage.rpm
rpm -e olddriverpackage.rpm
```

#### <a name="commands-to-confirm"></a>Comandi da confermare
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>Errore di aggiornamento di SuSE HLIs GRUB
Le istanze large di SAP in Azure HANA (Type I) possono trovarsi in uno stato non avviabile dopo l'aggiornamento. La procedura seguente corregge questo problema.
#### <a name="execution-steps"></a>Passaggi di esecuzione


*   Eseguire `multipath -ll` comando.
*   Ottenere l'ID LUN la cui dimensione è approssimativamente 50G o usare il comando: `fdisk -l | grep mapper`
*   Aggiornare `/etc/default/grub_installdevice` file con `/dev/mapper/<LUN ID>`di riga. Esempio:/dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>L'ID LUN varia da server a server.


### <a name="disable-edac"></a>Disabilitare EDAC 
   Il modulo di rilevamento e correzione degli errori (EDAC) consente di rilevare e correggere gli errori di memoria. Tuttavia, l'hardware sottostante per SAP HANA in istanze Large di Azure (Type I) sta già eseguendo la stessa funzione. Se la stessa funzionalità è abilitata a livello di hardware e sistema operativo, può causare conflitti e causare arresti occasionali e non pianificati del server. È quindi consigliabile disabilitare il modulo dal sistema operativo.

#### <a name="execution-steps"></a>Passaggi di esecuzione

* Controllare se il modulo EDAC è abilitato. Se viene restituito un output nel comando seguente, significa che il modulo è abilitato. 
```
lsmod | grep -i edac 
```
* Disabilitare i moduli aggiungendo le righe seguenti al file `/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
È necessario riavviare il sistema per apportare modifiche. Eseguire `lsmod` comando e verificare che il modulo non sia presente nell'output.


### <a name="kernel-parameters"></a>Parametri del kernel
   Verificare che siano state applicate le impostazioni corrette per `transparent_hugepage`, `numa_balancing`, `processor.max_cstate`, `ignore_ce` e `intel_idle.max_cstate`.

* intel_idle. max_cstate = 1
* processore. max_cstate = 1
* transparent_hugepage = mai
* numa_balancing = Disabilita
* MCE = ignore_ce


#### <a name="execution-steps"></a>Passaggi di esecuzione

* Aggiungere questi parametri alla riga `GRB_CMDLINE_LINUX` nel file `/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Creare un nuovo file grub.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Riavviare il sistema.


## <a name="next-steps"></a>Passaggi successivi
- Vedere [Backup e ripristino](hana-overview-high-availability-disaster-recovery.md) per la classe SKU di tipo I.
- Vedere [backup del sistema operativo per SKU di tipo II di revisione 3 timbri](os-backup-type-ii-skus.md) per la classe SKU di tipo II.
