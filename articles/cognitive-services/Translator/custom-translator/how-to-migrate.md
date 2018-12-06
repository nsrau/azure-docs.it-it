---
title: Eseguire la migrazione dell'area di lavoro e dei progetti di Microsoft Translator Hub - Custom Translator
titleSuffix: Azure Cognitive Services
description: Eseguire la migrazione dell'area di lavoro e dei progetti di Hub in Custom Translator.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 611cd0878e88d2e1c0a988f73b57e391c5a8551d
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975908"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Eseguire la migrazione dell'area di lavoro e dei progetti di Hub in Custom Translator

È possibile eseguire la migrazione dell'area di lavoro e dei progetti di [Microsoft Translator Hub](https://hub.microsofttranslator.com/) in Custom Translator. La migrazione viene avviata da Hub.


Durante il processo viene eseguita la migrazione degli elementi seguenti:

1.  Le definizioni di uno o più progetti.

2.  La definizione di training verrà usata per creare una nuova definizione di modello per Custom Translator.

3.  Tutti i file paralleli e monolingue usati nell'ambito dei training verranno trasferiti come nuovi documenti in Custom Translator.

4.  I dati di test e ottimizzazione del sistema generati automaticamente verranno esportati e creati come nuovi documenti in Custom Translator.

Per tutti i training distribuiti, Custom Translator eseguirà il training del modello gratuitamente. È possibile distribuire i training manualmente.

>[!Note]
>Per il corretto completamento di un training, Custom Translator richiede almeno 10.000 frasi estratte. Se il numero di frasi estratte è inferiore al [minimo consigliato](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences), Custom Translator non può eseguire il training.

Tutti i training completati correttamente ma non distribuiti verranno trasferiti come bozza in Custom Translator.

## <a name="find-custom-translator-workspace-id"></a>Trovare l'ID dell'area di lavoro di Custom Translator

Per eseguire la migrazione dell'area di lavoro di [Microsoft Translator Hub](https://hub.microsofttranslator.com/) è necessario l'ID dell'area di lavoro di destinazione di Custom Translator. L'area di lavoro di destinazione di Custom Translator è la posizione in cui verrà eseguita la migrazione di tutte le aree di lavoro e tutti i progetti di Hub.

L'ID dell'area di lavoro di destinazione è disponibile nella pagina delle impostazioni di Custom Translator: 

1.  Passare alla pagina delle impostazioni nel portale Custom Translator.

2.  L'ID dell'area di lavoro è disponibile nella sezione Basic Information (Informazioni di base).

    ![Come trovare l'ID dell'area di lavoro di destinazione](media/how-to/how-to-find-destination-ws-id.png)

3. Conservare l'ID dell'area di lavoro di destinazione per riferimento durante il processo di migrazione.

## <a name="migrate-workspace"></a>Eseguire la migrazione dell'area di lavoro

Quando si esegue la migrazione dell'area di lavoro completa di Hub in Custom Translator, progetti, documenti e training vengono trasferiti in Custom Translator. Prima della migrazione, è necessario decidere se si vuole eseguire la migrazione solo dei training distribuiti o se si vogliono includere nella migrazione tutti i training completati.

Per eseguire la migrazione di un'area di lavoro:

1.  Accedere a Microsoft Translator Hub.

2.  Passare alla pagina "Settings" (Impostazioni).

