---
title: Testare la latenza di rete della macchina virtuale di Azure in una rete virtuale di Azure | Microsoft Docs
description: Informazioni su come testare la latenza di rete tra macchine virtuali di Azure in una rete virtuale
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 00efc2754948d53d4f80a6261dbd4041b358185b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896371"
---
# <a name="test-vm-network-latency"></a>Testare la latenza di rete delle macchine virtuali

Per ottenere risultati più accurati, misurare la latenza di rete della macchina virtuale (VM) di Azure con uno strumento progettato per l'attività. Gli strumenti disponibili pubblicamente, ad esempio SockPerf (per Linux) e latte. exe (per Windows), possono isolare e misurare la latenza di rete escludendo altri tipi di latenza, ad esempio la latenza dell'applicazione. Questi strumenti si concentrano sul tipo di traffico di rete che influiscono sulle prestazioni dell'applicazione (in particolare Transmission Control Protocol [TCP] e il traffico UDP (User Datagram Protocol). 

Altri strumenti comuni di connettività, ad esempio ping, possono misurare la latenza, ma i risultati potrebbero non rappresentare il traffico di rete usato nei carichi di lavoro reali. Ciò è dovuto al fatto che la maggior parte di questi strumenti utilizza il Internet Control Message Protocol (ICMP), che può essere trattato in modo diverso dal traffico dell'applicazione e i cui risultati potrebbero non essere applicabili ai carichi di lavoro che utilizzano TCP e UDP. 

Per un test accurato della latenza di rete dei protocolli usati dalla maggior parte delle applicazioni, SockPerf (per Linux) e latte. exe (per Windows) producono i risultati più rilevanti. Questo articolo illustra entrambi gli strumenti.

## <a name="overview"></a>Panoramica

Utilizzando due macchine virtuali, una come mittente e una come ricevente, viene creato un canale di comunicazione bidirezionale. Con questo approccio, è possibile inviare e ricevere pacchetti in entrambe le direzioni e misurare il tempo di round trip (RTT).

È possibile usare questo approccio per misurare la latenza di rete tra due macchine virtuali o persino tra due computer fisici. Le misurazioni di latenza possono essere utili per gli scenari seguenti:

- Stabilire un benchmark per la latenza di rete tra le macchine virtuali distribuite.
- Confrontare gli effetti delle modifiche alla latenza di rete dopo aver apportato le modifiche correlate a:
  - Sistema operativo o software dello stack di rete, incluse le modifiche di configurazione.
  - Un metodo di distribuzione della macchina virtuale, ad esempio la distribuzione in una zona di disponibilità o in un gruppo di posizionamento di prossimità (PPG).
  - Proprietà della macchina virtuale, ad esempio la rete accelerata o le modifiche alle dimensioni.
  - Una rete virtuale, ad esempio le modifiche di routing o filtro.

### <a name="tools-for-testing"></a>Strumenti per il test
Per misurare la latenza, sono disponibili due opzioni dello strumento diverse:

