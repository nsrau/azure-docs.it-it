---
title: Regole di azione per gli avvisi di monitoraggio di Azure
description: Comprendere quali sono le regole di azione in Monitoraggio di Azure e su come configurare e gestirli.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: df069ee398ea2937f03765b10576061b5e541390
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656733"
---
# <a name="action-rules-preview"></a>Regole di azione (anteprima)

Regole di azione consentono di definire o eliminare le azioni in qualsiasi ambito di gestione risorse di Azure (sottoscrizione di Azure, gruppo di risorse o risorsa di destinazione). Possono essere filtrati vari della Guida in linea è restringere il subset specifico di istanze di avviso che si desidera agire sui.

## <a name="why-and-when-should-you-use-action-rules"></a>Perché e quando è opportuno utilizzare regole di azione?

### <a name="suppression-of-alerts"></a>Eliminazione degli avvisi

Esistono molti scenari in cui è utile eliminare le notifiche che generano avvisi. Questi scenari comprese tra eliminazione durante una finestra di manutenzione pianificata e eliminazione durante le ore non lavorative. Ad esempio, il team responsabile **ContosoVM** vuole eliminare le notifiche di avviso per il fine settimana prossimo, perché **ContosoVM** è in fase di manutenzione pianificata. 

Anche se il team può disabilitare ogni regola di avviso configurata nel **ContosoVM** manualmente e abilitarla nuovamente al termine della manutenzione, non è un processo semplice. Regole di azione consentono di definire l'eliminazione avvisi su larga scala con la possibilità di configurare in modo flessibile il periodo di eliminazione. Nell'esempio precedente, il team può definire una regola di azione nel **ContosoVM** che elimina tutte le notifiche di avviso per i fine settimana.


### <a name="actions-at-scale"></a>Operazioni su larga scala

Anche se le regole di avviso consentono di definire il gruppo di azione che viene attivata quando l'avviso viene generato, i clienti hanno spesso un gruppo di azioni comuni nel proprio ambito di operazioni. Ad esempio, un team responsabile del gruppo di risorse **ContosoRG** definirà probabilmente lo stesso gruppo di azione per tutte le regole di avviso definite all'interno **ContosoRG**. 

Regole di azione consentono di semplificare il processo. Con la definizione di azioni su larga scala, un gruppo di azioni può essere attivato per qualsiasi avviso che viene generato nell'ambito configurato. Nell'esempio precedente, il team può definire una regola di azione nel **ContosoRG** che genera lo stesso gruppo di azione per tutti gli avvisi generati in esso contenuti.

> [!NOTE]
> Regole di azione attualmente non si applicano agli avvisi di integrità dei servizi Azure.

## <a name="configuring-an-action-rule"></a>Configurare una regola di azione

È possibile accedere alla funzionalità selezionando **gestire le azioni** dalle **avvisi** pagina di destinazione in Monitoraggio di Azure. Quindi, selezionare **regole di azione (anteprima)** . Le regole è possibile accedere selezionando **regole di azione (anteprima)** dal dashboard della pagina di destinazione per gli avvisi.

![Regole di azione dalla pagina di destinazione di monitoraggio di Azure](media/alerts-action-rules/action-rules-landing-page.png)

Selezionare **+ nuova azione regola**. 

![Aggiungi nuova azione regola](media/alerts-action-rules/action-rules-new-rule.png)

In alternativa, è possibile creare una regola di azione mentre si configura una regola di avviso.

![Aggiungi nuova azione regola](media/alerts-action-rules/action-rules-alert-rule.png)

Ora si dovrebbe vedere la pagina di flusso per la creazione di regole di azione. Configurare gli elementi seguenti: 

