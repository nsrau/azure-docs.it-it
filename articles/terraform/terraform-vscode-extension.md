---
title: Installare e usare l'estensione Azure Terraform di Visual Studio Code
description: Questo articolo illustra come installare e usare l'estensione Azure Terraform in Visual Studio Code.
services: terraform
ms.service: azure
keywords: terraform, azure, devops, visual studio code, estensione
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 7dd87f38ca5dfb1c2fdd38b56b314e0cbbe3ce12
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169924"
---
# <a name="install-and-use-the-azure-terraform-visual-studio-code-extension"></a>Installare e usare l'estensione Azure Terraform di Visual Studio Code

L'estensione Microsoft Azure Terraform di Visual Studio Code è stata progettata per aumentare la produttività degli sviluppatori durante la creazione, il test e l'uso di Terraform con Azure. L'estensione offre supporto per i comandi di Terraform, la visualizzazione del grafico delle risorse e l'integrazione di Cloud Shell direttamente in Visual Studio Code.

In questo articolo viene spiegato come:
> [!div class="checklist"]
> * Usare Terraform per automatizzare e semplificare il provisioning dei servizi di Azure.
> * Installare e usare l'estensione Microsoft Terraform di Visual Studio Code per i servizi di Azure.
> * Usare Visual Studio Code per scrivere, configurare ed eseguire i piani di Terraform.

## <a name="prerequisites"></a>Prerequisiti
- **Sottoscrizione di Azure**: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

- **Terraform**: [installare e configurare Terraform](/azure/virtual-machines/linux/terraform-install-configure).

