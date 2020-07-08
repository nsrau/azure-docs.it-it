---
title: Assegnare punteggi al modello Vowpal Wabbit
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Score Vowpal Wabbit Model per generare punteggi per un set di dati di input, usando un modello di Wabbit Vowpal con training esistente.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 02f4fe4f97d3e976675757835e3931666d1c6410
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857743"
---
# <a name="score-vowpal-wabbit-model"></a>Assegnare punteggi al modello Vowpal Wabbit
Questo articolo descrive come usare il modulo **Score Vowpal Wabbit modello** in Azure Machine Learning Designer (Preview) per generare i punteggi per un set di dati di input, usando un modello di Wabbit Vowpal con training esistente.  

Questo modulo fornisce la versione più recente di Vowpal Wabbit Framework, versione 8.8.1. Usare questo modulo per assegnare un punteggio ai dati usando un modello sottoposto a training salvato nel formato VW versione 8.  

## <a name="how-to-configure-score-vowpal-wabbit-model"></a>Come configurare Score Vowpal Wabbit Model

1.  Aggiungere il modulo **Score Vowpal Wabbit Model** all'esperimento.  
  
2.  Aggiungere un modello Wabbit Vowpal sottoposto a training e connetterlo alla porta di input di sinistra. È possibile usare un modello sottoposto a training creato nello stesso esperimento o individuare un modello salvato nella categoria **set di impostazioni** del riquadro di spostamento a sinistra della finestra di progettazione. Tuttavia, il modello deve essere disponibile in Azure Machine Learning Designer.  
  
    > [!NOTE]
    > Sono supportati solo i modelli Vowpal Wabbit 8.8.1; non è possibile connettere i modelli salvati sottoposti a training usando altri algoritmi.
  
3.  Aggiungere il set di dati di test e connetterlo alla porta di input di destra. Se il set di dati di test è una directory che contiene il file di dati di test, specificare il nome del file di dati di test con il **nome del file di dati di test**. Se il set di dati di test è un singolo file, lasciare vuoto **il nome del file di dati di test** .

4. Nella casella di testo **argomenti VW** Digitare un set di argomenti della riga di comando validi per il file eseguibile Wabbit di Vowpal.  

    Per informazioni sugli argomenti Vowpal Wabbit supportati e non supportati in Azure Machine Learning, vedere la sezione [Note tecniche](#technical-notes) .  

5.  **Nome del file di dati di test**: digitare il nome del file che contiene i dati di input. Questo argomento viene utilizzato solo quando il set di dati di test è una directory.

6. **Specifica tipo di file**: indica il formato usato dai dati di training. Vowpal Wabbit supporta questi due formati di file di input:  

   - **VW** rappresenta il formato interno usato da Vowpal Wabbit. Per informazioni dettagliate, vedere la [pagina wiki di Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) . 
   - **SVMLight** è un formato usato da altri strumenti di machine learning. 

7. Selezionare l'opzione **Includi una colonna aggiuntiva contenente le etichette**, se si desidera restituire le etichette insieme ai punteggi.  

   In genere, quando si gestiscono dati di testo, Vowpal Wabbit non richiede etichette e restituirà solo i punteggi per ogni riga di dati.  

8. Selezionare l'opzione **Includi una colonna aggiuntiva contenente punteggi non elaborati**, se si desidera restituire punteggi non elaborati insieme ai risultati.  

9. Inviare la pipeline.

## <a name="results"></a>Risultati

Al termine del training:

+ Per visualizzare i risultati, fare clic con il pulsante destro del mouse sull'output del modulo [Score Vowpal Wabbit Model](score-vowpal-wabbit-model.md) . L'output indica un punteggio di stima normalizzato compreso tra 0 e 1. 

+ Per valutare i risultati, il set di dati di output deve contenere nomi di colonna di Punteggio specifici, che soddisfano i requisiti del modulo Evaluate Model.

  + Per l'attività di regressione, il set di dati da valutare deve avere una colonna, denominata `Regression Scored Labels` , che rappresenta le etichette con punteggio.
  + Per l'attività di classificazione binaria, il set di dati da valutare deve avere due colonne, denominate, `Binary Class Scored Labels` `Binary Class Scored Probabilities` , che rappresentano le etichette con punteggio e le probabilità rispettivamente.
  + Per l'attività di classificazione multiclassificazione, il set di dati da valutare deve avere una colonna, denominata `Multi Class Scored Labels` , che rappresenta le etichette con punteggio.

  Si noti che non è possibile valutare direttamente i risultati del modulo Score Vowpal Wabbit Model. Prima di valutare, il set di dati deve essere modificato in base ai requisiti indicati sopra.

##  <a name="technical-notes"></a>Note tecniche

Questa sezione contiene informazioni dettagliate sull'implementazione, suggerimenti e risposte alle domande più frequenti.

### <a name="parameters"></a>Parametri

Vowpal Wabbit include molte opzioni della riga di comando per la scelta e l'ottimizzazione degli algoritmi. Una descrizione completa di queste opzioni non è possibile. è consigliabile visualizzare la [pagina wiki di Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).  

I parametri seguenti non sono supportati in Azure Machine Learning Studio (classico).  

-   Opzioni di input/output specificate in[https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     Queste proprietà sono già configurate automaticamente dal modulo.  
  
-   Inoltre, qualsiasi opzione che genera più output o accetta più input non è consentita. Sono inclusi *`--cbt`* , *`--lda`* e *`--wap`* .  
  
-   Sono supportati solo gli algoritmi di apprendimento supervisionato. In questo modo non sono consentite le opzioni seguenti: *`–active`* , `--rank` e *`--search`* così via.  

Sono consentiti tutti gli argomenti diversi da quelli descritti in precedenza.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 