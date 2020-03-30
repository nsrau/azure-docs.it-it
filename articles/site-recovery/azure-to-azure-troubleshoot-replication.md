---
title: Risolvere i problemi di replica delle macchine virtuali di Azure con Azure Site RecoveryTroubleshoot replication of Azure VMs with Azure Site Recovery
description: Risolvere i problemi di replica nel ripristino di emergenza della macchina virtuale di Azure con Azure Site RecoveryTroubleshoot replication in Azure VM disaster recovery with Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.openlocfilehash: 67b68cc8a1db4a058675dc51fb3805093c455908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276666"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Risolvere i problemi di replica nel ripristino di emergenza della macchina virtuale di AzureTroubleshoot replication in Azure VM disaster recovery

L'articolo descrive i problemi comuni di Azure Site Recovery, quando si esegue la replica e il ripristino delle macchine virtuali di Azure da un'area a un'altra area. Viene inoltre illustrato come risolvere i problemi comuni. Per altre informazioni sulle configurazioni supportate, vedere la [matrice di supporto per la replica delle VM di Azure](site-recovery-support-matrix-azure-to-azure.md).

Azure Site Recovery replica in modo coerente i dati dall'area di origine all'area di ripristino di emergenza. Crea inoltre un punto di ripristino coerente con l'arresto anomalo del sistema ogni 5 minuti. Se non è possibile creare punti di ripristino da Site Recovery per 60 minuti, l'utente viene avvisato con queste informazioni:

Messaggio di errore: "Nessun punto di ripristino coerente con l'arresto anomalo del sistema disponibile per la macchina virtuale negli ultimi 60 minuti".</br>
ID errore: 153007

Le sezioni seguenti descrivono cause e soluzioni.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a><a name="high-data-change-rate-on-the-source-virtal-machine"></a>Elevata frequenza di modifica dei dati nella macchina virtuale di origine

Azure Site Recovery crea un evento se la frequenza di modifica dei dati nella macchina virtuale di origine è superiore ai limiti supportati. Per verificare se il problema è dovuto all'elevata varianza, passare a Eventi > **VM** >  **elementi replicati****- ultime 72 ore**.
Dovresti vedere l'evento "Frequenza di modifica dei dati oltre i limiti supportati":

