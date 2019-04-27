---
title: Risoluzione dei problemi di Azure Site Recovery per gli errori di replica in corso da Azure ad Azure | Microsoft Docs
description: Risoluzione dei problemi e degli errori che si verificano quando si esegue la replica di macchine virtuali di Azure per il ripristino di emergenza
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 9ff756270c368d39b7ef78d7c1046f7c91169668
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103747"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Risolvere i problemi di replica delle macchine virtuali da Azure ad Azure

L'articolo descrive i problemi comuni di Azure Site Recovery, quando si esegue la replica e il ripristino delle macchine virtuali di Azure da un'area a un'altra area. Si spiega inoltre come risolverli. Per altre informazioni sulle configurazioni supportate, vedere la [matrice di supporto per la replica delle VM di Azure](site-recovery-support-matrix-azure-to-azure.md).

Azure Site Recovery replica in modo coerente i dati dall'area di origine all'area di ripristino di emergenza e crea un punto di recupero coerente con l'arresto anomalo ogni 5 minuti. Se non è possibile creare punti di ripristino da Site Recovery per 60 minuti, l'utente viene avvisato con queste informazioni:

Messaggio di errore: "Nessun punto di recupero coerente con l'arresto anomalo disponibile per la macchina virtuale negli ultimi 60 minuti".</br>
ID errore: 153007 </br>

Le sezioni seguenti descrivono cause e soluzioni.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Elevata frequenza di modifica dei dati nella macchina virtuale di origine
Azure Site Recovery genera un evento se la frequenza di modifica dei dati nella macchina virtuale di origine è maggiore dei limiti supportati. Per verificare se il problema è dovuto a una varianza elevata, andare su **Elementi replicati** > **Macchina virtuale** > **Eventi - ultime 72 ore**.
Verrà visualizzato l'evento "Frequenza di modifica dei dati superiore ai limiti supportati":

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Se si seleziona l'evento, si dovrebbero vedere le informazioni esatte del disco:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Limiti di Azure Site Recovery
La tabella seguente indica i limiti di Azure Site Recovery. Questi limiti si basano sui test di Microsoft, ma non possono coprire tutte le possibili combinazioni di I/O delle applicazioni. I risultati effettivi possono variare in base alla combinazione di I/O delle applicazioni. 

Esistono due limitazioni da prendere in considerazione, la varianza dei dati per ogni disco e la varianza dei dati per ogni macchina virtuale. Ecco un esempio sul disco P20 Premium nella tabella seguente. Site Recovery può gestire 5 MB/s di varianza per ogni disco con un massimo di cinque dischi per ogni macchina virtuale, a causa del limite di 25 MB/s della varianza totale per ogni macchina virtuale.

**Destinazione archiviazione di replica** | **Dimensione media I/O per disco di origine** |**Varianza media dei dati per disco di origine** | **Varianza totale giornaliera dei dati per disco di origine**
---|---|---|---
Archiviazione standard | 8 KB | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 8 KB  | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 16 KB | 4 MB/s |  336 GB per disco
Disco P10 o P15 Premium | 32 KB o superiori | 8 MB/s | 672 GB per disco
Disco P20, P30, P40 o P50 Premium | 8 KB    | 5 MB/s | 421 GB per disco
Disco P20, P30, P40 o P50 Premium | 16 KB o superiori |10 MB/s | 842 GB per disco

### <a name="solution"></a>Soluzione
Azure Site Recovery prevede limiti di frequenza di modifica dei dati, in base al tipo di disco. Per determinare se il problema è ricorrente o momentaneo, individuare il modello della frequenza di modifica dei dati della macchina virtuale interessata. Andare alla macchina virtuale di origine, trovare le metriche in **Monitoraggio**, quindi aggiungere le metriche, come mostra questo screenshot:

![Processo di tre passaggi per individuare la frequenza di modifica dei dati](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Selezionare **Aggiungi metrica** e aggiungere **Byte scritti su disco/sec per un disco del sistema operativo** e **Byte scritti su disco/sec per un disco dati**.
2. Monitorare il picco come mostrato nello screenshot.
3. Visualizzare il numero totale di operazioni di scrittura eseguite sui dischi del sistema operativo e in tutti i dischi dati combinati. Queste metriche potrebbero non fornire informazioni a livello di dischi singoli, ma indicano il modello di varianza totale dei dati.

Se il picco proviene da un burst di dati occasionale e la frequenza di modifica dei dati supera i 10 MB/s (Premium) e i 2 MB/s (Standard) solo per un determinato intervallo di tempo e poi si riduce, la replica viene aggiornata. Tuttavia se la varianza è ben oltre il limite supportato per la maggior parte del tempo, se possibile considerare una di queste opzioni:

* **Escludere il disco che causa la frequenza di modifica dei dati elevata**: è possibile escludere il disco usando [PowerShell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine).
* **Modificare il livello del disco di archiviazione per il ripristino di emergenza**: questa opzione è possibile solo se la varianza dei dati del disco è inferiore a 10 MB/s. Si supponga che una macchina virtuale con disco P10 abbia una varianza dei dati maggiore di 8 MB/s, ma minore di 10 MB/s. Se il cliente può usare il disco P30 per l'archiviazione di destinazione durante la protezione, il problema può essere risolto.

## <a name="Network-connectivity-problem"></a>Problemi di connettività di rete

### <a name="network-latency-to-a-cache-storage-account"></a>Latenza di rete per l'account di archiviazione della cache
Site Recovery invia i dati replicati all'account di archiviazione della cache. È possibile visualizzare la latenza di rete se il caricamento dei dati da una macchina virtuale all'account di archiviazione della cache è inferiore a 4 MB in 3 secondi. 

Per controllare se sono presenti problemi di latenza, usare [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) per caricare i dati dalla macchina virtuale all'account di archiviazione della cache. Se la latenza è elevata, verificare che si stia usando un'appliance virtuale di rete (NVA) per controllare il traffico di rete in uscita dalle macchine virtuali. Se tutto il traffico di replica passa attraverso un'appliance virtuale di rete, l'appliance può subire limitazioni. 

È consigliabile creare un endpoint del servizio di rete nella rete virtuale per "Archiviazione", in modo che il traffico di replica non venga indirizzato all'appliance virtuale di rete. Per altre informazioni, vedere [Configurazione dell'appliance di rete virtuale](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Connettività di rete
Per il funzionamento della replica di Site Recovery, è necessaria la connettività in uscita dalla VM a intervalli IP o URL specifici. Se la macchina virtuale è protetta da un firewall o usa regole di gruppi di sicurezza di rete (NGS) per controllare la connettività in uscita, potrebbe verificarsi uno di questi problemi. Vedere [Connettività in uscita per gli URL di Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) per assicurarsi che tutti gli URL siano connessi. 