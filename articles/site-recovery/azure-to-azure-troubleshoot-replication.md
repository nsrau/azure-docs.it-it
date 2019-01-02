---
title: Risoluzione dei problemi di Azure Site Recovery per gli errori di replica da Azure ad Azure | Microsoft Docs
description: Risoluzione dei problemi e degli errori che si verificano quando si esegue la replica di macchine virtuali di Azure per il ripristino di emergenza
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 9a32ac1ae71cb7bd89c4252157c3a5cd395b2694
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842340"
---
# <a name="troubleshoot-azure-to-azure-vm-ongoing-replication-issues"></a>Risolvere i problemi di replica delle macchine virtuali da Azure ad Azure

Questo articolo descrive i problemi comuni in Azure Site Recovery quando si eseguono la replica e il ripristino di macchine virtuali di Azure da un'area a un'altra e illustra come risolverli. Per altre informazioni sulle configurazioni supportate, vedere la [matrice di supporto per la replica delle VM di Azure](site-recovery-support-matrix-azure-to-azure.md).


## <a name="recovery-points-not-getting-generated"></a>Mancata generazione dei punti di ripristino

MESSAGGIO DI ERRORE: No crash consistent recovery point available for the VM in the last 60 minutes (Nessun punto di recupero coerente con l'arresto anomalo disponibile per la VM negli ultimi 60 minuti).</br>
ID ERRORE: 153007 </br>

Azure Site Recovery replica in modo coerente i dati dall'area di origine all'area di ripristino di emergenza e crea un punto coerente con l'arresto anomalo ogni 5 minuti. Se Site Recovery non riesce a creare punti di ripristino per 60 minuti, genera un avviso per l'utente. Di seguito vengono indicate le possibili cause di questo errore:

**Causa 1: [Elevata frequenza di modifica dei dati nella macchina virtuale di origine](#high-data-change-rate-on-the-source-virtal-machine)**    
**Causa 2: [Problema di connettività di rete ](#Network-connectivity-issue)**

## <a name="causes-and-solutions"></a>Cause e soluzioni

### <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Elevata frequenza di modifica dei dati nella macchina virtuale di origine
Azure Site Recovery genera un evento se la frequenza di modifica dei dati nella macchina virtuale di origine è maggiore dei limiti supportati. Per verificare se il problema è dovuto a una varianza elevata, passare a Elementi replicati > Macchina virtuale > fare clic su "Eventi - Ultime 72 ore".
Verrà visualizzato l'evento "Frequenza di modifica dei dati superiore ai limiti supportati", mostrato nello screenshot seguente

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Se si fa clic sull'evento, verranno visualizzate informazioni precise sul disco, come mostrato nello screenshot seguente

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


#### <a name="azure-site-recovery-limits"></a>Limiti di Azure Site Recovery
La tabella seguente indica i limiti di Azure Site Recovery. Questi limiti si basano su test di Microsoft, ma non possono coprire tutte le possibili combinazioni di I/O delle applicazioni. I risultati effettivi possono variare in base alla combinazione di I/O delle applicazioni. È necessario considerare due limiti, la varianza dei dati per disco e la varianza dei dati per macchina virtuale.
Ad esempio, se si esamina il disco P20 Premium nella tabella seguente, Site Recovery può gestire una varianza di 5 MB/s per disco con un massimo di cinque di questi dischi per macchina virtuale, a causa del limite di varianza totale di 25 MB/s per macchina virtuale.

**Destinazione archiviazione di replica** | **Dimensioni medie I/O disco di origine** |**Varianza dati media disco di origine** | **Varianza dati totale giornaliera disco di origine**
---|---|---|---
Archiviazione standard | 8 KB | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 8 KB  | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 16 KB | 4 MB/s |  336 GB per disco
Disco P10 o P15 Premium | 32 KB o superiori | 8 MB/s | 672 GB per disco
Disco P20, P30, P40 o P50 Premium | 8 KB    | 5 MB/s | 421 GB per disco
Disco P20, P30, P40 o P50 Premium | 16 KB o superiori |10 MB/s | 842 GB per disco

### <a name="solution"></a>Soluzione
È necessario ricordare che Azure Site Recovery prevede limiti di frequenza di modifica dei dati in base al tipo di disco. Per determinare se il problema è ricorrente o momentaneo, è importante individuare il modello della frequenza di modifica dei dati della macchina virtuale interessata.
Per determinare la frequenza di modifica dei dati nella macchina virtuale interessata, passare alla macchina virtuale di origine > metriche in Monitoraggio e quindi aggiungere la metrica, come mostrato di seguito.

![high_data_change_rate](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Fare clic su "Aggiungi metrica" e aggiungere "Byte scritti su disco/sec per un disco del sistema operativo" e "Byte scritti su disco/sec per un disco dati".
2. Monitorare il picco come mostrato nello screenshot.
3. Indicherà il numero totale di operazioni di scrittura eseguite sul disco del sistema operativo e in tutti i dischi dati combinati. A questo punto, questa metrica può non fornire informazioni a livello di disco, ma è un buon indicatore del modello di variazione dei dati totale.

Se in casi come il precedente si tratta di un picco di dati occasionale e la frequenza di modifica dei dati è maggiore di 10 MBps (Premium) e 2 MBps (Standard) solo per un breve periodo e quindi si riduce, la replica verrà avviata di nuovo. Tuttavia se la varianza è ben oltre il limite supportato per la maggior parte del tempo, è opportuno considerare una delle opzioni indicate di seguito, se possibile:

**Opzione 1:** escludere il disco che causa la frequenza di modifica dei dati elevata: </br>
Attualmente è possibile escludere il disco tramite [PowerShell per Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine)

**Opzione 2:** modificare il livello del disco di archiviazione per il ripristino di emergenza: </br>
Questa opzione è possibile solo se la varianza dei dati del disco è inferiore a 10 MB/s. Si supponga che una macchina virtuale con disco P10 abbia una varianza dei dati maggiore di 8 MB/s, ma minore di 10 MB/s. Se il cliente può usare il disco P30 per l'archiviazione di destinazione durante la protezione, il problema può essere risolto.

### <a name="Network-connectivity-issue"></a>Problema di connettività di rete

#### <a name="network-latency-to-cache-storage-account-"></a>Latenza di rete per l'account di archiviazione della cache:
 Site Recovery invia i dati replicati nell'account di archiviazione della cache. Il problema può verificarsi se per caricare 4 MB di dati dalla macchina virtuale all'account di archiviazione della cache sono necessari più di 3 secondi. Per controllare se sono presenti problemi di latenza, usare [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) per caricare i dati dalla macchina virtuale all'account di archiviazione della cache.<br>
Se la latenza è elevata, controllare se per controllare il traffico di rete in uscita dalle macchine virtuali viene usata un'appliance virtuale di rete. Se tutto il traffico di replica passa attraverso un'appliance virtuale di rete, l'appliance può subire limitazioni. È consigliabile creare un endpoint del servizio di rete nella rete virtuale per "Archiviazione" in modo che il traffico di replica non venga indirizzato all'appliance virtuale di rete. Fare riferimento a [Configurazione di appliance virtuali di rete](https://docs.microsoft.com/en-us/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)

#### <a name="network-connectivity"></a>Connettività di rete
Per il funzionamento della replica di Site Recovery, è necessaria la connettività in uscita dalla VM a intervalli IP o URL specifici. Se la macchina virtuale è protetta da un firewall o usa regole di gruppi di sicurezza di rete (NGS) per controllare la connettività in uscita, potrebbe verificarsi uno di questi problemi.</br>
Fare riferimento a [Connettività in uscita per gli URL di Site Recovery](https://docs.microsoft.com/en-us/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) per assicurarsi che tutti gli URL siano connessi 