3.  Nella pagina "Settings" (Impostazioni) fare clic su "Migrate Workspace data to Custom Translator" (Esegui la migrazione dei dati dell'area di lavoro in Custom Translator).

    ![Come eseguire la migrazione da Hub](media/how-to/how-to-migrate-workspace-from-hub.png)

4.  Nella pagina successiva selezionare una di queste due opzioni:

    a.  Deployed Trainings only (Solo training distribuiti): se si seleziona questa opzione, verrà eseguita solo la migrazione dei sistemi distribuiti e dei documenti correlati.

    b.  All Successful Trainings (Tutti i training completati): se si seleziona questa opzione, verrà eseguita la migrazione di tutti i training completati e dei documenti correlati.

    c.  Immettere l'ID dell'area di lavoro di destinazione in Custom Translator.

    ![Come eseguire la migrazione da Hub](media/how-to/how-to-migrate-from-hub-screen.png)

5.  Fare clic su Submit Request (Invia richiesta).

## <a name="migrate-project"></a>Eseguire la migrazione di un progetto

Se si vuole eseguire la migrazione dei progetti in modo selettivo, Microsoft Translator Hub offre questa possibilità.

Per eseguire la migrazione di un progetto:

1.  Accedere a Microsoft Translator Hub.

2.  Passare alla pagina "Projects" (Progetti).

3.  Fare clic sul collegamento "Migrate" (Esegui la migrazione) per il progetto appropriato.

    ![Come eseguire la migrazione da Hub](media/how-to/how-to-migrate-from-hub.png)

4.  Nella pagina successiva selezionare una di queste due opzioni:

    a.  Deployed Trainings only (Solo training distribuiti): se si seleziona questa opzione, verrà eseguita solo la migrazione dei sistemi distribuiti e dei documenti correlati. 

    b.  All Successful Trainings (Tutti i training completati): se si seleziona questa opzione, verrà eseguita la migrazione di tutti i training completati e dei documenti correlati.

    c.  Immettere l'ID dell'area di lavoro di destinazione in Custom Translator.

    ![Come eseguire la migrazione da Hub](media/how-to/how-to-migrate-from-hub-screen.png)

5.  Fare clic su "Submit Request" (Invia richiesta).

## <a name="migration-history"></a>Cronologia della migrazione

Se è stata richiesta la migrazione dell'area di lavoro e dei progetti da Hub, nella pagina delle impostazioni di Custom Translator sarà disponibile la cronologia della migrazione. 

Per visualizzare la cronologia della migrazione, seguire questa procedura:

1.  Passare alla pagina delle impostazioni nel portale Custom Translator.

2.  Nella sezione Migration History (Cronologia migrazione) della pagina Settings (Impostazioni) fare clic su Migration History (Cronologia migrazione).

    ![Cronologia della migrazione](media/how-to/how-to-migration-history.png)

Nella pagina Migration History (Cronologia migrazione) vengono visualizzate le informazioni di riepilogo seguenti relative a ogni migrazione richiesta.

1.  Migrated By (Migrazione eseguita da): nome e indirizzo di posta elettronica dell'utente che ha inviato la richiesta di migrazione

2.  Migrated On (Migrazione eseguita il): data e ora della migrazione

3.  Projects (Progetti): numero di progetti di cui è stata richiesta la migrazione rispetto al numero di progetti di cui è stata eseguita la migrazione.

4.  Trainings (Training): numero di training di cui è stata richiesta la migrazione rispetto al numero di training di cui è stata eseguita la migrazione.

5.  Documents (Documenti): numero di documenti di cui è stata richiesta la migrazione rispetto al numero di documenti di cui è stata eseguita la migrazione.

    ![Dettagli della cronologia della migrazione](media/how-to/how-to-migration-history-details.png)

Se si vuole un report di migrazione più dettagliato su progetti, training e documenti, è possibile esportare i dettagli come file con estensione csv.

>[!Note]
>La migrazione è supportata solo per le coppie di lingue per le quali è disponibile la traduzione automatica neurale. Verificare l'elenco delle [lingue con traduzione automatica neurale attualmente supportate](https://www.microsoft.com/translator/business/languages/). Per le coppie di lingue per le quali non è disponibile la traduzione automatica neurale, i dati verranno spostati da Hub a Custom Translator, ma non sarà possibile eseguire training specifici.

## <a name="custom-translator-versus-hub"></a>Custom Translator e Hub

Questa tabella mette a confronto le funzionalità di Microsoft Translator Hub con quelle di Custom Translator.

|   | Hub | Custom Translator |
|:-----|:----:|:----:|
|Stato delle funzionalità di personalizzazione   | Disponibilità generale  | Anteprima |
| Versione API per testo  | V2    | V3  |
| Personalizzazione SMT | Yes   | No  |
| Personalizzazione NMT | No     | Yes |
| Personalizzazione dei nuovi servizi voce unificati | No     | Yes |
| Nessuna traccia | Yes | Yes |

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il training di un modello](how-to-train-model.md).
- Iniziare a usare il modello di traduzione personalizzata distribuito tramite l'[API Traduzione testuale Microsoft V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
