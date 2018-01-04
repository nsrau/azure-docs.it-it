---
title: Creare, visualizzare e gestire gli avvisi tramite Monitor di Azure - avvisi (anteprima) | Documenti Microsoft
description: Utilizzare la nuova esperienza unificata gli avvisi di Azure per creare, visualizzare e gestire metrica e le regole di avviso di log da un'unica posizione.
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: vinagara
ms.openlocfilehash: e3902ec5fc27c829fa97042d15552c8c895c6408
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="create-view-and-manage-alerts-using-azure-monitor---alerts-preview"></a>Creare, visualizzare e gestire gli avvisi tramite il monitoraggio di Azure - avvisi (anteprima)

## <a name="overview"></a>Panoramica
In questo articolo viene illustrato come configurare avvisi utilizzando la nuova interfaccia avvisi (anteprima) all'interno di portale di Azure. Definizione di una regola di avviso è suddiviso in tre parti:
- Destinazione: Specifiche risorse di Azure, ovvero da monitorare
- Criteri: Condizione specifica o logica che quando nel segnale, deve essere attivata l'azione
- Azione: Chiamata specifica inviati a un ricevitore di una notifica - posta elettronica, SMS, webhook e così via.

Avvisi (anteprima) viene utilizzato il termine **gli avvisi del registro** per descrivere gli avvisi in cui query personalizzata basata su segnale [Azure Log Analitica](../log-analytics/log-analytics-tutorial-viewdata.md). La funzionalità di avviso metrica chiamato [quasi in tempo reale metrica avvisi](monitoring-near-real-time-metric-alerts.md) degli avvisi esistenti è denominata esperienza **metrica avvisi** degli avvisi (anteprima). In *metrica avvisi*, alcuni tipi di risorse forniscono [metrica multidimensionale](monitoring-metric-charts.md) per le risorse di Azure specifica e pertanto gli avvisi per tali risorse più specifica utilizzando filtri aggiuntivi su dimensioni; Questi avvisi sono detti **avvisi metrica multidimensionale**. Gli avvisi di Azure (anteprima) fornisce anche una visualizzazione unificata per tutte le regole di avviso e possibilità di gestirli come un'unica posizione; tra cui la visualizzazione di tutti gli avvisi non risolti. Ulteriori informazioni sulla funzionalità da [Alerts(Preview) Azure - Panoramica](monitoring-overview-unified-alerts.md).

> [!NOTE]
> Mentre gli avvisi di Azure (anteprima) offre un'esperienza di nuova e migliorata per la creazione di avvisi in Azure. Esistente [avvisi Azure](monitoring-overview-alerts.md) esperienza rimangono utilizzabili
>

Dettaglio di seguito è la Guida dettagliata per l'utilizzo degli avvisi di Azure (anteprima).

