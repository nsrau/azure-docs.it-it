---
title: Guida introduttiva di Azure - Creare un runbook di Automazione di Azure | Microsoft Docs
description: Questo articolo illustra come iniziare a creare un runbook di Automazione di Azure.
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 948ca820347c7cdcd560ade46e850f66b25bc88e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987285"
---
# <a name="create-an-azure-automation-runbook"></a>Creare un runbook di Automazione di Azure

È possibile creare runbook di Automazione di Azure tramite Azure. Questo metodo offre un'interfaccia utente basata sul browser per la creazione di runbook di Automazione. Questa guida introduttiva illustra la creazione, la modifica, il test e la pubblicazione di un runbook PowerShell di Automazione.

Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere ad Azure all'indirizzo https://portal.azure.com.

## <a name="create-the-runbook"></a>Creare il runbook

Creare prima di tutto un runbook. Per impostazione predefinita, il runbook di esempio creato in questa guida ha come output `Hello World`.

1. Aprire l'account di Automazione.

1. Fare clic su **Runbook** in **Automazione processi**. Viene visualizzato l'elenco di runbook.

1. Fare clic su **Crea un runbook** all'inizio dell'elenco.

1. Immettere `Hello-World` per il nome del runbook nel campo **Nome** e selezionare **PowerShell** per il campo **Tipo di runbook**. 

   ![Immettere le informazioni sul runbook di Automazione nella pagina](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Fare clic su **Crea**. Il runbook viene creato e si apre la pagina Modifica runbook di PowerShell.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png" alt-text="Screenshot della pagina Modifica runbook di PowerShell.":::

1. Digitare o copiare e incollare nel riquadro di modifica il codice seguente, che crea un parametro di input facoltativo denominato `Name` con il valore predefinito `World` e genera come output una stringa che usa questo valore di input:

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Fare clic su **Salva** per salvare una bozza del runbook.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook.png" alt-text="Screenshot della pagina Modifica runbook di PowerShell.":::

## <a name="test-the-runbook"></a>Testare il runbook

Dopo avere creato il runbook, è necessario testarlo per verificarne il funzionamento.

1. Fare clic su **Pannello di test** per aprire il pannello di test.

1. Immettere un valore per **Nome** e fare clic su **Avvia**. Il processo di test viene avviato e vengono visualizzati lo stato e l'output del processo.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-test-runbook.png" alt-text="Screenshot della pagina Modifica runbook di PowerShell.":::

1. Chiudere il riquadro Test facendo clic sulla **X** nell'angolo in alto a destra. Fare clic su **OK** nel popup visualizzato.

1. Nella pagina Modifica runbook di PowerShell fare clic su **Pubblica** per pubblicare il runbook come versione ufficiale nell'account.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png" alt-text="Screenshot della pagina Modifica runbook di PowerShell.":::

## <a name="run-the-runbook"></a>Eseguire il runbook

Dopo la pubblicazione del runbook, viene visualizzata la pagina di panoramica.

1. Nella pagina di panoramica del runbook fare clic su **Avvia** per aprire la pagina di configurazione Avvia runbook.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png" alt-text="Screenshot della pagina Modifica runbook di PowerShell.":::

1. Lasciare vuoto **Nome** in modo che venga usato il valore predefinito e fare clic su **OK**. Il processo del runbook viene inviato e si apre la pagina Processo.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-job-page.png" alt-text="Screenshot della pagina Modifica runbook di PowerShell.":::

1. Quando lo stato del processo diventa `Running` o `Completed`, fare clic su **Output** per aprire il riquadro Output e visualizzare l'output del runbook.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png" alt-text="Screenshot della pagina Modifica runbook di PowerShell.":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il runbook. A questo scopo, selezionare il runbook nell'elenco di runbook e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato, modificato e pubblicato un runbook ed è stato avviato un processo del runbook. Per altre informazioni sui runbook di Automazione, passare all'articolo sui diversi tipi di runbook che è possibile creare e usare in Automazione.

> [!div class="nextstepaction"]
> [Tipi di runbook di Automazione di Azure](./automation-runbook-types.md)
