---
title: Regole di azione per gli avvisi di monitoraggio di Azure
description: Informazioni sulle regole di azione in monitoraggio di Azure e su come configurarle e gestirle.
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.author: robb
ms.date: 04/25/2019
ms.openlocfilehash: e9de7a1fe4cee16cd1d22ba764ab9eccdf3979fd
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767688"
---
# <a name="action-rules-preview"></a>Regole di azione (anteprima)

Le regole di azione consentono di definire o escludere azioni in qualsiasi ambito Azure Resource Manager (sottoscrizione di Azure, gruppo di risorse o risorsa di destinazione). Hanno diversi filtri che consentono di limitare il subset specifico di istanze di avviso su cui si vuole agire.

## <a name="why-and-when-should-you-use-action-rules"></a>Perché e quando utilizzare le regole di azione?

### <a name="suppression-of-alerts"></a>Eliminazione di avvisi

Esistono molti scenari in cui è utile disattivare le notifiche generate dagli avvisi. Questi scenari variano dall'eliminazione durante una finestra di manutenzione pianificata per l'eliminazione durante le ore non lavorative. Ad esempio, il team responsabile di **ContosoVM** desidera disattivare le notifiche di avviso per il prossimo weekend, perché **ContosoVM** è in fase di manutenzione pianificata. 

Sebbene il team possa disabilitare manualmente ogni regola di avviso configurata in **ContosoVM** (e abilitarla di nuovo dopo la manutenzione), non si tratta di un processo semplice. Le regole di azione consentono di definire l'eliminazione degli avvisi su larga scala con la possibilità di configurare in modo flessibile il periodo di eliminazione. Nell'esempio precedente, il team può definire una regola di azione in **ContosoVM** che elimina tutte le notifiche di avviso per il fine settimana.


### <a name="actions-at-scale"></a>Azioni su larga scala

Sebbene le regole di avviso consentano di definire il gruppo di azioni che viene attivato quando viene generato l'avviso, i clienti hanno spesso un gruppo di azioni comune nell'ambito delle operazioni. Ad esempio, un team responsabile del gruppo di risorse **ContosoRG** definirà probabilmente lo stesso gruppo di azioni per tutte le regole di avviso definite in **ContosoRG**. 

Le regole di azione consentono di semplificare questo processo. Definendo azioni su larga scala, un gruppo di azioni può essere attivato per qualsiasi avviso generato nell'ambito configurato. Nell'esempio precedente, il team può definire una regola di azione in **ContosoRG** che attiverà lo stesso gruppo di azione per tutti gli avvisi generati al suo interno.

> [!NOTE]
> Le regole di azione attualmente non si applicano agli avvisi di integrità del servizio di Azure.

## <a name="configuring-an-action-rule"></a>Configurazione di una regola di azione

È possibile accedere alla funzionalità selezionando **Gestisci azioni** dalla pagina di destinazione **avvisi** in monitoraggio di Azure. Selezionare quindi **regole di azione (anteprima)** . È possibile accedere alle regole selezionando **regole di azione (anteprima)** dal dashboard della pagina di destinazione per gli avvisi.

![Regole di azione dalla pagina di destinazione di monitoraggio di Azure](media/alerts-action-rules/action-rules-landing-page.png)

Selezionare **+ nuova regola di azione**. 

![Aggiungi nuova regola azione](media/alerts-action-rules/action-rules-new-rule.png)

In alternativa, è possibile creare una regola di azione durante la configurazione di una regola di avviso.

![Aggiungi nuova regola azione](media/alerts-action-rules/action-rules-alert-rule.png)

A questo punto verrà visualizzata la pagina flusso per la creazione di regole di azione. Configurare gli elementi seguenti: 

