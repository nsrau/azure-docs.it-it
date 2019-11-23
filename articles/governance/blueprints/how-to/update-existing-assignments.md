---
title: Update an existing assignment from the portal
description: Learn about the mechanism for updating an existing blueprint assignment from the portal in Azure Blueprints.
ms.date: 11/21/2019
ms.topic: conceptual
ms.openlocfilehash: b4cf03d88103b85bc00dbd815816ead2740f2093
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406387"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Come aggiornare un'assegnazione di progetto esistente

Quando viene assegnato un progetto, l'assegnazione può essere aggiornata. Esistono diversi motivi per aggiornare un'assegnazione esistente, tra cui:

- Aggiungere o rimuovere il [blocco delle risorse](../concepts/resource-locking.md)
- Modificare il valore dei [parametri dinamici](../concepts/parameters.md#dynamic-parameters)
- Aggiornare l'assegnazione a una versione più recente **Pubblicata** del progetto

## <a name="updating-assignments"></a>Aggiornamento delle assegnazioni

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare **Progetti assegnati** nella pagina a sinistra.

1. Nell'elenco dei progetti fare clic sull'assegnazione del progetto Then click the **Update assignment** button OR right-click the blueprint assignment and select **Update assignment**.

   ![Update an existing blueprint assignment](../media/update-existing-assignments/update-assignment.png)

1. The **Assign blueprint** page will load pre-filled with all values from the original assignment.
   È possibile modificare la **versione della definizione di progetto**, lo stato di **Blocca assegnazione** e i parametri dinamici presenti nella definizione del progetto. Al termine delle modifiche, fare clic su **Assegna**.

1. Nella pagina dei dettagli dell'assegnazione aggiornata, vedere il nuovo stato. In questo esempio, è stato aggiunto il parametro **Blocco** all'assegnazione.

   ![Updated an existing blueprint assignment - lock mode changed](../media/update-existing-assignments/updated-assignment.png)

1. Explore details about other **Assignment operations** using the drop-down. The table of **Managed resources** updates by selected assignment operation.

   ![Assignment operations of a blueprint assignment](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Regole per l'aggiornamento delle assegnazioni

La distribuzione delle assegnazioni aggiornate segue alcune regole importanti. Queste regole definiscono che cosa accade alle risorse già distribuite. La modifica richiesta e il tipo di risorsa dell'artefatto distribuito o aggiornato determinano le azioni che vengono eseguite.

- Assegnazioni di ruoli
  - Se cambia il ruolo o l'assegnatario del ruolo (utente, gruppo o app), viene creata una nuova assegnazione di ruolo. Le assegnazioni di ruolo distribuite in precedenza vengono mantenute.
- Assegnazioni di criteri
  - Se vengono modificati i parametri dell'assegnazione dei criteri, l'assegnazione esistente viene aggiornata.
  - Se viene modificata la definizione dell'assegnazione dei criteri, viene creata una nuova assegnazione di criteri.
    Le assegnazioni di criteri distribuite in precedenza vengono mantenute.
  - Se l'artefatto dell'assegnazione dei criteri viene rimosso dal progetto, l'assegnazione di criteri distribuita viene mantenuta.
- Modelli di Azure Resource Manager
  - Il modello viene elaborato tramite Resource Manager come **PUT**. Dato che ogni tipo di risorsa gestisce questo tipo di azione in modo diverso, vedere la documentazione per ogni risorsa inclusa per determinare l'impatto di questa azione quando viene eseguita dai progetti.

## <a name="possible-errors-on-updating-assignments"></a>Possibili errori per l'aggiornamento delle assegnazioni

Quando si aggiornano le assegnazioni, è possibile apportare modifiche che causano errori durante l'esecuzione. Un esempio è la modifica della posizione di un gruppo di risorse dopo che è già stato distribuito. È possibile apportare qualsiasi modifica supportata da [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md), ma qualsiasi modifica che causerebbe un errore in Azure Resource Manager comporterà anche l'esito negativo dell'assegnazione.

Non è previsto alcun limite al numero di volte che è possibile aggiornare un'assegnazione. Se si verifica un errore, determinare l'errore ed eseguire un altro aggiornamento per l'assegnazione.  Esempi di scenari di errore:

- Un parametro non valido
- Un oggetto già esistente
- Una modifica non supportata da Azure Resource Manager

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).