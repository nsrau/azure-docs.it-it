---
title: Glossario dei termini - Servizio visione artificiale personalizzato
titlesuffix: Azure Cognitive Services
description: Glossario dei termini del Servizio visione artificiale personalizzato.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: 3a34af77a2806ceb56e939e2b153f2e68bba61cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605152"
---
# <a name="glossary-of-terms-for-custom-vision-service"></a>Glossario dei termini del Servizio visione artificiale personalizzato

Di seguito sono indicati alcuni termini comunemente usati nel servizio visione artificiale personalizzato:

## <a name="classifier"></a>Classificatore

Un classificatore è un modello che si genera usando il Servizio visione artificiale personalizzato attraverso l'uso di alcune immagini di esercitazione. Dopo avere esercitato un nuovo classificatore, si riceve un endpoint di valutazione (HTTPS) da aggiungere alla propria app. Ogni classificatore che si genera è incluso in un proprio progetto. Tutti i progetti sono visibili dopo che è stato effettuato l'accesso.

## <a name="domain"></a>Domain

Quando si crea un progetto, si seleziona un "dominio" per il progetto. Il dominio ottimizza un classificatore per un tipo specifico di oggetto nelle immagini usate. Se ad esempio lo scenario prevede di classificare tra immagini di una torta di mele e immagini di una torta di carote, è consigliabile selezionare il dominio "Cibo". Se non si è sicuri di quale dominio scegliere, selezionare il dominio "Generale".

- **Dominio Cibo.** Ottimizzato per i piatti che si possono vedere nel menu di un ristorante. Non è ottimizzato per riconoscere i singoli frutti o le singole verdure. Se si intende classificare le fotografie di singoli frutti o singole verdure, usare il dominio Generale.
- **Dominio Riferimento.** Ottimizzato per i luoghi riconoscibili, sia naturali che artificiali. Questo dominio funziona meglio quando il luogo è chiaramente visibile nella fotografia, anche se è leggermente nascosto da un gruppo di persone in posa davanti ad esso.
- **Dominio Vendita al dettaglio.** Ottimizzato per classificare le immagini di un catalogo di vendita o di un sito Web di vendita. Se si vuole classificare con elevata precisione abiti, pantaloni, magliette, ecc, è consigliabile usare il dominio Vendita al dettaglio.
- **Dominio Generale.** Adatto a un'ampia gamma di attività di classificazione delle immagini.

I modelli generati da **domini compatti** possono essere esportati con la funzionalità di esportazione iterazione. I modelli sono ottimizzati per i vincoli di classificazione in tempo reale su dispositivi mobili. I classificatori creati con un dominio compatto possono essere leggermente meno precisi rispetto a un dominio standard con la stessa quantità di dati di training. Il compromesso è che sono sufficientemente piccoli da poter essere eseguiti localmente in near real time. 

## <a name="evaluation"></a>Valutazione

Dopo avere esercitato il classificatore, è possibile inviare qualsiasi immagine per la valutazione usando l'endpoint HTTPS generato automaticamente. Il classificatore restituisce un set di tag previsti, in ordine di attendibilità.

## <a name="iteration"></a>Iterazione

Ogni volta che si esercita o riesercita il classificatore, si crea una nuova iterazione del modello. Si conservano le iterazioni passate per poter confrontare i progressi nel tempo. È possibile eliminare qualsiasi iterazione che non è più ritenuta utile. L'eliminazione di un'iterazione è permanente e si eliminano anche tutte le immagini o le modifiche che erano specifiche dell'iterazione eliminata. 

## <a name="precision"></a>Precision

Quando si classifica un'immagine, quante probabilità ha il classificatore di classificare l'immagine correttamente? Di tutte le immagini usate per esercitare il classificatore (cani e pony), in che percentuale il modello ha ottenuto il risultato corretto? 99 tag corretti su 100 immagini rappresenta una precisione del 99%.

## <a name="predictions"></a>Previsioni

Man mano che il classificatore accetta nuove immagini da classificare, le memorizza. È possibile usare queste immagini per migliorare la precisione del classificatore applicato i tag corretti alle immagini interpretate in modo errato. È quindi possibile usare queste nuove immagini per esercitare nuovamente il classificatore.

## <a name="recall"></a>Richiamo

Di tutte le immagini da classificare, quante sono state identificate correttamente dal classificatore? Un risultato pari a 100% indica che se nelle immagini usate per esercitare il classificatore erano presenti 38 cani, il classificatore ha individuato 38 cani.

## <a name="settings"></a>Impostazioni

Sono disponibili due tipi di impostazioni: quelle a livello di progetto e quelle a livello di utente.

- Impostazioni a livello di progetto:
  
  Le impostazioni a livello di progetto si applicano a un progetto o un classificatore. tra cui:

   - Dominio del progetto
   - Nome progetto
   - Descrizione del progetto
   - Utilizzo:
      - Numero di immagini di esercitazione
      - Numero di tag creati
      - Numero di iterazioni create

- Impostazioni a livello di utente: 
   - Chiavi di sottoscrizione: una per l'esercitazione e una per la valutazione/previsione.
   - Utilizzo:
      - Numero di progetti creati
      - Numero di chiamate alle API di valutazione/previsione effettuate.

## <a name="tags"></a>Tag

Usare i tag per etichettare gli oggetti nelle immagini di esercitazione. Se si crea un classificatore per identificare cani e pony, occorre aggiungere un tag "cane" sulle immagini che contengono cani, un tag "pony" su quelle che contengono pony ed entrambi i tag "cane" e "pony" sulle immagini che ritraggono entrambi gli animali.

## <a name="training-image"></a>Immagine di esercitazione

Per creare un classificatore ad alta precisione, il Servizio visione artificiale personalizzato necessita di numerose immagini di esercitazione. Un'immagine di esercitazione è una fotografia che si vuole classificare tramite il Servizio visione artificiale personalizzato. Per classificare ad esempio le arance, è necessario caricare numerose immagini di arance nel Servizio visione artificiale personalizzato per consentire al servizio di creare un classificatore in grado di riconoscere le arance. Si consiglia di caricare almeno 30 immagini per tag.

## <a name="workspace"></a>Area di lavoro

L'area di lavoro contiene tutte le immagini di esercitazione e riflette tutte le modifiche dall'iterazione più recente, ad esempio le immagini aggiunte o rimosse. Quando si esercita il classificatore, si crea una nuova iterazione del classificatore usando le immagini presenti nell'area di lavoro.