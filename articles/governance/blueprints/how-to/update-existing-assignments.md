---
title: Come aggiornare un'assegnazione di progetto di Azure esistente
description: Informazioni sul meccanismo per l'aggiornamento di un'assegnazione esistente in Azure Blueprint.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: ecac0fb21a6691874d5e8db49eadd7114d41845f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956201"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Come aggiornare un'assegnazione di progetto esistente

Quando viene assegnato un progetto, l'assegnazione può essere aggiornata. Esistono diversi motivi per aggiornare un'assegnazione esistente, tra cui:

- Aggiungere o rimuovere il [blocco delle risorse](../concepts/resource-locking.md)
- Modificare il valore dei [parametri dinamici](../concepts/parameters.md#dynamic-parameters)
- Aggiornare l'assegnazione a una versione **pubblicata** più recente del progetto

## <a name="updating-assignments"></a>Aggiornamento delle assegnazioni

1. Avviare il servizio Azure Blueprint nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri** nel riquadro a sinistra. Nella pagina **Criteri** fare clic su **Progetti**.

1. Selezionare **Progetti assegnati** nella pagina a sinistra.

1. Nell'elenco dei progetti fare clic sull'assegnazione del progetto e quindi fare clic sul pulsante **Aggiorna assegnazione** oppure fare clic con il pulsante destro del mouse sull'assegnazione del progetto e scegliere **Aggiorna assegnazione**.

   ![Aggiorna assegnazione](../media/update-existing-assignments/update-assignment.png)

1. La pagina **Assegna progetto** verrà caricata precompilata con tutti i valori dall'assegnazione originale. È possibile modificare la **versione della definizione di progetto**, lo stato di **Blocca assegnazione** e i parametri dinamici presenti nella definizione del progetto. Al termine delle modifiche, fare clic su **Assegna**.

1. Nella pagina dei dettagli dell'assegnazione aggiornata, vedere il nuovo stato. In questo esempio, è stato aggiunto il parametro **Blocco** all'assegnazione.

   ![Assegnazione aggiornata - bloccata](../media/update-existing-assignments/updated-assignment.png)

1. Vedere i dettagli delle altre operazioni di assegnazione usando l'elenco a discesa **Operazione di assegnazione**. La tabella **Risorse gestite** viene aggiornata in base all'operazione di assegnazione selezionata.

   ![Operazioni di assegnazione](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Regole per l'aggiornamento delle assegnazioni

La distribuzione delle assegnazioni aggiornate segue alcune regole importanti. Queste regole determinano cosa accade a una risorsa esistente a seconda della modifica richiesta e il tipo di risorsa artefatto da distribuire o aggiornare.

- Assegnazioni di ruoli
  - Se cambia il ruolo o l'assegnatario del ruolo (utente, gruppo o app), viene creata una nuova assegnazione di ruolo. L'assegnazione di ruolo distribuita in precedenza viene mantenuta.
- Assegnazioni di criteri
  - Se vengono modificati i parametri dell'assegnazione dei criteri, l'assegnazione esistente viene aggiornata.
  - Se viene modificata la definizione dell'assegnazione dei criteri, viene creata una nuova assegnazione di criteri. L'assegnazione di criteri distribuita in precedenza viene mantenuta.
  - Se l'artefatto dell'assegnazione dei criteri viene rimosso dal progetto, l'assegnazione di criteri distribuita in precedenza viene mantenuta.
- Modelli di Gestione risorse di Azure
  - Il modello viene elaborato tramite Resource Manager come **PUT**. Dato che ogni tipo di risorsa gestisce questo tipo di operazione in modo diverso, vedere la documentazione per ogni risorsa inclusa per determinare l'impatto di questa azione quando viene eseguita dai progetti.

## <a name="possible-errors-on-updating-assignments"></a>Possibili errori per l'aggiornamento delle assegnazioni

Quando si aggiornano le assegnazioni, è possibile apportare modifiche che causano errori durante l'esecuzione. Un esempio è la modifica della posizione di un gruppo di risorse dopo che è già stato distribuito. È possibile apportare qualsiasi modifica supportata da [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md), ma qualsiasi modifica che causerebbe un errore in Azure Resource Manager comporterà anche l'esito negativo dell'assegnazione.

Non è previsto alcun limite al numero di volte che è possibile aggiornare un'assegnazione. Di conseguenza, se si verifica un errore, a causa di un parametro non valido, di un oggetto già esistente o di una modifica non consentita da Azure Resource Manager, determinare l'errore e aggiornare di nuovo l'assegnazione.

## <a name="next-steps"></a>Passaggi successivi

- Acquisire informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md)
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md)
- Imparare a personalizzare l'[ordine in sequenza del progetto](../concepts/sequencing-order.md)
- Scoprire come usare il [blocco delle risorse del progetto](../concepts/resource-locking.md)
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md)
