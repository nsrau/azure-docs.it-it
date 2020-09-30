---
title: Prestazioni del database Oracle in Azure NetApp Files volume singolo | Microsoft Docs
description: Descrive i risultati dei test delle prestazioni di un Azure NetApp Files singolo volume nel database Oracle.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: c6cdf2f6dada0aa4dea2f70f18237b7ee39e3ea1
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91571417"
---
# <a name="oracle-database-performance-on-azure-netapp-files-single-volumes"></a>Prestazioni del database Oracle in Azure NetApp Files volumi singoli

Questo articolo illustra gli argomenti seguenti relativi a Oracle nel cloud. Questi argomenti possono essere di particolare interesse per un amministratore di database, un progettista di cloud o un progettista di archiviazione:   

* Quando si esegue un carico di lavoro di elaborazione delle transazioni online (OLTP) (principalmente I/O casuale) o di un Online Analytical Processing (OLAP) (principalmente I/O sequenziali), quali sono le prestazioni?   
* Qual è la differenza tra le prestazioni tra il client normale di Linux kernel NFS (kNFS) e il client NFS diretto di Oracle?
* Per quanto concerne la larghezza di banda, le prestazioni di un singolo volume Azure NetApp Files sufficiente?

## <a name="testing-environment-and-components"></a>Ambiente e componenti di test

Il diagramma seguente illustra l'ambiente usato per il test. Per coerenza e semplicità, sono stati usati PlayBook Ansible per distribuire tutti gli elementi del letto del test.

![Ambiente di test Oracle](../media/azure-netapp-files/performance-oracle-test-environment.png)  

### <a name="virtual-machine-configuration"></a>Configurazione della macchina virtuale

I test hanno usato la configurazione seguente per la macchina virtuale:
* Sistema operativo:   
    RedHat Enterprise Linux 7,8 (WLE-ora01)
* Tipi di istanza:   
    Per i test sono stati usati due modelli: D32s_v3 e D64s_v3
* Numero di interfacce di rete:   
    Uno (1) inserito nella subnet 3  
* Dischi   
    I file binari e il sistema operativo Oracle sono stati inseriti in un singolo disco Premium

### <a name="azure-netapp-files-configuration"></a><a name="anf_config"></a>Configurazione di Azure NetApp Files
I test hanno usato la configurazione di Azure NetApp Files seguente:   

* Dimensioni pool di capacità:  
    Sono state configurate varie dimensioni del pool: 4 TiB, 8 TiB, 16 TiB, 32 TiB 
* Livello di servizio:  
    Ultra (128 MiB/s della larghezza di banda per 1 TiB della capacità del volume allocato)
* Volumi:  
    Uno e due test del volume sono stati valutati

### <a name="workload-generator"></a>Generatore di carico di lavoro 

I test hanno utilizzato il carico di lavoro 2.5.4.2. Questo è un generatore di carico di lavoro noto nello spazio Oracle progettato per sottoporre a stress e testare il sottosistema di I/O con un carico di lavoro di I/O fisico con buffer SGA.  

Il file 2.5.4.2 non supporta il database innestabile (PDB). Di conseguenza, è stata aggiunta una modifica agli `setup.sh` `runit.sh` script e per aggiungere il supporto PDB.  

Le variabili di cui è stato usato nei test sono descritte nelle sezioni riportate di seguito.

#### <a name="workload-80-select-20-update--random-io--slobconf-variables"></a>Carico di lavoro 80% SELECT, aggiornamento del 20% | Variabili I/O casuali `slob.conf`   

`UPDATE_PCT=20`   
`SCAN_PCT=0`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

#### <a name="workload-100-select--sequential-io--slobconf-variables"></a>Carico di lavoro 100% SELECT | I/O sequenziali- `slob.conf` variabili

`UPDATE_PCT=0`   
`SCAN_PCT=100`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

### <a name="database"></a>Database

La versione Oracle utilizzata per i test è Oracle Database Enterprise Edition 19.3.0.0.

