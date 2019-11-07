---
title: Testare la latenza di rete delle macchine virtuali di Azure in una rete virtuale di Azure | Microsoft Docs
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
ms.openlocfilehash: 760a181b4459db28d3a6eee5022cc0f984c4bee0
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588278"
---
# <a name="testing-network-latency"></a>Test della latenza di rete

La misurazione della latenza di rete con uno strumento progettato per l'attività fornirà i risultati più accurati. Gli strumenti disponibili pubblicamente come SockPerf (per Linux) e latte (per Windows) sono esempi di strumenti che consentono di isolare e misurare la latenza di rete escludendo altri tipi di latenza, ad esempio la latenza dell'applicazione. Questi strumenti si concentrano sul tipo di traffico di rete che influiscono sulle prestazioni dell'applicazione, ovvero TCP e UDP. Altri strumenti comuni di connettività, ad esempio ping, possono essere usati a volte per misurare la latenza, ma questi risultati potrebbero non essere rappresentativi del traffico di rete usato nei carichi di lavoro reali. Questo perché la maggior parte di questi strumenti usa il protocollo ICMP, che può essere trattato in modo diverso dal traffico dell'applicazione e i risultati potrebbero non essere applicabili ai carichi di lavoro che usano TCP e UDP. Per un test accurato della latenza di rete per i protocolli usati dalla maggior parte delle applicazioni, SockPerf (per Linux) e latte (per Windows) producono i risultati più rilevanti. In questo documento vengono illustrati entrambi gli strumenti.

## <a name="overview"></a>Panoramica

Con due macchine virtuali, una come mittente e una come ricevente, viene creato un canale di comunicazione bidirezionale per inviare e ricevere pacchetti in entrambe le direzioni per misurare il tempo di round trip (RTT).

Questi passaggi possono essere usati per misurare la latenza di rete tra due macchine virtuali (VM) o anche tra due computer fisici. Le misurazioni di latenza possono essere utili per gli scenari seguenti:

- Stabilire un benchmark per la latenza di rete tra le macchine virtuali distribuite
- Confrontare gli effetti delle modifiche alla latenza di rete dopo aver apportato le modifiche correlate a:
  - Software dello stack del sistema operativo o della rete, incluse le modifiche di configurazione
  - Metodo di distribuzione della macchina virtuale, ad esempio la distribuzione in una zona o un PPG
  - Proprietà della macchina virtuale, ad esempio la rete accelerata o le modifiche alle dimensioni
  - Rete virtuale, ad esempio le modifiche di routing o filtro

### <a name="tools-for-testing"></a>Strumenti per il test
Per misurare la latenza sono disponibili due opzioni diverse, una per i sistemi basati su Windows e una per i sistemi basati su Linux

