---
title: Azure Load Balancer Standard - Diagnostica | Microsoft Docs
description: Uso delle metriche e delle informazioni sull'integrità disponibili per la diagnostica per Azure Load Balancer Standard
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
ms.date: 03/21/2018
ms.author: Kumud
ms.openlocfilehash: 1d39cdc13e69740dc99e67f935b60db218536044
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Metriche e diagnostica dell'integrità per Load Balancer Standard

Azure Load Balancer Standard fornisce le funzionalità di diagnostica seguenti per le risorse:
* Metriche multidimensionali: Load Balancer Standard offre nuove funzionalità di diagnostica multidimensionale per le configurazioni di Load Balancer sia pubbliche che interne. In questo modo è possibile monitorare, gestire e risolvere i problemi delle risorse di Load Balancer.

* Integrità risorse: la pagina di Load Balancer nel portale di Azure e la pagina Integrità risorse (in Monitoraggio) espongono l'integrità delle risorse per la configurazione pubblica di bilanciamento del carico di Load Balancer Standard.

Questo articolo offre una breve panoramica di queste funzionalità e illustra come usarle per Load Balancer Standard.

## <a name = "MultiDimensionalMetrics"></a>Metriche multidimensionali

Azure Load Balancer fornisce le nuove metriche multidimensionali tramite la nuova funzionalità Metriche di Azure (anteprima) nel portale e consente di ottenere informazioni dettagliate di diagnostica in tempo reale sulle risorse di Load Balancer. 

Attualmente sono disponibili le metriche seguenti per le diverse configurazioni di Load Balancer (LB) Standard:

| Metrica | Tipo di risorsa | DESCRIZIONE | Aggregazione consigliata |
| --- | --- | --- | --- |
| Disponibilità VIP (disponibilità del percorso dati) | LB pubblico | Load Balancer Standard esercita continuamente il percorso dati dall'interno di un'area al front-end di Load Balancer e infine allo stack SDN che supporta la macchina virtuale. Finché sono presenti istanze integre, la misurazione segue lo stesso percorso del traffico con bilanciamento del carico dell'applicazione. Viene anche convalidato il percorso dati usato dai clienti. La misurazione è invisibile all'applicazione e non interferisce con altre operazioni.| Media |
| Disponibilità DIP (stato del probe di integrità) |  LB pubblico e interno | Load Balancer Standard usa un servizio di probe dell'integrità distribuito che monitora l'integrità dell'endpoint dell'applicazione in base alle impostazioni di configurazione. Questa metrica offre una visualizzazione filtrata, aggregata o per endpoint di ogni singolo endpoint dell'istanza nel pool di Load Balancer.  In questo modo è possibile visualizzare l'integrità dell'applicazione rilevata da Load Balancer, in base alla configurazione del probe di integrità. |  Media |
| Pacchetti SYN |  LB pubblico | Load Balancer Standard non termina le connessioni TCP, né interagisce con i flussi di pacchetti TCP o UDP. I flussi e i relativi handshake sono sempre tra l'origine e l'istanza VM. Per risolvere meglio i problemi degli scenari del protocollo TCP, è possibile usare contatori di pacchetti SYN per determinare quanti tentativi di connessione TCP vengono eseguiti. La metrica indica il numero di pacchetti SYN TCP ricevuti.| Media |
| Connessioni SNAT |  LB pubblico |Load Balancer Standard segnala il numero di flussi in uscita mascherati per il front-end dell'indirizzo IP pubblico. Le porte SNAT sono una risorsa esauribile. Questa metrica può indicare l'uso che l'applicazione fa di SNAT per i flussi originati in uscita.  Vengono segnalati i contatori per i flussi SNAT con esito positivo e negativo, che è possibile usare per risolvere i problemi e comprendere l'integrità dei flussi in uscita.| Media |
| Contatori di byte |  LB pubblico e interno | Load Balancer Standard restituisce i dati elaborati per ogni front-end.| Media |
| Contatori di pacchetti |  LB pubblico e interno | Load Balancer Standard restituisce i pacchetti elaborati per ogni front-end.| Media |

### <a name="view-load-balancer-metrics-in-the-portal"></a>Visualizzare le metriche di Load Balancer nel portale

Il portale di Azure espone le metriche di Load Balancer nella pagina Metriche (anteprima), disponibile nella pagina delle risorse di Load Balancer per una specifica risorsa di Load Balancer, oltre che nella pagina Monitoraggio di Azure. 

