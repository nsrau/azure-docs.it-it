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
ms.openlocfilehash: 5a5e3590e0e184633760f080dfd3402ed75bd48e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307675"
---
# <a name="custom-commands-concepts-and-definitions"></a>Concetti e definizioni dei comandi personalizzati

Questo articolo funge da riferimento per i concetti e le definizioni per le applicazioni di comandi personalizzati.

## <a name="commands-configuration"></a>Configurazione comandi
I comandi sono i blocchi predefiniti di base di un'applicazione comandi personalizzata. Un comando è un set di configurazioni necessarie per completare un'attività specifica definita da un utente.

### <a name="example-sentences"></a>Frasi di esempio
Le espressioni di esempio sono gli esempi di set che l'utente può impostare per attivare un determinato comando. Si noti che è necessario fornire solo un campione di espressioni e non un elenco esaustivo. 

### <a name="parameters"></a>Parametri
I parametri sono le informazioni richieste dai comandi per completare un'attività. In scenari complessi, i parametri possono essere usati anche per definire le condizioni che attivano azioni personalizzate.

### <a name="completion-rules"></a>Regole di completamento
Serie di regole da eseguire quando il comando è pronto per essere completato, ad esempio quando vengono soddisfatte tutte le condizioni delle regole.

### <a name="interaction-rules"></a>Regole di interazione
Regole aggiuntive per gestire situazioni più specifiche o complesse. È possibile aggiungere altre convalide o configurare funzionalità avanzate, ad esempio conferme o correzioni in un solo passaggio. È anche possibile creare regole di interazione personalizzate.

## <a name="parameters-configuration"></a>Configurazione dei parametri

I parametri sono le informazioni richieste dai comandi per completare un'attività. In scenari complessi, i parametri possono essere usati anche per definire le condizioni che attivano azioni personalizzate.

### <a name="name"></a>Nome
Un parametro è identificato dalla proprietà Name. È sempre necessario assegnare un nome descrittivo a un parametro. È possibile fare riferimento a un parametro in sezioni diverse, ad esempio, durante la costruzione di condizioni, risposte vocali o altre azioni.
 
### <a name="isglobal"></a>IsGlobal
Casella di controllo che indica se l'ambito di questo parametro è condiviso tra tutti i comandi nell'applicazione. Se un parametro è globale, il suo valore può essere potenzialmente fornito da qualsiasi ambito del comando e, una volta assegnato un valore, può essere fatto riferimento da uno qualsiasi dei comandi. 

### <a name="required"></a>Necessario
Casella di controllo che indica se un valore per questo parametro è necessario per l'evasione o il completamento del comando. È necessario configurare le risposte per richiedere all'utente di fornire valore se un parametro è contrassegnato come obbligatorio.

### <a name="type"></a>Type
I comandi personalizzati supportano i tipi di parametri seguenti:


* Datetime
* Area geografica
* Number
* string

Tutti questi tipi di parametro supportano la configurazione dei valori predefiniti che è possibile configurare dal portale.

### <a name="configuration"></a>Configurazione
La configurazione è una proprietà di parametro definita solo per Type: String. Di seguito sono riportati i valori supportati
* nessuno
* Accetta input completo: se si abilita questa opzione, il parametro accetta qualsiasi espressione di input. questa operazione è utile quando l'utente necessita di un parametro con l'espressione completa. ad esempio indirizzi postali.
* Accetta valori di input predefiniti da catalogo esterno: usato per configurare il parametro che può assumere un'ampia gamma di valori. ad esempio catalogo vendite. In questo caso il catalogo è ospitato in un endpoint Web esterno e può essere configurato in modo indipendente.
* Accetta valori di input predefiniti da catalogo interno: usato per configurare il parametro che può assumere alcuni valori. In questo caso, i valori devono essere configurati in speech studio.


### <a name="validation"></a>Convalida
Le convalide sono costrutti applicabili a determinati tipi di parametro che consentono di configurare i vincoli sul valore del parametro. Attualmente, i comandi personalizzati supportano le convalide sui tipi di parametri seguenti:
* Datetime
* Number

## <a name="rules-configuration"></a>Configurazione delle regole
Una regola nei comandi personalizzati è definita da un set di **condizioni**, che, se soddisfatte, eseguirà un set di **azioni**. La regola consente inoltre di configurare il relativo **stato post-esecuzione** e le **aspettative** per il successivo turno.

### <a name="types"></a>Tipi
I comandi personalizzati supportano le categorie di regole seguenti:
* Regole di completamento

    Elenco di regole da eseguire all'evasione del comando. Verranno eseguite tutte le regole configurate in questa sezione per le quali vengono soddisfatte le condizioni.
    
* Regole di interazione

    È possibile utilizzare le regole di interazione per configurare convalide personalizzate aggiuntive, conferme, correzioni in un solo passaggio o per per eseguire qualsiasi altra logica di dialogo personalizzata. Queste regole vengono valutate a ogni elaborazione a turni e possono essere usate per attivare le regole di completamento.

Le diverse azioni configurate come parte di una regola vengono eseguite nell'ordine in cui sono visualizzate nel portale di creazione.
    
### <a name="conditions"></a>Condizioni
Set di condizioni che devono essere soddisfatte per l'esecuzione di una regola. La condizione delle regole può essere dei seguenti tipi:
* Il valore del parametro è uguale a: il valore del parametro configurato è uguale a un valore specifico.
* Nessun valore di parametro: i parametri configurati non devono avere alcun valore.
* Parametri obbligatori: il parametro configurato ha un valore.
* Tutti i parametri obbligatori: tutti i parametri che sono stati contrassegnati come necessari hanno un valore.
* Parametri aggiornati: uno o più valori di parametro sono stati aggiornati in seguito all'elaborazione dell'input corrente (espressione/attività).
* La conferma è stata completata: l'espressione di input/attività è stata confermata correttamente (Sì).
* La conferma è stata negata: la conferma dell'input/attività è stata completata (no).
* È necessario aggiornare il comando precedente: questa condizione viene utilizzata nelle istanze di quando si desidera rilevare una conferma negata insieme a un aggiornamento. Dietro le quinte, questa configurazione viene configurata per quando il motore di dialogo rileva una conferma negativa in cui lo scopo è identico a quello precedente e l'utente ha risposto con un aggiornamento.

### <a name="actions"></a>Azioni
* Invia risposta vocale: Invia una risposta vocale al client.
* Aggiorna valore parametro: aggiorna il valore di un parametro di comando a un valore specificato.
* Cancellare il valore del parametro del comando.
* Chiama endpoint Web: effettua una chiamata a un endpoint Web.
* Invia attività al client: Invia un'attività personalizzata al client.

### <a name="expectations"></a>Aspettative
Le aspettative vengono usate per configurare gli hint per l'elaborazione dell'input utente successivo. Sono supportati i tipi seguenti:
* Prevista conferma da parte dell'utente: indica che l'applicazione è prevista una conferma (sì/no) per l'input dell'utente successivo.
* Previsto input/i parametro dall'utente: specificare uno o più parametri di comando che l'applicazione prevede dall'input dell'utente.

### <a name="post-execution-state"></a>Stato di post-esecuzione
Stato della finestra di dialogo dopo l'elaborazione dell'input corrente (espressione/attività). È dei seguenti tipi:
* Comando completato: completare il comando. nessuna regola aggiuntiva del comando verrà elaborata.
* Eseguire le regole di completamento: eseguire tutte le regole di completamento valide.
* Attendi l'input dell'utente: attendere il successivo input dell'utente.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Vedere gli esempi in GitHub](https://aka.ms/speech/cc-samples)
