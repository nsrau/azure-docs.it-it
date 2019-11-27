---
title: Sincronizzazione dell'ora per le macchine virtuali Linux in Azure
description: Sincronizzazione dell'ora per le macchine virtuali Linux.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 1e459e96c128e20f44f1a5adcb18c5b1824c3bf5
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534114"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Sincronizzazione dell'ora per le macchine virtuali Linux in Azure

La sincronizzazione dell'ora è importante per la sicurezza e la correlazione degli eventi. In alcuni casi viene usata per l'implementazione di transazioni distribuite. La precisione dell'ora tra più sistemi di computer si ottiene con la sincronizzazione. La sincronizzazione può essere interessata da più fattori, tra cui i riavvii e il traffico di rete tra l'origine dell'ora e il computer che la recupera. 

Azure è supportato da un'infrastruttura che esegue Windows Server 2016. Windows Server 2016 ha migliorato gli algoritmi usati per correggere l'ora e imporre all'orologio locale la sincronizzazione con l'ora UTC.  La funzionalità dell'ora esatta di Windows Server 2016 ha notevolmente migliorato il modo in cui il servizio VMICTimeSync gestisce le macchine virtuali con l'host per l'ora esatta. I miglioramenti includono un'ora iniziale più accurata per l'avvio o il ripristino della macchina virtuale e la correzione della latenza di interrupt. 