![Pagina Di Azure Site Recovery che mostra una frequenza di modifica dei dati elevata troppo elevataAzure Site Recovery page that shows a high data change rate that is too high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Se si seleziona l'evento, si dovrebbero vedere le informazioni esatte del disco:

![Pagina che mostra i dettagli dell'evento sulla frequenza di modifica dei dati](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)

### <a name="azure-site-recovery-limits"></a>Limiti di Azure Site Recovery

La tabella seguente indica i limiti di Azure Site Recovery. Questi limiti sono basati sui test, ma non sono in grado di coprire tutte le possibili combinazioni I/O (Input-Output) dell'applicazione. I risultati effettivi possono variare in base alla combinazione di I/O delle applicazioni.

Esistono due limiti da considerare: la varianza dei dati per disco e la varianza dei dati per macchina virtuale. Esaminiamo il disco Premium P20 nella tabella seguente per un esempio. Per una singola macchina virtuale, Site Recovery è in grado di gestire 5 MB/s di varianza per disco con un massimo di cinque dischi di questo tipo. Ripristino del sito ha un limite di 25 MB/s di varianza totale per macchina virtuale.

**Destinazione di archiviazione della replica** | **Dimensione media I/O per disco di origine** |**Varianza media dei dati per disco di origine** | **Varianza totale giornaliera dei dati per disco di origine**
---|---|---|---
Archiviazione standard | 8 KB    | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 8 KB    | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 16 KB | 4 MB/s |    336 GB per disco
Disco P10 o P15 Premium | 32 KB o superiori | 8 MB/s | 672 GB per disco
Disco P20, P30, P40 o P50 Premium | 8 KB    | 5 MB/s | 421 GB per disco
Disco P20, P30, P40 o P50 Premium | 16 KB o superiori |20 MB/s | 1684 GB per disco

### <a name="solution"></a>Soluzione

Azure Site Recovery ha limiti sulla frequenza di modifica dei dati, a seconda del tipo di disco. Per verificare se il problema è ricorrente o temporaneo, individuare la frequenza di modifica dei dati della macchina virtuale interessata. Andare alla macchina virtuale di origine, trovare le metriche in **Monitoraggio**, quindi aggiungere le metriche, come mostra questo screenshot:

![Pagina che mostra il processo in tre fasi per trovare la frequenza di modifica dei dati](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Selezionare **Aggiungi metrica** e aggiungere **Byte scritti su disco/sec per un disco del sistema operativo** e **Byte scritti su disco/sec per un disco dati**.
1. Monitorare il picco come mostrato nello screenshot.
1. Visualizzare il numero totale di operazioni di scrittura eseguite sui dischi del sistema operativo e in tutti i dischi dati combinati. Queste metriche potrebbero non fornire informazioni a livello di dischi singoli, ma indicano il modello di varianza totale dei dati.

Un picco nella frequenza di modifica dei dati potrebbe provenire da un burst di dati occasionale. Se la frequenza di modifica dei dati è maggiore di 10 MB/s (per Premium) o 2 MB/s (per Standard) e si arresta, la replica raggiungerà il ritardo. Se la varianza è costantemente ben oltre il limite supportato, prendere in considerazione una di queste opzioni:

- Escludere il disco che causa un'elevata frequenza di modifica dei dati: in primo luogo, disabilitare la replica. È quindi possibile escludere il disco utilizzando [PowerShell](./azure-to-azure-exclude-disks.md).
- Modificare il livello del disco di archiviazione di ripristino di emergenza: questa opzione è possibile solo se la varianza dei dati del disco è inferiore a 20 MB/s. Si supponga che una macchina virtuale con un disco P10 abbia una varianza di dati superiore a 8 MB/s ma inferiore a 10 MB/s. Se il cliente può usare il disco P30 per l'archiviazione di destinazione durante la protezione, il problema può essere risolto. Questa soluzione è possibile solo per i computer che utilizzano dischi gestiti da Premium.This solution is only possible for machines that are using Premium-Managed Disks. A tale scopo, seguire questa procedura:

    1. Passare a **Dischi** del computer replicato interessato e copiare il nome del disco di replica.
    1. Passare a questa replica del disco gestito.
    1. È possibile che venga visualizzato un banner in **Panoramica** che indica che è stato generato un URL di chiamata in stato di condiviso. Selezionare questo banner e annullare l'esportazione. Ignorare questo passaggio se il banner non viene visualizzato.
    1. Non appena l'URL di accesso condiviso viene revocato, passare a **Configurazione** per il disco gestito. Aumentare le dimensioni in modo che Site Recovery supporti la frequenza di varianza osservata sul disco di origine.

## <a name="network-connectivity-problems"></a><a name="Network-connectivity-problem"></a>Problemi di connettività di rete

### <a name="network-latency-to-a-cache-storage-account"></a>Latenza di rete per l'account di archiviazione della cache

Site Recovery invia i dati replicati all'account di archiviazione della cache. Se il caricamento dei dati da una macchina virtuale all'account di archiviazione della cache è più lento di 4 MB in 3 secondi, potrebbe verificarsi una latenza di rete.

Per verificare la presenza di un problema relativo alla latenza, utilizzare [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy). È possibile usare questa utilità della riga di comando per caricare i dati dalla macchina virtuale all'account di archiviazione della cache. Se la latenza è elevata, verificare se si usa un'appliance virtuale di rete (NVA) per controllare il traffico di rete in uscita dalle macchine virtuali. Se tutto il traffico di replica passa attraverso un'appliance virtuale di rete, l'appliance può subire limitazioni.

È consigliabile creare un endpoint del servizio di rete nella rete virtuale per "Archiviazione", in modo che il traffico di replica non venga indirizzato all'appliance virtuale di rete. Per altre informazioni, vedere [Configurazione dell'appliance di rete virtuale](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Connettività di rete

Affinché la replica di Site Recovery funzioni, è necessaria la macchina virtuale per fornire la connettività in uscita a URL o intervalli IP specifici. È possibile avere la macchina virtuale dietro un firewall o usare le regole del gruppo di sicurezza di rete (NSG) per controllare la connettività in uscita. In tal caso, potrebbero verificarsi problemi. Vedere [Connettività in uscita per gli URL di Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) per assicurarsi che tutti gli URL siano connessi.

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>ID errore 153006 - Nessun punto di ripristino coerente dell'app disponibile per la macchina virtuale negli ultimi minuti "X"

Di seguito sono riportati alcuni dei problemi più comuni.

#### <a name="known-issue-in-sql-server-20082008-r2"></a>Problema noto in SQL Server 2008/2008 R2

**Come risolvere:** C'è un problema noto con SQL Server 2008/2008 R2. Fare riferimento all'articolo Agente di ripristino siti di [Azure o altro backup VSS non componente ha esito negativo per un server che ospita SQL Server 2008 R2.](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>I processi di Azure Site Recovery non riescono nei server che ospitano qualsiasi versione di istanze di SQL Server con database di AUTO_CLOSE

Come risolvere il **problema:** fare riferimento all'articolo [Backup VSS non componenti, ad esempio processi di Azure Site Recovery, che non riescono nei server che ospitano istanze](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)di SQL Server con AUTO_CLOSE db.


#### <a name="known-issue-in-sql-server-2016-and-2017"></a>Problema noto in SQL Server 2016 e 2017

**Come risolvere**: Fare riferimento all'articolo Errore si verifica quando si esegue il backup di [una macchina virtuale con backup non basato su componenti in SQL Server 2016 e 2017](https://support.microsoft.com/en-us/help/4508218/cumulative-update-16-for-sql-server-2017).

#### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Si usa la configurazione diretta degli spazi di archiviazione di AzureYou're using Azure Storage Spaces Direct Configuration

**Come risolvere:** Azure Site Recovery non è in grado di creare un punto di ripristino coerente dell'applicazione per la configurazione diretta degli spazi di archiviazione. [Configurare i criteri di replica](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms).

### <a name="more-causes-because-of-vss-related-issues"></a>Altre cause a causa di problemi relativi a VSS:

Per risolvere ulteriormente, controllare i file nel computer di origine per ottenere il codice di errore esatto per l'errore:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Per individuare gli errori nel file, cercare la stringa "vacpError" aprendo il file vacp.log in un editor.

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

Nell'esempio precedente, **2147754994** è il codice di errore che indica l'errore successivo a questa frase.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>Il writer VSS non è installato - Errore 2147221164

**Come risolvere:** per generare tag di coerenza dell'applicazione, Azure Site Recovery usa il servizio Copia Shadow del volume (VSS). Site Recovery installa un provider VSS per il proprio funzionamento per creare snapshot di coerenza dell'app. Azure Site Recovery installa questo provider VSS come servizio. Se il provider VSS non è installato, la creazione dello snapshot di coerenza dell'applicazione ha esito negativo. Mostra l'ID errore 0x80040154 "Classe non registrata". Fare riferimento all'articolo per la risoluzione dei problemi di [installazione del writer VSS](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures).

#### <a name="vss-writer-is-disabled---error-2147943458"></a>Il writer VSS è disabilitato - Errore 2147943458

**Come risolvere:** per generare il tag di coerenza dell'applicazione, Azure Site Recovery usa VSS. Site Recovery installa un provider VSS per il proprio funzionamento per creare snapshot di coerenza dell'app. Questo provider VSS viene installato come servizio. Se il servizio provider VSS non è abilitato, la creazione dello snapshot di coerenza dell'applicazione ha esito negativo. Viene visualizzato l'errore "il servizio specificato è disabilitato e non può essere avviato (0x80070422)".

Se VSS è disabilitato:

- Verificare che il tipo di avvio del servizio provider VSS sia impostato su **Automatico**.
- Riavviare i seguenti servizi:
   - Servizio VSS
   - Provider VSS di Azure Site Recovery
   - Servizio VDS

#### <a name="vss-provider-not_registered---error-2147754756"></a>NOT_REGISTERED PROVIDER VSS - Errore 2147754756

**Come risolvere:** per generare il tag di coerenza dell'applicazione, Azure Site Recovery usa VSS. Verificare se il servizio Provider VSS di Azure Site Recovery è installato.

Utilizzare i seguenti comandi per reinstallare il provider VSS:
1. Disinstallare il provider esistente: C: (x86) Programmi (x86) Microsoft Azure Site Recovery agent InMageVSSProvider_Uninstall.cmd
1. Reinstallare il provider VSS: C: .NET Framework (x86) File di programma (x86) Microsoft Azure Site Recovery, agente InMageVSSProvider_Install.cmd

Verificare che il tipo di avvio del servizio provider VSS sia impostato su **Automatico**.

Riavviare i seguenti servizi:
- Servizio VSS
- Provider VSS di Azure Site Recovery
- Servizio VDS
