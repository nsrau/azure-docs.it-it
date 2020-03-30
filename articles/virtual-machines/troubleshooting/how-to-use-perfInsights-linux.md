---
title: Come usare PerfInsights Linux in Microsoft Azure Documenti Microsoft
description: Informazioni su come usare PerfInsights per risolvere i problemi di prestazioni delle macchine virtuali Linux.Learns how to use PerfInsights to troubleshoot Linux VM performance problems.
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: 19b2fcaed2c80d4ca52ada9f9f0898479e73bcf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266988"
---
# <a name="how-to-use-perfinsights"></a>Come usare PerfInsights

[PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) è uno strumento di diagnostica self-help che raccoglie e analizza i dati di diagnostica e fornisce un report per risolvere i problemi di prestazioni delle macchine virtuali Linux in Azure.PerfInsights Linux is a self-help diagnostics tool that collects and analyzes the diagnostic data, and provides a report to help troubleshoot Linux virtual machine performance problems in Azure. PerfInsights può essere eseguito nelle macchine virtuali supportate come strumento autonomo o direttamente dal portale usando Diagnostica prestazioni per le macchine virtuali di [Azure.](performance-diagnostics.md)

Se si verificano problemi di prestazioni delle macchine virtuali, eseguire lo strumento prima di contattare l'assistenza.

## <a name="supported-troubleshooting-scenarios"></a>Scenari per la risoluzione dei problemi supportati

PerfInsights può raccogliere e analizzare diversi tipi di informazioni. Le sezioni seguenti trattano scenari comuni.

### <a name="quick-performance-analysis"></a>Analisi rapida delle prestazioni

Questo scenario raccoglie informazioni di base, ad esempio l'archiviazione e la configurazione hardware della macchina virtuale, vari log, tra cui:This scenario collects basic information such as storage and hardware configuration of your virtual machine, various logs, including:

- Informazioni sul sistema operativo

- Informazioni sul dispositivo PCI

- Registri generali del sistema operativo guest

- File di configurazione

- Informazioni sull'archiviazione

