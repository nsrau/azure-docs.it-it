---
title: Convalidare la velocità effettiva VPN in una rete virtuale di Microsoft AzureValidate VPN throughput to a Microsoft Azure Virtual Network
description: Lo scopo di questo documento è quello di consentire a un utente di convalidare la velocità effettiva della rete dalle relative risorse locali a una macchina virtuale di Azure.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 05/29/2019
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: dcf86deda32069bf9711dbeb733dc9361e22a771
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631777"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Come convalidare la velocità effettiva della VPN verso una rete virtuale

Una connessione del gateway VPN consente di stabilire una connettività cross-premise sicura tra la rete virtuale in Azure e l'infrastruttura IT locale.

Questo articolo descrive come convalidare la velocità effettiva della rete dalle risorse locali a una macchina virtuale (VM) di Azure.

> [!NOTE]
> Questo articolo ha lo scopo di semplificare la diagnosi e la risoluzione dei problemi comuni. Se non si riesce a risolvere il problema tramite le informazioni seguenti, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="overview"></a>Panoramica

La connessione del gateway VPN coinvolge i componenti seguenti:

* Dispositivo VPN locale (visualizzare un elenco di [dispositivi VPN convalidati).](vpn-gateway-about-vpn-devices.md#devicetable)
* Internet pubblico
* Gateway VPN di Azure
* Macchina virtuale di Azure

Il diagramma seguente mostra la connettività logica di una rete locale a una rete virtuale di Azure tramite VPN.

![Connettività logica della rete del cliente alla rete MSFT tramite VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Calcolare il traffico in ingresso/in uscita massimo previsto

1. Determinare i requisiti di velocità effettiva di base dell'applicazione.
1. Determinare i limiti di velocità effettiva del gateway VPN di Azure. Per assistenza, vedere la sezione "SKU del gateway" di [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md#gwsku).
1. Determinare le [informazioni aggiuntive relative alla velocità effettiva della macchina virtuale di Azure](../virtual-machines/virtual-machines-windows-sizes.md) in base alle dimensioni della macchina virtuale.
1. Determinare la larghezza di banda del provider di servizi Internet (ISP).
1. Calcolare la velocità effettiva prevista utilizzando la larghezza di banda minima della macchina virtuale, del gateway VPN o dell'ISP. che è misurato in Megabital al secondo (/) diviso per otto (8).

Se la velocità effettiva calcolata non soddisfa i requisiti di velocità effettiva di base dell'applicazione, è necessario aumentare la larghezza di banda della risorsa identificata come collo di bottiglia. Per ridimensionare un gateway VPN di Azure, vedere [Changing a gateway SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Modifica SKU del gateway). Per ridimensionare una macchina virtuale, vedere [Ridimensionare una VM ](../virtual-machines/virtual-machines-windows-resize-vm.md). Se non si verifica la larghezza di banda Internet prevista, è possibile contattare anche l'ISP.

> [!NOTE]
> La velocità effettiva del gateway VPN è un'aggregazione di tutte le connessioni da sito a sito, da VNET a VNET o da punto a sito.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Convalidare la velocità effettiva della rete mediante gli strumenti di prestazioni

È consigliabile eseguire questa convalida al di fuori delle ore di punta, poiché la saturazione della velocità effettiva del tunnel VPN durante il test non fornisce risultati accurati.

Lo strumento usato per questo test è iPerf, che è supportato sia su Windows sia su Linux e dispone di modalità client e server. È limitato a 3 Gbps per le macchine virtuali Windows.It is limited to 3Gbps for Windows VMs.

Questo strumento non esegue operazioni di lettura/scrittura su disco, ma produce solo traffico TCP generato automaticamente da un'entità finale all'altra. Genera statistiche basate sulla sperimentazione che misura la larghezza di banda disponibile tra i nodi client e server. Durante il test tra due nodi, un nodo funge da server e l'altro nodo funge da client. Una volta completato questo test, è consigliabile invertire i ruoli dei nodi per testare sia il caricamento che il download della velocità effettiva in entrambi i nodi.

### <a name="download-iperf"></a>Scaricare iPerf

Scarica [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Per dettagli, vedere la [documentazione di iPerf](https://iperf.fr/iperf-doc.php).

 > [!NOTE]
 > I prodotti di terze parti descritti in questo articolo sono prodotti da società indipendenti da Microsoft. Microsoft non fornisce alcuna garanzia, implicita o esplicita, sulle prestazioni o sull'affidabilità di questi prodotti.

### <a name="run-iperf-iperf3exe"></a>Eseguire iPerf (iperf3.exe)

1. Abilitare una regola NSG/ACL che consenta il traffico (per il test dell'indirizzo IP pubblico nella macchina virtuale di Azure).

1. In entrambi i nodi abilitare un'eccezione firewall per la porta 5001.

   **Windows:** eseguire questo comando come amministratore.

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Per rimuovere la regola al termine del test, eseguire questo comando:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Linux di Azure:Azure Linux:** Le immagini Linux di Azure hanno firewall permissivi. Se un'applicazione è in ascolto su una porta, è consentito il passaggio del traffico. Per le immagini personalizzate protette potrebbero essere necessarie porte aperte in modo esplicito. Firewall a livello di sistema operativo Linux comuni includono `iptables`, `ufw` o `firewalld`.

1. Sul nodo del server passare alla directory in cui è stato estratto iperf3.exe. Quindi eseguire iPerf in modalità server e impostarlo per l'ascolto sulla porta 5001 come i seguenti comandi:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > La porta 5001 è personalizzabile per tenere conto di particolari restrizioni del firewall nell'ambiente.

1. Nel nodo client passare alla directory in cui è stato estratto lo strumento iperf e quindi eseguire questo comando:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   Il client dirige trenta secondi di traffico sulla porta 5001, al server. Il flag '-P' indica che stiamo effettuando 32 connessioni simultanee al nodo del server.

   La schermata seguente mostra l'output di questo esempio:

   ![Output](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. (FACOLTATIVO) Per mantenere i risultati del test, eseguire questo comando:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. Dopo aver completato i passaggi precedenti, eseguire gli stessi passaggi con i ruoli invertiti, in modo che il nodo del server sarà ora il nodo client e viceversa.

> [!Note]
> Iperf non è l'unico strumento. [NTTTCP è una soluzione alternativa per il test](https://docs.microsoft.com/azure/virtual-network/virtual-network-bandwidth-testing).

## <a name="test-vms-running-windows"></a>Testare le macchine virtuali che eseguono WindowsTest VMs running Windows

### <a name="load-latteexe-onto-the-vms"></a>Caricare Latte.exe nelle macchine virtualiLoad Latte.exe onto the VMs

Scarica la versione più recente di [Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Valutare la possibilità di inserire Latte.exe in una cartella separata, ad esempio`c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Allow Latte.exe tramite il firewall di Windows

Sul destinatario, creare una regola Consenti in Windows Firewall per consentire l'arrivo del traffico Latte.exe. È più semplice consentire l'intero programma Latte.exe in base al nome anziché consentire porte TCP specifiche in ingresso.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Consenti Latte.exe tramite Windows Firewall in questo modo

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Ad esempio, se è stato copiato latte.exe nella cartella "c:"tools", questo sarebbe il comando

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Eseguire test di latenza

Avviare latte.exe sul RECEIVER (eseguito da CMD, non da PowerShell):

`latte -a <Receiver IP address>:<port> -i <iterations>`

Circa 65k iterazioni è abbastanza lungo per restituire risultati rappresentativi.

Qualsiasi numero di porta disponibile va bene.

Se la macchina virtuale ha un indirizzo IP 10.0.0.4,

`latte -c -a 10.0.0.4:5005 -i 65100`

Avviare latte.exe su SENDER (eseguito da CMD, non da PowerShell)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

Il comando risultante è lo stesso del ricevitore, tranne che con l'aggiunta di "-c" per indicare che si tratta del "client" o del mittente

`latte -c -a 10.0.0.4:5005 -i 65100`

Attendere i risultati. A seconda della distanza tra le macchine virtuali, il completamento delle macchine virtuali potrebbe richiedere alcuni minuti. È consigliabile iniziare con un numero inferiore di iterazioni da testare per l'esito positivo prima di eseguire test più lunghi.

## <a name="test-vms-running-linux"></a>Testare le macchine virtuali che eseguono LinuxTest VMs running Linux

Usare [SockPerf](https://github.com/mellanox/sockperf) per testare le macchine virtuali.

### <a name="install-sockperf-on-the-vms"></a>Installare SockPerf nelle macchine virtualiInstall SockPerf on the VMs

On the Linux VMs (both SENDER and RECEIVER), run these commands to prepare SockPerf on your VMs:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS / RHEL - Installa GIT e altri strumenti utili

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu - Installa GIT e altri strumenti utili

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash - tutti

Dalla riga di comando bash (presuppone che git sia installato)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

Make è più lento, potrebbe richiedere alcuni minuti

`make`

Fare l'installazione è veloce

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>Eseguire SockPerf sulle macchine virtualiRun SockPerf on the VMs

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>Comandi di esempio dopo l'installazione. Server/Ricevitore - presuppone che l'INDIRIZZO IP del server sia 10.0.0.4

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>Client - presuppone che l'INDIRIZZO IP del server sia 10.0.0.4

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> Assicurarsi che non siano presenti hop intermedi (ad esempio Virtual Appliance) durante il test della velocità effettiva tra la macchina virtuale e il gateway.
> Se ci sono risultati scarsi (in termini di velocità effettiva complessiva) provenienti dai test iPERF/NTTTCP sopra riportati, fare riferimento al seguente articolo per comprendere i fattori chiave alla base delle possibili cause principali del problema:https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

In particolare, l'analisi delle tracce di acquisizione dei pacchetti (Wireshark/Network Monitor) raccolte in parallelo dal client e dal server durante tali test aiuterà nelle valutazioni delle prestazioni non riuscite. Queste tracce possono includere perdita di pacchetti, latenza elevata, dimensioni MTU. frammentazione, finestra TCP 0, frammenti Fuori ordine e così via.

## <a name="address-slow-file-copy-issues"></a>Risolvere i problemi di esecuzione lenta della copia dei file

Anche se la velocità effettiva complessiva valutata con i passaggi precedenti (iPERF/NTTTCP/etc.) era buona, è possibile che si verifichi un rallentamento della gestione dei file quando si utilizza Esplora risorse o che si estrae tramite una sessione RDP. Questo problema è in genere dovuto a uno o a entrambi i fattori seguenti:

* Le applicazioni di copia dei file, ad esempio Esplora risorse e RDP, non usano più thread durante la copia dei file. Per prestazioni ottimali, usare un'applicazione per la copia dei file multithread, ad esempio [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx), per copiare i file a 16 o 32 thread. Per modificare il numero di filettatura per la copia di file in Richcopy, fare clic su**Opzioni** > di copia **azione** > **Copia file copia**.

   ![Problemi di esecuzione lenta della copia dei file](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Non tutte le applicazioni funzionano allo stesso modo e non tutte le applicazioni/processi utilizzano tutti i thread. Se si esegue il test, è possibile che alcuni thread siano vuoti e non forniscano risultati accurati della velocità effettiva.
   > Per verificare le prestazioni di trasferimento dei file dell'applicazione, utilizzare più thread aumentando il numero di thread in successione o la diminuzione per individuare la velocità effettiva ottimale dell'applicazione o del trasferimento di file.

* Velocità di lettura/scrittura disco macchina virtuale insufficiente. Per altre informazioni, vedere [Risoluzione dei problemi di Archiviazione di Azure](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Interfaccia con connessione rivolta all'esterno del dispositivo locale

Le subnet degli intervalli locali che si desidera che Azure raggiungano tramite VPN nel gateway di rete locale. Contemporaneamente, definire lo spazio degli indirizzi VNET in Azure nel dispositivo locale.

* **Gateway basato su route**: Il criterio o il selettore del traffico per le VPN basate su route viene configurato come any-to-any (o caratteri jolly).

* **Gateway basato su criteri:** le VPN basate su criteri crittografano e dirigono i pacchetti tramite tunnel IPsec in base alle combinazioni di prefissi di indirizzo tra la rete locale e la rete virtuale di Azure.Policy based Gateway : Policy-based VPNs encrypt and direct packets through IPsec tunnels based on the combinations of address prefixes between your on-premises network and the Azure VNet. I criteri (o selettore di traffico) vengono in genere definiti come un elenco di accesso nella configurazione VPN.

* **UsePolicyBasedTrafficSelector** connections: ("UsePolicyBasedTrafficSelectors" per $True in una connessione configurerà il gateway VPN di Azure per la connessione al firewall VPN basato su criteri in locale. Se si abilita PolicyBasedTrafficSelectors, è necessario assicurarsi che il dispositivo VPN disponga dei selettori di traffico corrispondenti definiti con tutte le combinazioni di prefissi di rete locale (gateway di rete locale) da e verso i prefissi della rete virtuale di Azure, anziché qualsiasi a qualsiasi.

Una configurazione inappropriata può portare a frequenti disconnessioni all'interno del tunnel, cadute di pacchetti, velocità effettiva non valida e latenza.

## <a name="check-latency"></a>Latenza di controllo

È possibile controllare la latenza utilizzando gli strumenti seguenti:You can check latency by using the following tools:

* WinMTR
* TCPTraceroute
* `ping`e `psping` (Questi strumenti possono fornire una buona stima di RTT, ma non possono essere utilizzati in tutti i casi.)

![Latenza di controllo](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

Se si nota un picco di latenza elevata in uno qualsiasi degli hop prima di entrare nella backbone di MS Network, si consiglia di procedere con ulteriori indagini con il provider di servizi Internet.

Se si nota un picco di latenza insolito e di grandi dimensioni dai hop all'interno di "msn.net", contattare il supporto MS per ulteriori indagini.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni o assistenza, consultare il seguente collegamento:

* [Supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
