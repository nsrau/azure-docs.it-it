---
title: Creare, visualizzare e gestire gli avvisi tramite Monitoraggio di Azure - Avvisi (anteprima) | Microsoft Docs
description: La nuova esperienza unificata gli avvisi di Azure consente di creare, visualizzare e gestire la metrica e le regole di avviso del log da un'unica posizione.
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 36729da3-e002-4a64-86b2-2513ca2cbb58
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: vinagara
ms.openlocfilehash: b537bb42d43c4232c100061322e09bf492f2a20f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="create-view-and-manage-alerts-using-azure-monitor---alerts-preview"></a>Creare, visualizzare e gestire gli avvisi tramite Monitoraggio di Azure - Avvisi (anteprima)

## <a name="overview"></a>Panoramica
Questo articolo illustra come configurare gli avvisi tramite la nuova interfaccia di Avvisi (anteprima) nel portale di Azure. La definizione di una regola di avviso è costituita da tre parti:
- Destinazione: risorsa specifica di Azure da monitorare
- Criteri: condizione specifica o logica che, se rilevata nel segnale, deve attivare l'azione
- Azione: chiamata specifica inviata a un ricevitore di una notifica di posta elettronica, SMS, webhook e così via.

In Avvisi (anteprima) il termine **avvisi del log** viene usato per descrivere gli avvisi in cui il segnale è basato su una query personalizzata in [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) o [Azure Application Insights](../application-insights/app-insights-analytics.md). La funzionalità di avviso delle metriche denominata [Avvisi delle metriche near real-time](monitoring-near-real-time-metric-alerts.md) nell'esperienza degli avvisi esistente è denominata **Avvisi metrica** in Avvisi (anteprima). In *Avvisi metrica*, alcuni tipi di risorse forniscono [metriche multidimensionali](monitoring-metric-charts.md) per risorse di Azure specifiche e pertanto gli avvisi per tali risorse possono essere resi più specifici tramite filtri aggiuntivi sulle dimensioni. Questi avvisi sono detti **avvisi delle metriche multidimensionali**.
Avvisi di Azure (anteprima) fornisce anche una visualizzazione unificata per tutte le regole di avviso e la possibilità di gestirle in un'unica posizione. È inclusa la visualizzazione di tutti gli avvisi non risolti. Altre informazioni sulle funzionalità sono disponibili in [Avvisi di Azure (anteprima) - Panoramica](monitoring-overview-unified-alerts.md).

> [!NOTE]
> Avvisi di Azure (anteprima) offre un'esperienza nuova e migliorata per la creazione di avvisi in Azure. L'esperienza di [Avvisi di Azure](monitoring-overview-alerts.md) esistente rimane utilizzabile
>

Di seguito è riportata una guida dettagliata per l'uso di Avvisi di Azure (anteprima).

