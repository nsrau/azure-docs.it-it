---
title: Inventario asset del Centro sicurezza di Azure
description: Informazioni sull'esperienza di gestione delle risorse del Centro sicurezza di Azure che offre visibilità completa su tutte le risorse monitorate del Centro sicurezza.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 08/11/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 7fab15ae4e9e58af58a4490beb2d512379976d1c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263940"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory-and-management-tools"></a>Esplorare e gestire le risorse con gli strumenti di inventario e gestione degli asset

La pagina inventario asset del Centro sicurezza di Azure offre un'unica pagina per visualizzare il comportamento di sicurezza delle risorse connesse al centro sicurezza. 

Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare le potenziali vulnerabilità di sicurezza. Vengono quindi fornite indicazioni su come correggere tali vulnerabilità.

Quando una risorsa presenta raccomandazioni in attesa, verranno visualizzate nell'inventario.

Usare questa visualizzazione e i relativi filtri per risolvere le domande seguenti:

- Quali sottoscrizioni del livello standard hanno raccomandazioni in attesa?
- Quali sono i computer con tag "Production" mancanti nell'agente di Log Analytics?
- Il numero di macchine virtuali, con tag con un tag specifico, presenta raccomandazioni in attesa?
- Quante risorse in un gruppo di risorse specifico presentano risultati di sicurezza da un servizio di valutazione della vulnerabilità?

Le possibilità di gestione delle risorse per questo strumento sono sostanziali e continuano ad aumentare. 


## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato versione:|Anteprima|
|Prezzi|Gratuito|
|Ruoli e autorizzazioni necessari:|tutti gli utenti|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Nazionale/sovrano (US Gov, Cina gov, altri gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>Quali sono le funzionalità principali di inventario asset?

Nella pagina inventario sono disponibili gli strumenti seguenti:

- **Riepiloghi** : prima di definire i filtri, viene visualizzata una striscia di valori prominente nella parte superiore della visualizzazione inventario:

    - **Risorse totali**: numero totale di risorse connesse al centro sicurezza.
    - **Risorse non integre**: risorse con raccomandazioni di sicurezza attive. [Altre informazioni sulle raccomandazioni sulla sicurezza](https://docs.microsoft.com/azure/security-center/security-center-recommendations).
    - **Risorse non monitorate**: risorse con problemi di monitoraggio degli agenti. l'agente log Analytics distribuito, ma l'agente non invia dati o presenta altri problemi di integrità.

- **Filtri** : i filtri multipli nella parte superiore della pagina consentono di ridefinire rapidamente l'elenco di risorse in base alla domanda a cui si sta provando a rispondere. Se, ad esempio, si desidera rispondere alla domanda su *quale computer con il tag "Production" manca l'agente di log Analytics* , è possibile combinare il filtro di **monitoraggio degli agenti** con il filtro **tag** , come illustrato nel clip seguente:

    ![Filtraggio per le risorse di produzione non monitorate](./media/asset-inventory/filtering-to-prod-unmonitored.gif)

    Non appena sono stati applicati i filtri, i valori di riepilogo vengono aggiornati per correlare i risultati della query. 

- **Opzioni di esportazione** -inventario consente di esportare i risultati delle opzioni di filtro selezionate in un file CSV. Inoltre, è possibile esportare la query in Azure Resource Graph Explorer per perfezionare, salvare o modificare la query KQL.

    ![Opzioni di esportazione dell'inventario](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > La documentazione di KQL fornisce un database con alcuni dati di esempio insieme ad alcune semplici query per ottenere la "sensazione" per il linguaggio. [Per altre informazioni, vedere questa esercitazione su KQL](https://docs.microsoft.com/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

- **Opzioni di gestione delle risorse** : l'inventario consente di eseguire query di individuazione complesse. Quando sono state trovate le risorse corrispondenti alle query, l'inventario fornisce collegamenti per operazioni quali:

    - Assegnare i tag alle risorse filtrate: selezionare le caselle di controllo accanto alle risorse che si desidera contrassegnare
    - Onboarding di nuovi server nel centro sicurezza: usare il pulsante della barra degli strumenti **Aggiungi server non Azure**


## <a name="how-does-asset-inventory-work"></a>Come funziona l'inventario asset?

Asset Inventory USA [Azure Resource Graph (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/), un servizio di Azure che offre la possibilità di eseguire query sui dati relativi alle posture di sicurezza del Centro sicurezza in più sottoscrizioni.

ARG è progettato per offrire un'efficace esplorazione delle risorse con la possibilità di eseguire query su larga scala.

Usando il [linguaggio di query kusto (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/), l'inventario degli asset può produrre rapidamente informazioni approfondite incrociate facendo riferimento a ASC data con altre proprietà delle risorse.


## <a name="how-to-use-asset-inventory"></a>Come usare Asset Inventory

1. Dall'intestazione laterale del Centro sicurezza selezionare **Inventory (inventario**).

1. Facoltativamente, per visualizzare una risorsa specifica, immettere il nome nella casella **Filtra per nome** .

1. Selezionare le opzioni pertinenti nei filtri per creare la query specifica che si desidera eseguire.

    ![Filtri dell'inventario](./media/asset-inventory/inventory-filters.png)

    Per impostazione predefinita, le risorse sono ordinate in base al numero di raccomandazioni di sicurezza attive.

    > [!IMPORTANT]
    > Le opzioni in ogni filtro sono specifiche per le risorse nelle sottoscrizioni attualmente selezionate **e** le selezioni negli altri filtri.
    >
    > Ad esempio, se è stata selezionata una sola sottoscrizione e la sottoscrizione non ha risorse con raccomandazioni di sicurezza in attesa per correggere (0 risorse non integre), il filtro **raccomandazioni** non avrà opzioni. 

1. Per usare i **risultati di sicurezza contengono** filtro, immettere il testo libero dall'ID, il controllo di sicurezza o il nome CVE di una vulnerabilità che trova per filtrare le risorse interessate:

    ![Filtro "i risultati della sicurezza contengono"](./media/asset-inventory/security-findings-contain-elements.png)

1. Per usare il filtro del piano **tariffario** , selezionare una o più opzioni (gratuito, parziale o standard):

    - Risorse **gratuite** per il piano tariffario gratuito
    - **Standard** -risorse disponibili nel piano tariffario standard
    - **Parziale** : si applica alle sottoscrizioni del piano tariffario standard, ma alcuni dei piani di sicurezza facoltativi sono disabilitati. Ad esempio, la sottoscrizione seguente si trova nel livello standard, ma ha cinque elementi del livello standard disabilitati. 

        ![Sottoscrizione al piano tariffario standard (parziale)](./media/asset-inventory/pricing-tier-partial.png)

1. Per esaminare ulteriormente i risultati della query, selezionare le risorse che interessano.

1. Facoltativamente, selezionare **Visualizza in Resource Graph Explorer** per aprire la query in Resource Graph Explorer.

    ![Query di inventario in ARG](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Se sono stati definiti alcuni filtri e la pagina è stata aperta, il Centro sicurezza non aggiornerà automaticamente i risultati. Eventuali modifiche alle risorse non avranno alcun effetto sui risultati visualizzati a meno che non si ricarichi manualmente la pagina o si selezioni **Aggiorna**.


## <a name="faq---inventory"></a>Domande frequenti-inventario

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>Perché non vengono visualizzati tutti i sottoscrizioni, i computer, gli account di archiviazione e così via?

La visualizzazione inventario elenca le risorse di una prospettiva CSPM (Cloud Security Posture Management). I filtri non restituiscono tutte le risorse nell'ambiente in uso. solo quelli con raccomandazioni in attesa (o ' attivo '). 

Se, ad esempio, si dispone di nove sottoscrizioni, ma solo otto sono attualmente presenti raccomandazioni, quando si filtra per **tipo di risorsa = sottoscrizioni** vengono visualizzate solo le otto sottoscrizioni con raccomandazioni attive:

![Non tutti i Subs restituiti se non sono presenti raccomandazioni attive](./media/asset-inventory/filtered-subscriptions-some.png)


### <a name="why-do-some-of-my-resources-show-blank-values-in-the-pricing-or-agent-monitoring-columns"></a>Perché alcune risorse visualizzano valori vuoti nelle colonne relative ai prezzi o al monitoraggio degli agenti?

Non tutte le risorse monitorate del Centro sicurezza hanno agenti. Ad esempio, account di archiviazione di Azure o risorse PaaS come dischi, app per la logica, analisi Data Lake e hub eventi.

Quando il monitoraggio dei prezzi o degli agenti non è pertinente per una risorsa, non viene visualizzato alcun elemento in tali colonne di inventario.

![Alcune risorse mostrano informazioni vuote nel monitoraggio degli agenti o nelle colonne dei prezzi](./media/asset-inventory/agent-pricing-blanks.png)



## <a name="next-steps"></a>Passaggi successivi

Questo articolo descrive la pagina inventario asset del Centro sicurezza di Azure.

Per ulteriori informazioni sugli strumenti correlati, vedere le pagine seguenti:

- [Grafico delle risorse di Azure (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/)

- [Linguaggio di query Kusto (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)