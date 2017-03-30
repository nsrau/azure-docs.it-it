---
title: Ispezione dei pacchetti con Azure Network Watcher | Microsoft Docs
description: Questo articolo descrive come usare Network Watcher per eseguire un&quot;ispezione approfondita dei pacchetti raccolti da una VM
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 62fa6a6d0cccc5545b94d4ae167f2fcc7e4cd0de
ms.lasthandoff: 03/21/2017

---

# <a name="packet-inspection-with-azure-network-watcher"></a>Ispezione dei pacchetti con Azure Network Watcher

Usando la funzionalità di acquisizione di pacchetti di Network Watcher, è possibile avviare e gestire sessioni di acquisizioni nelle VM di Azure dal portale, da PowerShell, dall'interfaccia della riga di comando e a livello di codice tramite l'SDK e l'API REST. L'acquisizione di pacchetti consente di gestire scenari in cui sono necessari dati a livello di pacchetto fornendo le informazioni in un formato subito utilizzabile. Sfruttando gli strumenti disponibili gratuitamente per ispezionare i dati, è possibile esaminare le comunicazioni inviate alle e dalle VM e ottenere informazioni dettagliate sul traffico di rete. Alcuni esempi di uso dei dati di acquisizione di pacchetti includono: esame dei problemi della rete o delle applicazioni, rilevamento dell'uso improprio della rete e dei tentativi di intrusione o gestione della conformità alle normative. In questo articolo viene illustrato come aprire un file di acquisizione di pacchetti fornito da Network Watcher usando un diffuso strumento open source. Verranno anche forniti esempi che illustrano come calcolare la latenza di una connessione, identificare il traffico anomalo ed esaminare le statistiche di rete.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo descrive alcuni scenari preconfigurati in un'acquisizione di pacchetti eseguita prima. Gli scenari esaminano un'acquisizione di pacchetti per illustrare le funzionalità accessibili. Questo scenario usa [WireShark](https://www.wireshark.org/) per ispezionare l'acquisizione di pacchetti.

