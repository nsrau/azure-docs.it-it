---
title: Creazione di avvisi con soglie dinamiche in Monitoraggio di Azure
description: Creare avvisi con apprendimento automatico in base a soglie dinamiche
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 9345138e948d84e0ea3c804dbd7a4b3c21daca2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668146"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Avvisi di metrica con soglie dinamiche in Monitoraggio di AzureMetri Alerts with Dynamic Thresholds in Azure Monitor

La funzionalità Avviso metrica con rilevamento di soglie dinamiche sfrutta la tecnologia avanzata di Machine Learning (ML) per ottenere informazioni sul comportamento delle metriche nel tempo e identificare modelli e anomalie che indicano possibili problemi del servizio. Offre un'interfaccia utente semplice e supporto per operazioni su larga scala, consentendo agli utenti di configurare regole di avviso tramite l'API di Azure Resource Manager, in modo completamente automatico.

Una volta creata, una regola di avviso viene attivata solo quando la metrica monitorata non si comporta come previsto, in base alle relative soglie personalizzate.

Il feedback degli utenti è bene accetto ed è possibile inviarlo all'indirizzo <azurealertsfeedback@microsoft.com>.

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Perché e quando è consigliabile usare condizioni di tipo dinamico?

1. **Avvisi scalabili:** le regole di avviso di soglia dinamiche possono creare soglie personalizzate per centinaia di serie di metriche alla volta, pur fornendo la stessa facilità di definizione di una regola di avviso in una singola metrica. Ti danno meno avvisi da creare e gestire. È possibile usare il portale di Azure o l'API di Azure Resource Manager per crearli. L'approccio scalabile è particolarmente utile quando si gestiscono dimensioni metriche o quando si applica a più risorse, ad esempio a tutte le risorse di sottoscrizione.  [Altre informazioni su come configurare gli avvisi delle metriche con soglie dinamiche usando modelli](alerts-metric-create-templates.md).

1. **Smart Metric Pattern Recognition** – Utilizzando la nostra tecnologia ML, siamo in grado di rilevare automaticamente i modelli metrici e adattarci ai cambiamenti metrici nel tempo, che spesso possono includere la stagionalità (oraria / giornaliera / settimanale). L'adattamento al comportamento delle metriche nel tempo e gli avvisi basati sulle deviazioni dal modello allevia l'onere di conoscere la soglia "giusta" per ogni metrica. L'algoritmo di Machine Learning usato per le soglie dinamiche è stato progettato in modo da impedire valori soglia non significativi (bassa precisione) o troppo ampi (basso richiamo) che non seguono un modello previsto.

1. **Configurazione intuitiva:** le soglie dinamiche consentono di impostare avvisi di metrica utilizzando concetti di alto livello, eliminando la necessità di disporre di una conoscenza approfondita del dominio sulla metrica.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Come configurare le regole di avviso con soglie dinamiche?

