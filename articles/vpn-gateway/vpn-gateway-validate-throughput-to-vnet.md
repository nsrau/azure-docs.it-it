---
title: Convalidare la velocità effettiva della VPN verso una rete virtuale di Microsoft Azure | Microsoft Docs
description: Lo scopo di questo documento è quello di consentire a un utente di convalidare la velocità effettiva della rete dalle relative risorse locali a una macchina virtuale di Azure.
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 05/29/2019
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: 9c2f50c49037305663330a3c455e40291b9e6242
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058807"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Come convalidare la velocità effettiva della VPN verso una rete virtuale

Una connessione del gateway VPN consente di stabilire una connettività cross-premise sicura tra la rete virtuale in Azure e l'infrastruttura IT locale.

Questo articolo descrive come convalidare la velocità effettiva della rete dalle risorse locali a una macchina virtuale (VM) di Azure.

> [!NOTE]
> Questo articolo ha lo scopo di semplificare la diagnosi e la risoluzione dei problemi comuni. Se non si riesce a risolvere il problema tramite le informazioni seguenti, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="overview"></a>Panoramica

La connessione del gateway VPN coinvolge i componenti seguenti:

* Dispositivo VPN locale (visualizzare un elenco di [dispositivi VPN](vpn-gateway-about-vpn-devices.md#devicetable)convalidati).
* Internet pubblico
* Gateway VPN di Azure
* Macchina virtuale di Azure

Il diagramma seguente mostra la connettività logica di una rete locale a una rete virtuale di Azure tramite VPN.

![Connettività logica della rete del cliente alla rete MSFT tramite VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Calcolare il traffico in ingresso/in uscita massimo previsto

1. Determinare i requisiti di velocità effettiva di base dell'applicazione.
1. Determinare i limiti di velocità effettiva del gateway VPN di Azure. Per informazioni, vedere la sezione "SKU del gateway" di [informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md#gwsku).
1. Determinare le [informazioni aggiuntive relative alla velocità effettiva della macchina virtuale di Azure](../virtual-machines/virtual-machines-windows-sizes.md) in base alle dimensioni della macchina virtuale.
1. Determinare la larghezza di banda del provider di servizi Internet (ISP).
1. Calcolare la velocità effettiva prevista selezionando la larghezza di banda minima della macchina virtuale, del gateway VPN o del provider di servizi Internet. misurato in megabit al secondo (/) diviso 8 (8).

Se la velocità effettiva calcolata non soddisfa i requisiti di velocità effettiva di base dell'applicazione, è necessario aumentare la larghezza di banda della risorsa identificata come collo di bottiglia. Per ridimensionare un gateway VPN di Azure, vedere [Changing a gateway SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Modifica SKU del gateway). Per ridimensionare una macchina virtuale, vedere [Ridimensionare una VM ](../virtual-machines/virtual-machines-windows-resize-vm.md). Se non si sta riscontrando la larghezza di banda Internet prevista, è anche possibile contattare il provider di servizi Internet.

> [!NOTE]
> La velocità effettiva del gateway VPN è un'aggregazione di tutte le connessioni da punto a sito o Site-to-Site\VNET-to-VNET.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Convalidare la velocità effettiva della rete mediante gli strumenti di prestazioni

È consigliabile eseguire questa convalida al di fuori delle ore di punta, poiché la saturazione della velocità effettiva del tunnel VPN durante il test non fornisce risultati accurati.

Lo strumento usato per questo test è iPerf, che è supportato sia su Windows sia su Linux e dispone di modalità client e server. È limitato a 3Gbps per le macchine virtuali Windows.

Questo strumento non esegue operazioni di lettura/scrittura su disco, ma produce solo traffico TCP generato automaticamente da un'entità finale all'altra. Genera statistiche basate sulla sperimentazione che misura la larghezza di banda disponibile tra i nodi client e server. Quando si esegue il test tra due nodi, un nodo funge da server e l'altro nodo funge da client. Una volta completato il test, è consigliabile invertire i ruoli dei nodi per testare la velocità effettiva di caricamento e download in entrambi i nodi.

### <a name="download-iperf"></a>Scaricare iPerf

Eseguire il download di [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Per dettagli, vedere la [documentazione di iPerf](https://iperf.fr/iperf-doc.php).

 > [!NOTE]
 > I prodotti di terze parti descritti in questo articolo sono prodotti da società indipendenti da Microsoft. Microsoft non fornisce alcuna garanzia, implicita o esplicita, in merito alle prestazioni o all'affidabilità di questi prodotti.

### <a name="run-iperf-iperf3exe"></a>Eseguire iPerf (iperf3.exe)

1. Abilitare una regola NSG/ACL che consenta il traffico (per il test dell'indirizzo IP pubblico nella macchina virtuale di Azure).

1. In entrambi i nodi abilitare un'eccezione firewall per la porta 5001.

   **Windows:** Eseguire il comando seguente come amministratore:

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Per rimuovere la regola al termine del test, eseguire questo comando:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Linux di Azure:** Le immagini Linux di Azure dispongono di firewall permissivi. Se un'applicazione è in ascolto su una porta, è consentito il passaggio del traffico. Per le immagini personalizzate protette potrebbero essere necessarie porte aperte in modo esplicito. Firewall a livello di sistema operativo Linux comuni includono `iptables`, `ufw` o `firewalld`.

1. Sul nodo del server passare alla directory in cui è stato estratto iperf3.exe. Eseguire quindi iPerf in modalità server e impostarlo in modo che sia in ascolto sulla porta 5001 come i comandi seguenti:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > La porta 5001 è personalizzabile per tenere conto di particolari restrizioni del firewall nell'ambiente in uso.

1. Nel nodo client passare alla directory in cui è stato estratto lo strumento iperf e quindi eseguire questo comando:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   Il client indirizza al server trenta secondi di traffico sulla porta 5001. Il flag '-P ' indica che sono in esecuzione 32 connessioni simultanee al nodo del server.

   La schermata seguente mostra l'output di questo esempio:

   ![Output](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. (FACOLTATIVO) Per mantenere i risultati del test, eseguire questo comando:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. Dopo aver completato i passaggi precedenti, eseguire gli stessi passaggi con i ruoli invertiti, in modo che il nodo del server sarà il nodo client e viceversa.

> [!Note]
> Iperf non è l'unico strumento. [NTTTCP è una soluzione alternativa per](https://docs.microsoft.com/azure/virtual-network/virtual-network-bandwidth-testing)il testing.

## <a name="test-vms-running-windows"></a>Testare le VM che eseguono Windows

### <a name="load-latteexe-onto-the-vms"></a>Caricare latte. exe nelle VM

Scaricare la versione più recente di [latte. exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Prendere in considerazione l'inserimento di latte. exe in una cartella separata, ad esempio`c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Consenti latte. exe attraverso Windows Firewall

Nel ricevitore creare una regola di autorizzazione per la Windows Firewall per consentire l'arrivo del traffico latte. exe. È più semplice consentire l'intero programma di caffellatte. exe per nome, anziché consentire porte TCP specifiche in ingresso.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Consenti a caffellatte. exe di usare la Windows Firewall come questa

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Ad esempio, se è stato copiato latte. exe nella cartella "c:\Tools", si tratta del comando

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Esegui test di latenza

Avviare latte. exe sul ricevitore (eseguire da CMD, non da PowerShell):

`latte -a <Receiver IP address>:<port> -i <iterations>`

Le iterazioni di 65K sono abbastanza lunghe per restituire risultati rappresentativi.

Qualsiasi numero di porta disponibile funziona correttamente.

Se la macchina virtuale ha un indirizzo IP di 10.0.0.4, avrà un aspetto simile al seguente:

`latte -c -a 10.0.0.4:5005 -i 65100`

Avviare latte. exe nel mittente (eseguire da CMD, non da PowerShell)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

Il comando risultante è identico a quello del ricevitore, tranne con l'aggiunta di "-c" per indicare che questo è il "client" o il mittente

`latte -c -a 10.0.0.4:5005 -i 65100`

Attendere i risultati. A seconda della distanza tra le macchine virtuali, potrebbero essere necessari alcuni minuti per il completamento. Si consiglia di iniziare con meno iterazioni per verificare la riuscita prima di eseguire test più lunghi.

## <a name="test-vms-running-linux"></a>Testare le macchine virtuali che eseguono Linux

Usare [SockPerf](https://github.com/mellanox/sockperf) per testare le VM.

### <a name="install-sockperf-on-the-vms"></a>Installare SockPerf nelle VM

Nelle VM Linux (mittente e ricevitore) eseguire questi comandi per preparare SockPerf nelle macchine virtuali:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS/RHEL: installare GIT e altri strumenti utili

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu-installare GIT e altri strumenti utili

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash-tutto

Dalla riga di comando bash (presuppone che git sia installato)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

Make è più lento. potrebbero essere necessari alcuni minuti

`make`

Make install è veloce

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>Eseguire SockPerf nelle macchine virtuali

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>Comandi di esempio dopo l'installazione. Server/ricevitore-presuppone che l'indirizzo IP del server sia 10.0.0.4

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>Client-presuppone che l'indirizzo IP del server sia 10.0.0.4

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> Assicurarsi che non siano presenti hop intermedi, ad esempio appliance virtuale, durante il test della velocità effettiva tra la macchina virtuale e il gateway.
> Se sono presenti risultati scarsi (in termini di velocità effettiva complessiva) provenienti dai test iPERF/NTTTCP precedenti, vedere l'articolo seguente per comprendere i fattori chiave alla base delle possibili cause principali del problema: https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

In particolare, l'analisi delle tracce di acquisizione pacchetti (Wireshark/Network Monitor) raccolte in parallelo da client e server durante tali test consentirà di valutare le prestazioni negative. Queste tracce possono includere perdite di pacchetti, latenza elevata, dimensioni MTU. frammentazione, finestra TCP 0, frammenti non ordinati e così via.

## <a name="address-slow-file-copy-issues"></a>Risolvere i problemi di esecuzione lenta della copia dei file

Anche se la velocità effettiva complessiva valutata con i passaggi precedenti (iPERF/NTTTCP/e così via) è stata corretta, è possibile che si verifichi un rallentamento dei file quando si usa Esplora risorse oppure si trascina e si elimina una sessione RDP. Questo problema è in genere dovuto a uno o a entrambi i fattori seguenti:

* Le applicazioni di copia dei file, ad esempio Esplora risorse e RDP, non usano più thread durante la copia dei file. Per prestazioni ottimali, usare un'applicazione per la copia dei file multithread, ad esempio [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx), per copiare i file a 16 o 32 thread. Per modificare il numero di thread per la copia dei file in Richcopy, fare clic su **Azione** > **Opzioni copia** > **Copia dei file**.

   ![Problemi di copia dei file lenti](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Non tutte le applicazioni funzionano allo stesso tempo e non tutte le applicazioni/processi utilizzano tutti i thread. Se si esegue il test, si noterà che alcuni thread sono vuoti e non forniscono risultati accurati sulla velocità effettiva.
   > Per controllare le prestazioni del trasferimento di file dell'applicazione, usare il multithreading aumentando il numero di thread in successione o riduzione per trovare la velocità effettiva ottimale dell'applicazione o il trasferimento di file.

* Velocità di lettura/scrittura disco macchina virtuale insufficiente. Per altre informazioni, vedere [Risoluzione dei problemi di Archiviazione di Azure](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Interfaccia con connessione rivolta all'esterno del dispositivo locale

Sono state citate le subnet degli intervalli locali che si desidera che Azure raggiunga tramite VPN sul gateway di rete locale. Definire contemporaneamente lo spazio di indirizzi della VNET in Azure al dispositivo locale.

* **Gateway basato su Route**: I criteri o selettori di traffico per le VPN basate su route vengono configurati come any-to-any (o caratteri jolly).

* **Gateway basato su criteri**: Le VPN basate su criteri crittografano e reindirizzano i pacchetti tramite tunnel IPsec basati su combinazioni di prefissi di indirizzo tra la rete locale e la rete virtuale di Azure. I criteri (o selettore di traffico) vengono in genere definiti come un elenco di accesso nella configurazione VPN.

* Connessioni **UsePolicyBasedTrafficSelector** : ("UsePolicyBasedTrafficSelectors" per $true in una connessione configurerà il gateway VPN di Azure per connettersi al firewall VPN basato su criteri in locale. Se si Abilita PolicyBasedTrafficSelectors, è necessario assicurarsi che il dispositivo VPN disponga dei selettori di traffico corrispondenti definiti con tutte le combinazioni dei prefissi della rete locale (gateway di rete locale) da e verso i prefissi della rete virtuale di Azure, anziché Any-to-any.

Una configurazione non appropriata può causare frequenti disconnessioni all'interno del tunnel, gocce di pacchetti, velocità effettiva e latenza errate.

## <a name="check-latency"></a>Verifica latenza

È possibile controllare la latenza utilizzando gli strumenti seguenti:

* WinMTR
* TCPTraceroute
* `ping`e `psping` (questi strumenti possono fornire una stima corretta di RTT, ma non possono essere usati in tutti i casi).

![Verifica latenza](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

Se si nota un picco a latenza elevata in uno degli hop prima di immettere la backbone di rete MS, è consigliabile procedere con ulteriori indagini con il provider di servizi Internet.

Se viene rilevato un picco di latenza insolito elevato da hop in "msn.net", contattare il supporto tecnico MS per ulteriori indagini.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni o per la guida, consultare il collegamento seguente:

* [Supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