![Nuovo flusso di creazione di azioni regola](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Ambito

Prima di tutto scegliere l'ambito (sottoscrizione di Azure, gruppo di risorse o risorsa di destinazione). È possibile anche la selezione multipla di una combinazione degli ambiti all'interno di una singola sottoscrizione.

![Ambito dell'azione regola](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>criteri di filtro

È inoltre possibile definire i filtri per limitare tali fino a un subset specifico di avvisi. 

I filtri disponibili sono: 

* **Gravità**: L'opzione per selezionare uno o più livelli di gravità degli avvisi. **Gravità = SEV1, con gestione** significa che la regola di azione è applicabile anche a tutti gli avvisi impostati per SEV1, con gestione.
* **Monitoraggio servizio**: Un filtro basato sul servizio di monitoraggio di origine. Questo filtro è anche la selezione multipla. Ad esempio, **servizio di monitoraggio = "Application Insights"** significa che la regola di azione è applicabile per tutti gli avvisi basati su Application Insights.
* **Tipo di risorsa**:  Un filtro basato su un tipo di risorsa specifico. Questo filtro è anche la selezione multipla. Ad esempio, **tipo di risorsa = "Macchine virtuali"** significa che la regola di azione è applicabile per tutte le macchine virtuali.
* **ID della regola di avviso**: Un'opzione per filtrare le regole di avviso specifiche usando l'ID di Resource Manager della regola di avviso.
* **Monitorare condizione**:  Un filtro per le istanze di avviso con uno **attivati** oppure **Resolved** come condizione di monitoraggio.
* **Descrizione**: Una corrispondenza regex (espressione regolare) che definisce la corrispondenza di stringa con la descrizione, definita come parte della regola di avviso. Ad esempio, **nella descrizione sono 'prod'** corrisponderanno a tutti gli avvisi che contengono la stringa "prod" nelle loro descrizioni.
* **Contesto (payload) dell'avviso**: Una corrispondenza di espressione regolare che definisce una stringa base alla quale confrontare i campi di contesto dell'avviso di payload di un avviso. Ad esempio, **avviso contesto (payload) contiene 'Computer-01'** corrisponderanno a tutti gli avvisi il cui payload contengono la stringa "Computer-01."

Questi filtri vengono applicati in combinazione con un'altra. Ad esempio, se si imposta **tipo di risorsa ' = le macchine virtuali** e **gravità ' = Sev0**, quindi aver applicato per tutti i **Sev0** avvisi su solo le macchine virtuali. 

![Filtri azione regola](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Configurazione del gruppo di eliminazione o azione

Successivamente, configurare la regola di azione per eliminare gli avvisi o il supporto di gruppo di azione. Non è possibile scegliere entrambi. La configurazione funziona su tutte le istanze di avviso corrispondenti all'ambito definito in precedenza e i filtri.

#### <a name="suppression"></a>Eliminazione

Se si seleziona **eliminazione**, configurare la durata per l'eliminazione delle azioni e notifiche. Scegliere una delle opzioni seguenti:
* **A partire da ora (sempre)** : Elimina tutte le notifiche per un periodo illimitato.
* **A un'ora pianificata**: Elimina le notifiche all'interno di una durata limitata.
* **Con una ricorrenza**: Elimina le notifiche su una pianificazione giornaliera, settimana o mensile ricorrente.

![Eliminazione di regole di azione](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Gruppo di azioni

Se si seleziona **gruppo di azioni** nell'interruttore, aggiungere un gruppo di azioni esistente o crearne uno nuovo. 

> [!NOTE]
> È possibile associare un solo gruppo di azioni con una regola di azione.

![Aggiungere o creare nuove regole di azione selezionando il gruppo di azione](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Dettagli azione regola

Infine, configurare i dettagli seguenti per la regola di azione:
* NOME
* Gruppo di risorse in cui viene salvato
* Descrizione 

## <a name="example-scenarios"></a>Scenari di esempio

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenario 1: Eliminazione di avvisi in base a gravità

Contoso desidera Elimina le notifiche per tutti gli avvisi Sev4 in tutte le macchine virtuali all'interno della sottoscrizione **ContosoSub** ogni settimana.

**Soluzione:** Creare una regola di azione con:
* Scope = **ContosoSub**
* Filtri
    * Severity = **Sev4**
    * Tipo di risorsa = **macchine virtuali**
* Eliminazione con ricorrenza impostata su settimanale, e **sabato** e **domenica** selezionata

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenario 2: Eliminazione di avvisi in base al contesto dell'avviso (payload)

Per eliminare le notifiche per tutti gli avvisi generati per di log Poiché Contoso desidera **Computer-01** nelle **ContosoSub** indefinitamente perché è in corso la manutenzione.

**Soluzione:** Creare una regola di azione con:
* Scope = **ContosoSub**
* Filtri
    * Monitoraggio servizio = **Log Analitica**
    * Avviso contesto (payload) contiene **Computer-01**
* Eliminazione impostato su **a partire da ora (sempre)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenario 3: Gruppo di azioni definito in un gruppo di risorse

Contoso ha definito [un avviso delle metriche a livello di sottoscrizione](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Ma vuole definire le azioni che attivano in modo specifico per gli avvisi generati dal gruppo di risorse **ContosoRG**.

**Soluzione:** Creare una regola di azione con:
* Scope = **ContosoRG**
* Nessun filtro
* Gruppo di azione impostata su **ContosoActionGroup**

> [!NOTE]
> *I gruppi di azioni definite all'interno delle regole di azione e le regole di avviso funzionano in modo indipendente, non deduplica.* Nello scenario descritto precedenti, se un gruppo di azione è definito per la regola di avviso, l'avviso si attiverà in combinazione con il gruppo di azioni definito nella regola di azione. 

## <a name="managing-your-action-rules"></a>Gestire le regole di azione

È possibile visualizzare e gestire le regole di azione dalla visualizzazione elenco:

![Visualizzazione elenco di regole di azione](media/alerts-action-rules/action-rules-list-view.png)

A questo punto, è possibile abilitare, disabilitare o eliminare le regole di azione su larga scala, selezionando la casella di controllo accanto agli. Quando si seleziona una regola di azione, verrà visualizzata la relativa pagina di configurazione. La pagina consente di aggiornare la definizione della regola azione e abilitarla o disabilitarla.

## <a name="best-practices"></a>Procedure consigliate

Registra avvisi creati con la [numero di risultati](alerts-unified-log.md) opzione generare una singola istanza di avviso tramite il risultato della ricerca intero (che potrebbe estendersi su più computer). In questo scenario, se un'azione di regola utilizza il **contesto avviso (payload)** filtro, agisce sull'istanza di avviso, purché vi sia una corrispondenza. Nello Scenario 2 descritto in precedenza, se i risultati di ricerca per l'avviso di log generato contengono entrambe **Computer-01** e **Computer-02**, viene eliminata la notifica di intera. È presente alcuna notifica per generato **Computer-02** affatto.

![Regole di azione e gli avvisi del log (numero di risultati)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Per utilizzare al meglio gli avvisi del log con regole di azione, creare avvisi del log con il [misurazione delle metriche](alerts-unified-log.md) opzione. Istanze separate di avvisi vengono generate da questa opzione, in base al relativo campo gruppo definito. Quindi, nello Scenario 2 istanze separate di avvisi vengono generate per **Computer-01** e **Computer-02**. A causa di regole di azione descritto nello scenario, solo la notifica relativa **Computer-01** viene eliminata. La notifica relativa **Computer-02** continua ad attivarsi come di consueto.

![Regole di azione e gli avvisi del log (numero di risultati)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Domande frequenti

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Anche se configurare una regola di azione, vorrei visualizzare tutte le possibili sovrapposti regole di azione, in modo che è possibile evitare le notifiche duplicate. È possibile eseguire questa operazione?

Dopo aver definito un ambito quando si configura una regola di azione, è possibile visualizzare un elenco di regole di azione che si sovrappongono nello stesso ambito (se presente). Questa sovrapposizione può essere una delle opzioni seguenti:

* Corrispondenza esatta: Ad esempio, si definisce la regola di azione e la regola di azione sovrapposti sono nella stessa sottoscrizione.
* Un subset: Ad esempio, si definisce la regola di azione è in una sottoscrizione e la regola di azione sovrapposti si trova in un gruppo di risorse all'interno della sottoscrizione.
* Un superset: Ad esempio, si definisce la regola di azione è in un gruppo di risorse e la sovrapposizione di azione regola è sulla sottoscrizione che contiene il gruppo di risorse.
* Un'intersezione: Ad esempio, si definisce la regola di azione è sul **VM1** e **VM2**, e la sovrapposizione applicata la regola azione **VM2** e **VM3**.

![La sovrapposizione delle regole di azione](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Configurare una regola di avviso, è possibile sapere se esistono già definite regole di azione che può agire su sto definendo la regola di avviso?

Dopo aver definito la risorsa di destinazione per la regola di avviso, è possibile visualizzare l'elenco delle regole di azione che operano nello stesso ambito (se presente) selezionando **Vista configurato azioni** sotto il **azioni** sezione. Questo elenco viene popolato in base gli scenari seguenti per l'ambito:

* Corrispondenza esatta: Ad esempio, si definisce la regola di avviso e la regola di azione sono nella stessa sottoscrizione.
* Un subset: Ad esempio, si definisce la regola di avviso è su una sottoscrizione e la regola di azione è in un gruppo di risorse all'interno della sottoscrizione.
* Un superset: Ad esempio, si definisce la regola di avviso è in un gruppo di risorse e la regola di azione è nella sottoscrizione che contiene il gruppo di risorse.
* Un'intersezione: Ad esempio, si definisce la regola di avviso è sul **VM1** e **VM2**, e l'azione applicata la regola **VM2** e **VM3**.
    
![La sovrapposizione delle regole di azione](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>È possibile visualizzare gli avvisi che sono stati eliminati da una regola di azione?

Nel [pagina di elenco degli avvisi](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), è possibile scegliere una colonna aggiuntiva denominata **eliminazione stato**. Se è stata eliminata la notifica relativa a un'istanza di avviso, Mostra lo stato nell'elenco.

![Istanze di avviso soppresse](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Se è presente una regola di azione con un gruppo di azione e l'altra con eliminazione attive nello stesso ambito, cosa accade?

Eliminazione sempre la precedenza nello stesso ambito.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Cosa accade se dispone di una risorsa che viene monitorata in due regole di azione separata. È possibile ottenere una o due notifiche? Ad esempio, **VM2** nello scenario seguente:

      "action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1"

Per ogni avviso in VM1 e VM3, gruppo di azioni AG1 verrà attivato una sola volta. Per ogni avviso relativo alle **VM2**, gruppo di azioni AG1 viene generato due volte, perché le regole di azione non deduplicare azioni. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Cosa accade se ho una risorsa controllata in due regole di azione separata e una chiama per azione mentre un altro per l'eliminazione? Ad esempio, **VM2** nello scenario seguente:

      "action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression"

Per ogni avviso in VM 1, gruppo di azioni AG1 verrà attivato una sola volta. Azioni e notifiche per ogni avviso in VM2 e VM3 verranno eliminate. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Cosa accade se si dispone di una regola di avviso e una regola di azione definita per la stessa risorsa, la chiamata a gruppi di azioni diverse? Ad esempio, **VM1** nello scenario seguente:

      "alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1" 
 
Per ogni avviso in VM 1, gruppo di azioni AG1 verrà attivato una sola volta. Ogni volta che viene attivata la regola avviso "rule1", viene inoltre attivata inoltre AG2. I gruppi di azioni definite all'interno delle regole di azione e le regole di avviso funzionano in modo indipendente, non deduplica. 

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sugli avvisi in Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
