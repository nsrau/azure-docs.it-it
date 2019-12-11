---
title: Protezione delle risorse di rete nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra le raccomandazioni presenti nel Centro sicurezza di Azure che facilitano la protezione delle risorse della rete di Azure e la conformità ai criteri di sicurezza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: memildin
ms.openlocfilehash: 5d13e944cbc083b314c71936d181101ec13037b4
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997140"
---
# <a name="protect-your-network-resources"></a>Proteggere le risorse di rete
Il Centro sicurezza di Azure analizza continuamente lo stato di sicurezza delle risorse di Azure per consigliare le procedure ottimali di protezione della rete. Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni utili per definire il processo di configurazione dei controlli necessari per rafforzare e tutelare le risorse.

Questo articolo illustra la pagina **rete** della sezione relativa alla sicurezza delle risorse del Centro sicurezza di Azure e alcune delle raccomandazioni che verranno visualizzate.

Per un elenco completo dei consigli per la rete, vedere [raccomandazioni](recommendations-network.md)per la rete.

Questo articolo illustra le raccomandazioni applicabili alle risorse di Azure da un punto di vista della sicurezza di rete. Raccomandazioni per la rete per i firewall di nuova generazione, i gruppi di sicurezza di rete, l'accesso JIT alle VM, le regole del traffico in ingresso eccessivamente permissive e altro ancora. Per visualizzare un elenco delle raccomandazioni per le risorse di rete e le azioni di correzione, vedere [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).

> [!NOTE]
> La pagina **Rete** consente di esaminare nei dettagli l'integrità delle risorse di Azure da una prospettiva di rete. La mappa di rete e i controlli per la rete adattiva sono disponibili solo per il livello standard del Centro sicurezza di Azure. [Se si usa il livello gratuito, è possibile fare clic sul pulsante per visualizzare le **Risorse di rete legacy** e ricevere raccomandazioni sulle risorse di rete](#legacy-networking).
>

La pagina **Rete** offre una panoramica delle sezioni da studiare per ottenere altre informazioni sull'integrità delle risorse di rete:

- Mappa di rete (solo per il livello standard di Centro sicurezza di Azure)
- Protezione avanzata adattiva della rete
- Raccomandazioni per la sicurezza della rete.
- Pannello **Rete** legacy (il precedente pannello Rete) 
 
![Pannello Rete](./media/security-center-network-recommendations/networking-pane.png)

## <a name="network-map"></a>Mappa di rete
La mappa di rete interattiva è una rappresentazione visiva con sovrimpressioni sulla sicurezza che offre raccomandazioni e informazioni dettagliate per la protezione avanzata delle risorse di rete. Nella mappa è possibile vedere la topologia di rete dei carichi di lavoro di Azure, le connessioni tra le macchine virtuali e le subnet e il dettaglio di risorse specifiche per cui vengono offerte raccomandazioni.

Per aprire la mappa di rete:

1. Nel Centro sicurezza, sotto Protezione della sicurezza delle risorse, selezionare **Rete**.
2. Sotto **Mappa di rete** fare clic su **Visualizza la topologia**.
 
La vista predefinita della mappa topologica contiene:

- Le sottoscrizioni selezionate in Azure. La mappa supporta più sottoscrizioni.
- VM, subnet e reti virtuali del tipo di risorsa Gestione risorse (le risorse di Azure classiche non sono supportate)
- Reti virtuali con peering
- Solo le risorse che hanno [raccomandazioni per la rete](security-center-recommendations.md) con gravità alta o media  
- Risorse con connessione Internet
- La mappa è ottimizzata per le sottoscrizioni selezionate in Azure. Se si modifica la selezione, la mappa viene ricalcolata e ottimizzata nuovamente in base alle nuove impostazioni.  

![Mappa della topologia di rete](./media/security-center-network-recommendations/network-map-info.png)

## <a name="understanding-the-network-map"></a>Informazioni sulla mappa di rete

La mappa di rete consente di visualizzare le risorse di Azure in una visualizzazione **Topologia** o una visualizzazione **Traffico**.

### <a name="the-topology-view"></a>La visualizzazione Topologia

Nella visualizzazione **Topologia** della mappa di rete è possibile accedere alle seguenti informazioni dettagliate sulle risorse di rete:

- Il cerchio più interno riporta tutte le reti virtuali all'interno delle sottoscrizioni selezionate, il cerchio successivo contiene tutte le subnet e il cerchio esterno tutte le macchine virtuali.
- Le linee che collegano le risorse nella mappa consentono di sapere quali risorse sono associate tra loro e come è strutturata la rete di Azure. 
- Usare gli indicatori di gravità per vedere rapidamente per quali risorse sono presenti raccomandazioni aperte del Centro sicurezza.
- Fare clic su una delle risorse per eseguire il drill-down e visualizzare direttamente i dettagli di tale risorsa e le indicazioni nel contesto della mappa di rete.  
- Se sulla mappa sono visualizzate troppe risorse, il Centro sicurezza di Azure usa un algoritmo proprietario per raggruppare le risorse in cluster smart che evidenziano le risorse con lo stato più critico e le raccomandazioni con gravità più elevata. 

Poiché la mappa è dinamica e interattiva, ogni nodo è selezionabile e la visualizzazione può cambiare in base ai filtri:

