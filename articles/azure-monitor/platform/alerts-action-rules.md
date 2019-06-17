---
title: Regole di azione per gli avvisi di monitoraggio di Azure
description: Informazioni su quali sono le regole di azione e come configurare e gestirli.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 6e97826499842a257f6402bd5268edc4cd6a486e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734907"
---
# <a name="action-rules-preview"></a>Regole di azione (anteprima)

Regole di azione consentono di definire le azioni (o l'eliminazione delle azioni) in qualsiasi ambito di gestione risorse (sottoscrizione, gruppo di risorse o risorsa). Hanno un'ampia gamma di filtri che consentono di limitare a un sottoinsieme di istanze di avviso che si desidera agire su specifici. 

## <a name="why-and-when-should-you-use-action-rules"></a>Perché e quando è opportuno utilizzare regole di azione?

### <a name="suppression-of-alerts"></a>Eliminazione degli avvisi

Spesso esistono molti scenari in cui sarebbe utile eliminare le notifiche generate da avvisi, che potrebbero variare da eliminazione durante una finestra di manutenzione pianificata a eliminazione durante non lavorative. Ad esempio, il team responsabile 'ContosoVM' vuole eliminare le notifiche di avviso per i fine settimana prossimo poiché 'ContosoVM' è in fase di una manutenzione pianificata. Anche se è possibile disabilitare ogni avviso regola configurata manualmente in 'ContosoVM' (e abilitare di nuovo registra la manutenzione), non è un'esperienza semplice. Regole di azione consentono di definire l'eliminazione avvisi su larga scala con la possibilità di configurare in modo flessibile il periodo di eliminazione. Tornando all'esempio precedente, il team può ora definire una regola di azione nel 'ContosoVM' che eliminerà tutte le notifiche di avviso per i fine settimana.


### <a name="actions-at-scale"></a>Operazioni su larga scala

Anche se le regole di avviso consentono di definire il gruppo di azione che viene attivata quando l'avviso viene generato, i clienti spesso tendono a disporre di un gruppo di azioni comuni nel proprio ambito di operazioni. Ad esempio, un team responsabile del gruppo di risorse 'ContosoRG' definirà probabilmente lo stesso gruppo di azione per tutte le regole di avviso definite all'interno di 'ContosoRG'. Regole di azione consentono di semplificare il processo, consentendo di definire le azioni su larga scala, in modo che un gruppo di azioni può essere attivato per qualsiasi avviso generato sull'ambito configurato. Tornando all'esempio precedente, il team può ora definire una regola di azione nel 'ContosoRG' che attiverà lo stesso gruppo di azione per tutti gli avvisi generati in esso contenuti.


## <a name="configuring-an-action-rule"></a>Configurare una regola di azione

È possibile accedere alla funzionalità selezionando **gestire le azioni** dagli avvisi pagina di destinazione in Monitoraggio di Azure. Quindi selezionare **regole di azione (anteprima)** . È possibile accedervi selezionando **regole di azione (anteprima)** dal dashboard della pagina di destinazione per gli avvisi.

![Regole di azione dalla pagina di destinazione di monitoraggio di Azure](media/alerts-action-rules/action-rules-landing-page.png)

Selezionare **+ nuova azione regola**. 

![Aggiungi nuova azione regola](media/alerts-action-rules/action-rules-new-rule.png)

In alternativa, è anche possibile creare una regola di azione durante la configurazione di una regola di avviso.

![Aggiungi nuova azione regola](media/alerts-action-rules/action-rules-alert-rule.png)

Si noterà ora il flusso di Creazione regole di azione aprire. Configurare gli elementi seguenti: 

![Nuovo flusso di creazione di azioni regola](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>`Scope`

Prima di tutto scegliere l'ambito, vale a dire, risorsa di destinazione, gruppo di risorse o sottoscrizione. È inoltre la possibilità di selezionare una combinazione di uno qualsiasi dei valori precedenti (all'interno di una singola sottoscrizione). 

![Ambito dell'azione regola](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>criteri di filtro

È inoltre possibile definire filtri per limitare ulteriormente fino a un subset specifico di avvisi sull'ambito definito. 

I filtri disponibili sono: 

* **Gravità**: Selezionare uno o più livelli di gravità degli avvisi. Gravità = SEV1, con gestione significa che la regola di azione è applicabile per tutti gli avvisi con gravità come SEV1, con gestione.
* **Monitoraggio servizio**: Filtro basato sul servizio di monitoraggio di origine. Si tratta anche di una seleziona multipla. Ad esempio, servizio di monitoraggio = "Application Insights" avvisi basati su significa che la regola di azione è applicabile per tutti i "Application Insights".
* **Tipo di risorsa**: Filtrare in base a un tipo di risorsa specifico. Si tratta anche di una seleziona multipla. Ad esempio, tipo di risorsa = "Macchine virtuali" significa che la regola di azione è applicabile per tutte le macchine virtuali.
* **ID della regola di avviso**: Consente di filtrare le regole di avviso specifiche usando l'ID di Resource Manager della regola di avviso.
* **Monitorare condizione**: Filtrare le istanze di avviso con "Attivato" o "Risolto" come condizione di monitoraggio.
* **Descrizione**: Espressione regolare corrispondenza all'interno della descrizione definita come parte della regola di avviso.
* **Contesto dell'avviso (payload)** : Espressione regolare corrispondenza all'interno di [al contesto dell'avviso](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) campi di un'istanza di avviso.

Questi filtri vengono applicati in combinazione tra loro. Ad esempio, se imposta 'Tipo di risorsa' = 'Macchine virtuali' e 'Gravità' = 'Sev0', quindi ho ho filtrato per tutti gli avvisi 'Sev0' su solo delle macchine virtuali. 

![Filtri azione regola](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Configurazione del gruppo di eliminazione o azione

Successivamente, configurare la regola di azione per eliminare gli avvisi o il supporto di gruppo di azione. Non è possibile scegliere entrambi. La configurazione funziona su tutte le istanze di avviso di ambito definito in precedenza e i filtri corrispondenti.

#### <a name="suppression"></a>Eliminazione

Se si seleziona **eliminazione**, configurare la durata per l'eliminazione delle azioni e notifiche. Scegliere una delle operazioni seguenti:
* **A partire da ora (sempre)** : Elimina tutte le notifiche per un periodo illimitato.
* **A un'ora pianificata**: Elimina notifiche entro un periodo di tempo limitato.
* **Con una ricorrenza**: Eliminare una pianificazione ricorrenza, che può essere giornaliera, settimanale o mensile.

![Eliminazione di regole di azione](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Gruppo di azioni

Se si seleziona **gruppo di azioni** nell'interruttore, aggiungere un gruppo di azioni esistente o crearne uno nuovo. 

> [!NOTE]
> È possibile associare un solo gruppo di azioni con una regola di azione.

![Gruppo di azione di azioni regola](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Dettagli azione regola

Infine, configurare i dettagli seguenti per la regola di azione
* Name
* Gruppo di risorse in cui verrà salvato
* Descrizione 

## <a name="example-scenarios"></a>Scenari di esempio

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenario 1: Eliminazione di avvisi in base a gravità

Contoso desidera Elimina le notifiche per tutti gli avvisi Sev4 in tutte le macchine virtuali all'interno di abbonamento 'ContosoSub' ogni fine settimana.

**Soluzione:** Creare una regola di azione con
* Scope = 'ContosoSub'
* Filtri
    * Severity = 'Sev4'
    * Tipo di risorsa = 'Macchine virtuali'
* Imposta una eliminazione con ricorrenza settimanale e "Saturday" e "Domenica" selezionata

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenario 2: Eliminazione di avvisi in base al contesto dell'avviso (payload)

Contoso desidera eliminare le notifiche per tutti i log di avvisi generati per 'Computer-01' in 'ContosoSub' all'infinito quando è in corso la manutenzione.

**Soluzione:** Creare una regola di azione con
* Scope = 'ContosoSub'
* Filtri
    * Monitoraggio servizio = 'Log Analitica'
    * Contesto dell'avviso (payload) contiene 'Computer-01'
* Eliminazione impostato su ' da adesso (sempre)'

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenario 3: Gruppo di azioni definito in un gruppo di risorse

Contoso ha definito [un avviso delle metriche a livello di sottoscrizione](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor), ma si desidera definire le azioni che attivano in modo specifico per gli avvisi generati dal relativo gruppo di risorse 'ContosoRG'.

**Soluzione:** Creare una regola di azione con
* Scope = 'ContosoRG'
* Nessun filtro
* Gruppo di azione impostata su 'ContosoActionGroup'

> [!NOTE]
> **I gruppi di azioni definite all'interno delle regole di azione e le regole di avviso funzionano in modo indipendente, con alcuna deduplicazione**. Nello scenario descritto in precedenza, se è presente un gruppo di azione definito per la regola di avviso, attiveranno in combinazione con il gruppo di azioni definito nella regola di azione. 

## <a name="managing-your-action-rules"></a>Gestire le regole di azione

È possibile visualizzare e gestire le regole di azione nella visualizzazione elenco come illustrato di seguito.

![Visualizzazione elenco di regole di azione](media/alerts-action-rules/action-rules-list-view.png)

A questo punto, è possibile regole di azione di abilitazione/disabilitazione/eliminazione su larga scala selezionando la casella di controllo accanto a essi. Apre la pagina di configurazione, che consente di aggiornare la definizione e abilitare o disabilitare lo facendo clic su una regola qualsiasi azione.

## <a name="best-practices"></a>Procedure consigliate

Creato con gli avvisi del log di ['numero di risultati'](alerts-unified-log.md) opzione generare **una sola istanza di avviso** utilizzando il risultato della ricerca intero (che può essere ad esempio tra più computer). In questo scenario, se una regola di azione utilizza il filtro 'Contesto avviso (payload)', verrà applicato l'istanza di avviso, purché vi sia una corrispondenza. Nello scenario 2 come descritto in precedenza, se i risultati di ricerca per l'avviso di log generati contengono 'Computer-01' sia 'Computer-02', la notifica intera viene eliminata (ovvero, non vi è alcuna notifica generato per 'Computer-02' affatto).

![Regole di azione e gli avvisi del log (numero di risultati)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Avvisi del log sfrutta migliore con le regole di azione, è consigliabile creare gli avvisi del log con il ['di misura della metrica'](alerts-unified-log.md) opzione. Con questa opzione, istanze separate di avvisi vengono generate in base al campo gruppo definito. Nello scenario 2, quindi istanze separate di avvisi vengono generate per 'Computer-01' e 'Computer-02'. Con la regola di azione descritta nello scenario, solo la notifica per 'Computer-01' potrebbe essere eliminata durante la notifica per 'Computer-02' continuerebbero a vengono attivati come di consueto.

![Regole di azione e gli avvisi del log (numero di risultati)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Domande frequenti

* D: Quando si configura una regola di azione, vorrei visualizzare tutte le possibili sovrapposti azioni alle regole che è possibile evitare le notifiche duplicate. È possibile eseguire questa operazione?

    R. Dopo aver definito un ambito durante la configurazione di una regola di azione, è possibile visualizzare un elenco di regole di azione che si sovrappongono nello stesso ambito (se presente). Questa sovrapposizione può essere una delle opzioni seguenti:
    * Corrispondenza esatta: Ad esempio, si definisce la regola di azione e la regola di azione sovrapposti sono nella stessa sottoscrizione.
    * Un subset: Ad esempio, si definisce la regola di azione è in una sottoscrizione e la regola di azione sovrapposti si trova in un gruppo di risorse all'interno della sottoscrizione.
    * Un superset: Ad esempio, si definisce la regola di azione è in un gruppo di risorse e la sovrapposizione di azione regola è sulla sottoscrizione che contiene il gruppo di risorse.
    * Un'intersezione: Ad esempio, la regola di azione che si sta definendo si trova in 'VM1' e 'VM2' e la sovrapposizione di azione regola si trova in 'VM2' e 'VM3'.

    ![La sovrapposizione delle regole di azione](media/alerts-action-rules/action-rules-overlapping.png)

* D: Durante la configurazione di una regola di avviso, è possibile sapere se esistono già definite regole di azione che può agire su sto definendo la regola di avviso?

    R. Dopo aver definito la risorsa di destinazione per la regola di avviso, è possibile visualizzare l'elenco delle regole di azione che operano nello stesso ambito, se presente, facendo clic su 'Visualizza configurate le azioni' con la sezione 'Actions'. Questo elenco viene popolato in base agli scenari seguenti per l'ambito:
    * Corrispondenza esatta: Ad esempio, si definisce la regola di avviso e la regola di azione sono nella stessa sottoscrizione.
    * Un subset: Ad esempio, si definisce la regola di avviso è su una sottoscrizione e la regola di azione è in un gruppo di risorse all'interno della sottoscrizione.
    * Un superset: Ad esempio, si definisce la regola di avviso è in un gruppo di risorse e la regola di azione è nella sottoscrizione che contiene il gruppo di risorse.
    * Un'intersezione: Ad esempio, la regola di avviso che si sta definendo sia in 'VM1' e 'VM2' e la regola di azione si trova in 'VM2' e 'VM3'.
    
    ![La sovrapposizione delle regole di azione](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

* D: È possibile visualizzare gli avvisi che sono stati eliminati da una regola di azione?

    R. Nel [pagina di elenco di avvisi](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), vi è una colonna aggiuntiva che può essere scelti chiamato 'eliminazione Status'. Se è stata eliminata la notifica relativa a un'istanza di avviso, Mostra lo stato nell'elenco.

    ![Istanze di avviso soppresse](media/alerts-action-rules/action-rules-suppressed-alerts.png)

* D: Se è presente una regola di azione con un gruppo di azione e l'altra con eliminazione attive nello stesso ambito, cosa accade?

    R. **Eliminazione sempre la precedenza nello stesso ambito**.

* D: Cosa accade se dispone di una risorsa controllata in due regole di azione separata? È possibile ottenere una o due notifiche? Ad esempio 'VM2' in questo scenario:

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1'
      action rule 'AR2' defined for 'VM2' and 'VM3' with action group 'AG1'

    R. Per ogni avviso su 'VM1' e 'VM3', gruppo di azione 'AG1' verrà attivato una sola volta. Per ogni avviso in VM '2', gruppo di azione 'AG1' viene generato due volte (**regole di azione non deduplicare azioni**). 

* D: Cosa accade se ho una risorsa controllata in due regole di azione separata e una chiama per azione mentre un altro per l'eliminazione? Ad esempio, 'VM2' in questo scenario:

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1' 
      action rule 'AR2' defined for 'VM2' and 'VM3' with suppression

    R. Per ogni avviso in VM '1', gruppo di azione 'AG1' verrà attivato una sola volta. Azioni e le notifiche per ogni avviso sul 'VM2' e 'VM3' verranno eliminate. 

* D: Cosa accade se si dispone di una regola di avviso e una regola di azione definita per la stessa risorsa, la chiamata a gruppi di azioni diverse? Ad esempio, 'VM1' in questo scenario:

      alert rule  'rule1' on          'VM1' with action group 'AG2'
      action rule 'AR1'   defined for 'VM1' with action group 'AG1' 
 
    R. Per ogni avviso in VM '1', gruppo di azione 'AG1' verrà attivato una sola volta. Ogni regola di avviso 'rule1' attivazione, viene inoltre attivata inoltre 'AG2'. **I gruppi di azioni definite all'interno delle regole di azione e le regole di avviso funzionano in modo indipendente, con alcuna deduplicazione**. 

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sugli avvisi in Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
