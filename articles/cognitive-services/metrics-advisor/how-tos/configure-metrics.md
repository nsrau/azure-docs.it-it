---
title: Configurare l'istanza di Advisor di metrica usando il portale Web
titleSuffix: Azure Cognitive Services
description: Come configurare l'istanza di Advisor di metrica e ottimizzare i risultati del rilevamento delle anomalie.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 93fdf2884ca6593cfdb4fb2878ba0dd21246266d
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91446359"
---
# <a name="how-to-configure-metrics-and-fine-tune-detecting-configuration"></a>Procedura: configurare le metriche e ottimizzare la configurazione del rilevamento

Usare questo articolo per iniziare a configurare l'istanza di Advisor di metrica usando il portale Web. Per esplorare le metriche per un feed di dati specifico, passare alla pagina **feed di dati** e selezionare uno dei feed. Verrà visualizzato un elenco di metriche associate.

:::image type="content" source="../media/metrics/select-metric.png" alt-text="Selezionare una metrica" lightbox="../media/metrics/select-metric.png":::

Fare clic su uno dei nomi delle metriche per visualizzarne i dettagli. In questa visualizzazione dettagliata è possibile passare a un'altra metrica nello stesso feed di dati usando l'elenco a discesa nell'angolo in alto a destra della schermata.

Quando si visualizzano i dettagli di una metrica per la prima volta, è possibile caricare una serie temporale consentendo a metrica Advisor di sceglierne una o specificando i valori da includere per ogni dimensione. 

È anche possibile selezionare intervalli di tempo e modificare il layout della pagina.

> [!NOTE]
> - L'ora di inizio è inclusa.
> - L'ora di fine è esclusiva. 

È possibile fare clic sulla scheda **eventi imprevisti** per visualizzare le anomalie e trovare un collegamento all' [Hub eventi imprevisti](diagnose-incident.md).

## <a name="tune-the-detecting-configuration"></a>Ottimizzare la configurazione di rilevamento

Una metrica può applicare una o più configurazioni di rilevamento. Esiste una configurazione predefinita per ogni metrica, che è possibile modificare o aggiungere a, in base alle esigenze di monitoraggio.

### <a name="tune-the-configuration-for-all-series-in-current-metric"></a>Ottimizza la configurazione per tutte le serie nella metrica corrente

Questa configurazione verrà applicata a tutte le serie in questa metrica, ad eccezione di quelle con una configurazione separata. Una configurazione a livello di metrica viene applicata per impostazione predefinita quando i dati vengono caricati e viene visualizzata nel riquadro sinistro. Gli utenti possono modificare direttamente la configurazione del livello di metrica nella pagina metrica. 

Sono presenti parametri aggiuntivi, ad esempio **Direction**, e un' **anomalia valida** che può essere usata per ottimizzare ulteriormente la configurazione. È possibile combinare anche metodi di rilevamento diversi. 

:::image type="content" source="../media/configuration-combination.png" alt-text="Selezionare una metrica" lightbox="../media/configuration-combination.png":::

### <a name="tune-the-configuration-for-a-specific-series-or-group"></a>Ottimizzare la configurazione per una serie o un gruppo specifico

Fare clic su **configurazione avanzata** sotto le opzioni di configurazione a livello di metrica per visualizzare la configurazione a livello di gruppo. È possibile aggiungere una configurazione per una singola serie o un gruppo di serie facendo clic sull' **+** icona in questa finestra. I parametri sono simili ai parametri di configurazione a livello di metrica, ma potrebbe essere necessario specificare almeno un valore di dimensione per una configurazione a livello di gruppo per identificare un gruppo di serie. E specificare tutti i valori della dimensione per la configurazione a livello di serie per identificare una serie specifica. 

Questa configurazione verrà applicata al gruppo di serie o a serie specifiche anziché alla configurazione a livello di metrica. Dopo aver impostato le condizioni per questo gruppo, salvarlo.

:::image type="content" source="../media/advanced-configuration.png" alt-text="Selezionare una metrica" lightbox="../media/advanced-configuration.png":::

### <a name="anomaly-detection-methods"></a>Metodi di Rilevamento delle anomalie

Metrica Advisor offre più metodi di rilevamento delle anomalie. È possibile utilizzarne una o combinarle utilizzando operatori logici facendo clic sul **+** pulsante. 

**Rilevamento intelligente** 

Il rilevamento intelligente è basato sull'apprendimento automatico che apprende i modelli dai dati cronologici e li usa per il rilevamento futuro. Quando si usa questo metodo, la **sensibilità** è il parametro più importante per l'ottimizzazione dei risultati del rilevamento. È possibile trascinarlo in un valore più piccolo o più grande per influire sulla visualizzazione sul lato destro della pagina. Sceglierne uno che corrisponda ai dati e salvarlo. 