>[!NOTE]
>Per una rapida panoramica del servizio Ora di Windows, guardare questo [video con informazioni generali di alto livello](https://aka.ms/WS2016TimeVideo).
>
> Per altre informazioni, vedere [Accurate time for Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time) (Ora esatta per Windows Server 2016). 

## <a name="overview"></a>Overview

La precisione di un orologio del computer viene misurata in base a quanto l'orologio del computer si avvicina allo standard dell'ora UTC. L'ora UTC è definita da un campione multinazionale di orologi atomici precisi che possono essere sfalsati solo di un secondo in 300 anni. Tuttavia, leggere l'ora UTC direttamente richiede un hardware specializzato. Al contrario, i server di riferimento ora vengono sincronizzati con UTC e sono accessibili da altri computer in modo da garantire scalabilità e affidabilità. In ogni computer viene eseguito un servizio di sincronizzazione dell'ora che sa quali server di riferimento ora usare e verifica periodicamente se l'orologio del computer deve essere corretto e, se necessario, regola l'ora. 

Gli host di Azure vengono sincronizzati con i server di riferimento ora interni di Microsoft che ricavano l'ora da dispositivi di strato 1 di proprietà di Microsoft, con antenne GPS. Le macchine virtuali di Azure possono dipendere dal proprio host per passare l'ora esatta (*ora host*) alla macchina virtuale oppure la macchina virtuale può ottenere direttamente l'ora da un server di riferimento ora o una combinazione di entrambi. 

Nei componenti hardware autonomi il sistema operativo Linux legge solo l'orologio dell'hardware dell'host in fase di avvio. Successivamente, l'orologio viene gestito usando il timer di interrupt nel kernel di Linux. In questa configurazione l'orologio si desincronizza nel tempo. Nelle distribuzioni di Linux più recenti in Azure, le macchine virtuali possono usare il provider VMICTimeSync, incluso nei servizi di integrazione di Linux, per eseguire query in modo da aggiornare più spesso l'orologio dall'host.

Le interazioni delle macchine virtuali con l'host possono influire sull'orologio. Durante la [manutenzione con mantenimento della memoria](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot) le macchine virtuali vengono messe in pausa fino a 30 secondi. Ad esempio, prima che inizi la manutenzione l'orologio della macchina virtuale indica 10:00:00 AM e dura 28 secondi. Quando l'esecuzione della macchina virtuale riprende, l'orologio della macchina virtuale indicherebbe ancora 10:00:00 AM, con un ritardo di 28 secondi. Per correggere questo inconveniente, il servizio VMICTimeSync monitora ciò che accade nell'host e chiede di apportare modifiche alle macchine virtuali per compensare.

Senza il lavoro di sincronizzazione dell'ora, l'orologio della macchina virtuale può accumulare errori. Quando è presente solo una macchina virtuale, l'effetto può non essere significativo, a meno che il carico di lavoro richieda una misurazione del tempo estremamente precisa. Tuttavia, nella maggior parte dei casi sono disponibili più macchine virtuali interconnesse che usano l'ora per tenere traccia delle transazioni e l'ora deve essere coerente in tutta la distribuzione. Quando l'ora è diversa tra le macchine virtuali, si possono osservare gli effetti seguenti:

- L'autenticazione ha esito negativo. I protocolli di sicurezza come Kerberos o la tecnologia dipendente dal certificato si basano sulla coerenza dell'ora tra i sistemi.
- È molto difficile capire cosa è successo in un sistema se le ore non coincidono nei log o in altri dati. Può sembrare che lo stesso evento si sia verificato in momenti diversi e questo rende difficile la correlazione.
- Se orologio è disattivato, la fatturazione può essere calcolata in modo non corretto.



## <a name="configuration-options"></a>Opzioni di configurazione

Sono sostanzialmente tre i modi per configurare la sincronizzazione dell'ora per le macchine virtuali Linux ospitate in Azure:

- La configurazione predefinita per le immagini di Azure Marketplace usa sia l'ora NTP, sia l'ora dell'host di VMICTimeSync. 
- Solo host usando VMICTimeSync.
- Usare un altro server di riferimento ora esterno usando o meno l'ora dell'host di VMICTimeSync.


### <a name="use-the-default"></a>Usare l'impostazione predefinita

Per impostazione predefinita, la maggior parte delle immagini di Azure Marketplace per Linux sono configurate per la sincronizzazione da due origini: 

- Come origine primaria NTP, che ottiene l'ora da un server NTP. Ad esempio, le immagini di Ubuntu 16.04 LTS del Marketplace usano **ntp.ubuntu.com**.
- Come origine secondaria il servizio VMICTimeSync, usato per comunicare l'ora dell'host alle macchine virtuali e apportare correzioni dopo che la macchina virtuale viene messa in pausa per la manutenzione. Gli host di Azure usano i dispositivi strato 1 di proprietà di Microsoft per mantenere l'ora esatta.

Nelle distribuzioni di Linux più recenti il servizio VMICTimeSync usa il protocollo PTP (Precision Time Protocol), ma è possibile che le distribuzioni precedenti non supportino PTP e che venga eseguito il fallback a NTP per recuperare l'ora dall'host.

Per verificare che NTP esegua correttamente la sincronizzazione, usare il comando `ntpq -p`.

### <a name="host-only"></a>Solo host 

Poiché i server NTP, ad esempio time.windows.com e ntp.ubuntu.com, sono pubblici, la sincronizzazione dell'ora con gli stessi richiede l'invio di traffico via Internet. La variazione dei ritardi dei pacchetti può influire negativamente sulla qualità della sincronizzazione dell'ora. La rimozione di NTP passando alla sincronizzazione solo host può talvolta migliorare i risultati della sincronizzazione del tempo.

Il passaggio alla sincronizzazione dell'ora solo per l'host ha senso se si verificano problemi di sincronizzazione dell'ora usando la configurazione predefinita. Provare la sincronizzazione solo per l'host per verificare se contribuisce a migliorare la sincronizzazione dell'ora nella macchina virtuale. 

### <a name="external-time-server"></a>Server di riferimento ora esterno

In caso di requisiti specifici per la sincronizzazione dell'ora, è anche disponibile un'opzione per l'uso di server di riferimento ora esterni. I server di riferimento ora esterni sono in grado di specificare orari precisi, che possono essere utili per gli scenari di test, garantendo l'uniformità dell'ora con i computer ospitati nei data center non Microsoft o gestendo i secondi intercalari in modo speciale.

È possibile combinare un server di riferimento ora esterno con il servizio VMICTimeSync per avere risultati simili alla configurazione predefinita. La combinazione di un server di riferimento ora esterno con VMICTimeSync è l'opzione migliore per gestire i problemi che possono verificarsi quando le macchine virtuali vengono messe in pausa per la manutenzione. 

## <a name="tools-and-resources"></a>Strumenti e risorse

Esistono alcuni comandi di base per controllare la configurazione della sincronizzazione dell'ora. La documentazione per la distribuzione di Linux include informazioni dettagliate sul modo migliore per configurare la sincronizzazione dell'ora per tale distribuzione.

### <a name="integration-services"></a>Servizi di integrazione

Verificare se il servizio di integrazione (hv_utils) viene caricato.

```bash
lsmod | grep hv_utils
```
Dovrebbe essere visualizzata una schermata simile alla seguente:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Verificare se il daemon dei servizi di integrazione Hyper-V è in esecuzione.

```bash
ps -ef | grep hv
```

Dovrebbe essere visualizzata una schermata simile alla seguente:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp"></a>Verifica della disponibilità del protocollo PTP

Con le versioni più recenti di Linux è disponibile un'origine di orologio PTP (Precision Time Protocol) come parte del provider VMICTimeSync. Nelle versioni più datate di Red Hat Enterprise Linux o CentOS 7.x i [servizi di integrazione Linux](https://github.com/LIS/lis-next) possono essere scaricati e usati per installare il driver aggiornato. Quando si usa PTP, il dispositivo Linux sarà nel formato /dev/ptp*x*. 

Verificare quali origini di orologio PTP sono disponibili.

```bash
ls /sys/class/ptp
```

In questo esempio il valore restituito è *ptp0* e può essere usato per verificare il nome dell'orologio. Per verificare il dispositivo, controllare il nome dell'orologio.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Il valore restituito dovrebbe essere **hyperv**.

### <a name="chrony"></a>chrony

In Red Hat Enterprise Linux e CentOS 7.x [chrony](https://chrony.tuxfamily.org/) è configurato per l'uso di un orologio PTP. Il daemon di Network Time Protocol (ntpd) non supporta le origini PTP, quindi si consiglia di usare **chronyd**. Per abilitare PTP, aggiornare **chrony.conf**.

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Per altre informazioni su Red Hat e NTP, vedere la pagina relativa alla [configurazione di NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-configure_ntp). 

Per altre informazioni su chrony, vedere la pagina relativa all'[uso di chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-using_chrony).

Se le origini chrony e TimeSync vengono abilitate simultaneamente, è possibile contrassegnarne una come **preferenza** che imposti l'altra origine come backup. Poiché i servizi NTP non aggiornano l'orologio in caso di sfasamenti di grandi dimensioni, se non dopo un lungo periodo, VMICTimeSync ripristinerà l'orologio dagli eventi della macchina virtuale messi in pausa molto più rapidamente rispetto ai soli strumenti basati su NTP.

Per impostazione predefinita, chronyd accelera o rallenta il clock di sistema per correggere eventuali deviazioni temporali. Se la deriva diventa troppo grande, Chrony non riuscirà a correggere la tendenza. Per ovviare a questo problema, è possibile modificare il parametro `makestep` in **/etc/Chrony.conf** in modo da forzare un TimeSync se la deriva supera la soglia specificata.
 ```bash
makestep 1.0 -1
```
In questo caso, Chrony forza un aggiornamento temporale se la tendenza è maggiore di 1 secondo. Per applicare le modifiche, riavviare il servizio chronyd.

```bash
systemctl restart chronyd
```


### <a name="systemd"></a>systemd 

In Ubuntu e SUSE la sincronizzazione dell'ora viene sincronizzata usando [systemd](https://www.freedesktop.org/wiki/Software/systemd/). Per altre informazioni su Ubuntu, vedere la sezione relativa alla [sincronizzazione dell'ora](https://help.ubuntu.com/lts/serverguide/NTP.html). Per altre informazioni su SUSE, vedere la sezione 4.5.8 delle [note sulla versione di SUSE Linux Enterprise Server 12 SP3](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Accurate time for Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time) (Ora esatta per Windows Server 2016).


