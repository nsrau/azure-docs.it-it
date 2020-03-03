---
title: Risoluzione dei problemi-personalizzatore
description: Questo articolo contiene le risposte alle domande frequenti sulla risoluzione dei problemi relativi a personalizzazione.
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: 39db920b663ace12f2fc6ca35f9082a791da1541
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228040"
---
# <a name="personalizer-troubleshooting"></a>Risoluzione dei problemi di personalizzazione

Questo articolo contiene le risposte alle domande frequenti sulla risoluzione dei problemi relativi a personalizzazione.

## <a name="transaction-errors"></a>Errori di transazione

<details>
<summary><b>Ricevo una risposta HTTP 429 (troppe richieste) dal servizio. Cosa posso fare?</b></summary>

**Risposta**: se è stato selezionato un piano tariffario gratuito quando è stata creata l'istanza di personalizzazione, è previsto un limite di quota per il numero di richieste di rango consentite. Controllare la frequenza delle chiamate API per l'API Rank (nel riquadro metrica della portale di Azure per la risorsa di personalizzazione) e modificare il piano tariffario (nel riquadro piano tariffario) se il volume di chiamate deve aumentare oltre la soglia per il piano tariffario scelto.

</details>

<details>
<summary><b>Viene ricevuto un errore 5xx per le API Rank o Reward. Cosa dovrei fare?</b></summary>

**Risposta**: questi problemi devono essere trasparenti. Se continuano, contattare il supporto tecnico selezionando **nuova richiesta di supporto** nella sezione **supporto e risoluzione dei problemi** nella portale di Azure per la risorsa di personalizzazione.

</details>

## <a name="learning-loop"></a>Ciclo di apprendimento

<details>
<summary>
<b>Il ciclo di apprendimento non raggiunge una corrispondenza del 100% con il sistema senza personalizzazione. Ricerca per categorie risolvere questo problema?</b></summary>

**Risposta**: i motivi per cui non si raggiunge l'obiettivo del ciclo di apprendimento:
* Funzionalità non sufficienti inviate con la chiamata API di rango
* Bug nelle funzionalità inviate, ad esempio l'invio di dati di funzionalità non aggregati, ad esempio i timestamp all'API Rank
* Bug con elaborazione del ciclo, ad esempio l'invio di dati Reward a reward API per gli eventi

Per risolvere il problema, è necessario modificare l'elaborazione modificando le funzionalità inviate al ciclo o assicurarsi che la ricompensa sia una valutazione corretta della qualità della risposta di rango.

</details>

<details>
<summary>
<b>Il ciclo di apprendimento non sembra essere appreso. Ricerca per categorie risolvere questo problema?</b></summary>

**Risposta**: il ciclo di apprendimento necessita di un numero ridotto di chiamate di ricompensa prima che le chiamate di rango siano classificate in modo efficace

Se non si è certi della modalità di funzionamento del ciclo di apprendimento, eseguire una [valutazione offline](concepts-offline-evaluation.md)e applicare i criteri di apprendimento corretti.

</details>

<details>
<summary><b>Continuo a ottenere risultati di rango con tutte le stesse probabilità per tutti gli elementi. Ricerca per categorie Conosci il personale che sta imparando?</b></summary>

**Risposta**: la personalizzazione restituisce le stesse probabilità in un risultato dell'API di rango quando è appena iniziata e ha un modello _vuoto_ oppure quando si reimposta il ciclo di personalizzazione e il modello è ancora nel periodo di **frequenza di aggiornamento del modello** .

Quando inizia il nuovo periodo di aggiornamento, viene usato il modello aggiornato e si noterà la modifica delle probabilità.

</details>

<details>
<summary><b>Il ciclo di apprendimento era in formazione ma sembra non essere più appreso e la qualità dei risultati di rango non è così efficace. Cosa dovrei fare?</b></summary>

**Risposta:**
* Assicurarsi di aver completato e applicato una valutazione nel portale di Azure per la risorsa di personalizzazione (ciclo di apprendimento).
* Assicurarsi che tutti i premi vengano inviati, tramite l'API Reward ed elaborati.

</details>


<details>
<summary><b>Ricerca per categorie sapere che il ciclo di apprendimento viene aggiornato regolarmente e viene usato per assegnare un punteggio ai dati?</b></summary>

**Risposta**: è possibile trovare l'ora in cui è stato eseguito l'ultimo aggiornamento del modello nella pagina **impostazioni di apprendimento e modello** del portale di Azure. Se viene visualizzato un timestamp obsoleto, è probabile che non vengano inviate le chiamate di rango e di ricompensa. Se il servizio non dispone di dati in ingresso, non aggiorna l'apprendimento. Se il ciclo di apprendimento non viene aggiornato con frequenza sufficiente, è possibile modificare la frequenza di **aggiornamento del modello**del ciclo.

</details>

## <a name="offline-evaluations"></a>Valutazioni offline

<details>
<summary><b>L'importanza della funzionalità della valutazione offline restituisce un lungo elenco con centinaia o migliaia di elementi. Cos'è successo?</b></summary>

**Risposta**: questa operazione è in genere dovuta a timestamp, ID utente o altre funzionalità con granularità fine inviate in.

</details>

<details>
<summary><b>È stata creata una valutazione offline che è riuscita quasi immediatamente. Perché? Non vengono visualizzati risultati?</b></summary>

**Risposta**: la valutazione offline usa i dati del modello sottoposto a training dagli eventi in quel periodo di tempo. Se non sono stati inviati dati nel periodo di tempo compreso tra l'ora di inizio e di fine della valutazione, l'operazione verrà completata senza alcun risultato. Invia una nuova valutazione offline selezionando un intervallo di tempo con gli eventi che conosci sono stati inviati al personalizzatore.

</details>


## <a name="learning-policy"></a>Criteri di apprendimento

<details>
<summary><b>Ricerca per categorie importare un criterio di formazione?</b></summary>

**Risposta**: altre informazioni sui [concetti](concept-active-learning.md#understand-learning-policy-settings) relativi ai criteri di formazione e su [come applicare](how-to-manage-model.md) un nuovo criterio di formazione. Se non si vuole selezionare un criterio di formazione, è possibile usare la [valutazione offline](how-to-offline-evaluation.md) per suggerire un criterio di apprendimento, in base agli eventi correnti.

</details>

## <a name="security"></a>Sicurezza

<details>
<summary><b>La chiave API per il ciclo è stata compromessa. Cosa posso fare?</b></summary>

**Risposta**: è possibile rigenerare una chiave dopo aver scambiato i client in modo da usare l'altra chiave. La presenza di due chiavi consente di propagare la chiave in modo lazy senza dover incorrere in tempi di inattività. È consigliabile eseguire questa operazione in un ciclo normale come misura di sicurezza.

</details>

## <a name="next-steps"></a>Passaggi successivi

[Configurare la frequenza di aggiornamento del modello](how-to-settings.md#model-update-frequency)