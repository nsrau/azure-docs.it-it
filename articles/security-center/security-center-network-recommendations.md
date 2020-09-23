---
title: Protezione delle risorse di rete nel centro sicurezza di Azure
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
ms.openlocfilehash: 868470292fbacd71e1eb2d39de7e3a9c5cf6900e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90883886"
---
# <a name="protect-your-network-resources"></a>Proteggere le risorse di rete
Il Centro sicurezza di Azure analizza continuamente lo stato di sicurezza delle risorse di Azure per consigliare le procedure ottimali di protezione della rete. Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni utili per definire il processo di configurazione dei controlli necessari per rafforzare e tutelare le risorse.

Per un elenco completo dei consigli per la rete, vedere [raccomandazioni](recommendations-reference.md#recs-network)per la rete.

Questo articolo illustra le raccomandazioni applicabili alle risorse di Azure da un punto di vista della sicurezza di rete. Raccomandazioni per la rete per i firewall di nuova generazione, i gruppi di sicurezza di rete, l'accesso JIT alle VM, le regole del traffico in ingresso eccessivamente permissive e altro ancora. Per visualizzare un elenco delle raccomandazioni per le risorse di rete e le azioni di correzione, vedere [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).

Le funzionalità di **rete** del Centro sicurezza includono: 

- Mappa di rete (richiede Azure Defender)
- [Protezione avanzata della rete adattiva](security-center-adaptive-network-hardening.md) (richiede Azure Defender)
- Raccomandazioni sulla sicurezza di rete
 
## <a name="view-your-networking-resources-and-their-recommendations"></a>Visualizzare le risorse di rete e le relative raccomandazioni

Dalla [pagina inventario asset](asset-inventory.md)usare il filtro tipo di risorsa per selezionare le risorse di rete da analizzare:

:::image type="content" source="./media/security-center-network-recommendations/network-filters-inventory.png" alt-text="Tipi di risorse di Asset Inventory Network" lightbox="./media/security-center-network-recommendations/network-filters-inventory.png":::


## <a name="network-map"></a>Mappa di rete

La mappa di rete interattiva è una rappresentazione visiva con sovrimpressioni sulla sicurezza che offre raccomandazioni e informazioni dettagliate per la protezione avanzata delle risorse di rete. Nella mappa è possibile vedere la topologia di rete dei carichi di lavoro di Azure, le connessioni tra le macchine virtuali e le subnet e il dettaglio di risorse specifiche per cui vengono offerte raccomandazioni.

Per aprire la mappa di rete:

1. Dal menu del Centro sicurezza aprire il dashboard di Azure Defender e selezionare **mappa di rete**.

    :::image type="content" source="./media/security-center-network-recommendations/opening-network-map.png" alt-text="Apertura della mappa di rete dal dashboard di Azure Defender" lightbox="./media/security-center-network-recommendations/opening-network-map.png":::

1. Scegliere la **topologia**dal menu **livelli** .
 
La vista predefinita della mappa topologica contiene:

- Le sottoscrizioni selezionate in Azure. La mappa supporta più sottoscrizioni.
- VM, subnet e reti virtuali del tipo di risorsa Gestione risorse (le risorse di Azure classiche non sono supportate)
- Reti virtuali con peering
- Solo le risorse che hanno [raccomandazioni per la rete](security-center-recommendations.md) con gravità alta o media  
- Risorse con connessione Internet
- La mappa è ottimizzata per le sottoscrizioni selezionate in Azure. Se si modifica la selezione, la mappa viene ricalcolata e ottimizzata nuovamente in base alle nuove impostazioni.  

[![Mappa della topologia di rete](./media/security-center-network-recommendations/network-map-info.png)](./media/security-center-network-recommendations/network-map-info.png#lightbox)

## <a name="understanding-the-network-map"></a>Informazioni sulla mappa di rete

La mappa di rete può visualizzare le risorse di Azure in una visualizzazione **topologia** e una visualizzazione **traffico** . 

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

[![Mappa del traffico di rete](./media/security-center-network-recommendations/network-map-traffic.png)](./media/security-center-network-recommendations/network-map-traffic.png#lightbox)


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:

- [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-protection.md)