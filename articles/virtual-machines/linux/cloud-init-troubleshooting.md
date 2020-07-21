---
title: Risolvere i problemi usando cloud-init
description: Risolvere i problemi di provisioning di una macchina virtuale di Azure con cloud-init.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 81e138e7149327c7b792df58180419b93417d263
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86510974"
---
# <a name="troubleshooting-vm-provisioning-with-cloud-init"></a>Risoluzione dei problemi relativi al provisioning di macchine virtuali con cloud-init

Se è stata creata un'immagine personalizzata generalizzata, usando cloud-init per eseguire il provisioning, ma è stato rilevato che la macchina virtuale non è stata creata correttamente, sarà necessario risolvere i problemi relativi alle immagini personalizzate.

Alcuni esempi di problemi con il provisioning:
- La macchina virtuale si blocca in fase di creazione per 40 minuti e la creazione della macchina virtuale è contrassegnata come non riuscita
- `CustomData`non viene elaborato
- Il montaggio del disco temporaneo non riesce
- Gli utenti non vengono creati o sono presenti problemi di accesso degli utenti
- La rete non è configurata correttamente
- Scambiare errori di file o partizioni

Questo articolo illustra come risolvere i problemi di cloud-init. Per informazioni più dettagliate, vedere [Deep Dive di cloud-init](./cloud-init-deep-dive.md).

## <a name="step-1-test-the-deployment-without-customdata"></a>Passaggio 1: testare la distribuzione senza`customData`

Cloud-init può accettare `customData` , che viene passato al momento della creazione della macchina virtuale. Per prima cosa è necessario assicurarsi che non si verifichino problemi con le distribuzioni. Provare a eseguire il provisioning della macchina virtuale senza passare alcuna configurazione. Se la macchina virtuale non viene sottoposta a provisioning, continuare con i passaggi seguenti, se si rileva che la configurazione che si sta passando non è stata applicata, andare al [passaggio 4](). 

## <a name="step-2-review-image-requirements"></a>Passaggio 2: esaminare i requisiti dell'immagine
La principale cause dell'errore di provisioning della macchina virtuale è che l'immagine del sistema operativo non soddisfa i prerequisiti per l'esecuzione in Azure. Assicurarsi che le immagini siano preparate correttamente prima di provare a effettuare il provisioning in Azure. 


Gli articoli seguenti illustrano i passaggi per preparare varie distribuzioni di Linux supportate in Azure:

- [Distribuzioni basate su CentOS](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES e openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)
- [Altro: Distribuzioni non approvate](create-upload-generic.md)

Per le [Immagini cloud-init di Azure supportate](./using-cloud-init.md), per le distribuzioni Linux sono già disponibili tutti i pacchetti e le configurazioni necessari per eseguire correttamente il provisioning dell'immagine in Azure. Se la macchina virtuale non è in grado di creare dalla propria immagine curata, provare un'immagine di Azure Marketplace supportata già configurata per cloud-init con l'opzione facoltativa `customData` . Se `customData` funziona correttamente con un'immagine di Azure Marketplace, probabilmente si verifica un problema con l'immagine curata.

## <a name="step-3-collect--review-vm-logs"></a>Passaggio 3: raccogliere & esaminare i log delle VM

Quando non è possibile eseguire il provisioning della macchina virtuale, in Azure viene visualizzato lo stato "creazione", per 20 minuti, viene riavviata la macchina virtuale e si attendono altri 20 minuti prima di contrassegnare infine la distribuzione della macchina virtuale come non riuscita, prima di contrassegnarla con un `OSProvisioningTimedOut` errore.

Mentre la macchina virtuale è in esecuzione, sono necessari i log della macchina virtuale per comprendere il motivo per cui il provisioning non è riuscito.  Per comprendere il motivo per cui il provisioning della macchina virtuale non è riuscito, non arrestare la macchina virtuale. Lasciare in esecuzione la macchina virtuale. Per raccogliere i log, è necessario evitare che la macchina virtuale sia in esecuzione in stato di esecuzione. Per raccogliere i log, usare uno dei metodi seguenti:

- [Console seriale](./serial-console-grub-single-user-mode.md)

