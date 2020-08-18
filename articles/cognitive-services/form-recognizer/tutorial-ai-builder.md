---
title: "Esercitazione: Creare un'app di elaborazione moduli con AI Builder - Riconoscimento modulo"
titleSuffix: Azure Cognitive Services
description: In questa esercitazione si userà AI Builder per creare un'applicazione di elaborazione moduli ed eseguirne il training.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 07/01/2020
ms.author: pafarley
ms.openlocfilehash: 981c6f6bb2b0eb597b32ce8e428ef0aa7d19929b
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003359"
---
# <a name="tutorial-create-a-form-processing-app-with-ai-builder"></a>Esercitazione: Creare un'app di elaborazione moduli con AI Builder

[AI Builder](https://docs.microsoft.com/ai-builder/overview) è una funzionalità di Power Platform che consente di automatizzare i processi e prevedere i risultati per migliorare le prestazioni aziendali. È possibile usare l'elaborazione dei moduli in AI Builder per creare modelli di intelligenza artificiale che identificano ed estraggono coppie chiave-valore e dati di tabella da documenti modulo.

> [!NOTE]
> Questo progetto è disponibile anche come [modulo Microsoft Learn](https://docs.microsoft.com/learn/modules/get-started-with-form-processing/).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un modello di intelligenza artificiale per l'elaborazione di moduli
> * Eseguire il training del modello
> * Pubblicare il modello da usare in Azure Power Apps o Power Automate

## <a name="prerequisites"></a>Prerequisiti

* Set di almeno cinque moduli dello stesso tipo da usare per i dati di training/test. Per suggerimenti e informazioni sulle opzioni per la creazione di un set di dati di training, vedere [Creare un set di dati di training](./build-training-data-set.md). Per questo argomento di avvio rapido, è possibile usare i file inclusi nella cartella **Train** del [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2128080).
* Licenza di Power Apps o Power Automate. Vedere la [guida alle licenze](https://go.microsoft.com/fwlink/?linkid=2085130). La licenza deve includere [Common Data Service](https://powerplatform.microsoft.com/en-us/common-data-service/).
* [Componente aggiuntivo o versione di valutazione](https://go.microsoft.com/fwlink/?LinkId=2113956&clcid=0x409) di AI Builder.


## <a name="create-a-form-processing-project"></a>Creare un progetto di elaborazione moduli

1. Passare a [Power Apps](https://make.powerapps.com/) o [Power Automate](https://flow.microsoft.com/signin) e accedere con l'account aziendale.
1. Nel riquadro sinistro selezionare **AI Builder** > **Compilazione**.
1. Selezionare la scheda **Elaborazione moduli**.
1. Digitare un nome per il modello.
1. Selezionare **Crea**.

## <a name="upload-and-analyze-documents"></a>Caricare e analizzare i documenti

Nella pagina **Aggiungi documenti** è necessario specificare i documenti di esempio per eseguire il training del modello per il tipo di modulo da cui si vogliono estrarre le informazioni. Dopo il caricamento dei documenti, AI Builder li analizza per verificare che siano sufficienti per il training di un modello.

> [!NOTE]
> Attualmente, AI Builder non supporta i tipi di dati di input di elaborazione moduli seguenti:
>
> - Tabelle complesse (tabelle nidificate, intestazioni unite o celle e così via)
> - Caselle di controllo o pulsanti di opzione
> - Documenti PDF con più di 50 pagine
> - PDF compilabili
>
> Per altre informazioni sui requisiti per i documenti di input, vedere [Requisiti di input](./overview.md#input-requirements).

### <a name="upload-your-documents"></a>Caricare i documenti

1. Selezionare **Aggiungi documenti** e quindi almeno cinque documenti e infine selezionare **Carica**.
1. Al termine del caricamento, selezionare **Chiudi**.
1. Selezionare quindi **Analizza**.

> [!NOTE] 
> Dopo aver caricato questi documenti, è comunque possibile rimuoverne alcuni o caricarne altri.

> [!div class="mx-imgBorder"]
> ![Pagina per l'aggiunta di documenti](./media/tutorial-ai-builder/add-documents-page.png)

### <a name="analyze-your-documents"></a>Analizzare i documenti

Durante il passaggio dell'analisi, AI Builder esamina i documenti caricati e rileva i campi e le tabelle. Il tempo necessario per completare questa operazione dipende dal numero di documenti specificati, ma nella maggior parte dei casi saranno sufficienti alcuni minuti.

Al termine dell'analisi, selezionare l'anteprima per aprire l'esperienza di selezione dei campi.

> [!IMPORTANT]
> Se l'analisi ha esito negativo, AI Builder probabilmente non ha potuto rilevare testo strutturato nei documenti. Verificare che i documenti aggiornati soddisfino i [requisiti di input](./overview.md#input-requirements).

## <a name="select-your-form-fields"></a>Selezionare i campi dei moduli

Nella pagina di selezione dei campi scegliere i campi a cui si è interessati:

1. Per selezionare un campo, fare clic su un rettangolo che indica un campo rilevato nel documento oppure selezionare più campi tramite trascinamento della selezione. È anche possibile selezionare i campi nel riquadro di destra.
1. Fare clic sul nome del campo selezionato se si vuole rinominarlo per allinearlo alle specifiche esigenze o per normalizzare le etichette estratte.

    Facendo clic su un campo rilevato, vengono visualizzate le informazioni seguenti.

    - **Nome del campo**: nome dell'etichetta del campo rilevato.
    - **Valore del campo**: valore del campo rilevato.

> [!div class="mx-imgBorder"]
> ![Pagina per l'aggiunta di documenti](./media/tutorial-ai-builder/select-fields-page.png)

### <a name="label-undetected-fields"></a>Etichettare i campi non rilevati

Se il campo che si vuole etichettare non è stato rilevato automaticamente dal modello, è possibile tracciare un rettangolo intorno al relativo valore e digitare un nome di etichetta nella finestra di dialogo visualizzata.

## <a name="train-your-model"></a>Eseguire il training del modello

1. Selezionare **Avanti** per verificare i campi del modulo selezionati. Se tutto risulta corretto, selezionare **Esegui il training** per eseguire il training del modello.

    > [!div class="mx-imgBorder"]
    > ![Pagina per l'aggiunta di documenti](./media/tutorial-ai-builder/summary-train-page.png)
1. Al termine del training, selezionare **Vai alla pagina Dettagli** nella schermata **Il training è stato completato**.
## <a name="quick-test-your-model"></a>Eseguire il test rapido del modello

La pagina Dettagli consente di testare il modello prima di pubblicarlo o usarlo:

1. Nella pagina Dettagli selezionare **Test rapido**.
2. È possibile trascinare e rilasciare un documento oppure selezionare **Carica dal dispositivo personale** per caricare il file di test. Per la visualizzazione dei risultati, il test rapido richiederà solo alcuni secondi.
3. È possibile selezionare **Ricomincia** per eseguire un altro test oppure **Chiudi** se si è terminato.

### <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

Se si ottengono risultati non validi o punteggi di attendibilità bassi per determinati campi, provare i suggerimenti seguenti:

- Ripetere il training usando moduli con valori diversi in ogni campo.
- Ripetere il training usando un set più ampio di documenti di training. Maggiore è il numero dei documenti contrassegnati, maggiore è la quantità di informazioni a disposizione di AI Builder per un migliore riconoscimento dei campi.
- È possibile ottimizzare i file PDF selezionando solo determinate pagine per il training. Per selezionare pagine specifiche all'interno del documento, usare l'opzione **Stampa** > **Stampa su PDF**.

## <a name="publish-your-model"></a>Pubblicare il modello

Se si è soddisfatti del modello, selezionare **Pubblica** per pubblicarlo. Al termine della pubblicazione, il modello viene alzato al livello **Pubblicazione completata** ed è pronto per essere usato.

> [!div class="mx-imgBorder"]
> ![Pagina per l'aggiunta di documenti](./media/tutorial-ai-builder/model-page.png)

Dopo la pubblicazione, il modello di elaborazione moduli può essere usato in un'[app canvas di Power Apps](https://docs.microsoft.com/ai-builder/form-processor-component-in-powerapps) o in [Power Automate](https://docs.microsoft.com/ai-builder/form-processing-model-in-flow).

## <a name="next-steps"></a>Passaggi successivi

Vedere la documentazione di AI Builder per l'uso di un modello di elaborazione moduli.

* [Utilizzare il componente di elaborazione moduli in Power Apps](https://docs.microsoft.com/ai-builder/form-processor-component-in-powerapps)
* [Utilizzare un modello di elaborazione moduli in Power Automate](https://docs.microsoft.com/ai-builder/form-processing-model-in-flow)