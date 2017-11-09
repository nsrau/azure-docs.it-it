---
title: "Convalidare la velocità effettiva della VPN verso una rete virtuale di Microsoft Azure | Microsoft Docs"
description: "Lo scopo di questo documento è quello di consentire a un utente di convalidare la velocità effettiva della rete dalle relative risorse locali a una macchina virtuale di Azure."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: jasmc
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2017
ms.author: radwiv;chadmat;genli
ms.openlocfilehash: 3a1a6e2acd2ff40c2b35a6099f8a9fc7eb104bbc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Come convalidare la velocità effettiva della VPN verso una rete virtuale

Una connessione del gateway VPN consente di stabilire una connettività cross-premise sicura tra la rete virtuale in Azure e l'infrastruttura IT locale.

Questo articolo descrive come convalidare la velocità effettiva della rete dalle risorse locali a una macchina virtuale (VM) di Azure. Fornisce inoltre informazioni aggiuntive sulla risoluzione dei problemi.

>[!NOTE]
>Questo articolo ha lo scopo di semplificare la diagnosi e la risoluzione dei problemi comuni. Se non si riesce a risolvere il problema tramite le informazioni seguenti, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="overview"></a>Panoramica

La connessione del gateway VPN coinvolge i componenti seguenti:

- Dispositivo VPN locale (visualizzare un elenco di [dispositivi VPN convalidati](vpn-gateway-about-vpn-devices.md#devicetable)).
- Internet pubblico
- Gateway VPN di Azure
- Macchina virtuale di Azure

Il diagramma seguente mostra la connettività logica di una rete locale a una rete virtuale di Azure tramite VPN.

![Connettività logica della rete del cliente alla rete MSFT tramite VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Calcolare il traffico in ingresso/in uscita massimo previsto

1.  Determinare i requisiti di velocità effettiva di base dell'applicazione.
2.  Determinare i limiti di velocità effettiva del gateway VPN di Azure. Per altre informazioni, vedere la sezione "Velocità effettiva aggregata stimata per tipo di VPN e SKU" in [Pianificazione e progettazione per il gateway VPN](vpn-gateway-plan-design.md).
3.  Determinare le [informazioni aggiuntive relative alla velocità effettiva della macchina virtuale di Azure](../virtual-machines/virtual-machines-windows-sizes.md) in base alle dimensioni della macchina virtuale.
4.  Determinare la larghezza di banda del provider di servizi Internet (ISP).
5.  Eseguire il calcolo di velocità effettiva prevista - larghezza di banda minima di (macchina virtuale, gateway, ISP) * 0,8.

Se la velocità effettiva calcolata non soddisfa i requisiti di velocità effettiva di base dell'applicazione, è necessario aumentare la larghezza di banda della risorsa identificata come collo di bottiglia. Per ridimensionare un gateway VPN di Azure, vedere [Changing a gateway SKU](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Modifica SKU del gateway). Per ridimensionare una macchina virtuale, vedere [Ridimensionare una VM ](../virtual-machines/virtual-machines-windows-resize-vm.md). Se non si dispone della larghezza di banda Internet prevista, è consigliabile anche contattare il provider di servizi Internet.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Convalidare la velocità effettiva della rete mediante gli strumenti di prestazioni

È consigliabile eseguire questa convalida al di fuori delle ore di punta, poiché la saturazione della velocità effettiva del tunnel VPN durante il test non fornisce risultati accurati.

Lo strumento usato per questo test è iPerf, che è supportato sia su Windows sia su Linux e dispone di modalità client e server. Per le macchine virtuali di Windows è limitato a 3 Gbps.

Questo strumento non esegue operazioni di lettura/scrittura su disco, ma produce solo traffico TCP generato automaticamente da un'entità finale all'altra. Genera statistiche in base alla sperimentazione che misura la larghezza di banda disponibile tra i nodi client e server. Durante l'esecuzione del test tra due nodi, uno agisce come server e l'altro come client. Al termine di questo test, è consigliabile invertire i ruoli per testare la velocità effettiva di caricamento e download su entrambi i nodi.

### <a name="download-iperf"></a>Scaricare iPerf
Eseguire il download di [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Per dettagli, vedere la [documentazione di iPerf](https://iperf.fr/iperf-doc.php).

 >[!NOTE]
 >Gli strumenti di terze parti citati in questo articolo sono prodotti da società indipendenti di Microsoft. Microsoft non fornisce alcuna garanzia, implicita o esplicita, sulle prestazioni o sull'affidabilità di questi prodotti.
 >
 >

### <a name="run-iperf-iperf3exe"></a>Eseguire iPerf (iperf3.exe)
1. Abilitare una regola NSG/ACL che consenta il traffico (per il test dell'indirizzo IP pubblico nella macchina virtuale di Azure).

2. In entrambi i nodi abilitare un'eccezione firewall per la porta 5001.

    **Windows:** eseguire questo comando come amministratore.

    ```CMD
    netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
    ```

    Per rimuovere la regola al termine del test, eseguire questo comando:

    ```CMD
    netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
    ```
    </br>
    **Linux di Azure**: le immagini Linux di Azure dispongono di firewall permissivi. Se un'applicazione è in ascolto su una porta, è consentito il passaggio del traffico. Per le immagini personalizzate protette potrebbero essere necessarie porte aperte in modo esplicito. Firewall a livello di sistema operativo Linux comuni includono `iptables`, `ufw` o `firewalld`.

3. Sul nodo del server passare alla directory in cui è stato estratto iperf3.exe. Eseguire quindi iPerf in modalità server e impostarlo per l'ascolto sulla porta 5001, come nei comandi seguenti:

     ```CMD
     cd c:\iperf-3.1.2-win65

     iperf3.exe -s -p 5001
     ```

4. Nel nodo client passare alla directory in cui è stato estratto lo strumento iperf e quindi eseguire questo comando:

    ```CMD
    iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
    ```

    Il client indirizza il traffico sulla porta 5001 verso il server per 30 secondi. Il flag '-P ' indica che vengono usate 32 connessioni simultanee per il nodo del server.

    La schermata seguente mostra l'output di questo esempio:

    ![Output](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

5. (FACOLTATIVO) Per mantenere i risultati del test, eseguire questo comando:

    ```CMD
    iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
    ```

6. Dopo aver completato i passaggi precedenti, eseguire la stessa procedura con i ruoli invertiti, in modo che il nodo del server sarà il client e viceversa.

## <a name="address-slow-file-copy-issues"></a>Risolvere i problemi di esecuzione lenta della copia dei file
È possibile che la copia dei file venga eseguita lentamente quando si usa Esplora risorse o il trascinamento della selezione tramite una sessione RDP. Questo problema è in genere dovuto a uno o a entrambi i fattori seguenti:

- Le applicazioni di copia dei file, ad esempio Esplora risorse e RDP, non usano più thread durante la copia dei file. Per prestazioni ottimali, usare un'applicazione per la copia dei file multithread, ad esempio [Richcopy](https://technet.microsoft.com/en-us/magazine/2009.04.utilityspotlight.aspx), per copiare i file a 16 o 32 thread. Per modificare il numero di thread per la copia dei file in Richcopy, fare clic su **Azione** > **Opzioni copia** > **Copia dei file**.<br><br>
![Problemi di esecuzione lenta della copia dei file](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>
- Velocità di lettura/scrittura disco macchina virtuale insufficiente. Per altre informazioni, vedere [Risoluzione dei problemi di Archiviazione di Azure](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Interfaccia con connessione rivolta all'esterno del dispositivo locale
Se l'indirizzo IP per Internet del dispositivo VPN locale è incluso nella definizione della [rete locale](vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) in Azure, potrebbe non essere possibile vedere la VPN, potrebbero verificarsi sporadiche disconnessioni o problemi di prestazioni.

## <a name="checking-latency"></a>Verifica della latenza
Usare tracert per tenere traccia del dispositivo perimetrale di Microsoft Azure per determinare se sono presenti eventuali ritardi con oltre 100 ms tra hop.

Dalla rete locale eseguire *tracert* all'indirizzo VIP del gateway o della macchina virtuale di Azure. Quando viene restituito solo un asterisco (*), vuol dire che è stato raggiunto il dispositivo periferico di Azure. Quando vengono restituiti nomi DNS che includono "MSN", vuol dire che è stata raggiunta la backbone di Microsoft.<br><br>
![Controllo della latenza](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

## <a name="next-steps"></a>Passaggi successivi
Per maggiori informazioni o assistenza, consultare i collegamenti seguenti:

- [Ottimizzare la velocità effettiva di rete per le macchine virtuali di Azure](../virtual-network/virtual-network-optimize-network-bandwidth.md)
- [Supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