I parametri Oracle sono i seguenti:  
* `sga_max_size`: 4096
* `sga_target`: 4096
* `db_writer_processes`: 12
* `awr_pdb_autoflush_enabled`: true
* `filesystemio_options`: SetAll
* `log_buffer`: 134217728

È stato creato un PDB per il database di.

Il diagramma seguente illustra lo spazio di tabella denominato PERFIO con 600 GB di dimensioni (20 file di dati, 30 GB ciascuno) creati per ospitare quattro schemi utente senza stato. Ogni schema utente ha dimensioni di 125 GB.

![Database Oracle](../media/azure-netapp-files/performance-oracle-tablespace.png)  

## <a name="performance-metrics"></a>Metriche delle prestazioni

L'obiettivo era quello di segnalare le prestazioni di i/o come sperimentato dall'applicazione. Pertanto, tutti i diagrammi di questo articolo utilizzano le metriche segnalate dal database Oracle tramite i report del repository automatico del carico di lavoro (AWR). Di seguito sono riportate le metriche utilizzate nei diagrammi:   

* **Media richieste IO/sec**   
    Corrisponde alla somma delle richieste i/o di lettura/sec e della media dei/o di scrittura/sec dalla sezione del profilo di carico
* **Media IO MB/sec**   
    Corrisponde alla somma della media i/o di lettura MB/sec e media i/o di scrittura/sec dalla sezione del profilo di carico
* **Latenza di lettura media**   
    Corrisponde alla latenza media dell'evento di attesa Oracle "lettura sequenziale del file di database" in microsecondi
* **Numero di thread/schema**   
    Corrisponde al numero di thread per utente per schema

## <a name="performance-measurement-results"></a>Risultati della misurazione delle prestazioni  

In questa sezione vengono descritti i risultati della misurazione delle prestazioni.

### <a name="linux-knfs-client-vs-oracle-direct-nfs"></a>Client kNFS Linux rispetto a Oracle Direct NFS

Questo scenario è stato eseguito in una macchina virtuale di Azure Standard_D32s_v3 (Intel E5-2673 v4 @ 2,30 GHz). Il carico di lavoro è 75% SELECT e il 25% di aggiornamento, principalmente I/O casuale e con un hit buffer del database pari a circa 7,5%. 

Come illustrato nel diagramma seguente, il client Oracle DNFS ha fornito fino a 2,8 x più velocità effettiva rispetto al normale client kNFS Linux:  

![Client kNFS Linux rispetto a Oracle Direct NFS](../media/azure-netapp-files/performance-oracle-kfns-compared-dnfs.png)  

Il diagramma seguente illustra la curva di latenza per le operazioni di lettura. In questo contesto, il collo di bottiglia per il client kNFS è la singola connessione socket TCP di NFS stabilita tra il client e il server NFS (il volume Azure NetApp Files).  

![Client kNFS Linux rispetto alla curva di latenza di Oracle Direct NFS](../media/azure-netapp-files/performance-oracle-latency-curve.png)  