* Per i sistemi basati su Windows: [latte. exe (Windows)](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* Per sistemi basati su Linux: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Usando questi strumenti, è possibile garantire che vengano misurati solo i tempi di recapito del payload TCP o UDP e non i tipi di pacchetti ICMP (ping) o altri tipi di pacchetti che non vengono usati dalle applicazioni e non influiscono sulle prestazioni.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Suggerimenti per la creazione di una configurazione di macchina virtuale ottimale

Quando si crea la configurazione della macchina virtuale, tenere presenti le raccomandazioni seguenti:
- Usare la versione più recente di Windows o Linux.
- Abilitare la rete accelerata per ottenere risultati ottimali.
- Distribuire le VM con un [gruppo di posizionamento di prossimità di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location).
- Le macchine virtuali di dimensioni maggiori offrono in genere prestazioni migliori rispetto alle VM più piccole

### <a name="tips-for-analysis"></a>Suggerimenti per l'analisi

Quando si analizzano i risultati dei test, tenere presenti le raccomandazioni seguenti:

- Stabilire una linea di base in anticipo, non appena vengono completate la distribuzione, la configurazione e le ottimizzazioni.
- Confrontare sempre i nuovi risultati con una baseline o, in caso contrario, da un test a un altro con modifiche controllate.
- Ripetere i test ogni volta che le modifiche vengono osservate o pianificate.


## <a name="test-vms-that-are-running-windows"></a>Testare le macchine virtuali che eseguono Windows

### <a name="get-latteexe-onto-the-vms"></a>Ottenere latte. exe nelle VM

Scaricare la [versione più recente di latte. exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b).

Si consiglia di inserire Lattes. exe in una cartella separata, ad esempio *c:\Tools*.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Consenti latte. exe attraverso Windows Defender Firewall

Nel *ricevitore*creare una regola Consenti in Windows Defender Firewall per consentire l'arrivo del traffico latte. exe. È più semplice consentire l'intero programma di caffellatte. exe per nome, anziché consentire porte TCP specifiche in ingresso.

Consentire a caffellatte. exe di usare Windows Defender Firewall eseguendo il comando seguente:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Ad esempio, se è stato copiato latte. exe nella cartella *c:\Tools* , si tratta del comando seguente:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Esegui test di latenza

* Sul *ricevitore*avviare latte. exe (eseguirlo dalla finestra cmd, non da PowerShell):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Circa 65.000 iterazioni è sufficientemente lungo da restituire risultati rappresentativi.

    Qualsiasi numero di porta disponibile funziona correttamente.

    Se la macchina virtuale ha un indirizzo IP di 10.0.0.4, il comando avrà un aspetto simile al seguente:

    `latte -a 10.0.0.4:5005 -i 65100`

* Sul *mittente*avviare latte. exe (eseguirlo dalla finestra cmd, non da PowerShell):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    Il comando risultante è identico a quello del ricevitore, tranne che per l'aggiunta di&nbsp; *-c* per indicare che si tratta del *client*o del *mittente*:

    `latte -c -a 10.0.0.4:5005 -i 65100`

Attendere i risultati. A seconda della distanza tra le macchine virtuali, il completamento del test può richiedere alcuni minuti. Si consiglia di iniziare con meno iterazioni per verificare la riuscita prima di eseguire test più lunghi.

## <a name="test-vms-that-are-running-linux"></a>Testare le macchine virtuali che eseguono Linux

Per testare le macchine virtuali che eseguono Linux, usare [SockPerf](https://github.com/mellanox/sockperf).

### <a name="install-sockperf-on-the-vms"></a>Installare SockPerf nelle VM

Nelle VM Linux, sia *mittente* che *ricevitore*, eseguire i comandi seguenti per preparare SockPerf nelle VM. Sono disponibili comandi per le distribuzioni principali.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>Per Red Hat Enterprise Linux (RHEL)/CentOS

Eseguire i comandi seguenti:

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>Per Ubuntu

Eseguire i comandi seguenti:

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>Per tutte le distribuzioni

Copiare, compilare e installare SockPerf in base ai passaggi seguenti:

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>Eseguire SockPerf nelle macchine virtuali

Al termine dell'installazione di SockPerf, le macchine virtuali sono pronte per l'esecuzione dei test di latenza. 

Innanzitutto, avviare SockPerf sul *ricevitore*.

Qualsiasi numero di porta disponibile funziona correttamente. In questo esempio viene usata la porta 12345:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Ora che il server è in ascolto, il client può iniziare a inviare pacchetti al server sulla porta su cui è in ascolto (in questo caso, 12345).

Circa 100 secondi sono sufficientemente lunghi per restituire risultati rappresentativi, come illustrato nell'esempio seguente:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Attendere i risultati. A seconda della distanza delle macchine virtuali, il numero di iterazioni può variare. Per verificare l'esito positivo prima di eseguire test più lunghi, provare a iniziare con test più brevi di circa 5 secondi.

Questo esempio di SockPerf usa una dimensione del messaggio di 350 byte, che è tipica per un pacchetto medio. È possibile regolare le dimensioni in un livello superiore o inferiore per ottenere risultati che rappresentano in modo più accurato il carico di lavoro in esecuzione nelle macchine virtuali.


## <a name="next-steps"></a>Passaggi successivi
* Migliorare la latenza con un [gruppo di posizionamento di prossimità di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location).
* Informazioni su come [ottimizzare la rete per le macchine virtuali](../virtual-network/virtual-network-optimize-network-bandwidth.md) per lo scenario.
* Leggere le informazioni sull' [allocazione della larghezza di banda alle macchine virtuali](../virtual-network/virtual-machine-network-throughput.md).
* Per altre informazioni, vedere [domande frequenti sulla rete virtuale di Azure](../virtual-network/virtual-networks-faq.md).