Gli avvisi con soglie dinamiche possono essere configurati tramite la funzionalità Avvisi delle metriche in Monitoraggio di Azure. [Altre informazioni su come configurare gli avvisi delle metriche](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Come vengono calcolate le soglie?

Le soglie dinamiche apprendono costantemente i dati della serie di metriche e provano a modellarli usando un set di algoritmi e metodi. Rileva modelli nei dati, ad esempio la ricorrenza (su base oraria, giornaliera o settimanale), ed è in grado di gestire le metriche non significative (come la memoria o la CPU del computer) e quelle con bassa dispersione (come la percentuale di disponibilità ed errore).

Le soglie vengono selezionate in modo che un'eventuale deviazione da queste indichi un'anomalia nel comportamento delle metriche.

> [!NOTE]
> Il rilevamento dei modelli stagionali è impostato su un intervallo di ore, giorni o settimane. Ciò significa che altri modelli come il modello bidirezionale o semisettimanale potrebbero non essere rilevati.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>A cosa serve l'impostazione "Sensibilità" per le soglie dinamiche?

La sensibilità delle soglie di avviso è un concetto generale che controlla il grado di deviazione dal comportamento della metrica necessario per attivare un avviso.
Questa opzione non richiede una conoscenza della metrica a livello di dominio come la soglia statica. Le opzioni disponibili sono:

- Alta: le soglie saranno estremamente vicine al modello della serie di metriche. Una regola di avviso verrà attivata nella deviazione più piccola, generando più avvisi.
- Media: le soglie saranno meno sensibili e più bilanciate, generando così meno avvisi rispetto alla sensibilità alta (impostazione predefinita).
- Bassa: le soglie saranno meno rigorose, con maggiore distanza dal modello della serie di metriche. Una regola di avviso verrà attivata solo su deviazioni di grandi dimensioni, con un conseguente minor numero di avvisi.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Quali sono le opzioni dell'impostazione "Operatore" per le soglie dinamiche?

Usando la stessa regola di avviso con soglie dinamiche è possibile creare soglie personalizzate in base al comportamento delle metriche per il limite minimo e quello massimo.
È possibile specificare che l'avviso venga attivato se si verifica una delle tre condizioni seguenti:

- Valore maggiore della soglia superiore o minore della soglia inferiore (impostazione predefinita)
- Valore maggiore della soglia superiore
- Valore minore della soglia inferiore.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>A cosa servono le impostazioni avanzate per le soglie dinamiche?

**Periodi di errore** - Soglie dinamiche consente inoltre di configurare "Violazioni numeriche per attivare l'avviso", un numero minimo di deviazioni necessarie entro un determinato intervallo di tempo affinché il sistema elevi un avviso (l'intervallo di tempo predefinito è di quattro deviazioni in 20 minuti). L'utente può configurare i periodi di errore e scegliere il criterio in base al quale essere avvisato modificando i periodi di errore e l'intervallo di tempo. Questa opzione consente di ridurre la generazione di avvisi non significativi a causa di picchi temporanei. Ad esempio:

Per attivare un avviso quando il problema continua per 20 minuti, 4 volte consecutive con periodicità di 5 minuti, usare le impostazioni seguenti:

![Impostazioni di periodi di errore relativi a un problema che continua per 20 minuti, 4 volte consecutive con periodicità di 5 minuti](media/alerts-dynamic-thresholds/0008.png)

Per attivare un avviso quando si è verificata una violazione di una soglia dinamica per 20 minuti sugli ultimi 30, con periodicità di 5 minuti, usare le impostazioni seguenti:

![Impostazioni di periodi di errore relativi a un problema che si è verificato per 20 minuti sugli ultimi 30, con periodicità di 5 minuti](media/alerts-dynamic-thresholds/0009.png)

**Ignorare i dati precedenti**: gli utenti possono anche definire una data a partire dalla quale il sistema dovrebbe iniziare il calcolo delle soglie. Un tipico caso d'uso può verificarsi quando una risorsa era in esecuzione in modalità di test e ora è stata promossa per gestire un carico di lavoro di produzione, e pertanto il comportamento di qualsiasi metrica durante la fase di test deve essere ignorato.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Come si spiega perché è stato attivato un avviso di soglie dinamiche?

È possibile esplorare le istanze di avviso attivate nella visualizzazione degli avvisi facendo clic sul collegamento nel messaggio di posta elettronica o di testo oppure nel browser per visualizzare la visualizzazione degli avvisi nel portale di Azure.You can explore triggered alert instances in the alerts view either clicking on the link in the email or text message, or browser to see the alerts view in the Azure portal. [Ulteriori informazioni sulla visualizzazione degli avvisi](alerts-overview.md#alerts-experience).

Viene visualizzata la vista degli avvisi:

- Tutti i dettagli della metrica al momento dell'avviso Soglie dinamiche sono stati generati.
- Grafico del periodo in cui è stato attivato l'avviso che include le soglie dinamiche utilizzate in quel momento.
- Possibilità di fornire feedback sugli avvisi di soglie dinamiche e sull'esperienza di visualizzazione degli avvisi, che potrebbe migliorare i rilevamenti futuri.

## <a name="will-slow-behavior-changes-in-the-metric-trigger-an-alert"></a>Le modifiche di comportamento lente nella metrica attiveranno un avviso?

La risposta è probabilmente negativa. Le soglie dinamiche sono utili per rilevare deviazioni significative anziché problemi che si evolvono lentamente.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Quanti dati vengono usati per visualizzare in anteprima le soglie e quindi calcolarle?

Quando viene creata una regola di avviso per la prima volta, le soglie visualizzate nel grafico vengono calcolate in base a dati cronologici sufficienti per calcolare modelli stagionali orari o giornalieri (10 giorni). Una volta creata una regola di avviso, le soglie dinamiche utilizzano tutti i dati cronologici necessari disponibili e che apprenderanno e si adatteranno continuamente in base ai nuovi dati per rendere le soglie più accurate. Ciò significa che dopo questo calcolo, il grafico visualizzerà anche i modelli settimanali.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Quanti dati sono necessari per attivare un avviso?

Se si dispone di una nuova risorsa o di dati di metrica mancanti, le soglie dinamiche non attivano avvisi prima di tre giorni o 30 campioni di dati delle metriche sono disponibili per garantire soglie accurate.

## <a name="dynamic-thresholds-best-practices"></a>Procedure consigliate per le soglie dinamiche

Le soglie dinamiche possono essere applicate a qualsiasi piattaforma o metrica personalizzata in Monitoraggio di Azure e sono state ottimizzate per le comuni metriche delle applicazioni e delle infrastrutture.
Di seguito sono illustrate le procedure consigliate per configurare gli avvisi per alcune di queste metriche usando le soglie dinamiche.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Soglie dinamiche per le metriche relative alle percentuali di CPU delle macchine virtuali

1. Nel [portale di Azure](https://portal.azure.com)fare clic su **Monitor**. La vista Monitoraggio consolida tutte le impostazioni e i dati di monitoraggio in una vista.

2. Fare clic su **Avvisi** e su **+Nuova regola di avviso**.

    > [!TIP]
    > Anche la maggior parte dei pannelli di risorse include la voce **Avvisi** nel menu delle risorse, in **Monitoraggio**: è possibile creare avvisi anche qui.

3. Fare clic su **Selezionare la destinazione** e, nel riquadro del contesto caricato, selezionare una risorsa di destinazione su cui si vuole impostare una regola di avviso. Usare gli elenchi a discesa **Sottoscrizione** e **Tipo di risorsa "Macchine virtuali"** per trovare la risorsa da monitorare. È anche possibile usare la barra di ricerca per trovare la risorsa.

4. Dopo aver selezionato una risorsa di destinazione, fare clic su **Aggiungi condizione**.

5. Selezionare **"Percentuale CPU"**.

6. Se si vuole, ridefinire la metrica modificando i valori di **Periodo** e **Aggregazione**. Non è consigliabile usare il tipo di aggregazione "Massima" per questo tipo di metrica perché è meno rappresentativo del comportamento. Per il tipo di aggregazione "Massima" può essere più appropriata la soglia statica.

7. Verrà visualizzato un grafico per la metrica per le ultime 6 ore. Definire i parametri dell'avviso:
    1. **Tipo di condizione**: scegliere l'opzione "Dinamica".
    1. **Sensibilità**: scegliere la sensibilità media/bassa per ridurre la frequenza degli avvisi.
    1. **Operatore**: scegliere "Maggiore di" a meno che il comportamento non rappresenti l'utilizzo dell'applicazione.
    1. **Frequenza**: valutare l'opportunità di abbassarla in base all'impatto aziendale dell'avviso.
    1. **Periodi con errori** (opzione avanzata): la finestra di tempo da considerare deve essere di almeno 15 minuti. Se ad esempio il periodo viene impostato su cinque minuti, i periodi con errori devono essere almeno tre.

8. Il grafico delle metriche mostrerà le soglie calcolate in base ai dati recenti.

9. Fare clic su **Fine**.

10. Compilare **Dettagli avviso**, ad esempio **Nome regola di avviso**, **Descrizione** e **Gravità**.

11. Aggiungere un gruppo di azioni per l'avviso selezionando un gruppo di azioni esistente o creandone uno nuovo.

12. Fare clic su **Fine** per salvare la regola di avviso per la metrica.

> [!NOTE]
> Le regole di avviso per le metriche create mediante il portale vengono generate nello stesso gruppo di risorse della risorsa di destinazione.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Soglie dinamiche per il tempo di esecuzione delle richieste HTTP di Application Insights

1. Nel [portale di Azure](https://portal.azure.com)fare clic su **Monitor**. La vista Monitoraggio consolida tutte le impostazioni e i dati di monitoraggio in una vista.

2. Fare clic su **Avvisi** e su **+Nuova regola di avviso**.

    > [!TIP]
    > Anche la maggior parte dei pannelli di risorse include la voce **Avvisi** nel menu delle risorse, in **Monitoraggio**: è possibile creare avvisi anche qui.

3. Fare clic su **Selezionare la destinazione** e, nel riquadro del contesto caricato, selezionare una risorsa di destinazione su cui si vuole impostare una regola di avviso. Usare gli elenchi a discesa **Sottoscrizione** e **Tipo di risorsa "Application Insights"** per trovare la risorsa da monitorare. È anche possibile usare la barra di ricerca per trovare la risorsa.

4. Dopo aver selezionato una risorsa di destinazione, fare clic su **Aggiungi condizione**.

5. Selezionare il **"tempo di esecuzione delle richieste HTTP"**.

6. Se si vuole, ridefinire la metrica modificando i valori di **Periodo** e **Aggregazione**. Non è consigliabile usare il tipo di aggregazione "Massima" per questo tipo di metrica perché è meno rappresentativo del comportamento. Per il tipo di aggregazione "Massima" può essere più appropriata la soglia statica.

7. Verrà visualizzato un grafico per la metrica per le ultime 6 ore. Definire i parametri dell'avviso:
    1. **Tipo di condizione**: scegliere l'opzione "Dinamica".
    1. **Operatore**: scegliere "Maggiore di" per ridurre gli avvisi generati in caso di miglioramento della durata.
    1. **Frequenza**: valutare l'opportunità di abbassarla in base all'impatto aziendale dell'avviso.

8. Il grafico delle metriche mostrerà le soglie calcolate in base ai dati recenti.

9. Fare clic su **Fine**.

10. Compilare **Dettagli avviso**, ad esempio **Nome regola di avviso**, **Descrizione** e **Gravità**.

11. Aggiungere un gruppo di azioni per l'avviso selezionando un gruppo di azioni esistente o creandone uno nuovo.

12. Fare clic su **Fine** per salvare la regola di avviso per la metrica.

> [!NOTE]
> Le regole di avviso per le metriche create mediante il portale vengono generate nello stesso gruppo di risorse della risorsa di destinazione.

## <a name="interpreting-dynamic-threshold-charts"></a>Interpretazione dei grafici a soglia dinamica

Di seguito è riportato un grafico che mostra una metrica, i limiti di soglia dinamici e alcuni avvisi generati quando il valore non rientra nelle soglie consentite.

![Ulteriori informazioni su come configurare gli avvisi di metricaLearn more about how to configure Metric Alerts](media/alerts-dynamic-thresholds/threshold-picture-8bit.png)

Utilizzare le informazioni seguenti per interpretare il grafico precedente.

- **Linea blu:** la metrica misurata effettiva nel tempo.
- **Area ombreggiata blu:** mostra l'intervallo consentito per la metrica. Finché i valori delle metriche rimangono all'interno di questo intervallo, non si verificherà alcun avviso.
- **Punti blu:** se si fa clic con il pulsante sinistro del mouse su una parte del grafico e quindi si passa il mouse sulla linea blu, sotto il cursore viene visualizzato un punto blu che mostra un singolo valore metrico aggregato.
- **Popup con punto blu:** mostra il valore metrico misurato (il punto blu) e i valori superiore e inferiore dell'intervallo consentito.  
- **Punto rosso con un cerchio nero:** mostra il primo valore metrico non compreso nell'intervallo consentito. Questo è il valore che genera un avviso di metrica e lo mette in uno stato attivo.
- **Punti rossi**- Indica valori misurati aggiuntivi al di fuori dell'intervallo consentito. Non verranno attivati ulteriori avvisi di metrica, ma l'avviso rimane attivo.
- **Area rossa:** mostra l'ora in cui il valore della metrica non rientra nell'intervallo consentito. L'avviso rimane nello stato attivo finché i valori misurati successivi non rientrano nell'intervallo consentito, ma non vengono generati nuovi avvisi.
- **Fine dell'area rossa** - Quando la linea blu è tornata all'interno dei valori consentiti, l'area rossa si arresta e la linea del valore misurata diventa blu. Lo stato dell'avviso metrico generato al momento del punto rosso con contorno nero è impostato su risolto. 