- Configurazione della macchina virtuale di Azure (raccolta tramite il [servizio metadati dell'istanza di Azure)](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

- Elenco dei processi in esecuzione, disco, memoria e utilizzo della CPU

- Informazioni di rete

Si tratta di una raccolta passiva di informazioni che non influirà sul sistema.

>[!Note]
>Lo scenario di analisi rapida delle prestazioni viene incluso automaticamente in ognuno degli scenari seguenti:Quick performance analysis scenario is automatically included in each of the following scenarios:

### <a name="performance-analysis"></a>Analisi delle prestazioni

Questo scenario è simile all'analisi rapida delle prestazioni, ma consente di acquisire informazioni di diagnostica per una durata più lunga.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Che tipo di informazioni vengono raccolte da PerfInsights

Vengono raccolte informazioni sulla macchina virtuale Linux, sul sistema operativo, sui dispositivi a blocchi, sui consumer di risorse elevate, sulla configurazione e su vari log. Di seguito sono disponibili maggiori dettagli:

- Sistema operativo
  - Distribuzione e versione Linux
  - Informazioni sul kernel
  - Informazioni sul conducente

- Hardware
  - Dispositivi PCI`*`[ ]

- Processi e memoria
  - Elenco dei processi (nome attività, memoria utilizzata, file aperti)
  - Memoria fisica totale, disponibile e libera
  - Memoria di scambio totale, disponibile e libera
  - Profilatura dell'acquisizione della CPU e utilizzo della CPU a intervalli di 5 secondi
  - Profiling dell'acquisizione dell'utilizzo della memoria dei processi a intervalli di 5 secondiProfiling capture of processes memory usage at 5-second interval

- Rete  
  - Elenco delle schede di rete con statistiche sulle schede
  - Tabella di routing di rete
  - Porte e stato aperti

- Archiviazione
  - Elenco dei dispositivi bloccati
  - Elenco partizioni
  - Elenco dei punti di montaggio
  - Informazioni sul volume MDADM
  - Informazioni sul volume LVM
  - Profilatura dell'acquisizione su tutti i dischi a intervalli di 5 secondi

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
  - /var/log/azure/[cartella di\*estensione]/ log\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - Uscita del journalctl negli ultimi cinque giorni

- [Metadati dell'istanza della macchina virtuale di AzureAzure virtual machine instance metadata](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] Informazioni PCI non sono ancora raccolte nelle distribuzioni Debian e SLES

## <a name="run-the-perfinsights-linux-on-your-vm"></a>Eseguire il Linux PerfInsights nella macchina virtualeRun the PerfInsights Linux on your VM

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Cosa devo sapere prima di eseguire lo strumento

#### <a name="tool-requirements"></a>Requisiti dello strumento

- Questo strumento deve essere eseguito nella macchina virtuale in cui si è verificato il problema di prestazioni.
- Python 2.7 deve essere installato nella macchina virtuale

- Le distribuzioni seguenti sono attualmente supportate:

    | Distribuzione               | Versione                                         |
    |----------------------------|-------------------------------------------------|
    | Oracle Linux Server        | 6.10`*`[ ], 7.3, 7.6, 7.5 (immagine del marketplace Oracle-Database-Ee 13.8)|
    | CentOS                     | 6.5`*`[ ], 7,6                                    |
    | RHEL                       | 7.2, 7.5, 8.0 [`*`]                               |
    | Ubuntu                     | 14.04, 16.04, 18.04                               |
    | Debian                     | 8, 9, 10 [`*`]                                    |
    | SLES                       | 12 SP4`*`[ ]                                      |
    |                            |                                                   |

>[!Note]
>[`*`] Fare riferimento alla sezione [Problemi noti](#known-issues)

### <a name="known-issues"></a>Problemi noti

- RHEL 8 non dispone di Python installato per impostazione predefinita. Per eseguire PerfInsights Linux, è necessario installare prima Python 2.7

- Raccolta di informazioni sull'agente guest potrebbe non riuscire in CentOS 6.x

- Le informazioni sui dispositivi PCI non vengono raccolte nelle distribuzioni basate su Debian

- Le informazioni LVM vengono parzialmente raccolte su alcune distribuzioni

### <a name="how-do-i-run-perfinsights"></a>Come si esegue PerfInsightsHow do Irun PerfInsights

È possibile eseguire PerfInsights in una macchina virtuale installando Diagnostica prestazioni di Azure dal portale di Azure.You can run PerfInsights on a virtual machine by installing Azure Performance Diagnostics from Azure portal. È anche possibile eseguirlo come strumento autonomo.

>[!Note]
>PerfInsights raccoglie e analizza semplicemente i dati. Non apporta alcuna modifica al sistema.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>Installare ed eseguire PerfInsights dal portale di Azure

Per altre informazioni su questa opzione, vedere Diagnostica prestazioni di Azure.For more information about this option, see [Azure Performance Diagnostics](performance-diagnostics.md).  

#### <a name="run-perfinsights-in-standalone-mode"></a>Eseguire PerfInsights in modalità autonoma

Per eseguire lo strumento PerfInsights, seguire questa procedura:

1. Scaricare [PerfInsights.tar.gz](https://aka.ms/perfinsightslinuxdownload) in una cartella della macchina virtuale ed estrarre il contenuto utilizzando i comandi seguenti dal terminale.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Passare alla cartella `perfinsights.py` che contiene il `perfinsights.py` file e quindi eseguire per visualizzare i parametri della riga di comando disponibili.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Screenshot dell'output della riga di comando Linux PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    La sintassi di base per eseguire gli scenari di PerfInsights è la seguente:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    È possibile utilizzare l'esempio seguente per eseguire uno scenario di analisi rapida delle prestazioni per 1 minuto e creare i risultati nella cartella /tmp/output:You can use the below example to run Quick performance analysis scenario for 1 minute and create the results under /tmp/output folder:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    È possibile usare l'esempio seguente per eseguire lo scenario di analisi delle prestazioni per 5 min e caricare la palla di catrame risultante nell'account di archiviazione:You can use the below example to run performance analysis scenario for 5 mins and upload the result tar ball to the storage account:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >Prima di eseguire uno scenario, PerfInsights chiede all'utente di acconsentire a condividere le informazioni di diagnostica e di accettare il contratto di licenza. Utilizzare **l'opzione -a o --accept-disclaimer-and-share-diagnostics** per ignorare questi prompt.
    >
    >Se si dispone di un ticket di supporto attivo con Microsoft e in esecuzione PerfInsights in base alla richiesta del tecnico del supporto che si sta utilizzando, assicurarsi di fornire il numero del ticket di supporto utilizzando l'opzione **-s o --support-request.**

Al termine dell'esecuzione, un nuovo file tar viene visualizzato nella stessa cartella di PerfInsights, a meno che non venga specificata alcuna cartella di output. Il nome del file è **PerformanceDiagnostics\_\_aaaa-MM-dd hh-mm-ss-fff.tar.gz.** È possibile inviare questo file all'agente di supporto per l'analisi o aprire il report all'interno del file per esaminare i risultati e i suggerimenti.

## <a name="review-the-diagnostics-report"></a>Esaminare il rapporto di diagnostica

All'interno del file **PerformanceDiagnostics\_\_aaaa-MM-dd hh-mm-ss-fff.tar.gz,** è disponibile un report HTML che descrive in dettaglio i risultati di PerfInsights. Per esaminare il report, espandere il file **\_PerformanceDiagnostics\_aaaa-MM-dd hh-mm-ss-fff.tar.gz,** quindi aprire il file **PerfInsights Report.html.**

### <a name="overview-tab"></a>Scheda Panoramica

La scheda **Panoramica** fornisce i dettagli di esecuzione di base e le informazioni sulle macchine virtuali. Nella scheda **Risultati** viene visualizzato un riepilogo dei consigli di tutte le diverse sezioni del report PerfInsights.

![Schermata del report PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Schermata del report PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> I risultati classificati come alti sono problemi noti che possono causare problemi di prestazioni. I risultati classificati come medi rappresentano configurazioni non ottimali che non necessariamente causano problemi di prestazioni. I risultati classificati come bassi sono solo istruzioni informative.

Esaminare gli elementi consigliati e i collegamenti per tutti i risultati alti e medi. Informazioni su come questi possono influenzare le prestazioni e sulle procedure consigliate per configurazioni ottimizzate per le prestazioni.

### <a name="cpu-tab"></a>Scheda CPU

**La** scheda CPU fornisce informazioni sull'utilizzo della CPU a livello di sistema durante l'esecuzione di PerfInsights.CPU tab provides information about system-wide CPU consumption during the PerfInsights run. Le informazioni sui periodi di utilizzo elevato della CPU e sui principali consumer di CPU a esecuzione prolungata saranno utili per risolvere problemi elevati relativi alla CPU.

![Screenshot della scheda CPU del rapporto PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Scheda Archiviazione

Nella sezione **Risultati** vengono visualizzate i risultati e le raccomandazioni inerenti all'archiviazione.

Le schede **Blocca dispositivi** e altre sezioni correlate, ad esempio **Partizioni,** **LVM**e **MDADM,** descrivono come i dispositivi a blocchi sono configurati e correlati tra loro.

![Schermata della scheda archiviazione](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![Screenshot della scheda MDADM](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Scheda Linux

La scheda **Linux** contiene informazioni sull'hardware e sul sistema operativo in esecuzione nella macchina virtuale. I dettagli includono un elenco di processi in esecuzione e informazioni su agente guest, PCI, CPU, driver e driver LIS.

![Screenshot della scheda Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>Passaggi successivi

È possibile caricare i log di diagnostica e i report nel supporto tecnico Microsoft per eseguire altre analisi. Quando si lavora con il personale del supporto Microsoft, potrebbe richiedere la trasmissione dell'output generato da PerfInsights per facilitare il processo di risoluzione dei problemi.

Lo screenshot seguente mostra un messaggio simile a quello che l'utente potrebbe ricevere:

![Screenshot del messaggio di esempio del supporto tecnico Microsoft](media/how-to-use-perfinsights-linux/support-email.png)

Seguire le istruzioni nel messaggio per accedere all'area di lavoro di trasferimento del file. Per maggiore sicurezza, l'utente dovrà cambiare la password al primo uso.

Dopo aver eseguito l'accesso, è possibile trovare una finestra di dialogo per caricare il file **PerformanceDiagnostics\_\_aaaa-MM-dd hh-mm-ss-fff.tar.gz** raccolto da PerfInsights.
