---
title: Come aggiornare un'assegnazione di progetto di Azure esistente
description: Informazioni sul meccanismo per l'aggiornamento di un'assegnazione esistente in Azure Blueprint.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 2c9f660e54da50e32ce1d0dc43b0efeacd643c57
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093786"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Come aggiornare un'assegnazione di progetto esistente

Quando viene assegnato un progetto, l'assegnazione può essere aggiornata. Esistono diversi motivi per aggiornare un'assegnazione esistente, tra cui:

- Aggiungere o rimuovere il [blocco delle risorse](../concepts/resource-locking.md)
- Modificare il valore dei [parametri dinamici](../concepts/parameters.md#dynamic-parameters)
- Aggiornare l'assegnazione a una versione **pubblicata** più recente del progetto

## <a name="updating-assignments"></a>Aggiornamento delle assegnazioni

1. Fare clic su **Tutti i servizi** e quindi cercare e selezionare **Criteri** nel riquadro sinistro. Nella pagina **Criteri** fare clic su **Progetti**.

1. Selezionare **Progetti assegnati** nella pagina a sinistra.

1. Nell'elenco dei progetti fare clic sull'assegnazione del progetto e quindi fare clic sul pulsante **Aggiorna assegnazione** oppure fare clic con il pulsante destro del mouse sull'assegnazione del progetto e scegliere **Aggiorna assegnazione**.

   ![Aggiorna assegnazione](../media/update-existing-assignments/update-assignment.png)

1. La pagina **Assegna progetto** verrà caricata precompilata con tutti i valori dall'assegnazione originale. È possibile modificare la **versione della definizione di progetto**, lo stato di **Blocca assegnazione** e i parametri dinamici presenti nella definizione del progetto. Al termine delle modifiche, fare clic su **Assegna**.

1. Nella pagina dei dettagli dell'assegnazione aggiornata, vedere il nuovo stato. In questo esempio, è stato aggiunto il parametro **Blocco** all'assegnazione.

   ![Assegnazione aggiornata - bloccata](../media/update-existing-assignments/updated-assignment.png)

1. Vedere i dettagli delle altre operazioni di assegnazione usando l'elenco a discesa **Operazione di assegnazione**. La tabella **Risorse gestite** viene aggiornata in base all'operazione di assegnazione selezionata.

   ![Operazioni di assegnazione](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Regole per l'aggiornamento delle assegnazioni

La distribuzione delle assegnazioni aggiornate segue alcune regole importanti. Queste regole definiscono che cosa accade alle risorse già distribuite. La modifica richiesta e il tipo di risorsa dell'artefatto distribuito o aggiornato determinano le azioni che vengono eseguite.

- Assegnazioni di ruoli
  - Se cambia il ruolo o l'assegnatario del ruolo (utente, gruppo o app), viene creata una nuova assegnazione di ruolo. Le assegnazioni di ruolo distribuite in precedenza vengono mantenute.
- Assegnazioni di criteri
  - Se vengono modificati i parametri dell'assegnazione dei criteri, l'assegnazione esistente viene aggiornata.
  - Se viene modificata la definizione dell'assegnazione dei criteri, viene creata una nuova assegnazione di criteri. Le assegnazioni di criteri distribuite in precedenza vengono mantenute.
  - Se l'artefatto dell'assegnazione dei criteri viene rimosso dal progetto, l'assegnazione di criteri distribuita viene mantenuta.
- Modelli di Gestione risorse di Azure
  - Il modello viene elaborato tramite Resource Manager come **PUT**. Dato che ogni tipo di risorsa gestisce questo tipo di azione in modo diverso, vedere la documentazione per ogni risorsa inclusa per determinare l'impatto di questa azione quando viene eseguita dai progetti.

## <a name="possible-errors-on-updating-assignments"></a>Possibili errori per l'aggiornamento delle assegnazioni

Quando si aggiornano le assegnazioni, è possibile apportare modifiche che causano errori durante l'esecuzione. Un esempio è la modifica della posizione di un gruppo di risorse dopo che è già stato distribuito. È possibile apportare qualsiasi modifica supportata da [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md), ma qualsiasi modifica che causerebbe un errore in Azure Resource Manager comporterà anche l'esito negativo dell'assegnazione.

Non è previsto alcun limite al numero di volte che è possibile aggiornare un'assegnazione. Se si verifica un errore, determinare l'errore ed eseguire un altro aggiornamento per l'assegnazione.  Esempi di scenari di errore:

- Un parametro non valido
- Un oggetto già esistente
- Una modifica non supportata da Azure Resource Manager

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md)
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md)
- Imparare a personalizzare l'[ordine in sequenza del progetto](../concepts/sequencing-order.md)
- Scoprire come usare il [blocco delle risorse del progetto](../concepts/resource-locking.md)
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md)
