---
title: Risolvere i problemi di replica delle macchine virtuali di Azure con Azure Site RecoveryTroubleshoot replication of Azure VMs with Azure Site Recovery
description: Risolvere i problemi di replica nel ripristino di emergenza della macchina virtuale di Azure con Azure Site RecoveryTroubleshoot replication in Azure VM disaster recovery with Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 04/03/2020
ms.openlocfilehash: 8cba02d3c7d1e649853570b199b646b1c4dcce2d
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667408"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Risolvere i problemi di replica nel ripristino di emergenza della macchina virtuale di AzureTroubleshoot replication in Azure VM disaster recovery

Questo articolo descrive i problemi comuni in Azure Site Recovery durante la replica e il ripristino di macchine virtuali di Azure (VM) da un'area a un'altra. Viene inoltre illustrato come risolvere i problemi comuni. Per altre informazioni sulle configurazioni supportate, vedere la [matrice di supporto per la replica delle VM di Azure](site-recovery-support-matrix-azure-to-azure.md).

Azure Site Recovery replica in modo coerente i dati dall'area di origine all'area di ripristino di emergenza. Crea inoltre un punto di ripristino coerente con l'arresto anomalo del sistema ogni 5 minuti. Se non è possibile creare punti di ripristino da Site Recovery per 60 minuti, l'utente viene avvisato con queste informazioni:

```plaintext
Error message: "No crash consistent recovery point available for the VM in the last 60 minutes."

Error ID: 153007
```

Le sezioni seguenti descrivono cause e soluzioni.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a>Elevata frequenza di modifica dei dati nella macchina virtuale di origine

Azure Site Recovery crea un evento se la frequenza di modifica dei dati nella macchina virtuale di origine è superiore ai limiti supportati. Per verificare se il problema è dovuto all'elevata varianza, passare a Eventi > **VM** >  **elementi replicati****- ultime 72 ore**.
Dovresti vedere la **frequenza**di modifica dei dati dell'evento oltre i limiti supportati :

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png" alt-text="Pagina Di Azure Site Recovery che mostra una frequenza di modifica dei dati elevata troppo elevata.":::

Se si seleziona l'evento, si dovrebbero vedere le informazioni esatte del disco:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png" alt-text="Pagina che mostra i dettagli dell'evento della frequenza di modifica dei dati.":::

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

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/churn.png" alt-text="Pagina che mostra il processo in tre fasi per trovare la frequenza di modifica dei dati.":::

1. Selezionare **Aggiungi metrica** e aggiungere **Byte scritti su disco/sec per un disco del sistema operativo** e **Byte scritti su disco/sec per un disco dati**.
1. Monitorare il picco come mostrato nello screenshot.
1. Visualizzare il numero totale di operazioni di scrittura eseguite sui dischi del sistema operativo e in tutti i dischi dati combinati. Queste metriche potrebbero non fornire informazioni a livello di dischi singoli, ma indicano il modello di varianza totale dei dati.

Un picco nella frequenza di modifica dei dati potrebbe provenire da un burst di dati occasionale. Se la frequenza di modifica dei dati è maggiore di 10 MB/s (per Premium) o 2 MB/s (per Standard) e si arresta, la replica raggiungerà il ritardo. Se la varianza è costantemente ben oltre il limite supportato, prendere in considerazione una di queste opzioni:

- Escludere il disco che causa un'elevata frequenza di modifica dei dati: in primo luogo, disabilitare la replica. È quindi possibile escludere il disco utilizzando [PowerShell](azure-to-azure-exclude-disks.md).
- Modificare il livello del disco di archiviazione di ripristino di emergenza: questa opzione è possibile solo se la varianza dei dati del disco è inferiore a 20 MB/s. Ad esempio, una macchina virtuale con un disco P10 ha una varianza di dati maggiore di 8 MB/s ma inferiore a 10 MB/s. Se il cliente può usare il disco P30 per l'archiviazione di destinazione durante la protezione, il problema può essere risolto. Questa soluzione è possibile solo per i computer che utilizzano dischi gestiti da Premium.This solution is only possible for machines that are using Premium-Managed Disks. A tale scopo, seguire questa procedura:

  1. Passare a **Dischi** del computer replicato interessato e copiare il nome del disco di replica.
  1. Passare a questa replica del disco gestito.
  1. È possibile che venga visualizzato un banner in **Panoramica** che indica che è stato generato un URL di chiamata in stato di condiviso. Selezionare questo banner e annullare l'esportazione. Ignorare questo passaggio se il banner non viene visualizzato.
  1. Non appena l'URL di accesso condiviso viene revocato, passare a **Configurazione** per il disco gestito. Aumentare le dimensioni in modo che Site Recovery supporti la frequenza di varianza osservata sul disco di origine.

## <a name="network-connectivity-problems"></a>Problemi di connettività di rete

### <a name="network-latency-to-a-cache-storage-account"></a>Latenza di rete per l'account di archiviazione della cache

Site Recovery invia i dati replicati all'account di archiviazione della cache. Se il caricamento dei dati da una macchina virtuale all'account di archiviazione della cache è più lento di 4 MB in 3 secondi, potrebbe verificarsi una latenza di rete.

