---
title: Come usare PerfInsights Linux in Microsoft Azure | Microsoft Docs
description: Informazioni su come usare PerfInsights per risolvere i problemi di prestazioni delle macchine virtuali Linux.
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: f618066f19a5cbbf25bc1fcc872cc654ce96dae3
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857222"
---
# <a name="how-to-use-perfinsights"></a>Come usare PerfInsights

[PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) è uno strumento di diagnostica self-help che raccoglie e analizza i dati di diagnostica e fornisce un report per la risoluzione dei problemi di prestazioni delle macchine virtuali Linux in Azure. PerfInsights può essere eseguito su macchine virtuali supportate come strumento autonomo o direttamente dal portale usando la [diagnostica delle prestazioni per le macchine virtuali di Azure](performance-diagnostics.md).

Se si verificano problemi di prestazioni delle macchine virtuali, eseguire lo strumento prima di contattare l'assistenza.

## <a name="supported-troubleshooting-scenarios"></a>Scenari per la risoluzione dei problemi supportati

PerfInsights può raccogliere e analizzare diversi tipi di informazioni. Le sezioni seguenti trattano scenari comuni.

### <a name="quick-performance-analysis"></a>Analisi rapida delle prestazioni

Questo scenario raccoglie informazioni di base, ad esempio la configurazione hardware e di archiviazione della macchina virtuale, diversi log, tra cui:

- Informazioni sul sistema operativo

- Informazioni sul dispositivo PCI

- Log del sistema operativo guest generale

- File di configurazione

- Informazioni sull'archiviazione