Per Windows: latte. exe (Windows) [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Per Linux: SockPerf (Linux) [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

L'uso di questi strumenti garantisce che vengano misurati solo i tempi di recapito del payload TCP o UDP e non i pacchetti ICMP (ping) o altri tipi di pacchetti che non vengono usati dalle applicazioni e non influiscono sulle prestazioni.

### <a name="tips-for-an-optimal-vm-configuration"></a>Suggerimenti per una configurazione di macchina virtuale ottimale:

- Usare la versione più recente di Windows o Linux
- Abilitare la rete accelerata per ottenere risultati ottimali
- Distribuire macchine virtuali con il [gruppo di posizionamento di prossimità di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
- Le macchine virtuali di dimensioni maggiori offrono prestazioni migliori rispetto alle VM più piccole

### <a name="tips-for-analysis"></a>Suggerimenti per l'analisi

- Stabilire una linea di base in anticipo, non appena vengono completate la distribuzione, la configurazione e le ottimizzazioni
- Confronta sempre i nuovi risultati con una baseline o in caso contrario da un test a un altro con modifiche controllate
- Ripetere i test ogni volta che le modifiche vengono osservate o pianificate


## <a name="testing-vms-running-windows"></a>Test di macchine virtuali che eseguono Windows

## <a name="get-latteexe-onto-the-vms"></a>Ottenere latte. exe nelle VM

Scaricare la versione più recente: [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Si consiglia di inserire Lattes. exe in una cartella separata, ad esempio c:\Tools

## <a name="allow-latteexe-through-the-windows-firewall"></a>Consenti latte. exe attraverso Windows Firewall

Nel ricevitore creare una regola di autorizzazione per la Windows Firewall per consentire l'arrivo del traffico latte. exe. È più semplice consentire l'intero programma di caffellatte. exe per nome, anziché consentire porte TCP specifiche in ingresso.

Consentire a caffellatte. exe di usare il Windows Firewall nel modo seguente:

netsh advfirewall firewall add rule Program =\<PATH\>\\latte. exe name =&quot;latte&quot; Protocol = any dir = in action = allow Enable = Yes profile = ANY


Ad esempio, se è stato copiato latte. exe nella cartella &quot;c:\\Tools&quot;, si tratta del comando seguente:

```cmd
netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

## <a name="running-latency-tests"></a>Esecuzione di test di latenza

Avviare latte. exe sul ricevitore (eseguire da CMD, non da PowerShell):

latte-a &lt;indirizzo IP del ricevitore&gt;:&lt;porta&gt;-i &lt;iterazioni&gt;

Le iterazioni di 65K sono abbastanza lunghe per restituire risultati rappresentativi.

Qualsiasi numero di porta disponibile funziona correttamente.

Se la macchina virtuale ha un indirizzo IP di 10.0.0.4, avrà un aspetto simile al seguente:

```cmd
latte -a 10.0.0.4:5005 -i 65100
```

Avviare latte. exe nel mittente (eseguire da CMD, non da PowerShell):

latte-c-a \<indirizzo IP ricevitore\>:\<porta\>-i \<iterazioni\>


Il comando risultante è identico a quello del ricevitore, tranne con l'aggiunta di &quot;-c&quot; per indicare che si tratta del client &quot;&quot; o mittente:

```cmd
latte -c -a 10.0.0.4:5005 -i 65100
```

Attendere i risultati. A seconda della distanza tra le macchine virtuali, potrebbero essere necessari alcuni minuti per il completamento. Si consiglia di iniziare con meno iterazioni per verificare la riuscita prima di eseguire test più lunghi.



## <a name="testing-vms-running-linux"></a>Test di macchine virtuali che eseguono Linux

Usare SockPerf. È disponibile da [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

### <a name="install-sockperf-on-the-vms"></a>Installare SockPerf nelle VM

Nelle VM Linux (mittente e ricevitore) eseguire questi comandi per preparare SockPerf nelle VM. Sono disponibili comandi per le distribuzioni principali.

#### <a name="for-rhel--centos-follow-these-steps"></a>Per RHEL/CentOS, seguire questa procedura:
```bash
#CentOS / RHEL - Install GIT and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu-follow-these-steps"></a>Per Ubuntu, attenersi alla seguente procedura:
```bash
#Ubuntu - Install GIT and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros-copy-compile-and-install-sockperf-according-to-the-following-steps"></a>Per tutte le distribuzioni, copiare, compilare e installare SockPerf in base ai passaggi seguenti:
```bash
#Bash - all distros

#From bash command line (assumes git is installed)
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

Innanzitutto, avviare SockPerf sul ricevitore.

Qualsiasi numero di porta disponibile funziona correttamente. In questo esempio viene usata la porta 12345:
```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```
Ora che il server è in ascolto, il client può iniziare a inviare pacchetti al server sulla porta su cui è in ascolto, 12345 in questo caso.

Circa 100 secondi sono sufficientemente lunghi per restituire risultati rappresentativi, come illustrato nell'esempio seguente:
```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Attendere i risultati. A seconda della distanza delle macchine virtuali, il numero di iterazioni può variare. È consigliabile iniziare con test più brevi di circa 5 secondi per verificare la riuscita prima di eseguire test più lunghi.

Questo esempio di SockPerf usa una dimensione del messaggio di 350 byte perché si tratta di un pacchetto di dimensioni medie tipico. Le dimensioni del messaggio possono essere modificate in modo più elevato o inferiore per ottenere risultati che rappresentano in modo più accurato il carico di lavoro che verrà eseguito nelle VM.


## <a name="next-steps"></a>Passaggi successivi
* Migliorare la latenza con il [gruppo posizionamento di prossimità di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
* Informazioni su come [ottimizzare la rete per le macchine virtuali](../virtual-network/virtual-network-optimize-network-bandwidth.md) per lo scenario.
* Sono disponibili informazioni sull'[allocazione di larghezza di banda alle macchine virtuali](../virtual-network/virtual-machine-network-throughput.md)
* Altre informazioni sono disponibili nell'articolo [Domande frequenti sulla rete virtuale di Azure](../virtual-network/virtual-networks-faq.md).