Per verificare la presenza di un problema relativo alla latenza, utilizzare [AzCopy](/azure/storage/common/storage-use-azcopy). È possibile usare questa utilità della riga di comando per caricare i dati dalla macchina virtuale all'account di archiviazione della cache. Se la latenza è elevata, verificare se si usa un'appliance virtuale di rete (NVA) per controllare il traffico di rete in uscita dalle macchine virtuali. Se tutto il traffico di replica passa attraverso un'appliance virtuale di rete, l'appliance può subire limitazioni.

È consigliabile creare un endpoint del servizio di rete nella rete virtuale per "Archiviazione", in modo che il traffico di replica non venga indirizzato all'appliance virtuale di rete. Per altre informazioni, vedere [Configurazione dell'appliance di rete virtuale](azure-to-azure-about-networking.md#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Connettività di rete

Affinché la replica di Site Recovery funzioni, è necessaria la macchina virtuale per fornire la connettività in uscita a URL o intervalli IP specifici. È possibile avere la macchina virtuale dietro un firewall o usare le regole del gruppo di sicurezza di rete (NSG) per controllare la connettività in uscita. In tal caso, potrebbero verificarsi problemi. Per assicurarsi che tutti gli URL siano connessi, vedere [Connettività in uscita per gli URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>ID errore 153006 - Nessun punto di ripristino coerente dell'app disponibile per la macchina virtuale negli ultimi minuti "X"

Di seguito sono riportati alcuni dei problemi più comuni.

### <a name="known-issue-in-sql-server-20082008-r2"></a>Problema noto in SQL Server 2008/2008 R2

**Come risolvere:** C'è un problema noto con SQL Server 2008/2008 R2. Fare riferimento all'articolo Agente di ripristino siti di [Azure o altro backup VSS non componente ha esito negativo per un server che ospita SQL Server 2008 R2.](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>I processi di Azure Site Recovery non riescono nei server che ospitano qualsiasi versione di istanze di SQL Server con database di AUTO_CLOSE

**Come risolvere:** Fare riferimento all'articolo I backup VSS non componenti, ad esempio i processi di [Azure Site Recovery, hanno esito negativo nei server che ospitano istanze](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)di SQL Server con AUTO_CLOSE dB .

### <a name="known-issue-in-sql-server-2016-and-2017"></a>Problema noto in SQL Server 2016 e 2017

**Come risolvere**il problema : Fare riferimento all'articolo [aggiornamento cumulativo 16 per SQL Server 2017](https://support.microsoft.com/help/4508218/cumulative-update-16-for-sql-server-2017).

### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Si usa la configurazione diretta degli spazi di archiviazione di AzureYou're using Azure Storage Spaces Direct Configuration

**Come risolvere:** Azure Site Recovery non è in grado di creare un punto di ripristino coerente dell'applicazione per la configurazione diretta degli spazi di archiviazione. [Configurare i criteri di replica](azure-to-azure-how-to-enable-replication-s2d-vms.md).

### <a name="more-causes-because-of-vss-related-issues"></a>Altre cause a causa di problemi relativi a VSS:

Per risolvere ulteriormente, controllare i file nel computer di origine per ottenere il codice di errore esatto per l'errore:

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log`

Per individuare gli errori, aprire il file _vacp.log_ in un editor di testo cercare la stringa **vacpError**.

```plaintext
Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|
```

Nell'esempio precedente, **2147754994** è il codice di errore che indica l'errore successivo a questa frase.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>Il writer VSS non è installato - Errore 2147221164

**Come risolvere:** per generare tag di coerenza dell'applicazione, Azure Site Recovery usa il servizio Copia Shadow del volume (VSS). Site Recovery installa un provider VSS per il proprio funzionamento per creare snapshot di coerenza dell'app. Azure Site Recovery installa questo provider VSS come servizio. Se il provider VSS non è installato, la creazione dello snapshot di coerenza dell'applicazione ha esito negativo. Mostra **l'ID errore 0x80040154 Classe non registrata**. Fare riferimento all'articolo per la risoluzione dei problemi di [installazione del writer VSS](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

#### <a name="vss-writer-is-disabled---error-2147943458"></a>Il writer VSS è disabilitato - Errore 2147943458

**Come risolvere:** per generare il tag di coerenza dell'applicazione, Azure Site Recovery usa VSS. Site Recovery installa un provider VSS per il proprio funzionamento per creare snapshot di coerenza dell'app. Questo provider VSS viene installato come servizio. Se il servizio provider VSS non è abilitato, la creazione dello snapshot di coerenza dell'applicazione ha esito negativo. Viene visualizzato l'errore: **il servizio specificato è disabilitato e non può essere avviato (0x80070422)**.

Se VSS è disabilitato:

- Verificare che il tipo di avvio del servizio provider VSS sia impostato su **Automatico**.
- Riavviare i seguenti servizi:
  - Servizio VSS.
  - Azure Site Recovery VSS Provider.
  - servizio VDS.

#### <a name="vss-provider-not_registered---error-2147754756"></a>NOT_REGISTERED PROVIDER VSS - Errore 2147754756

**Come risolvere:** per generare il tag di coerenza dell'applicazione, Azure Site Recovery usa VSS. Verificare se il servizio Provider VSS di Azure Site Recovery è installato.

Utilizzare i seguenti comandi per reinstallare il provider VSS:

1. Disinstallare il provider esistente:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd"`

1. Reinstallare il provider VSS:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Verificare che il tipo di avvio del servizio provider VSS sia impostato su **Automatico**.

Riavviare i seguenti servizi:

- Servizio VSS.
- Azure Site Recovery VSS Provider.
- servizio VDS.
