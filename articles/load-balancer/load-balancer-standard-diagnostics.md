---
title: Diagnostica in Azure Load Balancer Standard | Microsoft Docs
description: Uso delle metriche e delle informazioni sull'integrità disponibili per la diagnostica per Azure Load Balancer Standard.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/27/2018
ms.author: Kumud
ms.openlocfilehash: 43945dc8810151eb701aa9e1aa1be47d4fbb0491
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125645"
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Metriche e diagnostica dell'integrità per Load Balancer Standard

Azure Load Balancer Standard fornisce le funzionalità di diagnostica seguenti per le proprie risorse:
* **Metriche multidimensionali**: offre nuove funzionalità di diagnostica multidimensionale per le configurazioni di Load Balancer sia pubbliche che interne. Così è possibile monitorare, gestire e risolvere i problemi delle risorse di Load Balancer.

* **Integrità risorse**: la pagina di Load Balancer nel portale di Azure e la pagina Integrità risorse (in Monitoraggio) espongono la sezione di integrità delle risorse per la configurazione pubblica di bilanciamento del carico di Load Balancer Standard.

Questo articolo offre una breve panoramica di queste funzionalità e illustra come usarle per Load Balancer Standard.

## <a name = "MultiDimensionalMetrics"></a>Metriche multidimensionali

Azure Load Balancer fornisce le nuove metriche multidimensionali tramite la nuova funzionalità Metriche di Azure (anteprima) nel portale di Azure e consente di ottenere informazioni dettagliate di diagnostica in tempo reale sulle risorse di Load Balancer. 

Le varie configurazioni Load Balancer Standard forniscono le seguenti metriche:

| Metrica | Tipo di risorsa | Descrizione | Aggregazione consigliata |
| --- | --- | --- | --- |
| Disponibilità VIP (disponibilità del percorso dati) | Bilanciamento del carico pubblico | Load Balancer Standard esercita continuamente il percorso dati dall'interno di un'area al front-end di Load Balancer e infine allo stack SDN che supporta la macchina virtuale. Finché sono presenti istanze integre, la misurazione segue lo stesso percorso del traffico con bilanciamento del carico dell'applicazione. Viene anche convalidato il percorso dati usato dai clienti. La misurazione è invisibile all'applicazione e non interferisce con altre operazioni.| Media |
| Disponibilità DIP (stato del probe di integrità) |  Servizio di bilanciamento del carico interno e pubblico | Load Balancer Standard usa un servizio di probe dell'integrità distribuito che monitora l'integrità dell'endpoint dell'applicazione in base alle impostazioni di configurazione. Questa metrica offre una visualizzazione filtrata, aggregata o per endpoint di ogni endpoint dell'istanza nel pool di Load Balancer. In questo modo è possibile visualizzare l'integrità dell'applicazione rilevata da Load Balancer, in base alla configurazione del probe di integrità. |  Media |
| Pacchetti SYN (sincronizzazione) |  Bilanciamento del carico pubblico | Load Balancer Standard non termina le connessioni TCP (Transmission Control Protocol), né interagisce con i flussi di pacchetti TCP o UDP. I flussi e i relativi handshake sono sempre tra l'origine e l'istanza VM. Per risolvere meglio i problemi degli scenari del protocollo TCP, è possibile usare contatori di pacchetti SYN per determinare quanti tentativi di connessione TCP vengono eseguiti. La metrica indica il numero di pacchetti SYN TCP ricevuti.| Media |
| Connessioni SNAT |  Load Balancer pubblico |Load Balancer Standard segnala il numero di flussi in uscita mascherati per il front-end dell'indirizzo IP pubblico. Le porte Source Network Address Translation (SNAT) sono una risorsa esauribile. Questa metrica può indicare l'uso che l'applicazione fa di SNAT per i flussi originati in uscita. Vengono segnalati i contatori per i flussi SNAT con esito positivo e negativo, che è possibile usare per risolvere i problemi e comprendere l'integrità dei flussi in uscita.| Media |
| Contatori di byte |  Servizio di bilanciamento del carico interno e pubblico | Load Balancer Standard restituisce i dati elaborati per ogni front-end.| Media |
| Contatori di pacchetti |  Servizio di bilanciamento del carico interno e pubblico | Load Balancer Standard restituisce i pacchetti elaborati per ogni front-end.| Media |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Consente di visualizzare le metriche di Load Balancer nel portale di Azure

