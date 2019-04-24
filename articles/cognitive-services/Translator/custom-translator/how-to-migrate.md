---
title: Eseguire la migrazione dell'area di lavoro e dei progetti di Microsoft Translator Hub - Custom Translator
titleSuffix: Azure Cognitive Services
description: Eseguire la migrazione dell'area di lavoro e dei progetti di Hub in Custom Translator.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 73fc0d26612d32f2614899c62f680ff9e85d1609
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60512779"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Eseguire la migrazione dell'area di lavoro e dei progetti di Hub in Custom Translator

È possibile eseguire facilmente la migrazione dell'area di lavoro e dei progetti di [Microsoft Translator Hub](https://hub.microsofttranslator.com/) nel traduttore personalizzato. La migrazione viene avviata da Microsoft Hub selezionando un'area di lavoro o un progetto, quindi selezionando un'area di lavoro nel traduttore personalizzato e infine selezionando i training da trasferire. Dopo l'avvio della migrazione, le impostazioni di training selezionate vengono trasferite con tutti i documenti rilevanti. I modelli distribuiti vengono sottoposti a training e possono essere distribuiti automaticamente dopo il completamento.

Durante la migrazione vengono eseguite queste operazioni:
* Per tutti i documenti e le definizioni dei progetti, i nomi verranno trasferiti con l'aggiunta del prefisso "hub_". I dati di ottimizzazione e i test generati automaticamente saranno denominati hub_systemtune_\<modelid> o hub_systemtest_\<modelid>.
* Tutti i training nello stato distribuito al momento della migrazione verranno automaticamente sottoposti a training usando i documenti di training dell'hub. I costi per questo training non verranno addebitati nella sottoscrizione. Se per la migrazione è stata selezionata la distribuzione automatica, il modello sottoposto a training verrà distribuito dopo il completamento. Verranno applicati i normali costi di hosting.
* Eventuali training di cui è stata eseguita la migrazione non nello stato distribuito verranno impostati nello stato di bozza sottoposta a migrazione. In questo stato, è possibile eseguire il training di un modello con la definizione migrata, ma verranno addebitati i costi di training standard.
* In qualsiasi momento, il punteggio BLEU migrato dal training dell'hub è disponibile nella pagina TrainingDetails del modello sotto l'intestazione "Bleu score in MT Hub".

> [!Note]
> Per la formazione abbia esito positivo, Microsoft Translator personalizzato richiede almeno 10.000 frasi estratte univoche. Convertitore personalizzato non può condurre un training con meno del [minimo consigliato](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences).

## <a name="find-custom-translator-workspace-id"></a>Trovare l'ID dell'area di lavoro del traduttore personalizzato

Per eseguire la migrazione dell'area di lavoro di [Microsoft Translator Hub](https://hub.microsofttranslator.com/) è necessario l'ID dell'area di lavoro di destinazione nel traduttore personalizzato. L'area di lavoro di destinazione di Custom Translator è la posizione in cui verrà eseguita la migrazione di tutte le aree di lavoro e tutti i progetti di Hub.

L'ID dell'area di lavoro di destinazione è disponibile nella pagina delle impostazioni del traduttore personalizzato:

1. Passare alla pagina delle impostazioni nel portale Custom Translator.

2. L'ID dell'area di lavoro è disponibile nella sezione relativa alle informazioni di base.

    ![Come trovare l'ID dell'area di lavoro di destinazione](media/how-to/how-to-find-destination-ws-id.png)

3. Conservare l'ID dell'area di lavoro di destinazione per farvi riferimento durante il processo di migrazione.

## <a name="migrate-a-project"></a>Eseguire la migrazione di un progetto

Se si vuole eseguire la migrazione dei progetti in modo selettivo, Microsoft Translator Hub offre questa possibilità.

Per eseguire la migrazione di un progetto:

1. Accedere a Microsoft Translator Hub.

2. Passare alla pagina "Projects" (Progetti).

3. Fare clic sul collegamento "Migrate" (Esegui la migrazione) per il progetto appropriato.

    ![Come eseguire la migrazione da Hub](media/how-to/how-to-migrate-from-hub.png)

4. Dopo aver fatto clic sul collegamento per la migrazione, verrà visualizzato un modulo che consente di:
   * Specificare l'area di lavoro da trasferire nel traduttore personalizzato
   * Indicare se si vuole trasferire tutti i training con esito positivo o solo quelli distribuiti. Per impostazione predefinita, vengono trasferiti tutti i training con esito positivo.
   * Indicare se il training deve essere distribuito automaticamente al completamento. Per impostazione predefinita, il training non viene distribuito automaticamente dopo il completamento.

5. Fare clic su "Submit Request" (Invia richiesta).

## <a name="migrate-a-workspace"></a>Eseguire la migrazione di un'area di lavoro

Oltre alla migrazione di un singolo progetto, è anche possibile eseguire la migrazione tutti i progetti con training con esito positivo in un'area di lavoro. In questo modo ogni progetto nell'area di lavoro verrà valutato in modo analogo a quando si fa clic sul collegamento per la migrazione. Questa funzionalità è adatta per gli utenti con molti progetti che vogliono eseguire la migrazione di tutti i progetti nel traduttore personalizzato con le stesse impostazioni. La migrazione dell'area di lavoro può essere avviata dalla pagina delle impostazioni di Translator Hub.

Per eseguire la migrazione di un'area di lavoro:

1. Accedere a Microsoft Translator Hub.

2. Passare alla pagina "Settings" (Impostazioni).