- Configurazione della macchina virtuale di Azure (raccolta con il [servizio metadati dell'istanza di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service))

- Elenco dei processi in esecuzione, del disco, della memoria e dell'utilizzo della CPU

- Informazioni di rete

Si tratta di una raccolta passiva di informazioni che non influirà sul sistema.

>[!Note]
>Lo scenario di analisi rapida delle prestazioni viene incluso automaticamente in ognuno dei seguenti scenari:

### <a name="performance-analysis"></a>Analisi delle prestazioni

Questo scenario è simile all'analisi rapida delle prestazioni, ma consente di acquisire informazioni di diagnostica per un periodo di tempo più lungo.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Tipo di informazioni raccolte da PerfInsights

Vengono raccolte informazioni su macchina virtuale Linux, sistema operativo, dispositivi a blocchi, consumer di risorse elevati, configurazione e diversi log. Ecco altri dettagli:

- Sistema operativo
  - Distribuzione e versione di Linux
  - Informazioni kernel
  - Informazioni sul driver

- Hardware
  - Dispositivi PCI [`*`]

- Processi e memoria
  - Elenco di processi (nome attività, memoria utilizzata, file aperti)
  - Memoria fisica totale, disponibile e gratuita
  - Memoria di swap totale, disponibile e gratuita
  - Acquisizione profilatura della CPU e dei processi utilizzo CPU a intervalli di 5 secondi
  - Acquisizione della profilatura dell'utilizzo di memoria dei processi a intervalli di 5 secondi

- Rete  
  - Elenco di schede di rete con schede statistiche
  - Tabella di routing di rete
  - Porte aperte e stato

- Archiviazione
  - Elenco Blocca dispositivi
  - Elenco partizioni
  - Elenco punti di montaggio
  - Informazioni sul volume MDADM
  - Informazioni sul volume LVM
  - Acquisizione della profilatura su tutti i dischi a intervalli di 5 secondi

- Log
  - /var/log/messages
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/Azure/[cartella estensioni]/\*log\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - Output di journalctl per gli ultimi cinque giorni

- [Metadati dell'istanza di macchina virtuale di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] Le informazioni PCI non sono ancora state raccolte nelle distribuzioni di Debian e SLES

## <a name="run-the-perfinsights-linux-on-your-vm"></a>Eseguire PerfInsights Linux nella macchina virtuale

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Cosa è necessario sapere prima di eseguire lo strumento

#### <a name="tool-requirements"></a>Requisiti dello strumento

- Questo strumento deve essere eseguito nella macchina virtuale in cui si è verificato il problema di prestazioni.
- Python 2,7 deve essere installato nella macchina virtuale

- Attualmente sono supportate le distribuzioni seguenti:

    | Distribuzione               | Version                                         |
    |----------------------------|-------------------------------------------------|
    | Server Oracle Linux        | 6,10 [`*`], 7,3, 7,6, 7,5 (Oracle-database-EE 13,8 Marketplace image)|
    | CentOS                     | 6,5 [`*`], 7,6                                    |
    | RHEL                       | 7,2, 7,5, 8,0 [`*`]                               |
    | Ubuntu                     | 14.04, 16.04, 18.04                               |
    | Debian                     | 8, 9, 10 [`*`]                                    |
    | SLES                       | 12 SP4 [`*`]                                      |
    |                            |                                                   |

>[!Note]
>[`*`] Consultare la sezione [problemi noti](#known-issues)

### <a name="known-issues"></a>Problemi noti

- In RHEL 8 non è installato Python per impostazione predefinita. Per eseguire PerfInsights Linux, è necessario innanzitutto installare Python 2,7

- La raccolta di informazioni sull'agente guest potrebbe non riuscire in CentOS 6. x

- Le informazioni sui dispositivi PCI non vengono raccolte nelle distribuzioni basate su Debian

- Le informazioni LVM sono raccolte parzialmente in alcune distribuzioni

### <a name="how-do-i-run-perfinsights"></a>Ricerca per categorie eseguire PerfInsights

È possibile eseguire PerfInsights in una macchina virtuale installando diagnostica prestazioni di Azure da portale di Azure. È anche possibile eseguirlo come strumento autonomo.

>[!Note]
>PerfInsights raccoglie e analizza semplicemente i dati. Non vengono apportate modifiche al sistema.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>Installare ed eseguire PerfInsights dalla portale di Azure

Per altre informazioni su questa opzione, vedere [diagnostica delle prestazioni di Azure](performance-diagnostics.md).  

#### <a name="run-perfinsights-in-standalone-mode"></a>Eseguire PerfInsights in modalità autonoma

Per eseguire lo strumento PerfInsights, seguire questa procedura:

1. Scaricare [PerfInsights. tar. gz](https://aka.ms/perfinsightslinuxdownload) in una cartella della macchina virtuale ed estrarre il contenuto usando i comandi seguenti dal terminale.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Passare alla cartella che contiene `perfinsights.py` il file e quindi eseguire `perfinsights.py` per visualizzare i parametri della riga di comando disponibili.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Screenshot dell'output della riga di comando di PerfInsights Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    La sintassi di base per eseguire gli scenari di PerfInsights è la seguente:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    È possibile usare l'esempio seguente per eseguire lo scenario di analisi rapida delle prestazioni per 1 minuto e creare i risultati nella cartella/tmp/output:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    È possibile usare l'esempio seguente per eseguire lo scenario di analisi delle prestazioni per 5 minuti e caricare il risultato tar ball nell'account di archiviazione:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >Prima di eseguire uno scenario, PerfInsights chiede all'utente di acconsentire a condividere le informazioni di diagnostica e di accettare il contratto di licenza. Utilizzare **l'opzione-a o--Accept-Disclaimer-and-share-Diagnostics** per ignorare queste richieste.
    >
    >Se si dispone di un ticket di supporto attivo con Microsoft e si esegue PerfInsights in base alla richiesta del tecnico del supporto che si sta utilizzando, assicurarsi di specificare il numero di ticket di supporto utilizzando l'opzione **-s o--Support-Request** .

Al termine dell'esecuzione, un nuovo file tar viene visualizzato nella stessa cartella di PerfInsights, a meno che non venga specificata alcuna cartella di output. Il nome del file è **PerformanceDiagnostics\_aaaa-mm\_-GG fff. tar. gz.** È possibile inviare questo file all'agente di supporto per l'analisi o aprire il report all'interno del file per esaminare i risultati e le raccomandazioni.

## <a name="review-the-diagnostics-report"></a>Esaminare il rapporto di diagnostica

Nel file **PerformanceDiagnostics\_aaaa-mm-gg\_fff. tar. gz** è possibile trovare un report HTML che detaili i risultati di PerfInsights. Per esaminare il report, espandere il **file\_PerformanceDiagnostics aaaa-mm-\_GG fff. tar. gz** , quindi aprire il file **PerfInsights report. html** .

### <a name="overview-tab"></a>Scheda Panoramica

La scheda **Panoramica** fornisce i dettagli dell'esecuzione di base e le informazioni sulla macchina virtuale. La scheda **risultati** Visualizza un riepilogo delle indicazioni di tutte le diverse sezioni del report PerfInsights.

![Screenshot del report PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Screenshot del report PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> I risultati classificati come alti sono problemi noti che possono causare problemi di prestazioni. I risultati classificati come medi rappresentano configurazioni non ottimali che non necessariamente causano problemi di prestazioni. I risultati classificati come bassi sono solo istruzioni informative.

Esaminare gli elementi consigliati e i collegamenti per tutti i risultati alti e medi. Informazioni su come questi possono influenzare le prestazioni e sulle procedure consigliate per configurazioni ottimizzate per le prestazioni.

### <a name="cpu-tab"></a>Scheda CPU

Scheda **CPU** fornisce informazioni sull'utilizzo della CPU a livello di sistema durante l'esecuzione del PerfInsights. Le informazioni sui periodi di utilizzo elevato della CPU e sui consumer di CPU con esecuzione prolungata possono essere utili per risolvere i problemi relativi alla CPU elevata.

![Screenshot della scheda CPU del report PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Scheda Archiviazione

Nella sezione **Risultati** vengono visualizzate i risultati e le raccomandazioni inerenti all'archiviazione.

I **dispositivi a blocchi** e altre sezioni correlate, ad esempio le schede **partizioni**, **LVM**e **mdadm** , descrivono in che modo i dispositivi a blocchi sono configurati e correlati tra loro.

![Screenshot della scheda archiviazione](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![Screenshot della scheda MDADM](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Scheda Linux

La scheda **Linux** contiene informazioni sull'hardware e sul sistema operativo in esecuzione nella macchina virtuale. I dettagli includono un elenco dei processi in esecuzione e le informazioni su agenti Guest, PCI, CPU, driver e driver LIS.

![Screenshot della scheda Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>Passaggi successivi

È possibile caricare i log di diagnostica e i report nel supporto tecnico Microsoft per eseguire altre analisi. Quando si lavora con il personale di supporto tecnico Microsoft, è possibile che venga richiesto di trasmettere l'output generato da PerfInsights per facilitare il processo di risoluzione dei problemi.

Lo screenshot seguente mostra un messaggio simile a quello che l'utente potrebbe ricevere:

![Screenshot del messaggio di esempio del supporto tecnico Microsoft](media/how-to-use-perfinsights-linux/support-email.png)

Seguire le istruzioni nel messaggio per accedere all'area di lavoro di trasferimento del file. Per maggiore sicurezza, l'utente dovrà cambiare la password al primo uso.

Dopo aver eseguito l'accesso, si troverà una finestra di dialogo per caricare il file **PerformanceDiagnostics\_aaaa-mm\_-GG fff. tar. gz** raccolto da PerfInsights.
