---
title: Quando usare le entità ENUM e impostare le azioni dell'entità con un modello di Conversation Learner-Servizi cognitivi di Azure | Microsoft Docs
titleSuffix: Azure
description: Informazioni sul momento in cui è appropriato utilizzare le entità ENUM e impostare le azioni dell'entità con un modello di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nolachar
ROBOTS: NOINDEX
ms.openlocfilehash: 5443b97febd6bf3831690531bceb540181e7676c
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706984"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>Quando usare le entità ENUM e impostare le azioni dell'entità

In questa esercitazione verrà illustrato quando è necessario utilizzare le entità ENUM (Enumeration) e le azioni SET_ENTITY.

## <a name="video"></a>Video

[![Imposta l'anteprima dell'esercitazione sull'entità](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>Argomenti trattati

Questa esercitazione introdurrà due nuove funzionalità. Un nuovo tipo di entità denominato ENUM (short per Enumeration) e un nuovo tipo di azione denominato SET_ENTITY, che può fare riferimento a uno di questi valori enum e come suggerisce il nome, imposta l'entità su questo valore. Come si apprenderà di seguito, queste nuove funzionalità vengono usate insieme e verranno illustrate le modalità di utilizzo. Prima di entrare nei dettagli, è importante comprendere il problema che queste funzionalità contribuiscono a risolvere.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Problema

Ci sono casi di conversazioni in cui il significato delle parole dipende dal contesto.  Normalmente le parole chiave con etichetta vengono apprese ed estratte usando un servizio di comprensione del linguaggio, ma in questi casi questi sistemi potrebbero non essere in grado di apprendere l'uso di esempi con etichetta.

Si supponga di aver origliato parte di una conversazione tra persone vicine e di avere sentito solo la parola "Sì". Non è possibile sapere qual è il consenso o la conferma della "Sì" perché non è stata ricevuta la domanda prima. La domanda che precede è il contesto, che fornisce un significato alla risposta. Analogamente, poiché "Sì" è una risposta di questo tipo comune a molte domande diverse, non può essere appreso fornendo esempi come si farebbe con le entità con [training personalizzato](04-introduction-to-entities.md) , perché apprenderà ad etichettare ogni "Sì" come tale entità.

### <a name="example"></a>Esempio

È ora più chiaro l'esempio seguente:

Bot Si desidera servizi cognitivi di Azure?
Utente: Sì bot: Ti piace il gelato?
Utente: Sì

Nelle esercitazioni precedenti sono state esaminate le entità con [training personalizzato](04-introduction-to-entities.md) e il pensiero iniziale potrebbe consistere nel creare un'entità denominata "likesCogServices" ed etichettare il primo "Sì" come questa entità.  Tuttavia, il sistema indicherà anche il secondo "Sì". Quando si è provato a correggere l'etichetta della seconda "Sì" in "likesIceCream", si creerebbe un conflitto di due input "Yes", che significa che i diversi elementi e verrebbero bloccati.

In questi casi è necessario usare le entità ENUM e le azioni SET_ENTITY.

## <a name="when-to-use-enums-or-setentity-actions"></a>Quando usare le azioni ENUM o SET_ENTITY

Usare queste regole di seguito per capire quando usare le entità ENUM e le azioni SET_ENTITY:

- Il rilevamento o l'impostazione dell'entità è dipendente dal contesto
- Il numero di valori possibili è Fixed (Sì e no sarebbe due valori)

In altre parole, usare questi comandi per eventuali richieste di chiusura, ad esempio domande di conferma che comportano sempre sì o no.

> [!NOTE]
> Attualmente è prevista una limitazione di un massimo di 5 valori per entità enum. Ogni valore usa uno degli slot nel limite 64 corrente. Vedere [CL-values-and-](../cl-values-and-boundaries.md) delimitators

Esempio: Bot L'ordine è corretto?
Utente: Sì

Quando i valori possibili dell'entità sono aperti e non corretti, è necessario usare una funzionalità alternativa, ad esempio l' [entità prevista](05-expected-entity.md).

Esempio: Bot Come ti chiami?
Utente: Matt bot: Qual è il colore preferito?
Utente: Silver

Questi prompt sono considerati aperti, perché potrebbero ricevere risposte con valori arbitrari.

## <a name="what"></a>Dettagli

### <a name="enum-entities"></a>Entità ENUM

Le entità ENUM vengono create esattamente come le altre entità. Analogamente alle entità "programmatiche", non è possibile etichettare parole come queste entità. Ma devono essere impostate tramite codice o azioni SET_ENTITY.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Imposta azioni entità

Come indicato in precedenza, le azioni "set Entity" impostano semplicemente un'entità su un valore enum noto. È possibile ottenere gli stessi risultati creando un'azione di callback API e usando il gestore della memoria per impostare l'entità su un valore. ad esempio [https://login.microsoftonline.com/consumers/](`memory.Set(entityName, entityValue)`). La necessità di scrivere questo codice e creare queste azioni diventa noioso e difficile da gestire, quindi Conversation Learner dispone di azioni speciali per facilitare il lavoro e generare automaticamente queste azioni quando vengono usate. La presenza di questi come azioni indipendenti consente di mantenere la possibilità di comporre questi senza essere associati ad altre azioni o codice nel bot.

- È possibile creare azioni di entità set solo quando si fa riferimento a un valore di un'entità enum, quindi è necessario prima creare un'entità enum.
- Le azioni di impostazione dell'entità sono anche "non await" perché non hanno output visibile ed è necessario seguire un'azione di "attesa" che l'utente può visualizzare.
- Le azioni imposta entità non sono modificabili, pertanto non è possibile modificarle dopo la creazione.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Generazione automatica delle azioni

Se nel modello esiste un'entità enum, Conversation Learner creerà azioni segnaposto per ognuno dei valori possibili e le renderà disponibili per la selezione durante il training. Al momento della selezione, l'azione verrà creata automaticamente.

Ad esempio, se si crea un'entità enum con i valori "Yes" e "No":

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

Anche senza creare esplicitamente azioni per questa nuova enumerazione, verranno visualizzate due nuove azioni durante il training:

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Crea un bot usando queste nuove funzionalità

### <a name="requirements"></a>Requisiti

Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

Si creerà un bot per simulare l'ordinamento rapido degli alimenti. Avrà valori discreti per le dimensioni di bevande e patatine (SMALL/MEDIum/LARGE) e domande di conferma con risposte Sì/NO. Entrambe queste entità soddisfano le due regole sopra indicate come risposte dipendenti dal contesto e valori fissi.

### <a name="create-the-model"></a>Creare il modello

1. Nell'interfaccia utente Web fare clic su "Importa"
2. Selezionare l'esercitazione denominata "tutorial-enum-set-Entity"

Verrà visualizzata la pagina di gestione del modello.
Si noti che il modello contiene già alcune entità enum e imposta le azioni dell'entità.

### <a name="create-the-first-dialog"></a>Creare il primo dialogo

1. Nel pannello di navigazione a sinistra fare clic su "Training Dialogs", quindi fare clic sul pulsante "New Train Dialog".
2. In qualità di utente digitare, "Salve, desidero ordinare una Coca e Fries".
3. Fai clic sul pulsante "score actions"

   > L'utente non ha specificato le dimensioni per la bevanda o le patatine, quindi è necessario richiederle.

4. Selezionare l'azione con risposta: "Quali sono le dimensioni da bere?"
5. Il tipo di utente è "large"
6. Fai clic sul pulsante "score actions"
7. Selezionare l'azione SET_ENTITY-"drinkSize: GRANDI DIMENSIONI
8. Selezionare l'azione con risposta: "Quali sono le dimensioni di Fries?"
9. Come tipo di utilizzo in, "um, renderli un supporto.
10. Fai clic sul pulsante "score actions"
11. Selezionare l'azione SET_ENTITY-"friesSize: MEDIA
12. Selezionare l'azione con risposta: "Vuoi avere tutti i condimenti?"
13. Come il tipo di utilizzo in, "Yes"
14. Fai clic sul pulsante "score actions"
15. Selezionare l'azione SET_ENTITY-"condimentsConfirmation: SÌ
16. Selezionare l'azione con risposta: "OK, ho un ordine per una bevanda di grandi dimensioni e patatine medie. È corretto? "
17. Come il tipo di utilizzo in, "Yes"
18. Fai clic sul pulsante "score actions"
19. Selezionare l'azione SET_ENTITY-"orderConfirmation: SÌ
20. Selezionare l'azione con risposta: "OK, il numero di ordine è 58. Attendere. "
21. Fare clic su "Salva" per chiudere la finestra di dialogo

È stata appena creata la prima finestra di dialogo usando le entità ENUM e le azioni SET_ENTITY. È possibile eseguire molte più combinazioni dell'utente specificando informazioni parziali o sperimentando altri tipi di domande di chiusura.

> [!NOTE]
> Durante il training vengono visualizzati i segnaposto per le azioni SET_ENTITY, ad esempio
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> Tuttavia, durante la creazione di finestre di dialogo di log o l'uso di bot distribuiti, gli utenti non li vedranno.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Input alternativi](./10-alternative-inputs.md)