1. Applicare i filtri nella parte superiore per modificare le informazioni disponibili nella mappa di rete. È possibile concentrare le informazioni della mappa su:

   -  **Integrità della sicurezza**: è possibile filtrare la mappa in base al livello di gravità (alta, media, bassa) delle risorse di Azure.
   - **Raccomandazioni**: è possibile selezionare le risorse visualizzate in base alle raccomandazioni attive su tali risorse. Ad esempio, è possibile visualizzare solo le risorse per cui il Centro sicurezza consiglia di abilitare i gruppi di sicurezza di rete.
   - **Aree di rete**: per impostazione predefinita, nella mappa vengono visualizzate solo le risorse con connessione Internet, ma è possibile selezionare anche le macchine virtuali interne.
 
2. Fare clic su **Reimposta** nell'angolo in alto a sinistra in qualsiasi momento per ripristinare lo stato predefinito della mappa.

Per eseguire il drill-down in una risorsa:

1. Quando si seleziona una risorsa specifica nella mappa, si apre il riquadro di destra con informazioni generali sulla risorsa, eventuali soluzioni per la sicurezza della connessione e le raccomandazioni rilevanti per la risorsa. Il comportamento non cambia a seconda del tipo di risorsa selezionato. 
2. Quando si passa il mouse sopra un nodo della mappa, è possibile visualizzare informazioni generali relative alla risorsa, come la sottoscrizione, il tipo di risorsa e il gruppo di risorse.
3. Usare il collegamento per ingrandire la descrizione del comando e ricentrare la mappa su quel nodo specifico. 
4. Per allontanarsi da quel nodo specifico, eseguire lo zoom indietro.

### <a name="the-traffic-view"></a>Visualizzazione Traffico

La visualizzazione **Traffico** compila una mappa con tutto il traffico possibile tra le risorse. L'utente dispone così di una mappa visiva di tutte le regole che sono state configurate e che definiscono quali risorse possono comunicare tra loro. In questo modo è possibile vedere l'attuale configurazione dei gruppi di sicurezza di rete nonché identificare rapidamente le possibili configurazioni rischiose all'interno dei carichi di lavoro.

### <a name="uncover-unwanted-connections"></a>Individuare connessioni indesiderate

Il punto di forza di questa visualizzazione è la possibilità di mostrare le connessioni consentite insieme alle vulnerabilità esistenti, e utilizzare questa sezione trasversale dei dati per proteggere in modo più efficace le risorse. 

Ad esempio, si potrebbero scoprire due macchine che non era previsto che comunicassero e grazie a questa informazione l'utente potrebbe isolare meglio i carichi di lavoro e le subnet.

### <a name="investigate-resources"></a>Esaminare le risorse

Per eseguire il drill-down in una risorsa:

1. Quando si seleziona una risorsa specifica nella mappa, si apre il riquadro di destra con informazioni generali sulla risorsa, eventuali soluzioni per la sicurezza della connessione e le raccomandazioni rilevanti per la risorsa. Il comportamento non cambia a seconda del tipo di risorsa selezionato. 
2. Fare clic su **Traffico** per visualizzare il possibile traffico in ingresso e in uscita sulla risorsa. Questo è l'elenco completo di chi può comunicare con la risorsa, con chi la risorsa può comunicare e tramite quali protocolli e porte. Ad esempio, quando si seleziona una macchina virtuale, vengono visualizzate tutte le macchine virtuali con cui è possibile comunicare e quando si seleziona una subnet vengono visualizzate tutte le subnet con cui è possibile comunicare.

**Questi dati si basano sull'analisi dei gruppi di sicurezza di rete nonché sugli algoritmi di machine learning avanzati che analizzano più regole per comprendere incroci e interazioni.** 

![Mappa del traffico di rete](./media/security-center-network-recommendations/network-map-traffic.png)

## Risorse di rete legacy <a name ="legacy-networking"></a>

Se non si dispone del livello standard del Centro sicurezza, in questa sezione viene illustrato come visualizzare le raccomandazioni per la rete.

Per accedere a queste informazioni, fare clic su **Visualizza le risorse di rete legacy** nel pannello Rete. 

![Risorse di rete legacy](./media/security-center-network-recommendations/legacy-networking.png)

### <a name="internet-facing-endpoints-section"></a>Sezione Endpoint con connessione Internet
Nella sezione **Endpoint con connessione Internet** è possibile visualizzare le macchine virtuali attualmente configurate con un endpoint con connessione Internet e il relativo stato.

La tabella include il nome dell'endpoint, l'indirizzo IP con connessione Internet, lo stato corrente di gravità del gruppo di sicurezza di rete e le raccomandazioni per il firewall di nuova generazione. Questa tabella è ordinata in base alla gravità.

### <a name="networking-topology-section"></a>Sezione Topologia di rete
La sezione **Topologia di rete** include una visualizzazione gerarchica delle risorse.

Il contenuto di questa tabella, macchine virtuali e subnet, è ordinato in base alla gravità.

Nella visualizzazione Topologia il primo livello contiene le reti virtuali. Il secondo Visualizza le subnet e il terzo livello Visualizza le macchine virtuali che appartengono a tali subnet. La colonna di destra mostra lo stato attuale delle raccomandazioni per il gruppo di sicurezza di rete per queste risorse.

Il terzo livello contiene le macchine virtuali, che è simile a quello descritta in precedenza. È possibile fare clic su qualsiasi risorsa per saperne di più o applicare la configurazione o il controllo di sicurezza necessario.

## <a name="see-also"></a>Vedi anche
Per altre informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:

* [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-protection.md)
* [Protezione del servizio SQL di Azure nel Centro sicurezza di Azure](security-center-sql-service-recommendations.md)