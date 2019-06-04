---
title: Quando usare le entità ENUM e azioni dell'entità impostata con un modello di apprendimento di conversazioni - servizi cognitivi di Azure | Microsoft Docs
titleSuffix: Azure
description: Informazioni su quando è appropriato usare entità ENUM e azioni dell'entità impostata con un modello di apprendimento di conversazioni.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1393d0bd1c31a2c9c24652e260ef7f3182d91367
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476495"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>Quando usare azioni di SET di entità ed entità di ENUM

Questa esercitazione verrà spiegato quando è necessario usare azioni SET_ENTITY ed entità di ENUM (enumerazione).

## <a name="video"></a>Video

[![Anteprima di set di entità dell'esercitazione](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>Argomenti trattati

Questa esercitazione verrà introdotte due nuove funzionalità. Un nuovo tipo di entità denominata ENUM (abbreviazione di enumerazione) e un nuovo tipo di azione denominata SET_ENTITY, che può fare riferimento a uno di questi valori di enumerazione e, come suggerisce il nome, imposta l'entità a questo valore. Come verrà illustrato sotto, queste nuove funzionalità vengono usate insieme e verrà spiegato che cosa sono e come usarli riportato di seguito. Prima di passare i dettagli, è importante comprendere quale problema queste funzionalità consentono di risolvere.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Problema

Vi sono casi in cui il significato delle parole dipende dal contesto di conversazioni.  In genere le parole chiave con etichette vengono acquisite ed estratti usando un servizio di comprensione del linguaggio, ma in questi casi questi sistemi non siano in grado di apprende l'uso di esempi etichettati.

Si supponga si overhear parte di una conversazione tra persone nelle vicinanze e si sentono solo la parola "Sì". Non è possibile sapere che cos'è accettandone "Sì" o perché non hai sentito la domanda che conferma richiesto prima di esso. La domanda porre la prima è il contesto che fornisce un significato per la risposta. Allo stesso modo dopo "Sì" è una risposta a numerose domande non può essere riconosciuto, fornendo esempi di come si farebbe con così comune [sottoposto a training di Custom](04-introduction-to-entities.md) entità poiché verrebbe imparano etichettare ogni "Sì" come tale entità.

### <a name="example"></a>Esempio

Occorre chiarire ulteriormente con l'esempio seguente:

BOT: Servizi cognitivi di Azure è piacevole?
Utente: Sì Bot: Ice cream è piacevole?
Utente: Yes

Nelle esercitazioni precedenti abbiamo esaminato [sottoposto a training di Custom](04-introduction-to-entities.md) entità e il pensiero iniziale potrebbe essere per creare un'entità denominata "likesCogServices" e assegnare un'etichetta il primo "Sì" come questa entità.  Tuttavia, il sistema sarebbe l'etichetta la seconda "Sì". Quando si è tentato di risolvere l'etichetta della seconda "Sì" per "likesIceCream", è quindi necessario creare un conflitto di due input stesso "Yes" diversi significati significato e potrebbe essere bloccati.

È in questi casi in cui è necessario usare le azioni SET_ENTITY ed entità di Enumerazione.

## <a name="when-to-use-enums-or-setentity-actions"></a>Quando usare le enumerazioni o le azioni SET_ENTITY

Usare queste regole indicate di seguito per sapere quando usare le entità ENUM e SET_ENTITY azioni:

- Impostazione dell'entità o il rilevamento è dipendente dal contesto
- Numero di valori possibili è fisso (Sì e No sarebbe due valori)

In altre parole, usare questi valori per eventuali richieste è terminata di chiusura, ad esempio domande conferma che è sempre comportare Yes o No.

> [!NOTE]
> Attualmente sono la limitazione di valori fino a 5 per ogni entità enum. Ogni valore viene utilizzato uno degli slot nel limite di 64 corrente. Vedere [cl-valori-e-limiti](../cl-values-and-boundaries.md)

Esempio: BOT: L'ordine è corretta?
Utente: Yes

Quando i valori possibili dell'entità sono aperte e non corretto, è necessario usare, ad esempio una funzionalità alternativa [previsto entità](05-expected-entity.md).

Esempio: BOT: Come ti chiami?
Utente: Matt Bot: Che cos'è il colore preferito?
Utente: Silver

Queste richieste vengono considerate aperte perché si è stato possibile ottenere risposte con valori arbitrari.

## <a name="what"></a>Cosa

### <a name="enum-entities"></a>Entità ENUM

Le entità ENUM vengono create esattamente come altre entità. Analogamente alle entità "a, livello di codice", è Impossibile etichetta parole come queste entità. Al contrario, deve essere impostate tramite codice o SET_ENTITY azioni.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Impostare le azioni di entità

Come indicato in precedenza, le azioni di "Set di entità" è sufficiente impostare un'entità in un valore enum noti. È possibile ottenere gli stessi risultati tramite la creazione di un'azione di callback di API e Usa il gestore della memoria per impostare l'entità su un valore. ad esempio `memory.Set(entityName, entityValue)`. La necessità di scrivere il codice e creare queste azioni diventerebbe noioso e difficile da gestire, in modo da strumento di apprendimento di conversazioni sono azioni speciali per facilitare il lavoro e generare automaticamente queste azioni quando vengono usati. Con queste azioni indipendenti consente di mantenere la capacità di comporre questi senza essere accoppiati con altre azioni o il codice nel tuo bot.

- Impostare le azioni possono essere create solo quando si fa riferimento a un valore di un'entità di enumerazione è quindi necessario creare un'entità di enumerazione prima entità.
- Impostare le azioni di entità sono anche "non-await" poiché non dispongono di alcun output visibile e devono essere seguiti da un'azione "attendere" l'utente può visualizzare.
- Impostare le azioni di entità non sono modificabili vale a dire che non di modificarle dopo la creazione.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Generazione automatica di azione

Se esiste un'entità di enumerazione nel modello, strumento di apprendimento di conversazioni crea segnaposto azioni per ognuno dei valori possibili e renderli disponibili per la selezione durante il training. Volta effettuata la selezione, l'azione potrebbe essere creato automaticamente.

Ad esempio, se si crea un'entità di enumerazione con i valori "Yes" e "No":

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

Anche senza creare in modo esplicito le azioni per questa nuova enumerazione si vedrà due nuove azioni disponibili durante il training:

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Crea un Bot tramite queste nuove funzionalità

### <a name="requirements"></a>Requisiti

Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

Si creerà un bot per simulare l'ordinamento cibo fast. Avrà valori discreti per le dimensioni delle bevande e fritte (piccola/Media/grande) e conferma alle domande con Sì / risposte. Entrambe queste entità soddisfa le due regole di sopra dei valori fissi e risposte dipendente dal contesto.

### <a name="create-the-model"></a>Creare il modello

1. L'interfaccia utente Web, fare clic su "Importa"
2. Selezionare l'esercitazione denominato "Esercitazione-Enum-Set-Entity"

Questo si passerà alla pagina di gestione modelli.
Si noti che il modello contiene già alcune entità enum e impostare le azioni di entità.

### <a name="create-the-first-dialog"></a>Creare il primo dialogo

1. Nel riquadro di spostamento a sinistra fare clic su "Finestre di dialogo Train", quindi fare clic sul pulsante "Nuovo dialogo Train".
2. Come tipo di utente nella, "salve, mi piacerebbe ordinare un coke e fritte.".
3. Fare clic sul pulsante "Punteggio azioni"

   > L'utente non ha specificato le dimensioni per il bevande o fritte dobbiamo chiedere.

4. Selezionare l'azione con risposta: "Bevande quali dimensioni si?"
5. Come tipo di utente in "grande"
6. Fare clic sul pulsante "Punteggio azioni"
7. Selezionare l'azione SET_ENTITY - "drinkSize: GRANDE"
8. Selezionare l'azione con risposta: "Quali dimensioni fritte desiderato?"
9. Come tipo di utilizzo in "Um, rendere quelli un supporto.
10. Fare clic sul pulsante "Punteggio azioni"
11. Selezionare l'azione SET_ENTITY - "friesSize: MEDIUM"
12. Selezionare l'azione con risposta: "Si vuole qualsiasi condimenti?"
13. Come tipo di utilizzo, in "Sì"
14. Fare clic sul pulsante "Punteggio azioni"
15. Selezionare l'azione SET_ENTITY - "condimentsConfirmation: YES"
16. Selezionare l'azione con risposta: "Ok, ho un ordine per una bevanda grande e medie fritte. È giusto?"
17. Come tipo di utilizzo, in "Sì"
18. Fare clic sul pulsante "Punteggio azioni"
19. Selezionare l'azione SET_ENTITY - "orderConfirmation: YES"
20. Selezionare l'azione con risposta: "Bene, il numero di ordine è 58. Attendere. in questa posizione."
21. Fare clic su "Salva" per chiudere la finestra di dialogo

La prima finestra di dialogo usando le entità ENUM e azioni SET_ENTITY appena creato. È possibile apportare molte più combinazioni di utente specifica delle informazioni parziali o di esperimenti con altri tipi di domande che è terminata di chiusura.

> [!NOTE]
> Durante il training verrà visualizzato come segnaposto per le azioni SET_ENTITY
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> ma quando la creazione di log tramite distribuzione o le finestre di dialogo Bot non sarà possibile visualizzare questi.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Input alternativi](./10-alternative-inputs.md)
