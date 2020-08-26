---
title: Allocare i costi di Azure
description: Questo articolo illustra come creare regole di allocazione per distribuire i costi di sottoscrizioni, gruppi di risorse o tag a terzi.
author: bandersmsft
ms.author: banders
ms.date: 08/11/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: 025b9b7e503f38a111bd158f17b7fbeec5b23579
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684984"
---
# <a name="create-and-manage-azure-cost-allocation-rules-preview"></a>Creare e gestire le regole di allocazione dei costi di Azure (anteprima)

Nelle aziende di grandi dimensioni le risorse o i servizi di Azure vengono spesso gestiti centralmente, ma vengono utilizzati da reparti interni o business unit differenti. In genere, il team che si occupa della gestione centralizzata preferisce riallocare i costi dei servizi condivisi ai reparti interni o alle business unit aziendali che li usano attivamente. Questo articolo illustra come usare l'allocazione dei costi in Gestione costi di Azure.

Con l'allocazione dei costi è possibile riassegnare o distribuire i costi dei servizi condivisi da sottoscrizioni, gruppi di risorse o tag a sottoscrizioni, gruppi di risorse o tag differenti nell'organizzazione. L'allocazione dei costi sposta i costi dei servizi condivisi in un'altra sottoscrizione, in altri gruppi di risorse o in altri tag di proprietà dei reparti interni o delle business unit che li usano. In altri termini, l'allocazione dei costi consente di gestire e mostrare la _responsabilità dei costi_ da una posizione a un'altra.

L'allocazione dei costi non influisce sulla fatturazione. Le responsabilità di fatturazione non cambiano. Lo scopo principale dell'allocazione dei costi è consentire di addebitare i costi a terzi. Tutti i processi di chargeback avvengono nell'organizzazione all'esterno di Azure. L'allocazione dei costi consente di addebitare i costi mostrandoli quando vengono riassegnati o distribuiti.

I costi allocati vengono mostrati nell'analisi dei costi. Vengono visualizzati come elementi aggiuntivi associati a sottoscrizioni, gruppi di risorse o tag di destinazione specificati quando si crea una regola di allocazione dei costi.

> [!NOTE]
> La funzionalità di allocazione dei costi di Gestione costi di Azure è attualmente disponibile in anteprima pubblica. Alcune funzionalità di Gestione costi potrebbero non essere supportate o potrebbero presentare limitazioni.

## <a name="prerequisites"></a>Prerequisiti

- L'allocazione dei costi attualmente supporta solo i clienti con [Contratto del cliente Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) o [Contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
- Per creare o gestire una regola di allocazione dei costi, per i [contratti Enterprise](../manage/understand-ea-roles.md) è necessario usare un account amministratore dell'organizzazione. Oppure, per i contratti del cliente Microsoft, è necessario essere proprietari di un [account di fatturazione](../manage/understand-mca-roles.md).

## <a name="create-a-cost-allocation-rule"></a>Creare una regola di allocazione dei costi

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com/).
2. Passare a **Gestione dei costi e fatturazione** > **Gestione costi**.
3. In **Impostazioni** > **Configurazione** selezionare **Allocazione dei costi (anteprima)** .
4. Verificare che sia selezionata l'opzione corretta per la registrazione EA o l'account di fatturazione.
5. Selezionare **+Aggiungi**.
6. Immettere un testo descrittivo per il nome della regola di allocazione dei costi.

:::image type="content" source="./media/allocate-costs/rule-name.png" alt-text="Esempio che mostra la creazione di un nome di regola" lightbox="./media/allocate-costs/rule-name.png" :::

La data di inizio di valutazione della regola viene usata per generare le percentuali di allocazione dei costi precompilate.

1. Selezionare **Aggiungi origini** e quindi selezionare le sottoscrizioni, i gruppi di risorse o i tag per scegliere i costi da distribuire.
2. Selezionare **Aggiungi destinazioni** e quindi selezionare le sottoscrizioni, i gruppi di risorse o i tag che riceveranno i costi allocati.
3. Se è necessario creare regole aggiuntive per l'allocazione dei costi, ripetere questo processo.

## <a name="configure-the-allocation-percentage"></a>Configurare la percentuale di allocazione

Configurare la percentuale di allocazione per definire come dividere proporzionalmente i costi tra le destinazioni specificate. È possibile definire manualmente le percentuali in numeri interi per creare una regola di allocazione. In alternativa, è possibile dividere i costi proporzionalmente in base all'utilizzo corrente delle risorse di calcolo, archiviazione o rete tra le destinazioni specificate.

Quando si distribuiscono i costi in base a risorse di calcolo, di archiviazione o di rete, la percentuale proporzionale deriva dalla valutazione dei costi della destinazione selezionata. I costi sono associati al tipo di risorsa per il mese di fatturazione corrente.

