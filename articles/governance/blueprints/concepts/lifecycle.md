---
title: Comprendere il ciclo di vita di Azure Blueprint
description: Informazioni sulle fasi del ciclo di vita di un progetto e sui dettagli di ognuna.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e0790168a8b9590aaa440a04cd99f26c2ece2818
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991540"
---
# <a name="understand-the-life-cycle-of-an-azure-blueprint"></a>Comprendere il ciclo di vita di Azure Blueprint

Come molte risorse all'interno di Azure, un progetto di Azure Blueprint ha un ciclo di vita tipico e naturale. Sono creati, distribuiti e infine eliminati quando non sono più necessari o pertinenti.
Blueprints supporta le tradizionali operazioni del ciclo di vita CRUD (creare/leggere/aggiornare/eliminare), ma sviluppa anche su di esse, in modo da fornire livelli aggiuntivi di stato che supportano le pipeline di integrazione/distribuzione continua (CI/CD) per essere usate da chi gestisce l'infrastruttura tramite codice: un elemento chiave di DevOps detto Infrastruttura come codice (IaC).

Per informazioni complete sui progetti e le fasi, illustriamo un ciclo di vita standard:

> [!div class="checklist"]
> - Creazione e modifica di un progetto
> - Pubblicazione del progetto
> - Creazione e modifica di una nuova versione del progetto
> - Pubblicazione di una nuova versione del progetto
> - Eliminazione di una versione specifica del progetto
> - Eliminazione del progetto

## <a name="creating-and-editing-a-blueprint"></a>Creazione e modifica di un progetto

Quando si crea un progetto, aggiungere artefatti, salvarli in un gruppo di gestione e fornire un nome univoco e una versione univoca. A questo punto, il progetto è in modalità **Bozza** e non può ancora essere assegnato. Tuttavia, nella modalità **Bozza** può continuare a essere aggiornato e modificato.

Un progetto in modalità **Bozza** che non è mai stato pubblicato sarà visualizzato con un'icona diversa nella pagina **Definizioni di progetto** rispetto a quelli che sono stati **Pubblicati**. La **versione più recente** verrà inoltre visualizzata come **Bozza** per i progetti mai pubblicati.

