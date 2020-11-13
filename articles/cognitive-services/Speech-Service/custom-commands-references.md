---
title: Concetti e definizioni dei comandi personalizzati-servizio vocale
titleSuffix: Azure Cognitive Services
description: In questo articolo vengono fornite informazioni sui concetti e le definizioni per le applicazioni di comandi personalizzati.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 98510132b2341736664dfafa52e9567df95652be
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561098"
---
# <a name="custom-commands-concepts-and-definitions"></a>Concetti e definizioni dei comandi personalizzati

Questo articolo funge da riferimento per i concetti e le definizioni per le applicazioni di comandi personalizzati.

## <a name="commands-configuration"></a>Configurazione comandi
I comandi sono i blocchi predefiniti di base di un'applicazione comandi personalizzata. Un comando è un set di configurazioni necessarie per completare un'attività specifica definita da un utente.

### <a name="example-sentences"></a>Frasi di esempio
Le espressioni di esempio sono gli esempi di set che l'utente può impostare per attivare un determinato comando. È necessario fornire solo un campione di espressioni e non un elenco esaustivo. 

### <a name="parameters"></a>Parametri
I parametri sono le informazioni richieste dai comandi per completare un'attività. In scenari complessi, i parametri possono essere usati anche per definire le condizioni che attivano azioni personalizzate.

### <a name="completion-rules"></a>Regole di completamento
Le regole di completamento sono una serie di regole da eseguire dopo che il comando è pronto per essere completato, ad esempio quando vengono soddisfatte tutte le condizioni delle regole.

### <a name="interaction-rules"></a>Regole di interazione
Le regole di interazione sono regole aggiuntive per gestire situazioni più specifiche o complesse. È possibile aggiungere altre convalide o configurare funzionalità avanzate, ad esempio conferme o correzioni in un solo passaggio. È anche possibile creare regole di interazione personalizzate.

## <a name="parameters-configuration"></a>Configurazione dei parametri

I parametri sono le informazioni richieste dai comandi per completare un'attività. In scenari complessi, i parametri possono essere usati anche per definire le condizioni che attivano azioni personalizzate.

### <a name="name"></a>Nome
Un parametro è identificato dalla proprietà Name. È sempre necessario assegnare un nome descrittivo a un parametro. È possibile fare riferimento a un parametro in sezioni diverse, ad esempio quando si costruiscono condizioni, risposte vocali o altre azioni.
 
### <a name="isglobal"></a>IsGlobal
Questa casella di controllo indica se l'ambito di questo parametro è condiviso tra tutti i comandi dell'applicazione. Se un parametro è globale, il suo valore può essere potenzialmente fornito da qualsiasi ambito di comando. Una volta assegnato, un valore può essere definito da uno qualsiasi dei comandi. 

### <a name="required"></a>Necessario
Questa casella di controllo indica se un valore per questo parametro è necessario per l'evasione o il completamento del comando. È necessario configurare le risposte per richiedere all'utente di fornire un valore se un parametro è contrassegnato come obbligatorio.

Si noti che, se è stato configurato un **parametro obbligatorio** per avere un **valore predefinito** , il sistema richiederà comunque esplicitamente il valore del parametro.

### <a name="type"></a>Type
I comandi personalizzati supportano i tipi di parametro seguenti:

* Datetime
* Area geografica
* Numero
* string

Tutti questi tipi di parametro ad eccezione di geography, supportano la configurazione del valore predefinito, che è possibile configurare dal portale.

### <a name="configuration"></a>Configurazione
Configuration è una proprietà di parametro definita solo per la stringa di tipo. Sono supportati i valori seguenti:

* **Nessuno**.
* **Accetta input completo** : se abilitata, un parametro accetta qualsiasi espressione di input. Questa opzione è utile quando l'utente necessita di un parametro con l'espressione completa. Un esempio è quello degli indirizzi postali.
* **Accetta valori di input predefiniti da un catalogo esterno** : questo valore viene usato per configurare un parametro che può assumere un'ampia gamma di valori. Un esempio è un catalogo delle vendite. In questo caso, il catalogo è ospitato in un endpoint Web esterno e può essere configurato in modo indipendente.
* **Accetta valori di input predefiniti da catalogo interno** : questo valore viene usato per configurare un parametro che può assumere alcuni valori. In questo caso, i valori devono essere configurati in speech studio.