## <a name="create-an-alert-rule-with-the-azure-portal"></a>Creare una regola di avviso con il portale di Azure
1. Nel [portale](https://portal.azure.com/)selezionare **monitoraggio** e nella sezione monitoraggio - scegliere **avvisi (anteprima)**.  
    ![Monitoraggio](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. Selezionare il **nuova regola di avviso** pulsante per creare un nuovo avviso in Azure.
    ![Aggiungere un avviso](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. La sezione creare avviso viene visualizzata con composta da tre parti: *definire una condizione di avviso*, *definire i dettagli degli avvisi*, e *il gruppo di azioni di definire*.
    ![Creare una regola](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  Definire la condizione di avviso utilizzando innanzitutto il **Seleziona risorsa** collegamento e specificare la destinazione, selezionando una risorsa. Filtro in modo appropriato scegliendo necessari *sottoscrizione*, *tipo di risorsa*, e infine selezionare necessari *risorse*. Avvisi di Azure (anteprima) consentito per la creazione di vari tipi di avvisi provenienti da un'interfaccia singolare. in base al tipo di avviso desiderato scegliere il passaggio successivo come:
    - Per **metrica avvisi**: seguire i passaggi da 5 a 7; quindi andare direttamente al passaggio 11
    - Per **gli avvisi del registro**, andare al passaggio 8 poi

5. *Metrici avvisi*: verificare **tipo di risorsa** è servizio di piattaforma o il monitoraggio selezionato (diverso da *Log Analitica*), quindi una volta appropriato **risorse** è Fare clic su scelto *eseguita* per tornare alla creazione avvisi. Utilizzare quindi la **aggiungere criteri** pulsante per selezionare il segnale specifico dall'elenco delle opzioni di segnale, servizio di monitoraggio e tipo elencato - disponibili per la risorsa selezionata in precedenza.
    ![Selezionare una risorsa](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]
    > Nuove funzionalità di metrica, introdotte per avvisi rapido sono inclusi nei tipi di segnale come le metriche dal servizio di piattaforma
  

6. *Metrici avvisi*: una volta segnale è selezionato, è possibile dichiarare la logica per gli avvisi. Per riferimento, i dati cronologici del segnale vengono visualizzati con l'opzione per modificare la finestra di tempo utilizzando **Visualizza cronologia**, diversi dall'ultimo sei ore per la settimana scorsa. Con la visualizzazione sul posto, **logica avviso** possono essere selezionati da visualizzare opzioni di condizione, aggregazione e infine soglia. Come anteprima della logica di fornito, la condizione è illustrata nella visualizzazione e la cronologia di segnale, per indicare quando sarebbe attivata l'avviso. Infine specificare per il periodo di tempo, dovrebbe essere avviso per la condizione specificata scegliendo il **periodo** opzione con la frequenza con cui avviso deve essere eseguito selezionando **frequenza**.
    ![Configurare la logica di segnale per metrica](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *Metrici avvisi*: segnale se è una metrica, quindi finestra di avviso può essere filtrato utilizzando più punti dati o le dimensioni per la risorsa di Azure suddetta. Analogamente agli avvisi di metrica, visualizzazione della cronologia del segnale può essere selezionata che segnala la durata da **Visualizza cronologia** elenco a discesa. Inoltre, è possono selezionare dimensioni per la metrica scelta per filtrare le serie di tempo necessario. Scegliere le dimensioni è facoltativo e possono essere utilizzate fino a cinque dimensioni. **Avviso logica** possono essere selezionati da visualizzare opzioni di condizione, aggregazione e infine soglia. Come anteprima della logica di fornito, la condizione è illustrata nella visualizzazione e la cronologia di segnale, per indicare quando l'avviso attivato in precedenza. Infine specificare per il periodo di tempo, dovrebbe essere avviso per la condizione specificata scegliendo il **periodo** opzione con la frequenza con cui avviso deve essere eseguito selezionando **frequenza**.
    ![Configurare la logica di segnale per metrica multidimensionale](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *Registra avvisi*: verificare **tipo di risorsa** è un'origine analitica come *Log Analitica*, quindi una volta appropriato **risorse** è selezionata, fare clic su *Eseguita* pulsante. Utilizzare, quindi il **aggiungere criteri** pulsante per visualizzare l'elenco delle opzioni di segnale disponibili per la risorsa e l'elenco di segnale **ricerca di log personalizzata** opzione.
   ![Selezionare una risorsa - ricerca di log personalizzato](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

9.  *Registra avvisi*: una volta selezionato, query per gli avvisi possono essere espresse sotto **Query di ricerca** campo; se la sintassi di query è corretta, il campo, l'errore viene visualizzato in rosso. Se la sintassi di query è corretta - per riferimento i dati cronologici della query specificata viene visualizzati sotto forma di grafico con l'opzione per modificare l'intervallo di tempo dall'ultimo sei ore per la settimana scorsa. 
   ![Configurare una regola di avviso](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

    > [!NOTE]
    > Visualizzazione dei dati cronologici può essere visualizzata solo se i risultati della query sono i dettagli di esecuzione. Se la query restituisce i dati di riepilogo o di specifici valori della colonna - stesso viene visualizzato come un tracciato singolare 

10.  *Registra avvisi*: con la visualizzazione sul posto, **logica avviso** possono essere selezionati da visualizzare opzioni di condizione, aggregazione e infine soglia. Infine specificare nella logica, il tempo per valutare la condizione specificata, utilizzando **periodo** opzione. Con la frequenza con cui avviso deve essere eseguito selezionando **frequenza**.
Per **gli avvisi del registro** gli avvisi possono essere basati su:
   - *Numero di record*: viene creato un avviso se il numero di record restituiti dalla query è maggiore o minore del valore specificato.
   - *Misura metrico*: viene creato un avviso se ogni *valore di aggregazione* nei risultati supera il valore di soglia specificato ed è *raggruppato* valore scelto. Il numero di violazioni della sicurezza per un avviso è il numero di volte in cui che viene superata la soglia del periodo di tempo scelto. È possibile specificare totale violazioni della sicurezza per qualsiasi combinazione di violazioni della sicurezza tra il set di risultati o consecutivi violazioni della sicurezza per richiedere che la violazione devono verificarsi in campioni consecutivi.

   Altre informazioni, vedere [gli avvisi del registro e i relativi tipi](monitor-alerts-unified-log.md)

    > [!TIP]
    > Attualmente gli avvisi (anteprima) - gli avvisi di ricerca di log a disposizione personalizzati *periodo* e *frequenza* valore in minuti. I valori possono variare da 5 minuti e 1440 minuti (vale a dire) 24 ore. Pertanto, se si desidera avviso periodo come ad esempio tre ore, convertirlo in minuti - 180 minuti, prima dell'uso

11. Come secondo passaggio, definire un nome per l'avviso nel **nome regola di avviso** campo insieme a un **descrizione** che riporta in dettaglio le specifiche per l'avviso e **gravità** valore di opzioni disponibili. Questi dettagli vengono riutilizzati in tutti i messaggi di avviso, le notifiche o push eseguito da monitoraggio di Azure. Inoltre, l'utente può scegliere di attivare immediatamente la regola di avviso durante la creazione attivando in modo appropriato **Abilita regola al momento della creazione** opzione.

Per **gli avvisi del registro** è disponibile nei dettagli degli avvisi o solo, alcune funzionalità aggiuntive:
- *Esclusione di avvisi*: quando si attiva la soppressione per la regola di avviso, le azioni per la regola sono disabilitate per un periodo di tempo dopo la creazione di un nuovo avviso definito. La regola è ancora in esecuzione e crea i record degli avvisi purché i criteri vengono soddisfatti. Per consentire tempo per risolvere il problema senza eseguire azioni duplicate.
    ![Esclusione di avvisi per gli avvisi del Registro](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

12. Come passaggio nella terzo e ultimo, specificare eventuali **gruppo di azioni** deve essere attivato per la regola di avviso quando viene soddisfatta condizione di avviso. È possibile scegliere qualsiasi gruppo di azioni esistenti con avviso o creare un nuovo gruppo di azione. In base all'azione il gruppo selezionato, quando l'avviso è trigger Azure verrà: inviare email(s), inviare SMS(s), chiamare Webhook(s), correggere l'utilizzo dei runbook di Azure, push al strumento ITSM, e così via. Altre informazioni, vedere [gruppi di azioni](monitoring-action-groups.md).

Per **gli avvisi del registro** alcune funzionalità aggiuntive sono disponibili per eseguire l'override di quelle predefinite:
- *Notifica tramite posta elettronica*: esegue l'override dell'oggetto nel messaggio di posta elettronica inviato tramite il gruppo di azione. È possibile modificare il corpo del messaggio.
- *Includi payload Json personalizzato*: esegue l'override del webhook Json usato dai gruppi di azioni e invece di sostituire il payload predefinito con un payload personalizzato ![esegue l'override di azione per gli avvisi del Registro](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

13. Se tutti i campi sono validi e con contrassegno verde il **Crea regola di avviso** pulsante può essere selezionato e viene creato l'avviso in Monitoraggio di Azure - avvisi (anteprima). Tutti gli avvisi possono essere visualizzati nel dashboard avvisi (anteprima).
    ![Creazione della regola](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

## <a name="view-your-alerts-in-azure-portal"></a>Visualizzare gli avvisi nel portale di Azure
1. Nel [portale](https://portal.azure.com/)selezionare **monitoraggio** e nella sezione monitoraggio - scegliere **avvisi (anteprima)**.  

2. Il **avvisi Dashboard (anteprima)** è visualizzato, in cui tutti gli avvisi di Azure sono unificati e visualizzati in una lavagna singolare ![Dashboard avvisi](./media/monitoring-overview-unified/alerts-preview-overview.png)
1. Da sinistra nella parte superiore a destra, il Dashboard Mostra a colpo d'occhio, i seguenti elementi: è possibile fare clic per visualizzare un elenco dettagliato:
    - *Gli avvisi generati*: il numero di avvisi attualmente soddisfatti logica e in stato di generato
    - *Totale delle regole di avviso*: il numero di regole di avviso create e in testo secondario, il numero che sono attualmente abilitate
2. Viene visualizzato un elenco di tutti gli avvisi attivati che l'utente può fare clic per visualizzare i dettagli
3. Consentire gli avvisi; specifiche di ricerca una possibile utilizzare le opzioni di elenco a discesa nella parte superiore per il filtro specifico *gruppo di risorse di sottoscrizione, e/o risorsa*. Altri per qualsiasi non risolti avviso, uno utilizzare di *avviso di filtro* l'opzione per cercare fornita (parola chiave) - avvisi specifici di corrispondenza con *nome, avviso, risorsa gruppo di criteri e risorsa di destinazione*

## <a name="managing-your-alerts-in-azure-portal"></a>Gestire gli avvisi nel portale di Azure
1. Nel [portale](https://portal.azure.com/)selezionare **monitoraggio** e nella sezione monitoraggio - scegliere **avvisi (anteprima)**.  
2. Selezionare il **gestire le regole** pulsante nella barra superiore, per passare alla sezione regola - gestione in cui sono elencate tutte le regole di avviso create, inclusi gli avvisi che sono stati disabilitati.
3. Per individuare le regole di avviso specifiche di una possibile utilizzare i filtri di elenco a discesa nella parte superiore, che consentono la specifica per le regole di avviso ristretta *sottoscrizione, i gruppi di risorse e/o risorsa*. In alternativa sull'utilizzo della ricerca riquadro sopra l'elenco di regola di avviso contrassegnata *filtraggio degli avvisi*, uno può fornire una parola chiave, che viene confrontato con *avviso nome, condizione e risorsa di destinazione*; da mostrare solo regole di corrispondenza. 
   ![Avviso di gestire le regole](./media/monitoring-overview-unified/alerts-preview-rules.png)
4. Per visualizzare o modificare una regola di avviso specifica, fare clic sul relativo nome viene visualizzato come un collegamento ipertestuale.
5. Avviso definito viene mostrata - nella struttura di tre fasi: gruppo di 3) l'azione Dettagli avviso 2) condizione di avviso 1). **Criteri di destinazione** può essere selezionato per modificare la logica di avviso o un nuovo oggetto criteri possono essere aggiunti dopo aver utilizzato l'icona di bin per eliminare la logica di versioni precedenti. Analogamente, nella sezione dei dettagli degli avvisi - **descrizione** e **gravità** può essere modificato. E può essere modificato il gruppo di azioni o una nuova istanza può essere impostata per il collegamento all'avviso utilizzando il **nuovo gruppo di azioni** pulsante ![Modifica regola di avviso](./media/monitor-alerts-unified/AlertsPreviewEdit.png)
6. Tramite il pannello superiore, le modifiche apportate all'avviso possono essere inclusi riflettente: *salvare* per eseguire il commit di tutte le modifiche apportate all'avviso, *ignorare* per tornare alla schermata precedente senza eseguire il commit di tutte le modifiche apportate all'avviso, *disabilitare*  per disattivare l'avviso - dopo il quale non viene eseguito oppure attiva qualsiasi azione. E, infine, *eliminare* rimuovere definitivamente l'intera regola di avviso da Azure.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sugli [avvisi delle metriche quasi in tempo reale (anteprima)](monitoring-near-real-time-metric-alerts.md).
* Leggere una [panoramica della raccolta di metriche](insights-how-to-customize-monitoring.md) per verificare che il servizio sia disponibile e reattivo.
* Informazioni su [gli avvisi del registro negli avvisi di Azure (anteprima)](monitor-alerts-unified-log.md)
