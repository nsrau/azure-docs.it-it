---
title: Risolvere i problemi di replica delle VM di Azure con Azure Site Recovery
description: Risolvere i problemi di replica nel ripristino di emergenza delle macchine virtuali di Azure con Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.openlocfilehash: e5e52c6e8560c7369054cfc9fcf2ba4c405671e0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190804"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Risolvere i problemi di replica in Azure VM Recovery

L'articolo descrive i problemi comuni di Azure Site Recovery, quando si esegue la replica e il ripristino delle macchine virtuali di Azure da un'area a un'altra area. Viene inoltre illustrato come risolvere i problemi comuni. Per altre informazioni sulle configurazioni supportate, vedere la [matrice di supporto per la replica delle VM di Azure](site-recovery-support-matrix-azure-to-azure.md).

Azure Site Recovery replica costantemente i dati dall'area di origine all'area di ripristino di emergenza. Viene inoltre creato un punto di ripristino coerente con l'arresto anomalo del sistema ogni 5 minuti. Se non è possibile creare punti di ripristino da Site Recovery per 60 minuti, l'utente viene avvisato con queste informazioni:

Messaggio di errore: "nessun punto di ripristino coerente con l'arresto anomalo del sistema disponibile per la macchina virtuale negli ultimi 60 minuti".</br>
ID errore: 153007

Le sezioni seguenti descrivono cause e soluzioni.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Elevata frequenza di modifica dei dati nella macchina virtuale di origine

Azure Site Recovery crea un evento se la frequenza di modifica dei dati nella macchina virtuale di origine è superiore ai limiti supportati. Per verificare se il problema è dovuto a una varianza elevata, passare a **elementi replicati** > **VM** > **eventi-ultime 72 ore**.
Dovrebbe essere visualizzato l'evento "frequenza di modifica dei dati superiore ai limiti supportati":

