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
ms.openlocfilehash: 7fea0f74a90bc7b786a9b302d6282f9fb70e5412
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991484"
---
# <a name="operating-system-upgrade"></a>Aggiornamento del sistema operativo
Questo documento descrive i dettagli sugli aggiornamenti del sistema operativo nelle istanze HANA Large.

>[!NOTE]
>L'aggiornamento del sistema operativo è responsabilità del cliente, il supporto operativo Microsoft può guidare l'utente alle aree chiave da prestare attenzione durante l'aggiornamento. Prima di pianificare un aggiornamento, è consigliabile consultare anche il fornitore del sistema operativo.

Durante il provisioning dell'unità HLI, il team operativo Microsoft installa il sistema operativo.
È necessario mantenere il sistema operativo nel tempo (ad esempio applicando patch, eseguendo ottimizzazioni, applicando aggiornamenti e così via) nell'unità HLI.

Prima di apportare modifiche importanti al sistema operativo (ad esempio, l'aggiornamento SP1 a SP2), è necessario contattare il team Microsoft Operations aprendo un ticket di supporto per la consultazione.

Includi nel biglietto:

* L'ID di sottoscrizione HLI.
* Il nome del server.
* Il livello di patch che si prevede di applicare.
* La data in cui si prevede di effettuare la modifica. 

È consigliabile aprire il ticket almeno una settimana prima della data di aggiornamento desiderata per permettere al team di Microsoft Operations di verificare se è necessario un aggiornamento del firmware nel pannello del server.


Per la matrice di supporto delle diverse versioni di SAP HANA con le varie versioni di Linux, vedere la [nota SAP 2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Problemi noti

Ecco alcuni problemi comuni noti che possono presentarsi durante l'aggiornamento:
- Nello SKU classe di tipo II, il Software Foundation Server (SFS) viene rimosso dopo l'aggiornamento del sistema operativo. È necessario reinstallare il file SFS compatibile dopo l'aggiornamento del sistema operativo.
- I driver di scheda Ethernet (ENIC e FNIC) di cui è stato eseguito il rollback a una versione precedente. È necessario reinstallare la versione compatibile dei driver dopo l'aggiornamento.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>Configurazione consigliata per SAP HANA Large Instance (Tipo I)

La configurazione del sistema operativo può derivare dalle impostazioni consigliate nel tempo a causa dell'applicazione di patch, degli aggiornamenti del sistema e delle modifiche apportate dai clienti. Inoltre, Microsoft identifica gli aggiornamenti necessari per i sistemi esistenti per garantire che siano configurati in modo ottimale per ottenere prestazioni e resilienza ottimali. Le istruzioni riportate di seguito illustrano i suggerimenti che riguardano le prestazioni di rete, la stabilità del sistema e le prestazioni HANA ottimali.

### <a name="compatible-enicfnic-driver-versions"></a>Versioni compatibili dei driver eNIC/fNIC
  Al fine di avere prestazioni di rete adeguate e la stabilità del sistema, si consiglia di assicurarsi che la versione appropriata specifica del sistema operativo dei driver eNIC e fNIC sono installati come descritto nella seguente tabella di compatibilità. I server vengono consegnati ai clienti con versioni compatibili. Si noti che, in alcuni casi, durante l'applicazione di patch al sistema operativo/kernel, i driver possono tornare alle versioni predefinite dei driver. Assicurarsi che la versione del driver appropriata esegua operazioni di applicazione di patch post-oS/Kernel.
       
      
  |  Fornitore del sistema operativo    |  Versione pacchetto del sistema operativo     |  Versione del firmware  |  Driver eNIC |  Driver fNIC | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   Suse        |  SLES 12 SP2            |   3.1.3h           |  2.3.0.40    |   1.6.0.34   |
  |   Suse        |  SLES 12 SP3            |   3.1.3h           |  2.3.0.44    |   1.6.0.36   |
  |   Suse        |  SLES 12 SP4            |   3.2.3b           |  2.3.0.47    |   2.0.0.54   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3h           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Comandi per l'aggiornamento del driver e per pulire i vecchi pacchetti rpm
```
rpm -U driverpackage.rpm
rpm -e olddriverpackage.rpm
```

#### <a name="commands-to-confirm"></a>Comandi per confermare
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>Errore di aggiornamento GRUB SuSE HLIs
SAP in Azure HANA Large Instances (Tipo I) può trovarsi in uno stato non avviabile dopo l'aggiornamento. La procedura seguente consente di risolvere questo problema.
#### <a name="execution-steps"></a>Passaggi di esecuzione


*   Eseguire `multipath -ll` il comando.
*   Ottenere l'ID LUN la cui dimensione è di circa 50G o utilizzare il comando:`fdisk -l | grep mapper`
*   Aggiorna `/etc/default/grub_installdevice` file `/dev/mapper/<LUN ID>`con riga . Esempio: /dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>L'ID LUN varia da server a server.


### <a name="disable-edac"></a>Disabilitare EDAC 
   Il modulo EDAC (Error Detection And Correction) consente di rilevare e correggere gli errori di memoria. Tuttavia, l'hardware sottostante per SAP HANA in Azure Large Instances (Tipo I) sta già eseguendo la stessa funzione. La stessa funzionalità abilitata a livello di hardware e sistema operativo può causare conflitti e causare arresti occasionali e non pianificati del server. Pertanto, si consiglia di disattivare il modulo dal sistema operativo.

#### <a name="execution-steps"></a>Passaggi di esecuzione

* Verificare se il modulo EDAC è abilitato. Se viene restituito un output nel comando seguente, significa che il modulo è abilitato. 
```
lsmod | grep -i edac 
```
* Disattivare i moduli aggiungendo le seguenti righe al file`/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
È necessario riavviare il sistema per apportare modifiche. Eseguire `lsmod` il comando e verificare che il modulo non sia presente nell'output.


### <a name="kernel-parameters"></a>Parametri del kernel
   Assicurarsi che siano `transparent_hugepage` `numa_balancing`applicate `processor.max_cstate` `ignore_ce` l'impostazione corretta per , , e `intel_idle.max_cstate` .

* intel_idle.max_cstate 1
* processore.max_cstate1
* transparent_hugepage mai
* numa_balancing- disabilitare
* ignore_ce mce-ignore_ce


#### <a name="execution-steps"></a>Passaggi di esecuzione

* Aggiungere questi parametri `GRB_CMDLINE_LINUX` alla riga del file`/etc/default/grub`
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
- Fare riferimento al backup del sistema [operativo per gli SKU di tipo II dei timbri di revisione 3](os-backup-type-ii-skus.md) per la classe SKU di tipo II.