Il portale di Azure espone le metriche di Load Balancer nella pagina Metriche (anteprima), disponibile sia nella pagina delle risorse di Load Balancer per una specifica risorsa che nella pagina Monitoraggio di Azure. 

Per visualizzare le metriche delle risorse di Load Balancer Standard:
1. Accedere alla pagina Metriche (anteprima) ed eseguire una di queste operazioni:
   * Nella pagina delle risorse di Load Balancer, selezionare il tipo di metrica nell'elenco a discesa.
   * Nella pagina Monitoraggio di Azure, selezionare la risorsa di Load Balancer.
2. Impostare il tipo di aggregazione appropriato.
3. Facoltativamente, configurare il filtro e il raggruppamento necessari.

![Metriche (anteprima) per Load Balancer Standard](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*Figura: metrica relativa a disponibilità DIP e stato del probe di integrità per Load Balancer Standard*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Recuperare le metriche multidimensionali a livello di codice tramite le API

Per informazioni sull'API per il recupero dei valori e delle definizioni delle metriche multidimensionali, vedere la [Procedura dettagliata di API REST di Azure Monitoring](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api).

### <a name = "DiagnosticScenarios"></a>Scenari di diagnostica comuni e visualizzazioni consigliate

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Il percorso dei dati è disponibile per il mio Load Balancer VIP?

La metrica di disponibilità VIP descrive l’integrità del percorso dei dati all'interno della regione verso l'host di calcolo in cui si trovano le macchine virtuali. La metrica riflette lo stato di integrità delle infrastrutture di Azure. La metrica può essere utilizzata per:
- Monitorare la disponibilità esterna del servizio.
- Eseguire un approfondimento e comprendere se la piattaforma in cui viene distribuito il servizio è integra o se il sistema operativo guest o l'istanza dell'applicazione è integra.
- Isolare se un evento è correlato al servizio o al piano dati sottostante. Non confondere questa metrica con lo stato del probe di integrità ("disponibilità DIP").

Per ottenere la disponibilità VIP per le risorse di Load Balancer Standard:
1. Assicurarsi che sia selezionata la risorsa di Load Balancer corretta. 
2. Selezionare **Disponibilità VIP** dall'elenco a discesa **Metrica**. 
3. Nell’elenco a discesa **Aggregazione**, selezionare **Media**. 
4. Inoltre, è possibile aggiungere un filtro sull'indirizzo VIP o la porta VIP basato sulla dimensione con l'indirizzo IP front-end o la porta front-end richiesta e quindi procedere al raggruppamento in base alla dimensione selezionata.

![Esecuzione del probe VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Figura - Dettagli dell'esecuzione del probe VIP di Load Balancer*

La metrica viene generata da una misura attiva in banda. Un servizio di esecuzione del probe all'interno della regione genera traffico per la misura. Il servizio viene attivato non appena si crea una distribuzione con un front end pubblico e continua fino a quando non viene rimosso. 

>[!NOTE]
>I front-end interni per ora non sono supportati. 

Viene generato periodicamente un pacchetto corrispondente alla regola e al front-end di distribuzione che attraversa l'area dall'origine all'host in cui si trova una macchina virtuale nel pool back-end. L'infrastruttura di Load Balancer esegue le stesse operazioni di traslazione e bilanciamento del carico che vengono effettuate per tutto il resto del traffico. Questo probe è in banda nell'endpoint con carico bilanciato. Dopo che il probe arriva nell'host di calcolo in cui si trova una macchina virtuale integra nel pool back-end, l'host di calcolo genera una risposta al servizio di esecuzione del probe. La macchina virtuale non vede questo traffico.

La disponibilità VIP avrà esito negativo per le ragioni seguenti:
- La distribuzione non ha macchine virtuali integre rimanenti nel pool back-end. 
- Si è verificata un'interruzione dell'infrastruttura.

È possibile usare la [metrica relativa alla disponibilità VIP insieme allo stato del probe di integrità](#vipavailabilityandhealthprobes) per scopi diagnostici.

Usare **Media** come aggregazione per la maggior parte degli scenari.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>Le istanze di back-end per l'indirizzo VIP rispondono ai probe?

La metrica relativa allo stato del probe di integrità descrive l'integrità della distribuzione dell'applicazione come configurato dall'utente quando si configura il probe di integrità di Load Balancer. Load Balancer usa lo stato del probe di integrità per determinare dove inviare nuovi flussi. I probe di integrità hanno origine da un indirizzo dell'infrastruttura di Azure e sono visibili all'interno del sistema operativo guest della macchina virtuale.

Per ottenere la disponibilità DIP per le risorse di Load Balancer Standard:
1. Selezionare la metrica **Disponibilità DIP** con il tipo di aggregazione **Media**. 
2. Applicare un filtro sull'indirizzo IP o la porta VIP richiesta (o su entrambi).

![Disponibilità DIP](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*Figura - Disponibilità VIP per Load Balancer*

I probe di integrità hanno esito negativo per le ragioni seguenti:
- Se si configura un probe di integrità per una porta che non è in ascolto, che non risponde o che non utilizza il protocollo corretto. Se il servizio usa regole Direct Server Return (DSR o IP mobile), assicurarsi che il servizio sia in ascolto sull'indirizzo IP della configurazione IP della scheda di rete e non soltanto sul loopback configurato con l'indirizzo IP front-end.
- Se il probe non è consentito dal gruppo di sicurezza di rete, dal firewall del sistema operativo guest della macchina virtuale o dai filtri del livello dell'applicazione.

Usare **Media** come aggregazione per la maggior parte degli scenari.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Come è possibile controllare le statistiche di connessione in uscita? 

La metrica relativa alle connessioni SNAT descrive il volume di quelle con esito positivo e con esito negativo (connessioni per i [flussi in uscita](https://aka.ms/lboutbound)).

Un volume di connessioni non riuscite maggiore di zero indica l'esaurimento delle porte SNAT. È necessario effettuare un'analisi approfondita per determinare che cosa potrebbe causare questi errori. L'esaurimento delle porte SNAT si manifesta sotto forma di errore durante la definizione di un [flusso in uscita](https://aka.ms/lboutbound). Fare riferimento all'articolo sulle connessioni in uscita per comprendere gli scenari e i meccanismi coinvolti e scoprire come ridurre ed evitare l'esaurimento delle porte SNAT in fase di progettazione. 

Per ottenere statistiche sulle connessioni SNAT:
1. Selezionare il tipo di metrica **Connessioni SNAT** e **Somma** come aggregazione. 
2. Procedere al raggruppamento per **Stato connessione** per il conteggio delle connessioni SNAT riuscite e non riuscite che sono rappresentate da righe differenti. 

![Connessione SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Figura - Conteggio delle connessioni SNAT per Load Balancer*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Come è possibile verificare i tentativi di connessione in ingresso/uscita per il servizio?

La metrica relativa a un pacchetto SYN descrive il volume di pacchetti SYN TCP arrivati o inviati (per i [flussi in uscita](https://aka.ms/lboutbound)) che sono associati a uno specifico front-end. Questa metrica può essere usata per comprendere i tentativi di connessione TCP al servizio.

Usare **Totale** come aggregazione per la maggior parte degli scenari.

![Connessione SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Figura - Conteggio SYN per Load Balancer*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Come è possibile verificare il consumo di larghezza di banda di rete? 

La metrica relativa ai contatori di byte e pacchetti descrive il volume di byte e pacchetti che vengono inviati o ricevuti dal servizio per ogni front-end.

Usare **Totale** come aggregazione per la maggior parte degli scenari.

Per ottenere statistiche relative al conteggio di byte o pacchetti:
1. Selezionare il tipo di metrica **Conteggio byte** e/o **Conteggio pacchetti** con **Media** come aggregazione. 
2. Eseguire una di queste operazioni:
   * Applicare un filtro su una specifica porta IP front-end, porta front-end, porta IP back-end o porta back-end.
   * È possibile ottenere statistiche generali per la risorsa di Load Balancer senza applicare filtri.

![Conteggio byte](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Figura - Conteggio byte per Load Balancer*

#### <a name = "vipavailabilityandhealthprobes"></a>Come è possibile diagnosticare la distribuzione di Load Balancer?

La combinazione delle metriche relative alla disponibilità VIP e ai probe di integrità su un singolo grafico consente di identificare dove si verificano e si possono risolvere i problemi. È possibile assicurarsi che Azure funzioni correttamente e usare questa conoscenza per determinare definitivamente che la configurazione o l'applicazione costituisca la causa radice.

È possibile usare le metriche relative ai probe di integrità per comprendere in che modo Azure visualizza lo stato della distribuzione in base alla configurazione specificata. L'analisi dei probe di integrità costituisce sempre una valida opzione per iniziare a monitorare o determinare una causa.

È possibile eseguire un ulteriore passaggio e usare le metriche relative alla disponibilità VIP per ottenere informazioni approfondite sul modo in cui Azure visualizza l'integrità del piano dati sottostante che è responsabile per la specifica distribuzione. Quando si combinano entrambe le metriche, è possibile isolare il punto in cui potrebbe verificarsi l'errore come illustrato in questo esempio:

![Diagnostica VIP](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*Figura - Combinazione delle metriche relative alla disponibilità DIP e VIP*

In questo grafico vengono visualizzate le informazioni seguenti:
- L'infrastruttura di per sé era integra, l'infrastruttura che ospita le macchine virtuali era raggiungibile e più di una macchina virtuale è stata inserita nel back-end. Questa informazione è indicata dalla traccia blu per la disponibilità VIP (100%). 
- Tuttavia, lo stato del probe di integrità (disponibilità DIP) è allo 0% all'inizio del grafico, come indicato dalla traccia arancione. L'area cerchiata in rosso evidenzia il punto in cui lo stato (disponibilità DIP) è diventato integro e in quale punto la distribuzione del cliente è stata in grado di accettare nuovi flussi.

Il grafico consente al cliente di risolvere i problemi della distribuzione in modo autonomo senza dover fare supposizioni o contattare il supporto per sapere se si stanno verificando altri problemi. Il servizio non era disponibile perché i probe di integrità hanno avuto esito negativo a causa di una configurazione errata o di un'applicazione non riuscita.

### <a name = "Limitations"></a>Limitazioni

La disponibilità VIP è attualmente disponibile solo per i front-end pubblici.

## <a name = "ResourceHealth"></a>Stato di integrità delle risorse

Lo stato di integrità per le risorse di Load Balancer Standard viene esposto tramite **Integrità risorse** in **Monitoraggio > Integrità dei servizi**.

>[!NOTE]
>Stato di integrità risorse per Load Balancer è attualmente disponibile solo per la configurazione pubblica di Load Balancer Standard. Le risorse della configurazione interna di Load Balancer o lo SKU Basic di Load Balancer non espongono l'integrità delle risorse.

Per visualizzare l'integrità delle risorse della configurazione pubblica di Load Balancer Standard:
1. Selezionare **Monitor** > **Integrità dei servizi**.

   ![Pagina Monitoraggio](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Figura - Collegamento a Integrità dei servizi in Monitoraggio di Azure*

2. Selezionare **Integrità risorse** e assicurarsi che **ID di sottoscrizione** e **Tipo di risorsa = Load Balancer** siano selezionati.

   ![Stato di integrità delle risorse](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Figura: Selezionare la risorsa per la visualizzazione dell'integrità*

3. Selezionare la risorsa di Load Balancer dall'elenco per visualizzare il relativo stato di integrità cronologico.

    ![Stato di integrità di Load Balancer](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Figura: Visualizzazione dell'integrità delle risorse di Load Balancer*
 
La tabella seguente elenca i diversi stati di integrità delle risorse e le relative descrizioni: 

| Stato di integrità delle risorse | Descrizione |
| --- | --- |
| Disponibile | La risorsa della configurazione pubblica di Load Balancer Standard è integra e disponibile. |
| Non disponibile | La risorsa della configurazione pubblica di Load Balancer Standard non è integra. Diagnosticare l'integrità selezionando **Monitoraggio di Azure** > **Metriche**.<br>(Stato *Non disponibile* potrebbe anche significare che la risorsa non è collegata al Load Balancer Standard pubblico). |
| Sconosciuto | Lo stato di integrità della risorsa per la configurazione pubblica di Load Balancer Standard non è ancora stato aggiornato.<br>(Lo stato *Sconosciuto*  potrebbe anche significare che la risorsa non è connessa al servizio Load Balancer Standard pubblico).  |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Load Balancer Standard](load-balancer-standard-overview.md).
- Altre informazioni sulla [connettività in uscita di Load Balancer](https://aka.ms/lboutbound).
- Informazioni sull'[API REST delle metriche di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/metrics/).