Per visualizzare le metriche per le risorse di Load Balancer Standard, andare a Metriche (anteprima) in una di queste due pagine, selezionare il tipo di metrica dall'elenco a discesa (o la risorsa di Load Balancer se ci si trova nella pagina Monitoraggio), impostare il tipo di aggregazione appropriato e, facoltativamente, configurare l'applicazione del filtro e il raggruppamento necessari per visualizzare la cronologia delle metriche in base alle condizioni specificate.

![Metriche (anteprima) per Load Balancer Standard](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*Figura - Metrica relativa a disponibilità DIP/stato del probe di integrità per Load Balancer*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Recuperare le metriche multidimensionali a livello di codice tramite le API

Il materiale sussidiario per le API per il recupero delle definizioni e dei valori delle metriche multidimensionali è disponibile in [Procedura dettagliata sull'API REST di monitoraggio di Azure > API multidimensionale](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api).

### <a name = "DiagnosticScenarios"></a>Scenari di diagnostica comuni e visualizzazioni consigliate

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Il percorso dati è attivo e disponibile per l'indirizzo VIP di Load Balancer?

La metrica relativa alla disponibilità VIP descrive l'integrità del percorso dati all'interno dell'area relativo all'host di calcolo in cui si trovano le macchine virtuali e rispecchia l'integrità dell'infrastruttura di Azure. È possibile usare questa metrica per:
- Monitorare la disponibilità esterna del servizio.
- Eseguire un approfondimento e comprendere se la piattaforma in cui viene distribuito il servizio è integra o se il sistema operativo guest o l'istanza dell'applicazione è integra.
- Isolare se un evento è correlato al servizio o al piano dati sottostante. Non confondere questa metrica con lo stato del probe di integrità ("disponibilità DIP").

Per ottenere la disponibilità VIP per le risorse di Load Balancer Standard:
- Assicurarsi che sia selezionata la risorsa di Load Balancer corretta. 
- Selezionare **VIP Availability** (Disponibilità VIP) dall'elenco a discesa **Metrica**. 
- Selezionare **Media** per **Aggregazione**. 
- Inoltre, è possibile aggiungere un filtro sull'indirizzo VIP o la porta VIP basato sulla dimensione con l'indirizzo IP front-end o la porta front-end richiesta e procedere al raggruppamento in base alla dimensione selezionata.

![Esecuzione del probe VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Figura - Dettagli dell'esecuzione del probe VIP di Load Balancer*

La metrica viene generata da una misura attiva in banda. Un servizio di esecuzione del probe all'interno dell'area dà origine al traffico per questa misura e viene attivato non appena si crea una distribuzione con un front-end pubblico, continuando finché non si rimuove il front-end. 

>[!NOTE]
>I front-end interni per ora non sono supportati. 

Viene generato periodicamente un pacchetto corrispondente alla regola e al front-end di distribuzione che attraversa l'area dall'origine all'host in cui si trova una macchina virtuale nel pool back-end. L'infrastruttura di Load Balancer eseguirà le stesse operazioni di traslazione e bilanciamento del carico che vengono effettuate per tutto il resto del traffico. Questo probe è in banda nell'endpoint con carico bilanciato. Una volta che il probe arriva nell'host di calcolo in cui si trova una macchina virtuale integra nel pool back-end, l'host di calcolo genererà una risposta al servizio di esecuzione del probe. La macchina virtuale non vede questo traffico.
La disponibilità VIP avrà esito negativo per le ragioni seguenti:
- La distribuzione non ha macchine virtuali integre rimanenti nel pool back-end. 
- Si è verificata un'interruzione dell'infrastruttura che fa sì che la disponibilità VIP abbia esito negativo.

È possibile usare la [metrica relativa alla disponibilità VIP insieme allo stato del probe di integrità per scopi diagnostici](https://aka.ms/lbdiagnostics#vipavailabilityandhealthprobes).

Usare **Media** come aggregazione per la maggior parte degli scenari.

#### <a name="are-the-backend-instances-for-my-vip-responding-to-probes"></a>Le istanze di back-end per l'indirizzo VIP rispondono ai probe?

La metrica relativa allo stato del probe di integrità descrive l'integrità della distribuzione dell'applicazione come configurato dall'utente quando si configura il probe di integrità di Load Balancer. Load Balancer usa lo stato del probe di integrità per determinare dove inviare nuovi flussi. I probe di integrità hanno origine da un indirizzo dell'infrastruttura di Azure e sono visibili all'interno del sistema operativo guest della macchina virtuale.

Per ottenere la disponibilità DIP per le risorse di Load Balancer Standard:
- Selezionare la metrica **DIP Availability** (Disponibilità DIP) con il tipo di aggregazione **Media**. 
- Applicare un filtro sull'indirizzo IP o la porta VIP richiesta (o su entrambi).

![Disponibilità DIP](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*Figura - Disponibilità VIP per Load Balancer*

I probe di integrità hanno esito negativo per le ragioni seguenti:
- Se si configura un probe di integrità per una porta che non è in ascolto, che non risponde o che non ha il protocollo corretto. Se il servizio usa regole DSR (IP mobile), è necessario assicurarsi che il servizio sia in ascolto sull'indirizzo IP della configurazione IP della scheda di rete e non soltanto sul loopback configurato con l'indirizzo IP front-end.
- Se il probe non è consentito dal gruppo di sicurezza di rete, dal firewall del sistema operativo guest della macchina virtuale o dai filtri del livello dell'applicazione.

Usare **Media** come aggregazione per la maggior parte degli scenari.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Come è possibile controllare le statistiche di connessione in uscita? 

La metrica relativa alle connessioni SNAT descrive il volume di quelle con esito positivo e con esito negativo (per i [flussi in uscita](https://aka.ms/lboutbound)).

Un volume di connessioni non riuscite maggiore di zero indica l'esaurimento delle porte SNAT. È necessario effettuare un'analisi approfondita e determinare che cosa potrebbe causare questi errori. L'esaurimento delle porte SNAT si manifesta sotto forma di errori durante la definizione di un [flusso in uscita](https://aka.ms/lboutbound). Fare riferimento all'articolo sulle connessioni in uscita per comprendere gli scenari e i meccanismi coinvolti e come ridurre/evitare l'esaurimento delle porte SNAT in fase di progettazione. 

Per ottenere statistiche sulle connessioni SNAT:
- Selezionare il tipo di metrica **SNAT Connections** (Connessioni SNAT) e **Somma** come aggregazione. 
- Procedere al raggruppamento per **Stato connessione** per il conteggio delle connessioni SNAT riuscite e non riuscite rappresentate da righe differenti. 

![Connessione SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Figura - Conteggio delle connessioni SNAT per Load Balancer*


#### <a name="how-do-i-check-inbound--outbound-connection-attempts-for-my-service"></a>Come è possibile verificare i tentativi di connessione in ingresso/uscita per il servizio?

La metrica relativa ai pacchetti SYN descrive il volume di pacchetti SYN TCP arrivati o inviati (per i [flussi in uscita](https://aka.ms/lboutbound)) associati a un determinato front-end. Questa metrica può essere usata per comprendere i tentativi di connessione TCP al servizio.
Usare Totale come aggregazione per la maggior parte degli scenari.

![Connessione SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Figura - Conteggio SYN per Load Balancer*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Come è possibile verificare il consumo di larghezza di banda di rete? 

La metrica relativa ai contatori di byte/pacchetti descrive il volume di byte e pacchetti inviati o ricevuti dal servizio per ogni front-end.
Usare **Totale** come aggregazione per la maggior parte degli scenari.

Per ottenere statistiche relative al conteggio di byte o pacchetti
- Selezionare il tipo di metrica **Conteggio byte** e/o **Packet Count** (Conteggio pacchetti) con **Media** come aggregazione. 
- Applicare il filtro a un IP front-end o a una porta front-end specifica oppure all'IP back-end o alla porta in questione. 
- È anche possibile ottenere statistiche generali per la risorsa di Load Balancer senza applicare filtri.


Alcune visualizzazioni di esempio per le metriche con configurazioni diverse:

![Conteggio byte](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Figura - Conteggio byte per Load Balancer*

#### <a name = "vipavailabilityandhealthprobes"></a>Come è possibile diagnosticare la distribuzione di Load Balancer?

La combinazione delle metriche relative alla disponibilità VIP e ai probe di integrità su un singolo grafico consente di identificare dove si verificano e si possono risolvere i problemi. È possibile assicurarsi che Azure funzioni correttamente e usare questa combinazione per determinare definitivamente che la configurazione o l'applicazione costituisca la causa radice.

È possibile usare le metriche relative ai probe di integrità per comprendere in che modo Azure visualizza lo stato della distribuzione in base alla configurazione specificata. L'analisi dei probe di integrità costituisce sempre una valida opzione per iniziare a monitorare o determinare una causa.

È possibile eseguire un ulteriore passaggio e usare le metriche relative alla disponibilità VIP per ottenere informazioni approfondite sul modo in cui Azure visualizza l'integrità del piano dati sottostante responsabile per la specifica distribuzione. Quando si combinano entrambe le metriche, è possibile isolare il punto in cui potrebbe verificarsi l'errore come illustrato in questo esempio:

![Diagnostica VIP](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*Figura - Combinazione delle metriche relative alla disponibilità DIP e VIP*

Il grafico mostra le informazioni seguenti:
- L'infrastruttura di per sé era integra, l'infrastruttura che ospita le macchine virtuali era raggiungibile e più di una macchina virtuale è stata inserita nel back-end. Ciò è indicato dalla traccia blu per la disponibilità VIP (100%). 
- Tuttavia, lo stato del probe di integrità (disponibilità DIP) è allo 0% all'inizio del grafico, come indicato dalla traccia arancione. L'area cerchiata in rosso evidenzia il punto in cui i probe di integrità (disponibilità DIP) sono diventati integri e in quale punto la distribuzione del cliente è stata in grado di accettare nuovi flussi.

Il grafico ha consentito al cliente di risolvere i problemi della distribuzione in modo autonomo senza dover fare supposizioni o contattare il supporto per sapere se si sono verificati altri problemi. Il servizio del cliente non era disponibile perché i probe di integrità hanno avuto esito negativo a causa di una configurazione errata o di un'applicazione non riuscita.

### <a name = "Limitations"></a>Limitazioni

- La disponibilità VIP è attualmente disponibile solo per i front-end pubblici.

## <a name = "ResourceHealth"></a>Stato di integrità delle risorse

Lo stato di integrità per le risorse di Load Balancer Standard viene esposto tramite **Integrità risorse** in **Monitoraggio > Integrità dei servizi**.

>[!NOTE]
>Integrità risorse per Load Balancer è attualmente disponibile solo per la configurazione pubblica di Load Balancer Standard. Le risorse della configurazione interna di Load Balancer o lo SKU Basic di Load Balancer non espongono l'integrità delle risorse.

Per visualizzare l'integrità delle risorse della configurazione pubblica di Load Balancer Standard:
 - Andare a **Monitoraggio > Integrità dei servizi**.

  ![Pagina Monitoraggio](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Figura - Integrità dei servizi in Monitoraggio di Azure*

 - Selezionare **Integrità risorse** e assicurarsi di selezionare l'ID corretto per **Sottoscrizione** e **Tipo di risorsa = Load Balancer**.

  ![Stato di integrità delle risorse](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Figura - Selezionare la risorsa per la visualizzazione dell'integrità*

 - Selezionare la risorsa di Load Balancer dall'elenco per visualizzare il relativo stato di integrità cronologico.

    ![Stato di integrità di Load Balancer](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Figura - Visualizzazione dell'integrità delle risorse di Load Balancer*
 
La tabella seguente elenca i diversi stati di integrità delle risorse e le relative descrizioni. 

| Stato di integrità delle risorse | DESCRIZIONE |
| --- | --- |
| Disponibile | La risorsa della configurazione pubblica di Load Balancer Standard è integra e disponibile. |
| Non disponibile | La risorsa della configurazione pubblica di Load Balancer Standard non è integra. Diagnosticarne l'integrità tramite Monitoraggio di Azure > Metriche (*ciò potrebbe anche indicare che la risorsa non è di una configurazione pubblica di Load Balancer Standard*). |
| Sconosciuto | L'integrità della risorsa per la configurazione pubblica di Load Balancer Standard non è ancora stata aggiornata (*ciò potrebbe anche indicare che la risorsa non è di una configurazione pubblica di Load Balancer Standard*).  |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Load Balancer Standard](load-balancer-standard-overview.md)
- Altre informazioni sulla [connettività in uscita di Load Balancer](https://aka.ms/lboutbound)