- **Visual Studio Code**: installare la versione di [Visual Studio Code](https://code.visualstudio.com/download) appropriata per l'ambiente.

## <a name="prepare-your-dev-environment"></a>Preparare l'ambiente di sviluppo

### <a name="install-git"></a>Installare Git

Per eseguire gli esercizi in questo articolo, è necessario [installare Git](https://git-scm.com/).

### <a name="install-hashicorp-terraform"></a>Installare HashiCorp Terraform

Seguire le istruzioni riportate nella pagina Web di HashiCorp per l'[installazione di Terraform](https://www.terraform.io/intro/getting-started/install.html), che include informazioni su:

- Download di Terraform
- Installazione di Terraform
- Verifica della corretta installazione di Terraform

>[!Tip]
>Assicurarsi di seguire le istruzioni relative all'impostazione della variabile di sistema PATH.

### <a name="install-nodejs"></a>Installare Node.js

Per usare Terraform in Cloud Shell, è necessario [installare Node.js](https://nodejs.org/) 6.0+.

>[!NOTE]
>Per verificare se Node.js è installato, aprire una finestra del terminale e immettere `node -v`.

### <a name="install-graphviz"></a>Installare GraphViz

Per usare la funzione visualize di Terraform è necessario [installare GraphViz](https://graphviz.org/).

>[!NOTE]
>Per verificare se GraphViz è installato, aprire una finestra del terminale e immettere `dot -V`.

### <a name="install-the-azure-terraform-visual-studio-code-extension"></a>Installare l'estensione Azure Terraform di Visual Studio Code

1. Avviare Visual Studio Code.

1. Selezionare **Estensioni**.

    ![Pulsante delle estensioni](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

1. Usare la casella di testo **Search Extensions in Marketplace** (Cerca nelle estensioni in Marketplace) per cercare Azure Terraform:

    ![Cercare le estensioni di Visual Studio Code nel Marketplace](media/terraform-vscode-extension/tf-search-extensions.png)

1. Selezionare **Installa**.

    >[!NOTE]
    >Quando si seleziona **Install** (Installa) per l'estensione Azure Terraform, Visual Studio Code installa automaticamente l'estensione Azure Account. Quest'ultima è costituita da un file di dipendenza per l'estensione Azure Terraform che viene usato per eseguire le autenticazioni delle sottoscrizioni di Azure e le estensioni di codice correlate ad Azure.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Verificare che l'estensione Terraform sia installata in Visual Studio Code

1. Selezionare **Estensioni**.

1. Immettere `@installed` nella casella di testo di ricerca.

    ![Estensioni installate](media/terraform-vscode-extension/tf-installed-extensions.png)

L'estensione Azure Terraform verrà visualizzata nell'elenco delle estensioni installate.

![Estensioni Terraform installate](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

È ora possibile eseguire tutti i comandi di Terraform supportati nell'ambiente di Cloud Shell direttamente da Visual Studio Code.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Esercizio 1: Procedura dettagliata per l'esecuzione dei comandi Terraform di base

In questo esercizio si crea ed esegue un file di configurazione di Terraform di base che effettua il provisioning di un nuovo gruppo di risorse di Azure.

### <a name="prepare-a-test-plan-file"></a>Preparare un file di un piano di test

1. In Visual Studio Code selezionare **File > New File** (Nuovo file) dalla barra dei menu.

1. Nel browser passare alla [pagina azurerm_resource_group di Terraform](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) e copiare il codice nel blocco di codice riportato in **Example Usage** (Esempio di uso):

    ![Example Usage (Esempio di uso)](media/terraform-vscode-extension/tf-azurerm-resource-group-example-usage.png)

1. Incollare il codice copiato nel nuovo file creato in Visual Studio Code.

    ![Incollare il codice dell'esempio d'uso](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >È possibile modificare il valore **name** del gruppo di risorse, ma il nome deve essere univoco per la sottoscrizione di Azure.

1. Dalla barra dei menu selezionare **File > Save As**  (Salva con nome).

1. Nella finestra di dialogo **Save as** (Salva con nome) selezionare un percorso e quindi fare clic su **New folder** (Nuova cartella). Modificare il nome della nuova cartella usando un nome più descrittivo di *New folder* (Nuova cartella).

    >[!NOTE]
    >In questo esempio la cartella è denominata TERRAFORM-TEST-PLAN.

1. Verificare che la nuova cartella sia evidenziata (selezionata) e quindi fare clic su **Open** (Apri).

1. Nella finestra di dialogo **Save As** (Salva con nome) sostituire il nome predefinito del file con *main.tf*.

    ![Salvare con il nome main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

1. Selezionare **Salva**.
1. Dalla barra dei menu selezionare **File > Open Folder** (Apri cartella). Individuare e selezionare la nuova cartella creata.

### <a name="run-terraform-init-command"></a>Eseguire il comando *init* di Terraform

1. Avviare Visual Studio Code.

1. Dalla barra dei menu di Visual Studio Code selezionare **File > Open Folder** (Apri cartella) e quindi individuare e selezionare il file *main.tf*.

    ![File main.tf](media/terraform-vscode-extension/tf-main-tf.png)

1. Dalla barra dei menu selezionare **View > Command Palette... > Azure Terraform: Init** (Visualizza > Riquadro comandi > Azure Terraform: Init).

1. Quando viene visualizzata la conferma, selezionare **OK**.

    ![Messaggio di richiesta di apertura di Cloud Shell](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

1. La prima volta che si avvia Cloud Shell da una nuova cartella, viene chiesto di configurare l'applicazione Web. Scegliere **Open**(Apri).

    ![Primo avvio di Cloud Shell](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

1. Viene visualizzata la pagina Benvenuto in Azure Cloud Shell. Selezionare Bash o PowerShell.

    ![Benvenuto in Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >In questo esempio è stato selezionato Bash (Linux).

1. Se non si è già configurato un account di archiviazione di Azure, viene visualizzata la schermata seguente. Selezionare **Create storage** (Crea risorsa di archiviazione).

    ![Non sono state montate risorse di archiviazione](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Azure Cloud Shell viene avviato nella shell selezionata in precedenza e visualizza le informazioni per l'unità cloud appena creata per l'utente.

    ![L'unità cloud è stata creata](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

1. È ora possibile uscire da Cloud Shell.

1. Dalla barra dei menu selezionare **View** > **Command Palette** > **Azure Terraform: init** (Visualizza > Riquadro comandi > Azure Terraform: init).

    ![L'estensione Terraform è stata inizializzata correttamente](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Visualizzare il piano

In precedenza in questa esercitazione è stato installato GraphViz. Terraform può usare GraphViz per generare una rappresentazione visiva di una configurazione o un piano di esecuzione. L'estensione Azure Terraform di Visual Studio Code implementa questa funzione tramite il comando *visualize*.

- Dalla barra dei menu selezionare **View > Command Palette > Azure Terraform: Visualize** (Visualizza > Riquadro comandi > Azure Terraform: Visualize).

    ![Visualizzare il piano](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Eseguire il comando *plan* di Terraform

Il comando *plan* di Terraform viene usato per controllare se il piano di esecuzione per un set di modifiche verrà applicato nel modo previsto.

>[!NOTE]
>Il comando *plan* di Terraform non apporta modifiche alle risorse effettive di Azure. Per rendere effettive le modifiche contenute nel piano viene usato il comando *apply* di Terraform.

- Dalla barra dei menu selezionare **View** > **Command Palette** > **Azure Terraform: plan** (Visualizza > Riquadro comandi > Azure Terraform: plan).

    ![Piano Terraform](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Eseguire il comando *apply* di Terraform

Quando si è soddisfatti dei risultati del *piano* di Terraform, è possibile eseguire il comando *apply*.

1. Dalla barra dei menu selezionare **View** > **Command Palette** > **Azure Terraform: apply** (Visualizza > Riquadro comandi > Azure Terraform: apply).

    ![Comando terraform apply](media/terraform-vscode-extension/tf-terraform-apply.png)

1. Immettere `yes`.

    ![Conferma di terraform apply](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Verificare l'esecuzione del piano di Terraform

Per verificare se il nuovo gruppo di risorse di Azure è stato creato correttamente:

1. Aprire il Portale di Azure.

1. Nel riquadro di spostamento a sinistra selezionare **Gruppi di risorse**.

    ![Verificare la nuova risorsa](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Il nuovo gruppo di risorse dovrebbe essere elencato nella colonna **NAME** (NOME).

>[!NOTE]
>Per il momento è possibile lasciare aperta la finestra del portale di Azure poiché verrà usata nel passaggio successivo.

### <a name="run-terraform-destroy-command"></a>Eseguire il comando *destroy* di Terraform

1. Dalla barra dei menu selezionare **View** > **Command Palette** > **Azure Terraform: destroy** (Visualizza > Riquadro comandi > Azure Terraform: destroy).

    ![Comando terraform destroy](media/terraform-vscode-extension/tf-terraform-destroy.png)

1. Immettere *yes*.

    ![Conferma di terraform destroy](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Verificare l'eliminazione definitiva del gruppo di risorse

Per verificare che Terraform abbia eliminato definitivamente il nuovo gruppo di risorse:

1. Selezionare **Aggiorna** nella pagina **Gruppi di risorse** del portale di Azure.

1. Il gruppo di risorse non sarà più visualizzato nell'elenco.

    ![Verificare l'eliminazione definitiva del gruppo di risorse](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Esercizio 2: modulo *compute* di Terraform

In questo esercizio si apprenderà come caricare il modulo *compute* di Terraform nell'ambiente di Visual Studio Code.

### <a name="clone-the-terraform-azurerm-compute-module"></a>Clonare il modulo terraform-azurerm-compute

1. Usare [questo collegamento](https://github.com/Azure/terraform-azurerm-compute) per accedere al modulo terraform-azurerm-compute su GitHub.

1. Selezionare **Clona o scarica**.

    ![Clonare o scaricare](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >In questo esempio, alla cartella è stato assegnato il nome *terraform-azurerm-compute*.

### <a name="open-the-folder-in-visual-studio-code"></a>Aprire la cartella in Visual Studio Code

1. Avviare Visual Studio Code.

1. Dalla barra dei menu selezionare **File > Open Folder** (Apri cartella) e quindi individuare e selezionare la cartella creata nel passaggio precedente.

    ![Cartella terraform-azurerm-compute](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Inizializzare Terraform

Prima di iniziare a usare i comandi di Terraform da Visual Studio Code, si scaricano i plug-in per i due provider di Azure: random e azurerm.

1. Nel riquadro Terminal (Terminale) dell'IDE di VS Code immettere `terraform init`.

    ![Comando terraform init](media/terraform-vscode-extension/tf-terraform-init-command.png)

1. Immettere `az login`, premere **INVIO** e seguire le istruzioni visualizzate.

### <a name="module-test-lint"></a>Test del modulo: *lint*

1. Dalla barra dei menu selezionare **View > Command Palette > Azure Terraform: Execute Test** (Visualizza > Riquadro comandi > Azure Terraform: Execute Test).

1. Nell'elenco delle opzioni relative al tipo di test selezionare **lint**.

    ![Selezionare il tipo di test](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

1. Quando viene visualizzata la pagina di conferma, selezionare **OK** e seguire le istruzioni sullo schermo.

    ![Messaggio di richiesta di apertura di Cloud Shell](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Quando si esegue il test **lint** o **end to end**, Azure usa un servizio contenitore per effettuare il provisioning di un computer di test per l'esecuzione del test effettivo. Per questo motivo, la restituzione dei risultati del test potrebbe richiedere alcuni minuti.

Dopo qualche istante, nel riquadro Terminal (Terminale) viene visualizzato un elenco simile a questo esempio:

![Risultati del test Lint](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="module-test-end-to-end"></a>Test del modulo: *end-to-end*

1. Dalla barra dei menu selezionare **View > Command Palette > Azure Terraform: Execute Test** (Visualizza > Riquadro comandi > Azure Terraform: Execute Test).

1. Nell'elenco delle opzioni relative al tipo di test selezionare **end to end**.

    ![Selezionare il tipo di test](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

1. Quando viene visualizzata la pagina di conferma, selezionare **OK** e seguire le istruzioni sullo schermo.

    ![Messaggio di richiesta di apertura di Cloud Shell](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Quando si esegue il test **lint** o **end to end**, Azure usa un servizio contenitore per effettuare il provisioning di un computer di test per l'esecuzione del test effettivo. Per questo motivo, la restituzione dei risultati del test potrebbe richiedere alcuni minuti.

Dopo qualche istante, nel riquadro Terminal (Terminale) viene visualizzato un elenco simile a questo esempio:

![Risultati del test end-to-end](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Elenco di tutti i moduli di Terraform disponibili per Azure e altri provider supportati](https://registry.terraform.io/)