Quando si distribuiscono i costi in proporzione al costo totale, la percentuale proporzionale viene assegnata in base alla somma o al costo totale delle destinazioni selezionate per il mese di fatturazione corrente.

:::image type="content" source="./media/allocate-costs/cost-distribution.png" alt-text="Esempio che mostra la percentuale di allocazione" lightbox="./media/allocate-costs/cost-distribution.png" :::

Una volta impostate, le percentuali precompilate definite sono fisse. Vengono usate per tutte le allocazioni in corso. Le percentuali cambiano solo quando la regola viene aggiornata manualmente.

1. Selezionare una delle opzioni seguenti nell'elenco **Precompila la percentuale su**.
    - **Distribuisci in modo uniforme**: ogni destinazione riceve una percentuale uniforme del costo totale.
    - **Costo totale**: crea un rapporto proporzionale alle destinazioni in base al relativo costo totale. Il rapporto viene usato per distribuire i costi dalle origini selezionate.
    - **Costo delle risorse di calcolo**: crea un rapporto proporzionale alle destinazioni in base al costo delle risorse di calcolo di Azure (tipi di risorse nello spazio dei nomi [Microsoft.Compute](https://docs.microsoft.com/azure/templates/microsoft.compute/allversions). Il rapporto viene usato per distribuire i costi dalle origini selezionate.
    - **Costo di archiviazione**: crea un rapporto proporzionale alle destinazioni in base ai costi delle risorse di archiviazione di Azure (tipi di risorse nello spazio dei nomi [Microsoft.Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions)). Il rapporto viene usato per distribuire i costi dalle origini selezionate.
    - **Costo di rete**: crea un rapporto proporzionale alle destinazioni in base ai costi delle risorse di rete di Azure (tipi di risorse nello spazio dei nomi [Microsoft.Network](https://docs.microsoft.com/azure/templates/microsoft.network/allversions)). Il rapporto viene usato per distribuire i costi dalle origini selezionate.
    - **Personalizzata**: consente di specificare manualmente una percentuale in numero intero. Il totale specificato deve essere uguale al 100%.
1. Una volta configurata la regola, selezionare **Crea**.

Viene avviata l'elaborazione della regola di allocazione. Quando la regola è attiva, tutti i costi dell'origine selezionata vengono allocati alle destinazioni specificate.

> [!NOTE] 
> L'elaborazione della nuova regola può richiedere fino a due ore prima che venga completata e diventi attiva.

## <a name="verify-the-cost-allocation-rule"></a>Verificare la regola di allocazione dei costi

Quando la regola di allocazione dei costi è attiva, i costi delle origini selezionate vengono distribuiti alle destinazioni di allocazione specificate. Usare le informazioni seguenti per verificare se i costi vengono allocati correttamente alle destinazioni.

### <a name="view-cost-allocation-for-a-subscription"></a>Visualizzare l'allocazione dei costi per una sottoscrizione

È possibile visualizzare l'impatto della regola di allocazione nell'analisi dei costi. Passare a [Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nel portale di Azure. Selezionare una sottoscrizione nell'elenco di destinazioni di una regola di allocazione dei costi attiva. Selezionare quindi **Analisi dei costi** nel menu. In Analisi dei costi selezionare **Raggruppa per** e quindi **Allocazione dei costi**. La visualizzazione risultante mostra una suddivisione dei costi generata in base alla sottoscrizione. Vengono visualizzati anche i costi allocati alla sottoscrizione, come illustrato nell'immagine seguente.

:::image type="content" source="./media/allocate-costs/cost-breakdown.png" alt-text="Esempio che mostra la ripartizione dei costi" lightbox="./media/allocate-costs/cost-breakdown.png" :::

### <a name="view-cost-allocation-for-a-resource-group"></a>Visualizzare l'allocazione dei costi per un gruppo di risorse

Usare un processo simile per verificare l'impatto di una regola di allocazione dei costi per un gruppo di risorse. Nel portale di Azure passare a [Gruppi di risorse](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups). Selezionare un gruppo di risorse nell'elenco di destinazioni di una regola di allocazione dei costi attiva. Selezionare quindi **Analisi dei costi** nel menu. In Analisi dei costi selezionare **Raggruppa per** e quindi **Allocazione dei costi**. La visualizzazione mostra una suddivisione dei costi generata in base al gruppo di risorse. Vengono visualizzati anche i costi allocati al gruppo di risorse.

### <a name="view-cost-allocation-for-tags"></a>Visualizzare l'allocazione dei costi per i tag

Nel portale di Azure passare a **Gestione dei costi e fatturazione** > **Gestione costi** > **Analisi dei costi**. In Analisi dei costi selezionare **Aggiungi filtro**. Selezionare **Tag**, scegliere la chiave e i valori dei tag a cui sono stati allocati costi.

:::image type="content" source="./media/allocate-costs/tagged-costs.png" alt-text="Esempio che mostra i costi per gli elementi con tag" lightbox="./media/allocate-costs/tagged-costs.png" :::

## <a name="edit-an-existing-cost-allocation-rule"></a>Modificare una regola di allocazione dei costi esistente

È possibile modificare una regola di allocazione dei costi per cambiare l'origine o la destinazione oppure per aggiornare la percentuale precompilata per le opzioni di calcolo, archiviazione o rete. Modificare le regole nello stesso modo in cui vengono create. La rielaborazione della modifica delle regole esistenti può richiedere fino a due ore.

## <a name="frequently-asked-questions-faq"></a>Domande frequenti

Le sezioni seguenti contengono le domande più comuni relative all'allocazione dei costi.

### <a name="what-are-the-current-limitations-with-cost-allocation-in-public-preview"></a>Quali sono le limitazioni correnti con l'allocazione dei costi nell'anteprima pubblica?
<a name="limitations"></a>

Attualmente, l'allocazione dei costi è supportata in Gestione costi nelle visualizzazioni Analisi dei costi, budget e previsioni. I costi allocati vengono anche visualizzati nell'elenco di sottoscrizioni all'interno della pagina di panoramica Sottoscrizioni.

Gli elementi seguenti non sono attualmente supportati dall'anteprima pubblica di allocazione dei costi:

- [Esportazioni](tutorial-export-acm-data.md) pianificate
- Dati esposti dall'API [Dettagli utilizzo](https://docs.microsoft.com/rest/api/consumption/usagedetails/list)
- Area delle sottoscrizioni di fatturazione
- [App Power BI Gestione costi](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)
- [Connettore Power BI Desktop](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management)

### <a name="are-costs-factored-into-budgets-and-forecast-views"></a>I costi vengono fattorizzati nelle visualizzazioni di budget e previsioni?
<a name="budgets-forecast"></a>

Sì. I costi allocati vengono fattorizzati e supportati da budget e previsioni. Le visualizzazioni di budget e previsioni mostrano i rispettivi costi allocati, configurati dalle regole di allocazione dei costi.

### <a name="if-a-cost-allocation-rule-is-deleted-what-happens"></a>Cosa succede se una regola di allocazione dei costi viene eliminata?
<a name="delete-rule"></a>

Se una regola di allocazione dei costi viene eliminata, tutti i costi mensili della fatturazione, aperti e correnti, allocati alle destinazioni vengono rimossi. Se la regola di allocazione dei costi esiste da diversi mesi, i mesi precedenti dei dati di allocazione rimangono così come originariamente impostati dalla regola di allocazione.

### <a name="why-is-an-enrollment-admin-or-a-billing-account-admin-needed-to-create-cost-allocation-rules"></a>Perché è necessario un amministratore della registrazione o un account di fatturazione per creare le regole di allocazione dei costi?
<a name="why-admin"></a>

Le regole di allocazione dei costi vengono create nell'ambito della registrazione (Contratto Enterprise) o nell'ambito dell'account di fatturazione (Contratto del cliente Microsoft). Le autorizzazioni per apportare modifiche a questi ambiti richiedono privilegi di amministratore fatturazione.

### <a name="why-are-sources-and-targets-limited-to-25-per-rule"></a>Perché le origini e le destinazioni sono limitate a 25 per regola?
<a name="source-target-rule-limit"></a>

Il limite è previsto nell'anteprima per garantire prestazioni e scalabilità ottimali dell'allocazione dei costi. È probabile che i limiti verranno aumentati o rimossi quando l'allocazione dei costi diventerà disponibile a livello generale.

### <a name="what-can-happen-if-cost-allocation-rules-sourcestargets-overlap"></a>Che cosa succede se le regole di allocazione dei costi (origini/destinazioni) si sovrappongono?
<a name="rule-overlap"></a>

Le regole con origini o destinazioni sovrapposte non sono consigliate. Le regole di allocazione dei costi vengono applicate nell'ordine in base alla data di creazione, quindi se si sovrappongono, quella con la data di creazione meno recente avrà la precedenza.

## <a name="next-steps"></a>Passaggi successivi

- Creare o aggiornare le regole di allocazione usando l'[API REST di allocazione dei costi](https://go.microsoft.com/fwlink/?linkid=2135004)
- Informazioni su [come ottimizzare gli investimenti nel cloud con Gestione costi di Azure](cost-mgt-best-practices.md)