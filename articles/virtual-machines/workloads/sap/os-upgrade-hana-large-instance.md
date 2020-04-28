---
title: Aggiornamento del sistema operativo per SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Eseguire l'aggiornamento del sistema operativo per SAP HANA in Azure (istanze Large)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8485f3474da18e052bc0eab6c053be084ef884a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192417"
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
       
      
  |  Fornitore del sistema operativo    |  Versione del pacchetto del sistema operativo     |  Versione del firmware  |  Driver eNIC |  Driver fNIC | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3 h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3 h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3 i           |  2.3.0.47    |   2.0.0.54   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3 i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3 i           |  2.3.0.45    |   1.6.0.37   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3 h           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Comandi per l'aggiornamento dei driver e per pulire i pacchetti RPM precedenti

#### <a name="command-to-check-existing-installed-drivers"></a>Comando per verificare i driver installati esistenti
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>Elimina eNIC/fNIC RPM esistente
```
rpm -e <old-rpm-package>
```
#### <a name="install-the-recommended-enicfnic-driver-packages"></a>Installare i pacchetti driver eNIC/fNIC consigliati
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-the-installation"></a>Comandi per confermare l'installazione
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>Errore di aggiornamento di SuSE HLIs GRUB
Le istanze large di SAP in Azure HANA (Type I) possono trovarsi in uno stato non avviabile dopo l'aggiornamento. La procedura seguente corregge questo problema.
#### <a name="execution-steps"></a>Passaggi di esecuzione


*   Eseguire `multipath -ll` il comando.
*   Ottenere l'ID LUN la cui dimensione è approssimativamente 50G o usare il comando:`fdisk -l | grep mapper`
*   Aggiorna `/etc/default/grub_installdevice` il file con `/dev/mapper/<LUN ID>`la riga. Esempio:/dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>L'ID LUN varia da server a server.


### <a name="disable-edac"></a>Disabilitare EDAC 
   Il modulo di rilevamento e correzione degli errori (EDAC) consente di rilevare e correggere gli errori di memoria. Tuttavia, l'hardware sottostante per SAP HANA in istanze Large di Azure (Type I) sta già eseguendo la stessa funzione. Se la stessa funzionalità è abilitata a livello di hardware e sistema operativo, può causare conflitti e causare arresti occasionali e non pianificati del server. È quindi consigliabile disabilitare il modulo dal sistema operativo.

#### <a name="execution-steps"></a>Passaggi di esecuzione

* Controllare se il modulo EDAC è abilitato. Se viene restituito un output nel comando seguente, significa che il modulo è abilitato. 
```
lsmod | grep -i edac 
```
* Disabilitare i moduli aggiungendo le righe seguenti al file`/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
È necessario riavviare il sistema per apportare modifiche. Eseguire `lsmod` il comando e verificare che il modulo non sia presente nell'output.


### <a name="kernel-parameters"></a>Parametri del kernel
   Verificare che siano state applicate le `transparent_hugepage`impostazioni `numa_balancing`corrette `processor.max_cstate`per `ignore_ce` , `intel_idle.max_cstate` , e.

* intel_idle. max_cstate = 1
* processore. max_cstate = 1
* transparent_hugepage = mai
* numa_balancing = Disabilita
* MCE = ignore_ce


#### <a name="execution-steps"></a>Passaggi di esecuzione

* Aggiungere questi parametri alla `GRB_CMDLINE_LINUX` riga nel file`/etc/default/grub`
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