### <a name="validation"></a>Convalida
Le convalide sono costrutti applicabili a determinati tipi di parametro che consentono di configurare vincoli sul valore di un parametro. Attualmente, i comandi personalizzati supportano le convalide sui tipi di parametro seguenti:

* Datetime
* Numero

## <a name="rules-configuration"></a>Configurazione delle regole
Una regola nei comandi personalizzati è definita da un set di *condizioni* che, quando soddisfatte, eseguono un set di *azioni*. Le regole consentono inoltre di configurare *lo stato di post-esecuzione* e le *aspettative* per la prossima volta.

### <a name="types"></a>Tipi
I comandi personalizzati supportano le categorie di regole seguenti:

* **Regole di completamento** : queste regole devono essere eseguite all'evasione dei comandi. Verranno eseguite tutte le regole configurate in questa sezione per le quali vengono soddisfatte le condizioni. 
* **Regole di interazione** : queste regole possono essere usate per configurare convalide personalizzate aggiuntive, conferme e una correzione in un solo passaggio oppure per eseguire qualsiasi altra logica personalizzata della finestra di dialogo. Le regole di interazione vengono valutate a ogni turno nell'elaborazione e possono essere usate per attivare le regole di completamento.

Le diverse azioni configurate come parte di una regola vengono eseguite nell'ordine in cui sono visualizzate nel portale di creazione.

### <a name="conditions"></a>Condizioni
Le condizioni sono i requisiti che devono essere soddisfatti per l'esecuzione di una regola. Le condizioni delle regole possono essere dei tipi seguenti:

* Il **valore del parametro è uguale** a: il valore del parametro configurato è uguale a un valore specifico.
* **Nessun valore di parametro** : i parametri configurati non devono avere alcun valore.
* **Parametri obbligatori** : il parametro configurato ha un valore.
* **Tutti i parametri obbligatori** : tutti i parametri contrassegnati come necessari hanno un valore.
* **Parametri aggiornati** : uno o più valori di parametro sono stati aggiornati in seguito all'elaborazione dell'input corrente (espressione o attività).
* **Conferma riuscita** : l'espressione o l'attività di input è stata confermata correttamente (Sì).
* **Conferma negata** : l'espressione o l'attività di input non è stata confermata correttamente (no).
* È **necessario aggiornare il comando precedente** : questa condizione viene utilizzata nelle istanze di quando si desidera rilevare una conferma negata insieme a un aggiornamento. Dietro le quinte, questa condizione viene configurata per quando il motore di dialogo rileva una conferma negativa in cui lo scopo è identico a quello precedente e l'utente ha risposto con un aggiornamento.

### <a name="actions"></a>Azioni
* **Invia risposta vocale** : Invia una risposta vocale al client.
* **Aggiorna valore parametro** : consente di aggiornare il valore di un parametro di comando a un valore specificato.
* **Cancella valore parametro** : Cancella il valore del parametro del comando.
* **Call Web endpoint** : effettuare una chiamata a un endpoint Web.
* **Invia attività al client** : Invia un'attività personalizzata al client.

### <a name="expectations"></a>Aspettative
Le aspettative vengono usate per configurare gli hint per l'elaborazione del successivo input dell'utente. Sono supportati i tipi seguenti:

* **Prevista conferma da parte dell'utente** : questa previsione specifica che l'applicazione è prevista una conferma (sì/no) per l'input dell'utente successivo.
* **Previsto input/i parametro dall'utente** : questa previsione specifica uno o più parametri di comando che l'applicazione prevede dall'input dell'utente.

### <a name="post-execution-state"></a>Stato di post-esecuzione
Lo stato di post-esecuzione è lo stato della finestra di dialogo dopo l'elaborazione dell'input corrente (espressione o attività). È dei seguenti tipi:

* **Mantieni stato corrente** : Mantieni solo lo stato corrente.
* **Completare il comando** : completare il comando e non verrà elaborata alcuna regola aggiuntiva del comando.
* **Eseguire le regole di completamento** : eseguire tutte le regole di completamento valide.
* **Attendi l'input dell'utente** : attendere il successivo input dell'utente.



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Vedere gli esempi in GitHub](https://aka.ms/speech/cc-samples)