In modalità rilevamento intelligente, i parametri della versione di riservatezza e limite vengono usati per ottimizzare il risultato del rilevamento delle anomalie.

La sensibilità può influenzare la larghezza dell'intervallo di valori previsto di ogni punto. Quando è aumentato, l'intervallo di valori previsto sarà più rigido e verranno segnalate altre anomalie:

:::image type="content" source="../media/metrics/smart-detection-high-sensitivity.png" alt-text="Selezionare una metrica":::

Quando la sensibilità è disattivata, l'intervallo di valori previsto sarà più ampio e verrà segnalato un minor numero di anomalie:

:::image type="content" source="../media/metrics/smart-detection-low-sensitivity.png" alt-text="Selezionare una metrica":::

**Modifica soglia** 

La soglia di modifica viene in genere usata quando i dati delle metriche rimangono in genere intorno a un determinato intervallo. La soglia viene impostata in base alla **percentuale di modifica**. La modalità di **modifica della soglia** è in grado di rilevare le anomalie negli scenari:

* I dati sono in genere stabili e uniformi. Si desidera ricevere una notifica in caso di fluttuazioni.
* I dati sono in genere piuttosto instabili e fluttuano molto. Si desidera ricevere una notifica quando diventa troppo stabile o semplice.

Usare la procedura seguente per usare questa modalità:

1. Selezionare **modifica soglia** come metodo di rilevamento anomalie quando si impostano le configurazioni di rilevamento anomalie per le metriche o le serie temporali.
    
    :::image type="content" source="../media/metrics/change-threshold.png" alt-text="Selezionare una metrica":::

2. Selezionare non **compreso nell'intervallo** o **nel parametro di intervallo in** base allo scenario.

    Se si desidera rilevare le fluttuazioni, selezionare **fuori dall'intervallo**. Con le impostazioni seguenti, ad esempio, tutti i punti dati che cambiano oltre il 10% rispetto a quello precedente verranno rilevati come outlier.
    :::image type="content" source="../media/metrics/out-of-the-range.png" alt-text="Selezionare una metrica":::

    Se si desidera rilevare le linee piane nei dati, selezionare **nell'intervallo**. Con le impostazioni seguenti, ad esempio, qualsiasi punto dati modificato entro il 0,01% rispetto a quello precedente verrà rilevato come outlier. Poiché la soglia è così piccola (0,01%), rileva le linee piane nei dati come outlier.

    :::image type="content" source="../media/metrics/in-the-range.png" alt-text="Selezionare una metrica":::

3. Impostare la percentuale di modifica che verrà conteggiata come anomalia e i punti dati acquisiti in precedenza verranno utilizzati per il confronto. Questo confronto è sempre compreso tra il punto dati corrente e un singolo punto dati N punti precedenti.
    
    **Direction** è valido solo se si usa la modalità **out-of-range** :
    
    * **Up configura il** rilevamento per rilevare solo le anomalie quando (punto dati corrente)-(punto dati di confronto) > **+** percentuale di soglia.
    * **Down** configura il rilevamento in modo da rilevare solo le anomalie quando (punto dati corrente)-(punto dati di confronto) < **-** percentuale di soglia.
 
**Soglia rigida**

 La soglia rigida è un metodo di base per il rilevamento delle anomalie. È possibile impostare un limite superiore e/o inferiore per determinare l'intervallo di valori previsto. Eventuali punti che rientrino dal limite verranno identificati come anomalie. 


## <a name="preset-events"></a>Eventi preimpostati

In alcuni casi, gli eventi e le occorrenze previsti (ad esempio festività) possono generare dati anomali. Utilizzando gli eventi predefiniti, è possibile aggiungere flag all'output del rilevamento delle anomalie, durante i periodi specificati. Questa funzionalità deve essere configurata dopo l'onboarding del feed di dati. Ogni metrica può avere solo una configurazione di evento preimpostata.

> [!Note]
> La configurazione dell'evento preimpostato prenderà in considerazione le vacanze durante il rilevamento delle anomalie e potrebbe modificare i risultati. Verrà applicata ai punti dati inseriti dopo aver salvato la configurazione. 

Fare clic sul pulsante **Configura evento preimpostato** accanto all'elenco a discesa metriche in ogni pagina Dettagli metrica.
 
:::image type="content" source="../media/metrics/preset-event-button.png" alt-text="Selezionare una metrica":::