3. Nella pagina "Settings" (Impostazioni) fare clic su "Migrate Workspace data to Custom Translator" (Esegui la migrazione dei dati dell'area di lavoro in Custom Translator).

    ![Come eseguire la migrazione da Hub](media/how-to/how-to-migrate-workspace-from-hub.png)

4. Nella pagina successiva selezionare una di queste due opzioni:

    a. Deployed Trainings only (Solo training distribuiti): se si seleziona questa opzione, verrà eseguita solo la migrazione dei sistemi distribuiti e dei documenti correlati.

    b. All Successful Trainings (Tutti i training completati): se si seleziona questa opzione, verrà eseguita la migrazione di tutti i training completati e dei documenti correlati.

    c. Immettere l'ID dell'area di lavoro di destinazione in Custom Translator.

    ![Come eseguire la migrazione da Hub](media/how-to/how-to-migrate-from-hub-screen.png)

5. Fare clic su Submit Request (Invia richiesta).

## <a name="migration-history"></a>Cronologia della migrazione

Se è stata richiesta la migrazione dell'area di lavoro e dei progetti da Hub, nella pagina delle impostazioni di Custom Translator sarà disponibile la cronologia della migrazione.

Per visualizzare la cronologia della migrazione, seguire questa procedura:

1. Passare alla pagina delle impostazioni nel portale Custom Translator.

2. Nella sezione Migration History (Cronologia migrazione) della pagina Settings (Impostazioni) fare clic su Migration History (Cronologia migrazione).

    ![Cronologia della migrazione](media/how-to/how-to-migration-history.png)

Nella pagina Migration History (Cronologia migrazione) vengono visualizzate le informazioni di riepilogo seguenti relative a ogni migrazione richiesta.

1. Migrated By (Migrazione eseguita da): nome e indirizzo di posta elettronica dell'utente che ha inviato la richiesta di migrazione

2. Migrated On (Migrazione eseguita il): data e ora della migrazione

3. Progetti: numero di progetti di cui è stata richiesta la migrazione rispetto al numero di progetti di cui è stata eseguita correttamente la migrazione.

4. Trainings (Training): numero di training di cui è stata richiesta la migrazione rispetto al numero di training di cui è stata eseguita correttamente la migrazione.

5. Documents (Documenti): numero di documenti di cui è stata richiesta la migrazione rispetto al numero di documenti di cui è stata eseguita correttamente la migrazione.

    ![Dettagli della cronologia della migrazione](media/how-to/how-to-migration-history-details.png)

Se si vuole un report di migrazione più dettagliato su progetti, training e documenti, è possibile esportare i dettagli come file con estensione csv.

## <a name="implementation-notes"></a>Note sull'implementazione
* I sistemi con linguaggio coppie non ancora disponibili in Microsoft Translator personalizzato sarà disponibile solo per accedere ai dati o annullare la distribuzione tramite Microsoft Translator personalizzato. Questi progetti verranno contrassegnati come "Non disponibile" nella pagina dei progetti. Come verranno abilitate nuove coppie di linguaggi con funzione di conversione personalizzata, diventerà attivi per il training e distribuire i progetti. 
* La migrazione di un progetto dall'hub al traduttore personalizzato non ha alcun impatto sui training o sui progetti dell'hub. I progetti o i documenti non vengono eliminati dall'hub durante una migrazione e la distribuzione dei modelli non viene annullata.
* È possibile eseguire la migrazione una sola volta per ogni progetto. Se è necessario ripetere la migrazione di un progetto, è necessario contattare Microsoft.
* Funzione di conversione personalizzata supporta coppie di linguaggi NMT da e in lingua inglese. [Visualizzare l'elenco completo dei linguaggi supportati](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization). L'hub non richiede modelli di base e quindi supporta diverse migliaia di lingue. È possibile eseguire la migrazione di una coppia di lingue non supportata, tuttavia verrà eseguita la migrazione solo dei documenti e delle definizioni dei progetti. Non sarà possibile eseguire il training del nuovo modello. Inoltre, questi documenti e progetti verranno visualizzati come non attivi per indicare che al momento non possono essere usati. Se viene aggiunto il supporto per questi progetti e/o documenti, essi diventano attivi e possono essere sottoposti a training.
* Il traduttore personalizzato attualmente non supporta i dati di training in una sola lingua. Analogamente alle coppie di lingue non supportate, è possibile eseguire la migrazione di documenti in una sola lingua, che tuttavia vengono indicati come non attivi fino a quando non verrà introdotto il supporto per i dati in una sola lingua.
* Il traduttore personalizzato richiede 10.000 frasi parallele per eseguire il training. Microsoft Hub permette di eseguire il training su un set di dati più piccolo. Se viene eseguita la migrazione di un training che non soddisfa questo requisito, il training non verrà eseguito.

## <a name="custom-translator-versus-hub"></a>Custom Translator e Hub

Questa tabella mette a confronto le funzionalità di Microsoft Translator Hub con quelle di Custom Translator.

|   | Hub | Custom Translator |
|:-----|:----:|:----:|
|Stato delle funzionalità di personalizzazione   | Disponibilità generale  | Disponibilità generale |
| Versione API per testo  | V2    | V3  |
| Personalizzazione SMT | Sì   | No  |
| Personalizzazione NMT | No     | Sì |
| Personalizzazione dei nuovi servizi voce unificati | No     | Sì |
| Nessuna traccia | Sì | Sì |

## <a name="new-languages"></a>Nuove lingue

Se si è un community o un'organizzazione lavorare alla creazione di un nuovo sistema di linguaggio per Microsoft Translator, contattare [ custommt@microsoft.com ](mailto:custommt@microsoft.com) per altre informazioni.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il training di un modello](how-to-train-model.md).
- Iniziare a usare il modello di traduzione personalizzata distribuito tramite l'[API Traduzione testuale Microsoft V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
