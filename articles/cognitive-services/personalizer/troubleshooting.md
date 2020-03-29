---
title: Risoluzione dei problemi - Personalizer
description: Questo articolo contiene le risposte alle domande frequenti sulla risoluzione dei problemi relativi al personalista.
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: 904953f028eb31afe42cf477ac05be43e8b72a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336022"
---
# <a name="personalizer-troubleshooting"></a>Risoluzione dei problemi del personalista

Questo articolo contiene le risposte alle domande frequenti sulla risoluzione dei problemi relativi al personalista.

## <a name="transaction-errors"></a>Errori di transazione

<details>
<summary><b>Ottengo una risposta HTTP 429 (troppe richieste) dal servizio. Cosa posso fare?</b></summary>

**Risposta**: Se al momento della creazione dell'istanza Personalizer è stato selezionato un livello di prezzo gratuito, è previsto un limite di quota per il numero di richieste di classificazione consentite. Esaminare la frequenza delle chiamate API per l'API di classificazione (nel riquadro Metriche nel portale di Azure per la risorsa Personalizer) e modificare il piano tariffario (nel riquadro Livello di prezzo) se si prevede che il volume delle chiamate aumenti oltre la soglia per il piano tariffario scelto.

</details>

<details>
<summary><b>Viene visualizzato un errore 5xx sulle API Rank o Reward. Cosa dovrei fare?</b></summary>

**Risposta**: Questi problemi dovrebbero essere trasparenti. Se continuano, contattare il supporto selezionando Nuova richiesta di supporto nella sezione Supporto e risoluzione dei problemi nel portale di Azure per la risorsa Personalizer.If they continue, contact support by selecting **New support request** in the **Support - troubleshooting section,** in the Azure portal for your Personalizer resource.

</details>

## <a name="learning-loop"></a>Ciclo di apprendimento

<details>
<summary>
<b>Il ciclo di apprendimento non raggiunge una corrispondenza del 100% con il sistema senza Personalizer. Come posso risolvere questo problema?</b></summary>

**Risposta**: I motivi per cui non raggiungi il tuo obiettivo con il ciclo di apprendimento:
* Non abbastanza funzionalità inviate con chiamata API di classificazione
* Bug nelle funzionalità inviate, ad esempio l'invio di dati di entità geografiche non aggregati, ad esempio timestamp all'API di classificazione
* Bug con elaborazione del ciclo, ad esempio la non invio di dati premio all'API Reward per gli eventi

Per risolvere il problema, è necessario modificare l'elaborazione modificando le funzionalità inviate al ciclo o assicurarsi che la ricompensa sia una valutazione corretta della qualità della risposta del Rank.

</details>

<details>
<summary>
<b>Il ciclo di apprendimento non sembra imparare. Come posso risolvere questo problema?</b></summary>

**Risposta**: Il ciclo di apprendimento ha bisogno di alcune migliaia di chiamate Reward prima di Rank chiamate priorità in modo efficace.

Se non si è certi del comportamento del ciclo di apprendimento, eseguire una [valutazione offline](concepts-offline-evaluation.md)e applicare i criteri di apprendimento corretti.

</details>

<details>
<summary><b>Continuo a ottenere risultati di rango con tutte le stesse probabilità per tutti gli elementi. Come faccio a sapere che Personalizer sta imparando?</b></summary>

**Risposta**: Personalizer restituisce le stesse probabilità in un risultato dell'API Rank quando ha appena avviato e ha un modello _vuoto,_ o quando si reimposta il ciclo personalizer e il modello è ancora entro il periodo di frequenza di **aggiornamento del modello.**

Quando inizia il nuovo periodo di aggiornamento, viene utilizzato il modello aggiornato e le probabilità cambiano.

</details>

<details>
<summary><b>Il ciclo di apprendimento stava imparando, ma sembra non imparare più, e la qualità dei risultati di Rank non è così buono. Cosa dovrei fare?</b></summary>

**Risposta**:
* Assicurarsi di aver completato e applicato una valutazione nel portale di Azure per la risorsa Personalizer (ciclo di apprendimento).
* Assicurati che tutti i premi vengano inviati, tramite l'API Reward, ed elaborati.

</details>


<details>
<summary><b>Come faccio a sapere che il ciclo di apprendimento viene aggiornato regolarmente e viene utilizzato per segnare i miei dati?</b></summary>

**Risposta:** è possibile trovare l'ora dell'ultimo aggiornamento del modello nella pagina Impostazioni modello e apprendimento del portale di Azure.Answer : You can find the time the time the model was last updated in the **Model and Learning Settings** page of the Azure portal. Se vedi un vecchio timestamp, è probabile che tu non stia inviando le chiamate Rank e Reward. Se il servizio non dispone di dati in ingresso, non aggiorna l'apprendimento. Se il ciclo di apprendimento non viene aggiornato abbastanza frequentemente, è possibile modificare la **frequenza**di aggiornamento del modello del ciclo .

</details>

## <a name="offline-evaluations"></a>Valutazioni offline

<details>
<summary><b>L'importanza delle funzionalità di una valutazione offline restituisce un lungo elenco con centinaia o migliaia di elementi. Cos'è successo?</b></summary>

**Risposta**: Ciò è in genere dovuto a timestamp, ID utente o altre funzionalità grangranate inviate.

</details>

<details>
<summary><b>Ho creato una valutazione offline e ci è riuscita quasi istantaneamente. Perché? Non vedo nessun risultato?</b></summary>

**Risposta:** La valutazione offline utilizza i dati del modello sottoposto a training degli eventi in tale periodo di tempo. Se non sono stati inviati dati nel periodo di tempo compreso tra l'ora di inizio e l'ora di fine della valutazione, verranno completati senza alcun risultato. Invia una nuova valutazione offline selezionando un intervallo di tempo con gli eventi che sai sono stati inviati al Personalizer.

</details>


## <a name="learning-policy"></a>Politica di apprendimento

<details>
<summary><b>Come si importa un criterio di apprendimento?</b></summary>

**Risposta**: Ulteriori informazioni sui [concetti di apprendimento dei criteri](concept-active-learning.md#understand-learning-policy-settings) e su come [applicare](how-to-manage-model.md) una nuova politica di apprendimento. Se non si desidera selezionare un criterio di apprendimento, è possibile utilizzare la [valutazione offline](how-to-offline-evaluation.md) per suggerire un criterio di apprendimento, in base agli eventi correnti.

</details>

## <a name="security"></a>Security

<details>
<summary><b>La chiave API per il ciclo è stata compromessa. Cosa posso fare?</b></summary>

**Risposta**: È possibile rigenerare una chiave dopo aver scambiato i client per utilizzare l'altra chiave. La presenza di due chiavi consente di propagare la chiave in modo lazy senza dover avere tempi di inattività. Si consiglia di eseguire questa operazione su un ciclo regolare come misura di sicurezza.

</details>

## <a name="next-steps"></a>Passaggi successivi

[Configurare la frequenza di aggiornamento del modello](how-to-settings.md#model-update-frequency)