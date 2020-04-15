---
title: Aggiornare un'assegnazione esistente dal portale
description: Informazioni sul meccanismo per l'aggiornamento di un'assegnazione di blueprint esistente dal portale in Azure Blueprints.Learn about the mechanism for updating an existing blueprint assignment from the portal in Azure Blueprints.
ms.date: 04/15/2020
ms.topic: how-to
ms.openlocfilehash: a00a8bcc10b37af576777e3816a794225a3832f7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381801"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Come aggiornare un'assegnazione di progetto esistente

Quando viene assegnato un progetto, l'assegnazione può essere aggiornata. Esistono diversi motivi per aggiornare un'assegnazione esistente, tra cui:

- Aggiungere o rimuovere il [blocco delle risorse](../concepts/resource-locking.md)
- Modificare il valore dei [parametri dinamici](../concepts/parameters.md#dynamic-parameters)
- Aggiornare l'assegnazione a una versione più recente **Pubblicata** del progetto

## <a name="updating-assignments"></a>Aggiornamento delle assegnazioni

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare **Progetti assegnati** nella pagina a sinistra.

1. Nell'elenco dei progetti fare clic sull'assegnazione del progetto Quindi fare clic sul pulsante **Aggiorna assegnazione** OPPURE fare clic con il pulsante destro del mouse sull'assegnazione del blueprint e selezionare **Aggiorna assegnazione**.

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="Aggiornare un'assegnazione di blueprint esistente" border="false":::

1. La pagina **Assegna blueprint** verrà caricata precompilata con tutti i valori dell'assegnazione originale.
   È possibile modificare la **versione della definizione di progetto**, lo stato di **Blocca assegnazione** e i parametri dinamici presenti nella definizione del progetto. Al termine delle modifiche, fare clic su **Assegna**.

1. Nella pagina dei dettagli dell'assegnazione aggiornata, vedere il nuovo stato. In questo esempio, è stato aggiunto il parametro **Blocco** all'assegnazione.

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="Aggiornamento di un'assegnazione di blueprint esistente - modalità di blocco modificata" border="false":::

1. Esplorare i dettagli relativi ad altre operazioni di **assegnazione** utilizzando l'elenco a discesa. La tabella degli aggiornamenti delle **risorse gestite** in base all'operazione di assegnazione selezionata.

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="Operazioni di assegnazione di un'assegnazione di blueprint" border="false":::

## <a name="rules-for-updating-assignments"></a>Regole per l'aggiornamento delle assegnazioni

La distribuzione delle assegnazioni aggiornate segue alcune regole importanti. Queste regole definiscono che cosa accade alle risorse già distribuite. La modifica richiesta e il tipo di risorsa dell'artefatto distribuito o aggiornato determinano le azioni che vengono eseguite.

- Assegnazioni di ruoli
  - Se cambia il ruolo o l'assegnatario del ruolo (utente, gruppo o app), viene creata una nuova assegnazione di ruolo. Le assegnazioni di ruolo distribuite in precedenza vengono mantenute.
- Assegnazioni di criteri
  - Se vengono modificati i parametri dell'assegnazione dei criteri, l'assegnazione esistente viene aggiornata.
  - Se viene modificata la definizione dell'assegnazione dei criteri, viene creata una nuova assegnazione di criteri.
    Le assegnazioni di criteri distribuite in precedenza vengono mantenute.
  - Se l'artefatto dell'assegnazione dei criteri viene rimosso dal progetto, l'assegnazione di criteri distribuita viene mantenuta.
- Modelli di Gestione risorse di Azure
  - Il modello viene elaborato tramite Resource Manager come **PUT**. Dato che ogni tipo di risorsa gestisce questo tipo di azione in modo diverso, vedere la documentazione per ogni risorsa inclusa per determinare l'impatto di questa azione quando viene eseguita dai progetti.

## <a name="possible-errors-on-updating-assignments"></a>Possibili errori per l'aggiornamento delle assegnazioni

Quando si aggiornano le assegnazioni, è possibile apportare modifiche che causano errori durante l'esecuzione. Un esempio è la modifica della posizione di un gruppo di risorse dopo che è già stato distribuito. È possibile apportare qualsiasi modifica supportata da [Azure Resource Manager](../../../azure-resource-manager/management/overview.md), ma qualsiasi modifica che causerebbe un errore in Azure Resource Manager comporterà anche l'esito negativo dell'assegnazione.

Non è previsto alcun limite al numero di volte che è possibile aggiornare un'assegnazione. Se si verifica un errore, determinare l'errore ed eseguire un altro aggiornamento per l'assegnazione.  Esempi di scenari di errore:

- Un parametro non valido
- Un oggetto già esistente
- Una modifica non supportata da Azure Resource Manager

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul ciclo di vita del [blueprint.](../concepts/lifecycle.md)
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).