![Azure Site Recovery pagina che mostra una frequenza di modifica dei dati elevata troppo elevata](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Se si seleziona l'evento, si dovrebbero vedere le informazioni esatte del disco:

![Pagina che mostra i dettagli dell'evento della frequenza di modifica dei dati](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)

### <a name="azure-site-recovery-limits"></a>Limiti di Azure Site Recovery

La tabella seguente indica i limiti di Azure Site Recovery. Questi limiti sono basati sui test, ma non possono coprire tutte le possibili combinazioni di input/output (I/O) delle applicazioni. I risultati effettivi possono variare in base alla combinazione di I/O delle applicazioni.

Esistono due limiti da considerare: la varianza dei dati per disco e la varianza dei dati per ogni macchina virtuale. Viene ora esaminato il disco P20 Premium nella tabella seguente per un esempio. Per una singola macchina virtuale, Site Recovery possibile gestire 5 MB/s di varianza per disco con un massimo di cinque dischi di questo tipo. Site Recovery ha un limite di 25 MB/s di varianza totale per macchina virtuale.

**Destinazione archiviazione di replica** | **Dimensione media I/O per disco di origine** |**Varianza media dei dati per disco di origine** | **Varianza totale giornaliera dei dati per disco di origine**
---|---|---|---
Archiviazione standard | 8 KB | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 8 KB  | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 16 KB | 4 MB/s |  336 GB per disco
Disco P10 o P15 Premium | 32 KB o superiori | 8 MB/s | 672 GB per disco
Disco P20, P30, P40 o P50 Premium | 8 KB    | 5 MB/s | 421 GB per disco
Disco P20, P30, P40 o P50 Premium | 16 KB o superiori |10 MB/s | 842 GB per disco

### <a name="solution"></a>Soluzione

Azure Site Recovery presenta limiti per la frequenza di modifica dei dati, a seconda del tipo di disco. Per verificare se il problema è ricorrente o temporaneo, individuare la frequenza di modifica dei dati della macchina virtuale interessata. Andare alla macchina virtuale di origine, trovare le metriche in **Monitoraggio**, quindi aggiungere le metriche, come mostra questo screenshot:

![Pagina in cui viene illustrato il processo in tre passaggi per la ricerca della frequenza di modifica dei dati](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Selezionare **Aggiungi metrica** e aggiungere **Byte scritti su disco/sec per un disco del sistema operativo** e **Byte scritti su disco/sec per un disco dati**.
1. Monitorare il picco come mostrato nello screenshot.
1. Visualizzare il numero totale di operazioni di scrittura eseguite sui dischi del sistema operativo e in tutti i dischi dati combinati. Queste metriche potrebbero non fornire informazioni a livello di dischi singoli, ma indicano il modello di varianza totale dei dati.

Un picco della frequenza di modifica dei dati potrebbe provenire da un improvviso aumento dei dati. Se la frequenza di modifica dei dati è maggiore di 10 MB/s (per Premium) o 2 MB/s (per il livello standard) e si arresta, la replica si aggiornerà. Se la varianza è in modo coerente oltre il limite supportato, prendere in considerazione una delle opzioni seguenti:

- Escludere il disco che causa un elevato tasso di modifica dei dati: prima di tutto disabilitare la replica. È quindi possibile escludere il disco usando [PowerShell](./azure-to-azure-exclude-disks.md).
- Modificare il livello del disco di archiviazione per il ripristino di emergenza: questa opzione è possibile solo se la varianza dei dati del disco è inferiore a 20 MB/s. Supponiamo che una macchina virtuale con un disco P10 disponga di una varianza di dati superiore a 8 MB/s ma minore di 10 MB/s. Se il cliente può usare il disco P30 per l'archiviazione di destinazione durante la protezione, il problema può essere risolto. Questa soluzione è possibile solo per i computer che usano Managed Disks Premium. A tale scopo, seguire questa procedura:

    1. Passare a **dischi** del computer replicato interessato e copiare il nome del disco di replica.
    1. Passare a questa replica del disco gestito.
    1. È possibile che venga visualizzato un banner in **Panoramica** che indica che è stato generato un URL di firma di accesso condiviso. Selezionare questo banner e annullare l'esportazione. Ignorare questo passaggio se il banner non viene visualizzato.
    1. Non appena viene revocato l'URL di firma di accesso condiviso, passare a **configurazione** per il disco gestito. Aumentare le dimensioni in modo che Site Recovery supporti la percentuale di varianza osservata sul disco di origine.

## <a name="Network-connectivity-problem"></a>Problemi di connettività di rete

### <a name="network-latency-to-a-cache-storage-account"></a>Latenza di rete per l'account di archiviazione della cache

Site Recovery invia i dati replicati all'account di archiviazione della cache. È possibile che si verifichi una latenza di rete se il caricamento dei dati da una macchina virtuale nell'account di archiviazione della cache è più lento di 4 MB in 3 secondi.

Per verificare la presenza di un problema relativo alla latenza, usare [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy). È possibile usare questa utilità da riga di comando per caricare i dati dalla macchina virtuale nell'account di archiviazione della cache. Se la latenza è elevata, controllare se si sta usando un'appliance virtuale di rete per controllare il traffico di rete in uscita dalle macchine virtuali. Se tutto il traffico di replica passa attraverso un'appliance virtuale di rete, l'appliance può subire limitazioni.

È consigliabile creare un endpoint del servizio di rete nella rete virtuale per "Archiviazione", in modo che il traffico di replica non venga indirizzato all'appliance virtuale di rete. Per altre informazioni, vedere [Configurazione dell'appliance di rete virtuale](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Connettività di rete

Per il corretto funzionamento della replica Site Recovery, è necessario che la macchina virtuale fornisca la connettività in uscita a URL o intervalli IP specifici. Per controllare la connettività in uscita, è possibile che la macchina virtuale sia dietro a un firewall o che usi regole del gruppo di sicurezza di rete (NSG). In tal caso, è possibile che si verifichino problemi. Vedere [Connettività in uscita per gli URL di Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) per assicurarsi che tutti gli URL siano connessi.

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>ID errore 153006-nessun punto di ripristino coerente con l'app disponibile per la macchina virtuale negli ultimi "X" minuti

Di seguito sono riportati alcuni dei problemi più comuni.

#### <a name="known-issue-in-sql-server-20082008-r2"></a>Problema noto in SQL Server 2008/2008 R2

**Come risolvere**: esiste un problema noto con SQL Server 2008/2008 R2. [Per un server che ospita SQL Server 2008 R2, vedere l'articolo Azure Site Recovery Agent o un altro backup VSS non componente non riesce](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2).

#### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Azure Site Recovery i processi hanno esito negativo nei server che ospitano qualsiasi versione di SQL Server istanze con AUTO_CLOSE database

**Procedura**: fare riferimento all'articolo [backup VSS non componente, ad esempio Azure Site Recovery i processi hanno esito negativo nei server che ospitano SQL Server istanze con Auto_Close](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)database.


#### <a name="known-issue-in-sql-server-2016-and-2017"></a>Problema noto in SQL Server 2016 e 2017

**Come risolvere**: vedere l'articolo relativo all' [errore quando si esegue il backup di una macchina virtuale con backup non basato su componenti in SQL Server 2016 e 2017](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component).

#### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Si sta usando la configurazione di Azure Spazi di archiviazione diretta

**Procedura**: Azure Site Recovery non è possibile creare un punto di ripristino coerente con l'applicazione per spazi di archiviazione diretta configurazione. [Configurare i criteri di replica](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms).

### <a name="more-causes-because-of-vss-related-issues"></a>Altre cause a causa di problemi correlati a VSS:

Per risolvere il problema, controllare i file nella macchina di origine per ottenere il codice di errore esatto per l'errore:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Per individuare gli errori nel file, cercare la stringa "vacpError" aprendo il file vacp. log in un editor.

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

Nell'esempio precedente, **2147754994** è il codice di errore che indica l'errore che segue la frase.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS writer non è installato-errore 2147221164

**Correzione**: per generare un tag di coerenza dell'applicazione, Azure Site Recovery utilizza servizio Copia Shadow del volume (VSS). Site Recovery installa un provider VSS per la relativa operazione in modo da eseguire snapshot di coerenza delle app. Azure Site Recovery installa questo provider VSS come servizio. Se il provider VSS non è installato, la creazione dello snapshot di coerenza dell'applicazione non riesce. Mostra l'ID errore 0x80040154 "classe non registrata". Vedere l'articolo per la [risoluzione dei problemi di installazione VSS Writer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures).

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS writer è disabilitato-errore 2147943458

**Correzione**: per generare il tag di coerenza dell'applicazione, Azure Site Recovery utilizza VSS. Site Recovery installa un provider VSS per la relativa operazione in modo da eseguire snapshot di coerenza delle app. Questo provider VSS viene installato come servizio. Se il servizio del provider VSS non è abilitato, la creazione dello snapshot di coerenza dell'applicazione non riesce. Viene visualizzato l'errore "il servizio specificato è disabilitato e non può essere avviato (0x80070422)".

Se VSS è disabilitato:

- Verificare che il tipo di avvio del servizio provider VSS sia impostato su **automatico**.
- Riavviare i seguenti servizi:
   - Servizio VSS
   - Provider VSS di Azure Site Recovery
   - Servizio VDS

#### <a name="vss-provider-not_registered---error-2147754756"></a>NOT_REGISTERED del PROVIDER VSS-errore 2147754756

**Correzione**: per generare il tag di coerenza dell'applicazione, Azure Site Recovery utilizza VSS. Controllare se è installato il servizio provider Azure Site Recovery VSS.

Usare i comandi seguenti per reinstallare il provider VSS:
1. Disinstalla provider esistente: C:\Programmi (x86) \Microsoft Azure site Recovery\agent\ InMageVSSProvider_Uninstall. cmd
1. Reinstallare il provider VSS: C:\Programmi (x86) \Microsoft Azure site Recovery\agent\ InMageVSSProvider_Install. cmd

Verificare che il tipo di avvio del servizio provider VSS sia impostato su **automatico**.

Riavviare i seguenti servizi:
- Servizio VSS
- Provider VSS di Azure Site Recovery
- Servizio VDS
