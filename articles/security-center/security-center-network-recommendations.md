---
title: Protezione delle risorse di rete nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra le raccomandazioni presenti nel Centro sicurezza di Azure che facilitano la protezione delle risorse della rete di Azure e la conformità ai criteri di sicurezza.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: v-mohabe
ms.openlocfilehash: 6b3cef32cf79c2448d2e254e27c332e01ea83c62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66428374"
---
# <a name="protect-your-network-resources-in-azure-security-center"></a>Proteggere le risorse di Azure nel Centro sicurezza di Azure
Il Centro sicurezza di Azure analizza continuamente lo stato di sicurezza delle risorse di Azure per consigliare le procedure ottimali di protezione della rete. Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni utili per definire il processo di configurazione dei controlli necessari per rafforzare e tutelare le risorse.

Questo articolo illustra le raccomandazioni applicabili alle risorse di Azure da un punto di vista della sicurezza di rete. Le raccomandazioni per le risorse di rete sono incentrate sui firewall di nuova generazione, sui gruppi di sicurezza di rete, sull'accesso Just-In-Time a VM, su regole eccessivamente permissive per il traffico in ingresso e altro ancora. Per visualizzare un elenco delle raccomandazioni per le risorse di rete e le azioni di correzione, vedere [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).

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
- Le macchine virtuali, subnet e le reti virtuali del tipo di risorsa di Resource Manager (non sono supportate risorse di Azure classico)
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
2. Fare clic su **Traffico** per visualizzare il possibile traffico in ingresso e in uscita sulla risorsa. Questo è l'elenco completo di chi può comunicare con la risorsa, con chi la risorsa può comunicare e tramite quali protocolli e porte. Ad esempio, quando si seleziona una macchina virtuale, tutte le VM che possa comunicare con vengono visualizzati e quando si seleziona una subnet, vengono visualizzate tutte le subnet che possa comunicare con.

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

Nella visualizzazione Topologia il primo livello contiene le reti virtuali. Il secondo livello contiene le subnet e il terzo livello include le macchine virtuali appartenenti alle subnet. La colonna di destra mostra lo stato attuale delle raccomandazioni per il gruppo di sicurezza di rete per queste risorse.

Il terzo livello contiene le macchine virtuali, che è simile a quello descritta in precedenza. Fare clic su una risorsa per ottenere altre informazioni o per applicare la configurazione o il controllo di sicurezza necessario.

## <a name="network-recommendations"></a>Indicazioni per la rete

|Tipo di risorsa|Punteggio di sicurezza|Recommendation|Descrizione|
|----|----|----|----|
|Machine|40|Gruppi di sicurezza di rete per le macchine virtuali devono essere abilitati|Consente di abilitare i gruppi di sicurezza di rete per controllare l'accesso alla rete delle macchine virtuali.|
|Subnet|35|Gruppi di sicurezza di rete a livello di subnet devono essere abilitati|Consente di abilitare i gruppi di sicurezza di rete per controllare l'accesso alla rete delle risorse distribuite nelle subnet.|
|Machine|30|Controllo accesso alla rete Just-In-Time deve essere applicata in macchine virtuali|Applica un controllo di accesso Just-In-Time delle macchine virtuali per bloccare l'accesso in modo permanente ad alcune porte e consente agli utenti autorizzati di aprirle tramite lo stesso meccanismo e per un periodo di tempo limitato.|
|Machine|20|Restrict access through Internet facing endpoint (Limita accesso tramite endpoint con connessione Internet)|Finalizza i gruppi di sicurezza di rete delle macchine virtuali con connessione Internet limitando l'accesso delle regole di autorizzazione esistenti.|
|Machine|10|Aggiunger un firewall di nuova generazione|Consente di aggiungere una soluzione Firewall di nuova generazione per proteggere le macchine virtuali con connessione Internet.|
|Machine|5|Route traffic through network gateway firewall only (Instrada il traffico solo attraverso il firewall gateway di rete)|Per completare la distribuzione della soluzione Firewall di nuova generazione, il traffico verso le macchine virtuali protette con connessione Internet deve essere instradato solo tramite la soluzione Firewall di nuova generazione.|
|VNet|5|Abilita Protezione DDoS Standard|Le applicazioni con indirizzi IP pubblici in queste reti virtuali non sono protette con lo standard del servizio Protezione DDOS. È consigliabile abilitarlo per consentire la mitigazione di attacchi volumetrici e ai protocolli della rete.|

## <a name="see-also"></a>Vedere anche
Per altre informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:

* [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-recommendations.md)
* [Protecting your applications in Azure Security Center](security-center-application-recommendations.md)
* [Protezione del servizio SQL di Azure nel Centro sicurezza di Azure](security-center-sql-service-recommendations.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
