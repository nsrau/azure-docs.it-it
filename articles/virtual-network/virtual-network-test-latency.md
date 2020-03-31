---
title: Testare la latenza di rete della macchina virtuale di Azure in una rete virtuale di Azure. Documenti Microsoft
description: Informazioni su come testare la latenza di rete tra macchine virtuali di Azure in una rete virtualeLearn how to test network latency between Azure virtual machines on a virtual network
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896371"
---
# <a name="test-vm-network-latency"></a>Testare la latenza di rete delle macchine virtuali

Per ottenere risultati più accurati, misurare la latenza di rete della macchina virtuale di Azure con uno strumento progettato per l'attività. Gli strumenti disponibili pubblicamente come SockPerf (per Linux) e latte.exe (per Windows) possono isolare e misurare la latenza di rete escludendo altri tipi di latenza, ad esempio la latenza dell'applicazione. Questi strumenti si concentrano sul tipo di traffico di rete che influisce sulle prestazioni dell'applicazione (vale a dire, Transmission Control Protocol [TCP] e User Datagram Protocol [UDP]). 

Altri strumenti di connettività comuni, ad esempio Ping, potrebbero misurare la latenza, ma i risultati potrebbero non rappresentare il traffico di rete utilizzato nei carichi di lavoro reali. Questo perché la maggior parte di questi strumenti utilizza il protocollo ICMP (Internet Control Message Protocol), che può essere trattato in modo diverso dal traffico delle applicazioni e i cui risultati potrebbero non essere applicabili ai carichi di lavoro che utilizzano TCP e UDP. 

Per un test accurato della latenza di rete dei protocolli utilizzati dalla maggior parte delle applicazioni, SockPerf (per Linux) e latte.exe (per Windows) producono i risultati più rilevanti. Questo articolo illustra entrambi questi strumenti.

## <a name="overview"></a>Panoramica

Utilizzando due macchine virtuali, una come mittente e una come ricevitore, si crea un canale di comunicazione bidirezionale. Con questo approccio, è possibile inviare e ricevere pacchetti in entrambe le direzioni e misurare il tempo di andata e ritorno (RTT).

È possibile usare questo approccio per misurare la latenza di rete tra due macchine virtuali o anche tra due computer fisici. Le misurazioni della latenza possono essere utili per gli scenari seguenti:Latency measurements can be useful for the following scenarios:

- Stabilire un benchmark per la latenza di rete tra le macchine virtuali distribuite.
- Confrontare gli effetti delle modifiche nella latenza di rete dopo aver apportato modifiche correlate a:
  - sistema operativo (OS) o software dello stack di rete, incluse le modifiche alla configurazione.
  - Metodo di distribuzione di una macchina virtuale, ad esempio la distribuzione in una zona di disponibilità o in un gruppo di posizionamento di prossimità (PPG).
  - Proprietà della macchina virtuale, ad esempio Rete accelerata o modifiche delle dimensioni.
  - Una rete virtuale, ad esempio il routing o il filtro delle modifiche.

### <a name="tools-for-testing"></a>Strumenti per il test
Per misurare la latenza, sono disponibili due diverse opzioni dello strumento:To measure latency, you have two different tool options:

* Per i sistemi basati su Windows: [latte.exe (Windows)](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* Per sistemi basati su Linux: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Utilizzando questi strumenti, è possibile assicurarsi che vengano misurati solo i tempi di recapito del payload TCP o UDP e non ICMP (Ping) o altri tipi di pacchetti che non vengono utilizzati dalle applicazioni e non influiscono sulle loro prestazioni.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Suggerimenti per la creazione di una configurazione ottimale della macchina virtualeTips for creating an optimal VM configuration

Quando si crea la configurazione della macchina virtuale, tenere presente quanto segue:When you create your VM configuration, keep in mind the following recommendations:
- Utilizzare la versione più recente di Windows o Linux.
- Abilita la rete accelerata per ottenere risultati ottimali.
- Distribuire macchine virtuali con un gruppo di posizionamento di prossimità di Azure.Deploy VMs with an [Azure proximity placement group](https://docs.microsoft.com/azure/virtual-machines/linux/co-location).
- Le macchine virtuali più grandi in genere offrono prestazioni migliori rispetto alle macchine virtuali più piccole.

### <a name="tips-for-analysis"></a>Suggerimenti per l'analisi

Durante l'analisi dei risultati dei test, tenere presente quanto segue:

- Stabilire una linea di base in anticipo, non appena la distribuzione, la configurazione e le ottimizzazioni sono complete.
- Confrontare sempre i nuovi risultati con una linea di base o, in caso contrario, da un test a un altro con modifiche controllate.
- Ripetere i test ogni volta che le modifiche vengono osservate o pianificate.


## <a name="test-vms-that-are-running-windows"></a>Testare le macchine virtuali che eseguono WindowsTest VMs that are running Windows

### <a name="get-latteexe-onto-the-vms"></a>Ottenere latte.exe nelle macchine virtualiGet latte.exe onto the VMs

Scaricare la [versione più recente di latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b).

È consigliabile inserire latte.exe in una cartella separata, ad esempio *c:.*

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Consentire latte.exe tramite Windows Defender Firewall

Sul *destinatario*, creare una regola Consenti in Windows Defender Firewall per consentire l'arrivo del traffico latte.exe. È più semplice consentire l'intero programma latte.exe in base al nome anziché consentire porte TCP specifiche in ingresso.

Consentire latte.exe tramite Windows Defender Firewall eseguendo il comando seguente:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Ad esempio, se è stato copiato latte.exe nella cartella *c:,* questo sarebbe il comando:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Eseguire test di latenza

* Sul *ricevitore*, avviare latte.exe (eseguirlo dalla finestra CMD, non da PowerShell):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Circa 65.000 iterazioni sono abbastanza lunghe da restituire risultati rappresentativi.

    Qualsiasi numero di porta disponibile va bene.

    Se la macchina virtuale ha un indirizzo IP 10.0.0.4, il comando sarà simile al seguente:If the VM has an IP address of 10.0.0.4, the command would look like this:

    `latte -a 10.0.0.4:5005 -i 65100`

* Sul *mittente*, avviare latte.exe (eseguirlo dalla finestra CMD, non da PowerShell):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    Il comando risultante è lo stesso del destinatario, ad eccezione dell'aggiunta&nbsp;di *-c* per indicare che si tratta del *client*, o *mittente*:

    `latte -c -a 10.0.0.4:5005 -i 65100`

Attendere i risultati. A seconda della distanza tra le macchine virtuali, il completamento del test potrebbe richiedere alcuni minuti. È consigliabile iniziare con un numero inferiore di iterazioni da testare per l'esito positivo prima di eseguire test più lunghi.

## <a name="test-vms-that-are-running-linux"></a>Testare le macchine virtuali che eseguono LinuxTest VMs that are running Linux

Per testare le macchine virtuali che eseguono Linux, usare [SockPerf](https://github.com/mellanox/sockperf).

### <a name="install-sockperf-on-the-vms"></a>Installare SockPerf nelle macchine virtualiInstall SockPerf on the VMs

Nelle macchine virtuali Linux, sia *mittente* che *destinatario,* eseguire i comandi seguenti per preparare SockPerf nelle macchine virtuali. Vengono forniti comandi per le distribuzioni principali.

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

#### <a name="for-all-distros"></a>Per tutte le distro

Copiare, compilare e installare SockPerf secondo i seguenti passaggi:

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

### <a name="run-sockperf-on-the-vms"></a>Eseguire SockPerf sulle macchine virtualiRun SockPerf on the VMs

Al termine dell'installazione di SockPerf, le macchine virtuali sono pronte per eseguire i test di latenza. 

In primo luogo, avviare SockPerf sul *ricevitore*.

Qualsiasi numero di porta disponibile va bene. In questo esempio viene usata la porta 12345:In this example, we use port 12345:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Ora che il server è in ascolto, il client può iniziare a inviare pacchetti al server sulla porta su cui è in ascolto (in questo caso, 12345).

Circa 100 secondi sono abbastanza lunghi per restituire risultati rappresentativi, come illustrato nell'esempio seguente:About 100 seconds is enough long to return representative results, as shown in the following example:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Attendere i risultati. A seconda della distanza tra le macchine virtuali, il numero di iterazioni varierà. Per verificare l'esito positivo prima di eseguire test più lunghi, è consigliabile iniziare con test più brevi di circa 5 secondi.

In questo esempio SockPerf viene utilizzata una dimensione dei messaggi di 350 byte, tipica di un pacchetto medio. È possibile regolare le dimensioni superiori o inferiori per ottenere risultati che rappresentino in modo più accurato il carico di lavoro in esecuzione nelle macchine virtuali.


## <a name="next-steps"></a>Passaggi successivi
* Migliorare la latenza con un gruppo di posizionamento di prossimità di [Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
* Informazioni su come [ottimizzare](../virtual-network/virtual-network-optimize-network-bandwidth.md) la rete per le macchine virtuali per lo scenario.
* Informazioni su [come viene allocata la larghezza di banda alle macchine virtuali.](../virtual-network/virtual-machine-network-throughput.md)
* Per altre informazioni, vedere Domande frequenti sulla rete virtuale di Azure.For more [information,](../virtual-network/virtual-networks-faq.md)see Azure Virtual Network FAQ .