Creare e modificare un progetto tramite il [portale di Azure](../create-blueprint-portal.md#create-a-blueprint) o [API REST](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Pubblicazione di un progetto

Una volta apportate le modifiche desiderate a un progetto in modalità **Bozza**, può essere **Pubblicato** e reso disponibile per l'assegnazione. Il progetto **Pubblicato** non può essere modificato.
Una volta **Pubblicato**, il progetto è visualizzato con un'icona diversa rispetto ai progetti in **Bozza**. Inoltre, comparirà il numero della versione, specificato nella colonna **Versione più recente**.

Pubblicare un progetto tramite il [portale di Azure](../create-blueprint-portal.md#publish-a-blueprint) o [API REST](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Creazione e modifica di una nuova versione del progetto

Anche se un progetto **Pubblicato** non può essere modificato, è possibile aggiungere una nuova versione al progetto esistente e modificarla in base alle esigenze. Questa operazione viene eseguita apportando modifiche a un progetto esistente. Se il progetto è stato già **Pubblicato**, quando questi cambiamenti vengono salvati, tali modifiche sono mostrate come **Modifiche non pubblicate** nell'elenco delle specifiche di progetto. Il salvataggio delle modifiche consente di salvare una versione di **Bozza** del progetto.

Modificare un progetto nel [portale di Azure](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Pubblicazione di una nuova versione del progetto

Così come la prima versione di un progetto è stata **Pubblicata** per essere assegnata, ogni versione successiva dello stesso progetto deve essere **Pubblicata** prima di poter essere assegnata. Quando le **Modifiche non pubblicate** sono state apportate a un progetto, ma non sono ancora state **Pubblicate**, il pulsante**Pubblica progetto** è disponibile nella pagina di modifica del progetto. Se il pulsante non è visibile, il progetto è già stato **Pubblicato**, ma non presenta **Modifiche non pubblicate**.

> [!NOTE]
> Un progetto singolo può avere diverse versioni **Pubblicate** assegnate alle sottoscrizioni.

I passaggi per pubblicare un progetto con **Modifiche non pubblicate** come nuova versione di un progetto esistente sono identici a quelli usati per pubblicare un nuovo progetto.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Eliminazione di una versione specifica del progetto

Ogni versione di un progetto è un oggetto univoco e può essere **Pubblicato** individualmente. Questo significa anche che ogni versione di un progetto può essere eliminata. L'eliminazione di una versione di un progetto non ha alcun impatto sulle altre versioni di quel progetto.

> [!NOTE]
> Non è possibile eliminare un progetto che ha assegnazioni attive. Eliminare prima le assegnazioni, quindi eliminare la versione che si desidera rimuovere.

1. Avviare il servizio Azure Blueprint nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercare e selezionare **Criteri** nel riquadro a sinistra. Nella pagina **Criteri** fare clic su **Progetti**.

1. Selezionare **Definizioni di progetto** dalla pagina a sinistra e usare le opzioni di filtro per individuare il progetto di cui si desidera eliminare una versione. Fare clic su di esso per aprire la pagina di modifica.

1. Scegliere la scheda **Versioni pubblicate** e individuare la versione da eliminare.

1. Fare doppio clic sulla versione da eliminare e selezionare **Eliminare questa versione**.

## <a name="deleting-the-blueprint"></a>Eliminazione del progetto

Anche il progetto principale può essere eliminato. Eliminando il progetto principale vengono eliminate anche tutte le versioni di quel progetto, indipendentemente dallo stato **Bozza** oppure **Pubblicato**. Come accade con l'eliminazione di una versione di un progetto, l'eliminazione di un progetto principale non elimina le assegnazioni esistenti di ogni versione del progetto.

> [!NOTE]
> Non è possibile eliminare un progetto che ha assegnazioni attive. Eliminare prima le assegnazioni, quindi eliminare la versione che si desidera rimuovere.

Eliminare un progetto tramite il [portale di Azure](../create-blueprint-portal.md#delete-a-blueprint) o con [API REST](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Assegnazioni

Esistono diversi punti durante il ciclo di vita in cui un progetto può essere assegnato a una sottoscrizione.
Ogni volta che la modalità di una versione del progetto è sullo stato **Pubblicato**, tale versione può essere assegnata a una sottoscrizione. Anche quando esiste una versione **Bozza** del progetto, se sono presenti uno o più versioni di progetto in modalità **Pubblicato**, ognuna di queste versioni **Pubblicate** è disponibile per l'assegnazione. Questo consente alle versioni di un progetto di essere usate e assegnate attivamente mentre viene sviluppata una versione più recente.

Man mano che le versioni dei progetti sono assegnate, è importante comprendere dove e con quali parametri. I parametri possono essere statici o dinamici. Per altre informazioni, vedere [parametri statici e dinamici](parameters.md).

### <a name="updating-assignments"></a>Aggiornamento delle assegnazioni

Quando viene assegnato un progetto, l'assegnazione può essere aggiornata. Esistono diversi motivi per aggiornare un'assegnazione esistente, tra cui:

- Aggiungere o rimuovere il [blocco delle risorse](resource-locking.md)
- Modificare il valore dei [parametri dinamici](parameters.md#dynamic-parameters)
- Aggiornare l'assegnazione a una versione più recente **Pubblicata** del progetto

Per informazioni, vedere [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare [parametri statici e dinamici](parameters.md)
- Imparare a personalizzare l'[ordine in sequenza del progetto](sequencing-order.md)
- Scoprire come usare in modo ottimale il [blocco delle risorse del progetto](resource-locking.md)
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md)
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md)