Nella finestra che viene visualizzata configurare le opzioni in base all'utilizzo. Assicurarsi che **Abilita evento festività** sia selezionato per usare la configurazione. 

La sezione **evento festività** consente di evitare le anomalie non necessarie rilevate durante le festività. Sono disponibili due opzioni per l'opzione **strategia** che è possibile applicare:

* **Sospendi festività**: Disattiva tutte le anomalie e gli avvisi nei risultati del rilevamento anomalie durante il periodo festivo.
* **Holiday As week-end**: calcola i valori medi previsti di diversi week-end corrispondenti prima della festività e basa lo stato dell'anomalia su questi valori.

Sono disponibili diversi altri valori che è possibile configurare:

|Opzione  |Descrizione  |
|---------|---------|
|**Scegliere una dimensione come paese**     | Scegliere una dimensione contenente le informazioni relative al paese. Ad esempio un codice paese.         |
|**Mapping del codice paese**     | Il mapping tra un [codice paese](https://wikipedia.org/wiki/ISO_3166-1_alpha-2)standard e i dati del paese della dimensione scelta.        |
|**Opzioni festività**    | Indica se prendere in considerazione tutte le festività, solo le festività di PTO (tempo pagato) o solo le festività non PTO.         |
|**Giorni per l'espansione**    |  Giorni interessati prima e dopo un'festività.        |


La sezione **ciclo Event** può essere usata in alcuni scenari per ridurre gli avvisi non necessari usando modelli ciclici nei dati. Ad esempio: 

- Metriche con più modelli o cicli, ad esempio un modello settimanale e mensile. 
- Metriche che non dispongono di un modello chiaro, ma i dati sono confrontabili anno per anno (annua), mese per mese (MoM), settimana dopo settimana (WoW) o giorno per giorno (DoD).
 
Non tutte le opzioni sono selezionabili per ogni granularità. Di seguito sono elencate le opzioni disponibili per granularità:

| Granularità | YoY | Mamma | WoW | DoD |
|:-|:-|:-|:-|:-|
| Annuale | X | X | X | X |
| Mensilmente | X | X | X | X |
| Settimanale | ✔ | X | X | X |
| Ogni giorno | ✔ | ✔ | ✔ | X |
| Ogni ora | ✔ | ✔ | ✔ | ✔ |
| Controllare | X | X | X | X |
| In secondo luogo | X | X | X | X |
| Personalizzato | ✔ | ✔ | ✔ | ✔ |

X: non disponibile.  
✔ Disponibile.
  
\* Quando si usa una granularità personalizzata in secondi, è disponibile solo se la metrica è più lunga di un'ora e meno di un giorno.

L'evento Cycle viene usato per ridurre le anomalie se seguono un modello ciclico, ma segnala un'anomalia se più punti dati non seguono il modello. La **modalità Strict** viene utilizzata per abilitare la segnalazione anomalie Se anche un punto dati non segue il modello. 

:::image type="content" source="../media/metrics/preset-events.png" alt-text="Selezionare una metrica":::

## <a name="view-recent-incidents"></a>Visualizza gli eventi imprevisti recenti

Metrica Advisor rileva le anomalie in tutti i dati delle serie temporali Man mano che vengono inseriti. Tuttavia, non tutte le anomalie devono essere Escalate, perché potrebbero non avere un notevole effetto. L'aggregazione verrà eseguita sulle anomalie per raggruppare quelle correlate in eventi imprevisti. È possibile visualizzare questi eventi imprevisti dalla scheda **evento imprevisto** nella pagina Dettagli metrica. 

Fare clic su un evento imprevisto per passare alla pagina di **analisi degli eventi imprevisti** in cui è possibile visualizzare altri dettagli. Fare clic su **Gestisci eventi imprevisti in nuovo hub eventi**, per trovare la pagina dell' [Hub eventi](diagnose-incident.md) imprevisti in cui è possibile trovare tutti gli eventi imprevisti nella metrica specifica. 

## <a name="subscribe-anomalies-for-notification"></a>Sottoscrivere anomalie per la notifica

Se si vuole ricevere una notifica ogni volta che viene rilevata un'anomalia, è possibile sottoscrivere gli avvisi per la metrica usando un hook. Per ulteriori informazioni, vedere [configurare gli avvisi e ricevere notifiche tramite un hook](alerts.md) .


## <a name="next-steps"></a>Passaggi successivi 
- [Configurare avvisi e ottenere notifiche usando un hook](alerts.md)
- [Regolare il rilevamento anomalie usando il feedback](anomaly-feedback.md)
- [Diagnosticare un evento imprevisto](diagnose-incident.md).