Questo scenario presume che sia già stata eseguita un'acquisizione di pacchetti in una macchina virtuale. Per informazioni su come creare un'acquisizione di pacchetti, vedere [Manage packet captures with the portal](network-watcher-packet-capture-manage-portal.md) (Gestire le acquisizioni di pacchetti con il portale) o [Managing Packet Captures with REST API](network-watcher-packet-capture-manage-rest.md) (Gestione di acquisizioni di pacchetti con l'API REST).

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Scenario

In questo scenario:

* Si esamina un'acquisizione di pacchetti

## <a name="calculate-network-latency"></a>Calcolare la latenza di rete

In questo scenario viene illustrato come visualizzare il tempo di round trip (RTT, Round Trip Time) di una conversazione TCP (Transmission Control Protocol) che avviene tra due endpoint.

Quando viene stabilita una connessione TCP, i primi tre pacchetti inviati nella connessione seguono un modello chiamato in genere handshake a tre livelli. Esaminando i primi due pacchetti inviati in questo handshake, una richiesta iniziale dal client e una risposta dal server, è possibile calcolare la latenza quando questa connessione viene stabilita. Questa latenza è chiamata tempo di round trip. Per altre informazioni sul protocollo TCP e l'handshake a tre livelli, vedere la risorsa seguente. https://support.microsoft.com/it-it/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip

### <a name="step-1"></a>Passaggio 1

Avviare WireShark

### <a name="step-2"></a>Passaggio 2

Caricare il file con estensione **cap** dall'acquisizione di pacchetti. Questo file si trova nel BLOB se è stato salvato in locale nella macchina virtuale, a seconda della configurazione eseguita.

### <a name="step-3"></a>Passaggio 3

Per visualizzare il tempo di round trip iniziale nelle conversazioni TCP, verranno esaminati solo i primi due pacchetti coinvolti nell'handshake TCP. Nell'handshake a tre livelli verranno usati i primi due pacchetti, ovvero [SYN] e [SYN, ACK]. Il nome deriva dai flag impostati nell'intestazione TCP. L'ultimo pacchetto nell'handshake, il pacchetto [ACK], non verrà usato in questo scenario. Il pacchetto [SYN] viene inviato dal client. Dopo che è stato ricevuto, il server invia il pacchetto [ACK] come acknowledgement della ricezione di SYN dal client. Sfruttando il fatto che la risposta del server richiede un overhead molto basso, il tempo RTT viene calcolato sottraendo dall'ora in cui il pacchetto [SYN, ACK] è stato ricevuto dal client l'ora in cui il pacchetto [SYN] è stato inviato dal client.

Usando WireShark questo valore viene calcolato automaticamente.

Per visualizzare più facilmente i primi due pacchetti nell'handshake a tre livelli TCP, verrà utilizzata la funzionalità di filtro di WireShark.

Per applicare il filtro in WireShark, espandere il segmento "Transmission Control Protocol" di un pacchetto [SYN] nell'acquisizione ed esaminare i flag impostati nell'intestazione TCP.

Poiché si vogliono filtrare i pacchetti [SYN] e [SYN, ACK], nei flag seguenti verificare che il bit Syn sia impostato su 1, quindi fare clic con il pulsante destro del mouse sul bit Syn -> Apply as Filter (Applica come filtro) -> Selezionato.

![Figura 7][7]

### <a name="step-4"></a>Passaggio 4

Dopo avere filtrato la finestra per visualizzare solo i pacchetti con il bit [SYN] impostato, è possibile selezionare facilmente le conversazioni a cui si è interessati per visualizzare il tempo RTT iniziale. Per visualizzare facilmente il tempo RTT in WireShark, è sufficiente fare clic sul menu a discesa dell'analisi "SEQ/ACK". Verrà quindi visualizzato il tempo RTT. In questo caso, il tempo RTT era 0,0022114 secondi o 2,211 ms.

![Figura 8][8]

## <a name="unwanted-protocols"></a>Protocolli indesiderati

In un'istanza di una macchina virtuale distribuita in Azure potrebbero essere in esecuzione più applicazioni. Molte di queste applicazioni comunicano in rete, a volte senza esplicita autorizzazione. Usando l'acquisizione di pacchetti per archiviare la comunicazione di rete, è possibile esaminare come le applicazioni comunicano in rete e cercare eventuali problemi.

In questo esempio viene esaminata un'acquisizione di pacchetti già eseguita per trovare protocolli indesiderati che potrebbero indicare una comunicazione non autorizzata da un'applicazione in esecuzione nel computer.

### <a name="step-1"></a>Passaggio 1

Usando la stessa acquisizione dello scenario precedente, fare clic su **Statistics** (Statistiche) > **Protocol Hierarchy** (Gerarchia protocolli)

![Menu Protocol Hierarchy (Gerarchia protocolli)][2]

Verrà visualizzata la finestra della gerarchia dei protocolli. Questa visualizzazione fornisce un elenco di tutti i protocolli usati durante la sessione di acquisizione e il numero di pacchetti trasmessi e ricevuti usando i protocolli. Si tratta di una visualizzazione utile per trovare il traffico di rete indesiderato nelle macchine virtuali o in rete.

![Gerarchia dei protocolli aperta][3]

Come si può osservare nell'acquisizione della schermata seguente, parte del traffico usava il protocollo BitTorrent, che viene usato per la condivisione di file peer-to-peer. Un amministratore non si aspetta di trovare traffico BitTorrent in queste particolari macchine virtuali. Dopo avere trovato questo tipo di traffico, è possibile rimuovere il software peer-to-peer installato in questa macchina virtuale o bloccare il traffico usando i gruppi di sicurezza di rete o un firewall. È anche possibile scegliere di eseguire le acquisizioni di pacchetti in base a una pianificazione per poter esaminare periodicamente l'uso del protocollo nelle macchine virtuali. Per un esempio di come automatizzare le attività di rete in Azure, vedere [Monitor network resources with azure automation](network-watcher-monitor-with-azure-automation.md) (Monitorare le risorse di rete con Automazione di Azure)

## <a name="finding-top-destinations-and-ports"></a>Ricerca di porte e destinazioni principali

Conoscere i tipi di traffico, gli endpoint e le porte attraverso cui avviene la comunicazione è importante quando si monitorano o si risolvono i problemi delle applicazioni e delle risorse nella rete. Utilizzando un file di acquisizione di pacchetti già usato, è possibile conoscere rapidamente le destinazioni principali con cui comunica la VM e le porte utilizzate.

### <a name="step-1"></a>Passaggio 1

Usando la stessa acquisizione dello scenario precedente, fare clic su **Statistics** (Statistiche) > **IPv4 Statistics** (Statistiche IPv4) > **Destinations and Ports** (Destinazioni e porte)

![Finestra di acquisizione di pacchetti][4]

### <a name="step-2"></a>Passaggio 2

Tra i risultati si nota una riga in cui sono presenti più connessioni alla porta 111. La porta più usata è la 3389, che è un desktop remoto. Le altre sono porte dinamiche RPC.

Anche se questo traffico può non essere significativo, la porta è però stata usata per diverse connessioni ed è sconosciuta all'amministratore.

![Figura 5][5]

### <a name="step-3"></a>Passaggio 3

Dopo avere trovato una porta anomala, è possibile filtrare l'acquisizione in base alla porta.

Il filtro in questo scenario sarà:

```
tcp.port == 111
```

Si immette il testo del filtro precedente nella casella di testo del filtro e si preme INVIO.

![Figura 6][6]

Dai risultati è possibile osservare che tutto il traffico proviene da una macchina virtuale locale nella stessa subnet. Se l'origine di questo traffico non è ancora evidente, è possibile ispezionare meglio i pacchetti per determinare perché vengono eseguite queste chiamate sulla porta 111. Con queste informazioni è possibile eseguire l'azione appropriata.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle altre funzionalità di diagnostica di Network Watcher, vedere [Azure network monitoring overview](network-watcher-monitoring-overview.md) (Panoramica del monitoraggio della rete di Azure)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png