Il client DNFS è stato in grado di effettuare il push di più richieste di i/o al secondo grazie alla possibilità di creare centinaia di connessioni socket TCP, sfruttando quindi il parallelismo. Come descritto in [Azure NetApp files configurazione](#anf_config), ogni TIB aggiuntivo di capacità allocata consente un 128MiB aggiuntivo di larghezza di banda. DNFS ha superato 1 GiB/s di velocità effettiva, che rappresenta il limite imposto dalla selezione della capacità di 8 TiB. Dato una maggiore capacità, sarebbe stata basata una maggiore velocità effettiva.

La velocità effettiva è solo una delle considerazioni. Un'altra considerazione è la latenza, che ha un effetto principale sull'esperienza utente. Come illustrato nel diagramma seguente, gli aumenti della latenza possono essere molto più rapidi con kNFS rispetto a DNFS. 

![Client kNFS Linux rispetto alla latenza di lettura di Oracle Direct NFS](../media/azure-netapp-files/performance-oracle-read-latency.png)  

Gli istogrammi forniscono informazioni ottimali sulle latenze del database. Il diagramma seguente offre una vista completa dal punto di vista della "lettura sequenziale del file di database" registrata, usando DNFS al punto dati di concorrenza più elevato (32 thread/schema). Come illustrato nel diagramma seguente, il 47% di tutte le operazioni di lettura è stato rispettato tra 512 microsecondi e 1000 microsecondi, mentre il 90% di tutte le operazioni di lettura veniva servito a una latenza inferiore a 2 ms.

![Client kNFS Linux rispetto agli istogrammi NFS diretti Oracle](../media/azure-netapp-files/performance-oracle-histogram-read-latency.png)  

In conclusione, è chiaro che DNFS è un must-have quando si tratta di migliorare le prestazioni di un'istanza del database Oracle in NFS.

### <a name="single-volume-performance-limits"></a>Limiti delle prestazioni di un singolo volume

Questa sezione descrive i limiti delle prestazioni di un singolo volume con i/o casuale e i/O sequenziali. 

#### <a name="random-io"></a>I/O casuale

DNFS è in grado di consumare una larghezza di banda molto maggiore rispetto a quella offerta da una quota di prestazioni Azure NetApp Files da 8 TB. Aumentando la capacità del volume Azure NetApp Files a 16 TiB, che rappresenta una modifica istantanea, la quantità di larghezza di banda del volume è aumentata da 1024 MiB/s da 2X a 2048 MiB/s. 

Nel diagramma seguente viene illustrata una configurazione per un carico di lavoro di aggiornamento del 80% e del 20% e con una percentuale di riscontri del buffer del database pari all'8%. Il numero di istanze è stato in grado di indirizzare un singolo volume a 200.000 richieste di I/O NFS al secondo. Considerando che ogni operazione ha una dimensione di 8 KB, il sistema sottoposto a test è stato in grado di recapitare ~ 200.000 richieste IO/sec o 1600 MiB/s.
 
![Velocità effettiva DNFS Oracle](../media/azure-netapp-files/performance-oracle-dnfs-throughput.png)  

Il diagramma della curva di latenza di lettura seguente mostra che, con l'aumentare della velocità effettiva di lettura, la latenza aumenta in modo graduale al di sotto della riga 1 MS e raggiunge il ginocchio della curva in ~ 165.000 richieste i/o medie di lettura/sec alla latenza di lettura media di ~ 1,3 ms.  Questo valore è un valore di latenza incredibile per una velocità di I/O non ottenibile con quasi tutte le altre tecnologie nel cloud di Azure. 

![Curva di latenza Oracle DNFS](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

#### <a name="sequential-io"></a>I/O sequenziale  

Come illustrato nel diagramma seguente, non tutte le operazioni di I/O sono casuali, considerando un backup RMAN o una scansione di tabella completa, ad esempio, come carichi di lavoro che richiedono una larghezza di banda maggiore possibile.  Usando la stessa configurazione descritta in precedenza, ma con il volume ridimensionato a 32 TiB, il diagramma seguente mostra che una singola istanza di Oracle DB può proseguire verso l'alto di 3.900 MB/s di velocità effettiva, molto vicino alla quota di prestazioni del volume Azure NetApp Files di 32 TB (128 MB/s * 32 = 4096 MB/s).

![I/O Oracle DNFS](../media/azure-netapp-files/performance-oracle-dnfs-io.png)  

In breve, Azure NetApp Files consente di portare i database Oracle nel cloud. Offre prestazioni ottimali quando il database lo richiede. È possibile ridimensionare dinamicamente e non disruptively la quota del volume in qualsiasi momento.

## <a name="next-steps"></a>Passaggi successivi

- [Raccomandazioni sui test di benchmark delle prestazioni per Azure NetApp Files](azure-netapp-files-performance-metrics-volumes.md)
- [Benchmark delle prestazioni per Linux](performance-benchmarks-linux.md)