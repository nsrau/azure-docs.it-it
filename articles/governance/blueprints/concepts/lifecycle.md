---
title: Informazioni sul ciclo di vita di un progetto
description: Informazioni sulle fasi del ciclo di vita di un progetto e sui dettagli di ognuna.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/30/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 6112f6f2834f02cec970af886360844c5314150d
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678996"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Comprendere il ciclo di vita di un progetto di Azure

Come molte risorse all'interno di Azure, un progetto di Azure Blueprint ha un ciclo di vita tipico e naturale. Sono creati, distribuiti e infine eliminati quando non sono più necessari o pertinenti.
Blueprints supporta le operazioni del ciclo di vita standard. In seguito compila su di esse per fornire livelli di stato aggiuntivi che supportano pipeline comuni di integrazione e distribuzione continua per le organizzazioni che ne gestiscono l'infrastruttura come Codice, un elemento fondamentale in DevOps.

Per comprendere appieno i progetti e le relative fasi, illustriamo un ciclo di vita standard:

> [!div class="checklist"]
> - Creazione e modifica di un progetto
> - Pubblicazione del progetto
> - Creazione e modifica di una nuova versione del progetto
> - Pubblicazione di una nuova versione del progetto
> - Eliminazione di una versione specifica del progetto
> - Eliminazione del progetto

## <a name="creating-and-editing-a-blueprint"></a>Creazione e modifica di un progetto

Quando si crea un progetto, aggiungere artefatti, salvarli in un gruppo di gestione o in una sottoscrizione e fornire un nome univoco e una versione univoca. Il progetto è ora in modalità **Bozza** e non può ancora essere assegnato. Nella modalità **Bozza** può continuare a essere aggiornato e modificato.

Un progetto mai pubblicato in modalità **Bozza** viene visualizzato con un'icona diversa nella pagina **Definizioni di progetto** rispetto a quelli che sono stati **Pubblicati**. La **versione più recente** viene visualizzata come **bozza** per questi progetti mai pubblicati.

Creare e modificare un progetto tramite il [portale di Azure](../create-blueprint-portal.md#create-a-blueprint) o [API REST](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Pubblicazione di un progetto

Una volta apportate le modifiche pianificate a un progetto in modalità **Bozza**, può essere **Pubblicato** e reso disponibile per l'assegnazione. Il progetto **Pubblicato** non può essere modificato.
Una volta **Pubblicato**, il progetto è visualizzato con un'icona diversa rispetto ai progetti in **Bozza**. Inoltre, comparirà il numero della versione, specificato nella colonna **Versione più recente**.

Pubblicare un progetto tramite il [portale di Azure](../create-blueprint-portal.md#publish-a-blueprint) o [API REST](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Creazione e modifica di una nuova versione del progetto

Una versione **Pubblicata** di un progetto non può essere modificata. Tuttavia, è possibile aggiungere una nuova versione del progetto a quello esistente e modificarla in base alle esigenze. Apportare modifiche a un progetto esistente modificandolo. Al salvataggio delle nuove modifiche, il progetto includerà le **Modifiche non pubblicate**. Queste modifiche sono una nuova versione di **Bozza** del progetto.

Modificare un progetto nel [portale di Azure](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Pubblicazione di una nuova versione del progetto

Ogni versione modificata di un progetto deve essere **Pubblicata** prima di poter essere assegnata. Quando le **Modifiche non pubblicate** sono state apportate a un progetto ma non **Pubblicate**, il pulsante**Pubblica progetto** è disponibile nella pagina di modifica del progetto. Se il pulsante non è visibile, il progetto è già stato **Pubblicato** e non presenta **Modifiche non pubblicate**.

> [!NOTE]
> Un progetto singolo può avere diverse versioni **Pubblicate** assegnate alle sottoscrizioni.

Per pubblicare un progetto con **Modifiche non pubblicate**, usare la stessa procedura per la pubblicazione di un nuovo progetto.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Eliminazione di una versione specifica del progetto

Ogni versione di un progetto è un oggetto univoco e può essere **Pubblicato** individualmente. In quanto tale, ogni versione di un progetto può essere eliminata. L'eliminazione di una versione di un progetto non ha alcun impatto sulle altre versioni di quel progetto.

> [!NOTE]
> Non è possibile eliminare un progetto che ha assegnazioni attive. Eliminare prima le assegnazioni, quindi eliminare la versione che si desidera rimuovere.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare **definizioni progetto** nella pagina a sinistra e usare le opzioni di filtro per individuare il progetto di cui si vuole eliminare una versione. Fare clic su di esso per aprire la pagina di modifica.

1. Scegliere la scheda **Versioni pubblicate** e individuare la versione da eliminare.

1. Fare clic con il pulsante destro del mouse sulla versione da eliminare e selezionare **Elimina la versione**.

## <a name="deleting-the-blueprint"></a>Eliminazione del progetto

Anche il progetto principale può essere eliminato. Eliminando il progetto principale vengono eliminate anche tutte le versioni di quel progetto, inclusi i progetti in **Bozza** oppure **Pubblicati**. Come accade con l'eliminazione di una versione di un progetto, l'eliminazione di un progetto principale non elimina le assegnazioni esistenti di ogni versione del progetto.

> [!NOTE]
> Non è possibile eliminare un progetto che ha assegnazioni attive. Eliminare prima le assegnazioni, quindi eliminare la versione che si desidera rimuovere.

Eliminare un progetto tramite il [portale di Azure](../create-blueprint-portal.md#delete-a-blueprint) o con [API REST](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Assegnazioni

Esistono diversi punti durante il ciclo di vita in cui un progetto può essere assegnato a una sottoscrizione. Quando la modalità di una versione del progetto è sullo stato **Pubblicato**, tale versione può essere assegnata a una sottoscrizione. Questo ciclo di vita consente di usare e assegnare attivamente le versioni di un progetto mentre viene sviluppata una versione più recente.

Man mano che le versioni dei progetti sono assegnate, è importante comprendere dove e con quali parametri. I parametri possono essere statici o dinamici. Per altre informazioni, vedere [parametri statici e dinamici](parameters.md).

### <a name="updating-assignments"></a>Aggiornamento delle assegnazioni

Quando viene assegnato un progetto, l'assegnazione può essere aggiornata. Esistono diversi motivi per aggiornare un'assegnazione esistente, tra cui:

- Aggiungere o rimuovere il [blocco delle risorse](resource-locking.md)
- Modificare il valore dei [parametri dinamici](parameters.md#dynamic-parameters)
- Aggiornare l'assegnazione a una versione più recente **Pubblicata** del progetto

Per informazioni, vedere [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).

### <a name="unassigning-assignments"></a>Annullare l'assegnazione di assegnazioni

Se il progetto non è più necessario, è possibile che non sia assegnato dal gruppo di gestione o dalla sottoscrizione. Durante l'unassegnazione del progetto, si verifica quanto segue:

- Rimozione del [blocco delle risorse del progetto](resource-locking.md)
- Eliminazione dell'oggetto di assegnazione progetto
- Condizionale Se è stata usata un' **identità gestita assegnata dal sistema** , viene eliminata anche

> [!NOTE]
> Tutte le risorse distribuite dall'assegnazione progetto rimangono invariate, ma non sono più protette dai progetti di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare [parametri statici e dinamici](parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).