## <a name="create-an-alert-rule-with-the-azure-portal"></a>Creare una regola di avviso con il portale di Azure
1. Nel [portale](https://portal.azure.com/)selezionare **Monitoraggio** e nella sezione MONITORAGGIO scegliere **Avvisi (anteprima)**.  
    ![Monitoraggio](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. Selezionare il pulsante **Nuova regola di avviso** per creare un nuovo avviso in Azure.
    ![Aggiungi avviso](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. Viene visualizzata la sezione Crea avviso composta da tre parti: *Definire la condizione dell'avviso*, *Definire i dettagli dell'avviso* e *Definire il gruppo di azioni*.

    ![Creare una regola](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  Definire la condizione dell'avviso tramite il collegamento **Seleziona risorsa** e specificare la destinazione selezionando una risorsa. Filtrare in modo appropriato scegliendo la *Sottoscrizione*, il *Tipo di risorsa* e infine la *Risorsa* necessaria.

    >[!NOTE]

    > Prima di procedere, verificare i segnali disponibili per la risorsa selezionata.

    ![Selezionare una risorsa](./media/monitor-alerts-unified/Alert-SelectResource.png)

 Poiché Avvisi di Azure (anteprima) consente di creare vari tipi di avviso da un'interfaccia unica, in base al tipo di avviso desiderato scegliere il passaggio successivo:

    - Per gli **avvisi metrica** seguire i passaggi da 5 a 7, quindi andare direttamente al passaggio 11
    - Per gli **avvisi del log**, andare al passaggio 8.

    > [!NOTE]

    > Avvisi unificati (anteprima) supporta anche gli avvisi del log attività. [Altre informazioni](monitoring-activity-log-alerts-new-experience.md).

5. *Avvisi metrica*: verificare che in **Tipo di risorsa** sia selezionato piattaforma o servizio di monitoraggio (diverso da *Log Analytics*), quindi, una volta selezionata la **risorsa** appropriata, fare clic sul pulsante *Fatto* per tornare alla finestra Crea avviso. Usare quindi il pulsante **Aggiungi criteri** per selezionare il segnale specifico nell'elenco delle opzioni di segnale, il servizio di monitoraggio e il tipo elencato disponibili per la risorsa selezionata in precedenza.

    ![Selezionare una risorsa](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]

    > Le nuove funzionalità di metrica introdotte solo per gli avvisi rapidi sono incluse nei tipi di segnale come metriche del servizio di piattaforma

6. *Avvisi metrica*: dopo avere selezionato il segnale, è possibile dichiarare la logica per gli avvisi. I dati cronologici del segnale vengono visualizzati per riferimento con un'opzione per modificare l'intervallo di tempo tramite **Visualizza cronologia**, dalle ultime sei ore fino alla settimana precedente. Con la visualizzazione impostata, è possibile selezionare **Logica avvisi** dalle opzioni visualizzate: Condizione, Aggregazione e infine Soglia. Come anteprima della logica impostata, nella visualizzazione vengono mostrate la condizione e la cronologia del segnale, per indicare quando verrebbe attivato l'avviso. Specificare infine per quanto tempo l'avviso deve cercare la condizione specificata scegliendo l'opzione desiderata in **Periodo** e la frequenza con cui l'avviso deve essere eseguito selezionando **Frequenza**.

    ![Configurare la logica del segnale per la metrica](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *Avvisi metrica*: se il segnale è una metrica, la finestra degli avvisi può essere filtrata tramite più punti dati o dimensioni per la risorsa di Azure specifica. Analogamente agli avvisi di metrica, è possibile scegliere la visualizzazione della cronologia del segnale impostando la durata nell'elenco a discesa **Visualizza cronologia**. È inoltre possibile selezionare le dimensioni per la metrica scelta per filtrare per le serie temporali necessarie. La scelta delle dimensioni è facoltativa e possono essere usate fino a cinque dimensioni. È possibile selezionare **Logica avvisi** dalle opzioni visualizzate: Condizione, Aggregazione e infine Soglia. Come anteprima della logica impostata, nella visualizzazione vengono mostrate la condizione e la cronologia del segnale, per indicare quando l'avviso sarebbe stato attivato in passato. Specificare infine per quanto tempo l'avviso deve cercare la condizione specificata scegliendo l'opzione desiderata in **Periodo** e la frequenza con cui l'avviso deve essere eseguito selezionando **Frequenza**.

    ![Configurare la logica del segnale per la metrica multidimensionale](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *Avvisi del log*: verificare che **Tipo di risorsa** sia un'origine dati di analisi come *Log Analytics* o *Application Insights* e quindi, dopo avere selezionato la **risorsa** appropriata, fare clic su *Operazione completata*. Usare quindi il pulsante **Aggiungi criteri** per visualizzare l'elenco delle opzioni di segnale disponibili per la risorsa e nell'elenco dei segnali selezionare l'opzione di **ricerca log personalizzata** per il servizio di monitoraggio del log scelto, ad esempio *Log Analytics* o *Application Insights*.

   ![Selezionare una risorsa - ricerca log personalizzata](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Avvisi (anteprima) può importare query di Analytics come tipo di segnale **Log (query salvata)**, come illustrato nella figura precedente. Gli utenti possono quindi perfezionare la query in Analytics e salvarla per uso futuri in Avvisi. Altri dettagli sull'uso del salvataggio di query sono disponibili in [Usare la ricerca log in Log Analytics](../log-analytics/log-analytics-log-searches.md) o in [Query condivisa in Analytics in Application Insights](../log-analytics/log-analytics-overview.md). 

9.  *Avvisi del log*: una volta selezionata, la query per gli avvisi può essere immessa nel campo **Query di ricerca**. Se la sintassi della query non è corretta, nel campo viene visualizzato un errore in ROSSO. Se la sintassi della query è corretta, i dati cronologici della query specificata vengono visualizzati sotto forma di grafico come riferimento con un'opzione per modificare l'intervallo di tempo dalle ultime sei ore alla settimana precedente.

 ![Configurare una regola di avviso](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > La visualizzazione dei dati cronologici è possibile solo se i risultati della query includono dettagli temporali. Se la query restituisce dati di riepilogo o specifici valori di colonna, questi dati vengono visualizzati come tracciato singolo.

    >  Per il tipo Unità di misura della metrica degli avvisi del log con Application insights, è possibile specificare la variabile specifica per raggruppare i dati con l'opzione **Aggrega in base a**, come illustrato di seguito:

    ![opzione aggrega in base a](./media/monitor-alerts-unified/aggregate-on.png)

10.  *Avvisi del log*: con la visualizzazione impostata, è possibile selezionare in **Logica avvisi** una delle opzioni visualizzate: Condizione, Aggregazione e infine Soglia. Specificare l'ora in cui valutare la condizione specificata nella logica tramite l'opzione **Periodo**. Specificare la frequenza con cui l'avviso deve essere eseguito selezionando **Frequenza**.
Gli **avvisi del log** possono basarsi su:
   - *Numero di record*: viene creato un avviso se il numero di record restituiti dalla query è maggiore o minore del valore specificato.
   - *Unità di misura della metrica*: viene creato un avviso se ogni *valore di aggregazione* nei risultati supera il valore di soglia specificato ed è *raggruppato* in base al valore scelto. Il numero di violazioni per un avviso è il numero di volte in cui viene superata la soglia nel periodo di tempo scelto. È possibile specificare le violazioni totali per qualsiasi combinazione di violazioni nei set di risultati o le violazioni consecutive necessarie che devono verificarsi in campioni consecutivi. Per altre informazioni, vedere [Avvisi del log e relativi tipi](monitor-alerts-unified-log.md).

    > [!TIP]
    > Al momento in Avvisi (anteprima) gli avvisi di ricerca log possono avere un *periodo* e un valore di *frequenza* in minuti personalizzati. I valori possono variare da 5 a 1440 minuti (24 ore). Pertanto, se si vuole che il periodo dell'avviso sia ad esempio di tre ore, convertirlo in minuti (180 minuti) prima dell'uso

11. Come secondo passaggio, definire un nome per l'avviso nel campo **Nome regola di avviso** insieme a una **Descrizione** con le specifiche per l'avviso e il valore di **Gravità** nelle opzioni disponibili. Questi dettagli vengono riutilizzati in tutti i messaggi di avviso, le notifiche o notifiche push eseguite da Monitoraggio di Azure. Inoltre, l'utente può scegliere di attivare immediatamente la regola di avviso durante la creazione attivando in modo appropriato l'opzione **Abilita regola alla creazione**.

    Solo per gli **avvisi del log** sono disponibili alcune funzionalità aggiuntive nei dettagli degli avvisi:

    - **Elimina avvisi**: quando si attiva l'eliminazione per la regola di avviso, le azioni della regola vengono disabilitate per un periodo di tempo definito dopo la creazione di un nuovo avviso. La regola è ancora in esecuzione e, se i criteri vengono soddisfatti, crea i record di avviso. L'utente ha il tempo necessario per risolvere il problema senza eseguire azioni duplicate.

        ![Elimina avvisi per gli avvisi del log](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

12. Come terzo e ultimo passaggio, specificare se un **gruppo di azioni** deve essere attivato per la regola di avviso quando viene soddisfatta la condizione dell'avviso. È possibile scegliere qualsiasi gruppo di azioni esistente con un avviso o creare un nuovo gruppo di azioni. In base al gruppo di azioni selezionato, quando viene attivato l'avviso, Azure eseguirà queste operazioni: invio di messaggi di posta elettronica, invio di SMS, chiamata di webhook, correzione dell'uso di runbook di Azure, invio di notifiche push allo strumento Gestione dei servizi IT e così via. Altre informazioni sui [gruppi di azioni](monitoring-action-groups.md).

    Per gli **avvisi del log** sono disponibili alcune funzionalità aggiuntive per eseguire l'override di quelle predefinite:

    - **Notifica tramite posta elettronica**: esegue l'override dell'oggetto nel messaggio di posta elettronica inviato tramite il gruppo di azioni. È possibile modificare il corpo del messaggio.
    - **Includi payload JSON personalizzato**: esegue l'override del webhook JSON usato dai gruppi di azioni e sostituisce il payload predefinito con un payload personalizzato. Per altre informazioni sui formati dei webhook, vedere l'[azione del webhook per gli avvisi del log](monitor-alerts-unified-log-webhook.md)

        ![Override dell'azione per gli avvisi del log](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

13. Se tutti i campi sono validi e hanno un segno di spunta verde, il pulsante **Crea regola di avviso** può essere selezionato e viene creato un avviso in Monitoraggio di Azure - Avvisi (anteprima). Tutti gli avvisi possono essere visualizzati nella dashboard di Avvisi (anteprima).

    ![Creazione di regole](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

## <a name="view-your-alerts-in-azure-portal"></a>Visualizzare gli avvisi nel portale di Azure

1. Nel [portale](https://portal.azure.com/)selezionare **Monitoraggio** e nella sezione MONITORAGGIO scegliere **Avvisi (anteprima)**.  

2. Viene visualizzata la **dashboard di Avvisi (anteprima)**, in cui sono raccolti tutti gli avvisi di Azure visualizzati in un unico riquadro, ![Dashboard avvisi](./media/monitoring-overview-unified/alerts-preview-overview.png)
3. Nella parte superiore, da sinistra a destra, la dashboard mostra i seguenti elementi su cui è possibile fare clic per visualizzare un elenco dettagliato:
    - *Avvisi attivati*: numero di avvisi che attualmente hanno soddisfatto la logica e sono stati attivati
    - *Totale regole di avviso*: numero di regole di avviso create nel testo secondario, numero di regole attualmente abilitate
4. Viene visualizzato un elenco di tutti gli avvisi attivati su cui l'utente può fare clic per visualizzare i dettagli
5. Per cercare avvisi specifici, è possibile usare le opzioni dell'elenco a discesa nella parte superiore della pagina per filtrare *sottoscrizioni, gruppi di risorse e/o risorse* specifiche. Inoltre, per eventuali avvisi non risolti, usare l'opzione *Filtra avviso* per cercare la parola chiave fornita in avvisi specifici corrispondenti per *nome, criteri di avviso, gruppo di risorse e risorsa di destinazione*

## <a name="managing-your-alerts-in-azure-portal"></a>Gestione degli avvisi nel portale di Azure
1. Nel [portale](https://portal.azure.com/)selezionare **Monitoraggio** e nella sezione MONITORAGGIO scegliere **Avvisi (anteprima)**.  
2. Selezionare il pulsante **Gestisci regole** nella barra superiore per passare alla sezione di gestione delle regole in cui sono elencate tutte le regole di avviso create, inclusi gli avvisi che sono stati disabilitati.
3. Per individuare le regole di avviso specifiche, è possibile usare i filtri con elenco a discesa nella parte superiore, che consentono di filtrare le regole di avviso per *sottoscrizione, gruppi di risorse e/o risorsa* specifici. In alternativa all'uso del riquadro di ricerca sopra l'elenco di regole di avviso contrassegnato come *Filtra avvisi*, è possibile fornire una parola chiave, che viene confrontata con *nome avviso, condizione e risorsa di destinazione* per visualizzare solo le regole corrispondenti.
   ![Regole di gestione degli avvisi](./media/monitoring-overview-unified/alerts-preview-rules.png)
4. Per visualizzare o modificare una regola di avviso specifica, fare clic sul nome visualizzato come un collegamento selezionabile.
5. L'avviso definito viene visualizzato con la struttura di tre parti: 1) condizione dell'avviso 2) dettagli dell'avviso e 3) gruppo di azioni. È possibile selezionare **Criteri di destinazione** per modificare la logica degli avvisi o aggiungere nuovi criteri dopo aver usato l'icona del Cestino per eliminare la logica precedente. Analogamente, nella sezione dei dettagli degli avvisi è possibile modificare **Descrizione** e **Gravità**. È possibile modificare anche il gruppo di azioni o crearne uno nuovo per collegarlo all'avviso tramite il pulsante **Nuovo gruppo di azioni**.

   ![Modificare le regole di avviso](./media/monitor-alerts-unified/AlertsPreviewEdit.png)

6. Tramite il pannello superiore, le modifiche apportate all'avviso possono essere sottoposte a questi comandi: **Salva** per eseguire il commit di tutte le modifiche apportate all'avviso, **Elimina** per tornare alla schermata precedente senza eseguire il commit delle modifiche apportate all'avviso, **Disabilita** per disattivare l'avviso, dopo il quale non viene più eseguita o attivata alcuna azione. Infine, **Elimina** per rimuovere definitivamente l'intera regola di avviso da Azure.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sugli [avvisi delle metriche quasi in tempo reale (anteprima)](monitoring-near-real-time-metric-alerts.md).
- Leggere una [panoramica della raccolta di metriche](insights-how-to-customize-monitoring.md) per verificare che il servizio sia disponibile e reattivo.
- Informazioni sugli [avvisi di log in Avvisi di Azure (anteprima)](monitor-alerts-unified-log.md)
- [Informazioni sugli avvisi di log attività nell'esperienza di Avvisi (anteprima)](monitoring-activity-log-alerts-new-experience.md)