![Nuovo flusso di creazione della regola azione](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Scope

Scegliere prima di tutto l'ambito (sottoscrizione di Azure, gruppo di risorse o risorsa di destinazione). È anche possibile selezionare una combinazione di ambiti all'interno di una singola sottoscrizione.

![Ambito della regola azione](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Criteri di filtro

È inoltre possibile definire filtri per limitarli a un subset specifico di avvisi. 

I filtri disponibili sono: 

* **Gravità**: l'opzione per selezionare uno o più livelli di gravità degli avvisi. **Gravità = Sev1** significa che la regola di azione è applicabile per tutti gli avvisi impostati su Sev1.
* **Servizio**monitoraggio: un filtro basato sul servizio di monitoraggio di origine. Questo filtro è anche Select multiplo. Ad esempio, **Monitor Service = "Application Insights"** indica che la regola di azione è applicabile per tutti gli avvisi basati su Application Insights.
* **Tipo di risorsa**: un filtro basato su un tipo di risorsa specifico. Questo filtro è anche Select multiplo. Ad esempio, **Resource Type = "Virtual Machines"** significa che la regola di azione è applicabile a tutte le macchine virtuali.
* **ID regola di avviso**: opzione per filtrare le regole di avviso specifiche usando l'ID gestione risorse della regola di avviso.
* **Condizione di monitoraggio**: filtro per le istanze di avviso con **attivato** o **risolto** come condizione di monitoraggio.
* **Descrizione**: corrispondenza Regex (Regular Expression) che definisce una corrispondenza di stringa rispetto alla descrizione, definita come parte della regola di avviso. Ad esempio, la **Descrizione contiene ' prod '** corrispondente a tutti gli avvisi che contengono la stringa "prod" nelle descrizioni.
* **Contesto avviso (payload)** : una corrispondenza Regex che definisce una corrispondenza di stringa con i campi di contesto degli avvisi del payload di un avviso. Ad esempio, il **contesto dell'avviso (payload) contiene "computer-01"** corrisponde a tutti gli avvisi i cui payload contengono la stringa "computer-01".

Questi filtri vengono applicati insieme tra loro. Se ad esempio si imposta il **tipo di risorsa ' = macchine virtuali** e **gravità' = Sev0**, è stato applicato un filtro per tutti gli avvisi **Sev0** solo nelle VM. 

![Filtri delle regole azione](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Eliminazione o configurazione del gruppo di azioni

Configurare quindi la regola di azione per il supporto dell'eliminazione avvisi o del gruppo di azioni. Non è possibile scegliere entrambi. La configurazione agisce su tutte le istanze di avviso che corrispondono all'ambito e ai filtri definiti in precedenza.

#### <a name="suppression"></a>Soppressione

Se si seleziona l' **eliminazione**, configurare la durata per l'eliminazione di azioni e notifiche. Scegliere una delle opzioni seguenti:
* **Da Now (always)** : Disattiva tutte le notifiche a tempo indefinito.
* **All'ora pianificata**: disattiva le notifiche entro una durata limitata.
* **Con una ricorrenza**: disattiva le notifiche in base a una pianificazione ricorrente giornaliera, settimanale o mensile.

![Eliminazione regola azione](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Gruppo di azioni

Se si seleziona **gruppo di azioni** nell'interruttore, aggiungere un gruppo di azioni esistente o crearne uno nuovo. 

> [!NOTE]
> È possibile associare un solo gruppo di azione a una regola di azione.

![Aggiungere o creare una nuova regola di azione selezionando il gruppo di azioni](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Dettagli regola azione

Infine, configurare i dettagli seguenti per la regola di azione:
* name
* Gruppo di risorse in cui viene salvato
* Description 

## <a name="example-scenarios"></a>Scenari di esempio

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenario 1: eliminazione di avvisi in base alla gravità

Contoso vuole escludere le notifiche per tutti gli avvisi Sev4 su tutte le macchine virtuali nella sottoscrizione **ContosoSub** ogni fine settimana.

**Soluzione:** Creare una regola di azione con:
* Ambito = **ContosoSub**
* Filtri
    * Gravità = **Sev4**
    * Tipo di risorsa = **macchine virtuali**
* Eliminazione con ricorrenza impostata su settimanale, **sabato** e **domenica** controllati

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenario 2: eliminazione di avvisi in base al contesto dell'avviso (payload)

Contoso desidera disattivare le notifiche per tutti gli avvisi del log generati per **computer-01** in **ContosoSub** per un periodo di tempo indefinito durante la manutenzione.

**Soluzione:** Creare una regola di azione con:
* Ambito = **ContosoSub**
* Filtri
    * Servizio di monitoraggio = **log Analytics**
    * Il contesto dell'avviso (payload) contiene **computer-01**
* Eliminazione impostata su **da ora (sempre)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenario 3: gruppo di azioni definito in un gruppo di risorse

Contoso ha definito [un avviso di metrica a livello di sottoscrizione](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Ma desidera definire le azioni che vengono attivate in modo specifico per gli avvisi generati dal gruppo di risorse **ContosoRG**.

**Soluzione:** Creare una regola di azione con:
* Ambito = **ContosoRG**
* Nessun filtro
* Gruppo di azioni impostato su **ContosoActionGroup**

> [!NOTE]
> *I gruppi di azioni definiti nelle regole di azione e nelle regole di avviso operano in modo indipendente, senza deduplicazione.* Nello scenario descritto in precedenza, se per la regola di avviso viene definito un gruppo di azioni, questo viene attivato insieme al gruppo di azioni definito nella regola di azione. 

## <a name="managing-your-action-rules"></a>Gestione delle regole di azione

È possibile visualizzare e gestire le regole di azione dalla visualizzazione elenco:

![Visualizzazione elenco regole di azione](media/alerts-action-rules/action-rules-list-view.png)

Da qui è possibile abilitare, disabilitare o eliminare le regole di azione su larga scala selezionando la casella di controllo accanto. Quando si seleziona una regola di azione, viene visualizzata la pagina di configurazione. La pagina consente di aggiornare la definizione della regola di azione e di abilitarla o disabilitarla.

## <a name="best-practices"></a>Procedure consigliate

Gli avvisi del log creati con l'opzione [numero di risultati](alerts-unified-log.md) generano una singola istanza di avviso utilizzando l'intero risultato della ricerca, che può estendersi su più computer. In questo scenario, se una regola di azione usa il filtro del **contesto dell'avviso (payload)** , agisce sull'istanza di avviso purché esista una corrispondenza. Nello scenario 2, descritto in precedenza, se i risultati della ricerca per l'avviso del log generato contengono sia **computer-01** che **computer-02**, viene eliminato l'intera notifica. Non viene generata alcuna notifica per **computer-02** .

![Regole di azione e avvisi del log (numero di risultati)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Per usare al meglio gli avvisi del log con le regole di azione, creare gli avvisi del log con l'opzione [misurazione metrica](alerts-unified-log.md) . Le istanze di avviso separate vengono generate da questa opzione, in base al campo gruppo definito. Quindi, nello scenario 2 vengono generate istanze di avviso separate per **computer-01** e **computer-02**. A causa della regola di azione descritta nello scenario, viene eliminata solo la notifica per **computer-01** . La notifica per **computer-02** continua a essere attivata normalmente.

![Regole di azione e avvisi del log (numero di risultati)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>FAQ

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Durante la configurazione di una regola di azione, desidero visualizzare tutte le possibili regole di azione sovrapposte, in modo da evitare notifiche duplicate. È possibile eseguire questa operazione?

Dopo aver definito un ambito durante la configurazione di una regola di azione, è possibile visualizzare un elenco di regole di azione sovrapposte allo stesso ambito (se presente). Questa sovrapposizione può essere una delle seguenti opzioni:

* Una corrispondenza esatta: ad esempio, la regola di azione che si sta definendo e la regola azione sovrapposta si trovano nella stessa sottoscrizione.
* Un subset: ad esempio, la regola di azione che si sta definendo si trova in una sottoscrizione e la regola azione sovrapposta si trova in un gruppo di risorse all'interno della sottoscrizione.
* Un superset: ad esempio, la regola di azione che si sta definendo si trova in un gruppo di risorse e la regola di azione sovrapposta si trova nella sottoscrizione che contiene il gruppo di risorse.
* Un'intersezione: ad esempio, la regola di azione che si sta definendo si trova in **VM1** e **VM2**e la regola di azione sovrapposta si trova in **VM2** e **VM3**.

![Regole di azione sovrapposte](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Durante la configurazione di una regola di avviso, è possibile verificare se sono già state definite regole di azione che potrebbero agire sulla regola di avviso che sto definendo?

Dopo aver definito la risorsa di destinazione per la regola di avviso, è possibile visualizzare l'elenco delle regole di azione che agiscono sullo stesso ambito, se presente, selezionando **Visualizza azioni configurate** nella sezione **azioni** . Questo elenco viene popolato in base agli scenari seguenti per l'ambito:

* Una corrispondenza esatta: ad esempio, la regola di avviso che si sta definendo e la regola di azione si trovano nella stessa sottoscrizione.
* Un subset: ad esempio, la regola di avviso che si sta definendo si trova in una sottoscrizione e la regola di azione si trova in un gruppo di risorse all'interno della sottoscrizione.
* Un superset: ad esempio, la regola di avviso che si sta definendo si trova in un gruppo di risorse e la regola di azione si trova nella sottoscrizione che contiene il gruppo di risorse.
* Un'intersezione: ad esempio, la regola di avviso che si sta definendo si trova in **VM1** e **VM2**e la regola di azione si trova in **VM2** e **VM3**.
    
![Regole di azione sovrapposte](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>È possibile visualizzare gli avvisi che sono stati eliminati da una regola di azione?

Nella [pagina elenco avvisi](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)è possibile scegliere una colonna aggiuntiva denominata **stato di eliminazione**. Se la notifica relativa a un'istanza di avviso è stata eliminata, questo stato verrà visualizzato nell'elenco.

![Istanze di avvisi eliminati](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Se è presente una regola di azione con un gruppo di azioni e un'altra con eliminazione attiva nello stesso ambito, cosa accade?

L'eliminazione ha sempre la precedenza sullo stesso ambito.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Cosa accade se si dispone di una risorsa che viene monitorata in due regole di azione separate? Sono state ricevute una o due notifiche? Ad esempio, **VM2** nello scenario seguente:

      action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1

Per ogni avviso in VM1 e VM3, il gruppo di azioni AG1 verrebbe attivato una sola volta. Per ogni avviso in **VM2**, il gruppo di azioni AG1 verrebbe attivato due volte, perché le regole di azione non deduplicano le azioni. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Cosa accade se una risorsa viene monitorata in due regole di azione separate e una viene chiamata per l'azione mentre un'altra per l'eliminazione? Ad esempio, **VM2** nello scenario seguente:

      action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression

Per ogni avviso in VM1, il gruppo di azioni AG1 verrebbe attivato una sola volta. Le azioni e le notifiche per ogni avviso in VM2 e VM3 verranno eliminati. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Cosa accade se si dispone di una regola di avviso e di una regola di azione definita per la stessa risorsa che chiama gruppi di azioni diversi? Ad esempio, **VM1** nello scenario seguente:

      alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1 
 
Per ogni avviso in VM1, il gruppo di azioni AG1 verrebbe attivato una sola volta. Ogni volta che viene attivata la regola di avviso "rule1", attiverà anche AG2. I gruppi di azioni definiti nelle regole di azione e nelle regole di avviso operano in modo indipendente, senza deduplicazione. 

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sugli avvisi in Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
