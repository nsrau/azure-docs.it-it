---
title: Creazione di avvisi con soglie dinamiche in Monitoraggio di Azure
description: Creare avvisi con apprendimento automatico in base a soglie dinamiche
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: Yaniv.Lavi
ms.reviewer: mbullwin
ms.openlocfilehash: b9b56145c13cb83b1686004ce215b1960caced14
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53182996"
---
# <a name="alerts-with-dynamic-thresholds-in-azure-monitor-limited-private-preview"></a>Avvisi con soglie dinamiche in Monitoraggio di Azure (anteprima privata limitata)

Gli avvisi con soglie dinamiche sono un miglioramento degli avvisi sulle metriche di Azure in Monitoraggio di Azure e sfruttano funzionalità avanzate di Machine Learning per apprendere il comportamento cronologico delle metriche in modo da calcolare automaticamente le linee di base e usarle come soglie degli avvisi.

I vantaggi dell'uso di soglie dinamiche sono i seguenti:

- Riducono la complessità associata all'impostazione di un rigido limite predefinito, in quanto il monitoraggio apprende automaticamente le prestazioni cronologiche della metrica e applica algoritmi di Machine Learning per determinare le soglie degli avvisi.
- Possono identificare comportamenti stagionali e generare avvisi solo in caso di deviazioni dal comportamento previsto. Gli avvisi sulle metriche con soglie dinamiche non vengono attivati se il servizio è normalmente inattivo nei fine settimana e quindi presenta picchi ogni lunedì. Attualmente è supportata la stagionalità oraria, giornaliera e settimanale.
- Apprende continuamente le prestazioni delle metriche e si adatta alle modifiche delle metriche.

Gli avvisi basati su soglie dinamiche sono disponibili per tutte le origini di metriche basate su Monitoraggio di Azure elencate in questo [articolo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#what-resources-can-i-create-near-real-time-metric-alerts-for).

## <a name="sign-up-to-access-the-preview"></a>Iscriversi per accedere all'anteprima

Per provare questa funzionalità, è necessario [iscriversi all'anteprima](https://aka.ms/DynamicThresholdMetricAlerts). Come sempre, tutti i commenti e suggerimenti sono bene accetti ed è possibile inviarli all'indirizzo [azurealertsfeedback@microsoft.com](mailto:azurealertsfeedback@microsoft.com)

## <a name="how-to-configure-alerts-with-dynamic-thresholds"></a>Come configurare avvisi con soglie dinamiche

Gli avvisi con soglie dinamiche possono essere configurati tramite la funzionalità Avvisi in Monitoraggio di Azure

![Anteprima di Avvisi](media/alerts-dynamic-thresholds/0001.png)

## <a name="creating-an-alert-rule-with-dynamic-thresholds"></a>Creazione di una regola di avviso con soglie dinamiche

1. Nel riquadro Avvisi in Monitoraggio, selezionare il pulsante **Nuova regola di avviso** per creare una nuova regola in Azure.

   ![Nuova regola di avviso](media/alerts-dynamic-thresholds/002.png)

2. La sezione Crea regola viene visualizzata con le tre parti costituite da: _Definire la condizione dell'avviso_, _Definire i dettagli dell'avviso_ e _Definire il gruppo di azioni_. Iniziare dalla sezione _Definire la condizione dell'avviso_ e usare il collegamento **Selezionare la destinazione** per specificare la destinazione, selezionando una risorsa. Dopo aver scelto una risorsa appropriata, fare clic sul pulsante Fine.

   ![Selezionare la destinazione](media/alerts-dynamic-thresholds/0003.png)

3. Usare quindi il pulsante **Aggiungi criteri** per visualizzare un elenco delle opzioni di segnale disponibili per la risorsa e nell'elenco dei segnali selezionare l'opzione appropriata in **Metrica**. Ad esempio, Percentuale CPU.

   ![Aggiungi criteri](media/alerts-dynamic-thresholds/004.png)

4. Nella schermata Configura logica dei segnali all'interno della sezione Logica avvisi è possibile modificare la condizione scegliendo il tipo Dinamica, che genera automaticamente le soglie dinamiche (linee rosse) insieme alla metrica (linea blu).

   ![Dinamico](media/alerts-dynamic-thresholds/005.png)

5. Le soglie visualizzate nel grafico vengono calcolate in base agli ultimi sette giorni di dati cronologici. Una volta creato un avviso, le soglie dinamiche acquisiranno altri dati cronologici disponibili e apprenderanno continuamente dai nuovi dati per rendere le soglie più accurate.

6. Altre impostazioni per la logica degli avvisi:
   - Condizione: è possibile specificare che l'avviso venga attivato da una delle tre condizioni seguenti:
       - Valore maggiore della soglia superiore o minore della soglia inferiore (impostazione predefinita)
       - Valore maggiore della soglia superiore
       - Valore minore della soglia inferiore.
   - Aggregazione temporale: Media (impostazione predefinita), Somma, Min, Max.
   - Sensibilità avvisi:
       - Alta: più avvisi, in quanto vengono attivati avvisi alla minima deviazione.
       - Med: sensibilità minore rispetto all'impostazione Alta, meno avvisi con sensibilità elevata (impostazione predefinita).
       - Bassa: soglia meno sensibile.

    ![Impostazioni per la logica degli avvisi](media/alerts-dynamic-thresholds/00007.png)

7. Valutata in base a:
    -  Durata per cui l'avviso deve cercare la condizione specificata, in base a **Periodo**.

    ![Valutata in base a](media/alerts-dynamic-thresholds/007.png)

   > [!NOTE]
   > Valori di durata supportati: 5 minuti, 10 minuti, 30 minuti e 1 ora.

   Per ridurre la frequenza degli avvisi generata da picchi temporanei, è consigliabile usare l'impostazione "Number of violations to trigger the alert" (Numero di violazioni per l'attivazione dell'avviso). Questa funzionalità permette di ricevere un avviso solo se la soglia viene violata X volte consecutive, oppure Y volte sugli ultimi Z periodi. Ad esempio: 

    Per attivare un avviso quando il problema continua per 15 minuti, 3 volte consecutive per una durata specifica di 5 minuti, usare le impostazioni seguenti:

   ![Valutata in base a](media/alerts-dynamic-thresholds/0008.png)

    Per attivare un avviso quando si è verificata una violazione della soglia dinamica in 15 minuti sugli ultimi 30 minuti con una durata di 5 minuti, usare le impostazioni seguenti:

   ![Valutata in base a](media/alerts-dynamic-thresholds/0009.png)

8. Attualmente gli utenti possono ricevere avvisi con criteri di soglia dinamica come singolo criterio.

   ![Creare una regola](media/alerts-dynamic-thresholds/010.png)

## <a name="q--a"></a>Domande e risposte

- D: Se la metrica cambia lentamente nel tempo, questo comportamento attiverà un avviso con soglie dinamiche?

- R: Probabilmente no. Le soglie dinamiche sono utili per rilevare deviazioni significative anziché problemi che si evolvono lentamente.

- D: È possibile configurare soglie dinamiche tramite un'API?

- R: Attualmente no, ma Microsoft sta lavorando a questo scopo.