- [Abilitare la diagnostica di avvio](./tutorial-monitor.md#enable-boot-diagnostics) prima di creare la macchina virtuale e quindi [visualizzarla](./tutorial-monitor.md#view-boot-diagnostics) durante l'avvio.

- [Eseguire AZ VM Repair](../troubleshooting/repair-linux-vm-using-azure-virtual-machine-repair-commands.md) per connettere e montare il disco del sistema operativo, che consente di raccogliere i log seguenti:
```bash
/var/log/cloud-init*
/var/log/waagent*
/var/log/syslog*
/var/log/rsyslog*
/var/log/messages*
/var/log/kern*
/var/log/dmesg*
/var/log/boot*
```
Per avviare la risoluzione dei problemi iniziale, iniziare con i log di cloud-init e comprendere dove si è verificato l'errore, quindi usare gli altri log per approfondire e fornire informazioni aggiuntive. 
* /var/log/cloud-init.log
* /var/log/cloud-init-output.log
* Log di avvio/seriale

In tutti i log avviare la ricerca di "failed", "WARNING", "WARN", "Err", "Error", "ERROR". È consigliabile impostare la configurazione per ignorare le ricerche con distinzione tra maiuscole e minuscole. 

> [!TIP]
> Per risolvere i problemi relativi a un'immagine personalizzata, è consigliabile aggiungere un utente durante l'immagine. Se il provisioning non riesce a impostare l'utente amministratore, è comunque possibile accedere al sistema operativo.

## <a name="analyzing-the-logs"></a>Analisi dei log

Ecco altri dettagli sugli elementi da cercare in ogni log di cloud-init.

### <a name="varlogcloud-initlog"></a>/var/log/cloud-init.log

Per impostazione predefinita, vengono scritti tutti gli eventi cloud-init con una priorità di debug o superiore `/var/log/cloud-init.log` . Questo consente di visualizzare i log dettagliati di ogni evento che si è verificato durante l'inizializzazione di cloud-init. 

Ad esempio:

```console
2019-10-10 04:51:25,321 - util.py[DEBUG]: Failed mount of '/dev/sr0' as 'auto': Unexpected error while running command.
Command: ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpLIrklc']
Exit code: 32
Reason: -
Stdout:
Stderr: mount: unknown filesystem type 'udf'
2020-01-31 00:21:53,352 - DataSourceAzure.py[WARNING]: /dev/sr0 was not mountable
```


Dopo aver individuato un errore o un avviso, leggere indietro nel log di cloud-init per comprendere il tentativo di cloud-init prima di raggiungere l'errore o l'avviso. In molti casi cloud-init eseguirà comandi del sistema operativo o eseguirà operazioni di provisioning prima dell'errore, che può fornire informazioni dettagliate sul motivo per cui gli errori sono apparsi nei log. L'esempio seguente mostra che cloud-init ha tentato di montare un dispositivo immediatamente prima che venga visualizzato un errore.

```output
2019-10-10 04:51:24,010 - util.py[DEBUG]: Running command ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpXXXXX'] with allowed return codes [0] (shell=False, capture=True)
```

Se si ha accesso alla [console seriale](./serial-console-grub-single-user-mode.md), è possibile provare a eseguire di nuovo il comando che cloud-init stava tentando di eseguire.

La registrazione per `/var/log/cloud-init.log` può essere riconfigurata anche in/etc/cloud/cloud.cfg.d/05_logging. cfg. Per altri dettagli sulla registrazione di cloud-init, vedere la [documentazione di cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html). 

### <a name="varlogcloud-init-outputlog"></a>/var/log/cloud-init-output.log

È possibile ottenere informazioni da `stdout` e `stderr` durante le [fasi di cloud-init](cloud-init-deep-dive.md). Ciò comporta in genere informazioni sulle tabelle di routing, informazioni sulla rete, informazioni di verifica della chiave host ssh `stdout` e `stderr` per ogni fase di cloud-init, insieme al timestamp per ogni fase. Se lo si desidera, `stderr` `stdout` è possibile riconfigurare la registrazione da `/etc/cloud/cloud.cfg.d/05_logging.cfg` .

### <a name="serialboot-logs"></a>Log di avvio/seriale 

Cloud-init presenta più dipendenze, descritte in prerequisiti obbligatori per le immagini in Azure, ad esempio la rete, l'archiviazione, la possibilità di montare un file ISO e montare e formattare il disco temporaneo. Uno di questi potrebbe generare errori e causare un errore di cloud-init. Ad esempio, se la macchina virtuale non è in grado di ottenere un lease DHCP, cloud-init avrà esito negativo.

Se non è ancora possibile isolare il motivo per cui cloud-init non è stato in grado di eseguire il provisioning, è necessario comprendere le fasi di cloud-init e quando vengono eseguiti i moduli Per altri dettagli, vedere l' [approfondimento di cloud-init](cloud-init-deep-dive.md) .


## <a name="step-4-investigate-why-the-configuration-isnt-being-applied"></a>Passaggio 4: esaminare il motivo per cui la configurazione non viene applicata
Non tutti gli errori in cloud-init generano un errore di provisioning irreversibile. Ad esempio, se si usa il `runcmd` modulo in una configurazione cloud-init, un codice di uscita diverso da zero dal comando in esecuzione provocherà un errore del provisioning della macchina virtuale. Questo perché viene eseguito dopo le funzionalità di provisioning di base che si verificano nelle prime 3 fasi di cloud-init. Per risolvere il motivo per cui la configurazione non è stata applicata, esaminare i log nel passaggio 3 e i moduli cloud-init manualmente. Ad esempio:

- `runcmd`-gli script vengono eseguiti senza errori? Eseguire manualmente la configurazione dal terminale per assicurarsi che funzionino come previsto.
- Installazione di pacchetti: la VM può accedere ai repository dei pacchetti?
- È anche necessario controllare la `customData` configurazione dei dati fornita alla macchina virtuale, disponibile in `/var/lib/cloud/instances/<unique-instance-identifier>/user-data.txt` .


## <a name="next-steps"></a>Passaggi successivi

Se non si riesce ancora a isolare il motivo per cui cloud-init non ha eseguito la configurazione, è necessario esaminare in modo più accurato ciò che accade in ogni fase di cloud-init e quando vengono eseguiti i moduli. Per altre informazioni, vedere [approfondimento sulla configurazione di cloud-init](./cloud-init-deep-dive.md) . 
