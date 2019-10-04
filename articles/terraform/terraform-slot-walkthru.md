---
title: Terraform con gli slot di distribuzione del provider di Azure
description: Esercitazione sull'uso di Terraform con gli slot di distribuzione del provider di Azure
services: terraform
ms.service: azure
keywords: Terraform, DevOps, macchina virtuale, Azure, slot di distribuzione
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: ec2ed1da46df2793a241c9c89d168a6c5d462b9d
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169817"
---
# <a name="use-terraform-to-provision-infrastructure-with-azure-deployment-slots"></a>Usare Terraform per effettuare il provisioning dell'infrastruttura con slot di distribuzione di Azure

È possibile usare [slot di distribuzione di Azure](/azure/app-service/deploy-staging-slots) per alternare versioni diverse di un'app. Queste funzionalità consentono di ridurre al minimo l'impatto delle distribuzioni non funzionanti. 

Questo articolo presenta un esempio di uso degli slot di distribuzione descrivendo in modo dettagliato la distribuzione di due app tramite GitHub e Azure. Un'app è ospitata in uno slot di produzione. La seconda app è ospitata in uno slot di staging. I nomi "produzione" e "gestione temporanea" sono arbitrari ed è possibile usare i nomi desiderati in base al proprio scenario. Dopo aver configurato gli slot di distribuzione, è possibile usare Terraform per alternare i due slot in base alle esigenze.

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

- **Account GitHub**: è necessario un account [GitHub](https://www.github.com) per creare una copia tramite fork e usare il repository GitHub di test.

## <a name="create-and-apply-the-terraform-plan"></a>Creare e applicare il piano Terraform

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Aprire [Azure Cloud Shell](/azure/cloud-shell/overview). Se in precedenza non è stato selezionato un ambiente, selezionare **Bash** come ambiente.

    ![Prompt di Cloud Shell](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. Passare alla directory `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crea una directory denominata `deploy`.

    ```bash
    mkdir deploy
    ```

1. Crea una directory denominata `swap`.

    ```bash
    mkdir swap
    ```

1. Usare il comando Bash `ls` per verificare che entrambe le directory siano state create correttamente.

    ![Cloud Shell dopo la creazione delle directory](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Passare alla directory `deploy`.

    ```bash
    cd deploy
    ```

1. Usare l'[editor vi](https://www.debian.org/doc/manuals/debian-tutorial/ch-editor.html) per creare un file denominato `deploy.tf`. Questo file conterrà la [configurazione di Terraform](https://www.terraform.io/docs/configuration/index.html).

    ```bash
    vi deploy.tf
    ```

1. Attivare la modalità di inserimento con il tasto I.

1. Incollare il codice seguente nell'editor:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
        app_service_name    = "${azurerm_app_service.slotDemo.name}"
    }
    ```

1. Premere ESC per uscire dalla modalità di inserimento.

1. Salvare il file e chiudere l'editor vi immettendo il comando seguente:

    ```bash
    :wq
    ```

1. Dopo aver creato il file, verificarne il contenuto.

    ```bash
    cat deploy.tf
    ```

1. Inizializzare Terraform.

    ```bash
    terraform init
    ```

1. Creare il piano Terraform.

    ```bash
    terraform plan
    ```

1. Effettuare il provisioning delle risorse definite nel file di configurazione `deploy.tf`. Confermare l'azione immettendo `yes` al prompt.

    ```bash
    terraform apply
    ```

1. Chiudere la finestra di Cloud Shell.

1. Nel menu principale del portale di Azure selezionare **Gruppi di risorse**.

    ![Selezione di "Gruppi di risorse" nel portale](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. Nella scheda **Gruppi di risorse** selezionare **slotDemoResourceGroup**.

    ![Gruppo di risorse creato da Terraform](./media/terraform-slot-walkthru/resource-group.png)

È ora possibile vedere tutte le risorse create da Terraform.

![Risorse create da Terraform](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>Creare una copia tramite fork del progetto

Prima di testare la creazione e lo scambio da e verso gli slot di distribuzione, è necessario creare una copia tramite fork del progetto di test da GitHub.

1. Passare al [repository awesome-terraform su GitHub](https://github.com/Azure/awesome-terraform).

1. Creare una copia tramite fork del repository **awesome-terraform**.

    ![Creare una copia tramite fork del repository awesome-terraform su GitHub](./media/terraform-slot-walkthru/fork-repo.png)

1. Seguire tutte le istruzioni per creare una copia tramite fork nell'ambiente.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Distribuire il progetto da GitHub negli slot di distribuzione

Dopo aver creato una copia tramite fork del repository del progetto di test, configurare gli slot di distribuzione tramite i passaggi seguenti:

1. Nel menu principale del portale di Azure selezionare **Gruppi di risorse**.

1. Selezionare **slotDemoResourceGroup**.

1. Selezionare **slotAppService**.

1. Selezionare **Opzioni di distribuzione**.

    ![Opzioni di distribuzione per una risorsa del servizio app](./media/terraform-slot-walkthru/deployment-options.png)

1. Nella scheda **Opzione di distribuzione** selezionare **Scegliere l'origine** e quindi **GitHub**.

    ![Selezionare l'origine di distribuzione](./media/terraform-slot-walkthru/select-source.png)

1. Dopo che Azure stabilisce la connessione e visualizza tutte le opzioni, selezionare **Autorizzazione**.

1. Nella scheda **Autorizzazione** selezionare **Autorizza** e fornire le credenziali necessarie perché Azure possa accedere all'account GitHub. 

1. Dopo che Azure convalida le credenziali di GitHub, viene visualizzato un messaggio che indica che il processo di autorizzazione è stato completato. Selezionare **OK** per chiudere la scheda **Autorizzazione**.

1. Selezionare **Scegliere l'azienda** e selezionare l'azienda.

1. Selezionare **Scegliere il progetto**.

1. Nella scheda **Scegliere il progetto** selezionare il progetto **awesome-terraform**.

    ![Scegliere il progetto awesome-terraform](./media/terraform-slot-walkthru/choose-project.png)

1. Selezionare **Scegliere il ramo**.

1. Nella scheda **Scegliere il ramo** selezionare **master**.

    ![Scegliere il ramo master](./media/terraform-slot-walkthru/choose-branch-master.png)

1. Nella scheda **Opzione di distribuzione** selezionare **OK**.

A questo punto, è stato distribuito lo slot di produzione. Per distribuire lo slot di staging, completare tutti i passaggi precedenti in questa sezione, ma con poche modifiche:

- Nel passaggio 3 selezionare la risorsa **slotAppServiceSlotOne**.

- Nel passaggio 13 selezionare il ramo di lavoro invece del ramo master.

    ![Scegliere il ramo di lavoro](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Testare le distribuzioni delle app

Nelle sezioni precedenti sono stati configurati due slot, **slotAppService** e **slotAppServiceSlotOne**, per la distribuzione da rami diversi in GitHub. Visualizzare ora le app Web in anteprima per verificare che siano state distribuite correttamente.

Eseguire due volte i passaggi seguenti. Nel passaggio 3 selezionare **slotAppService** la prima volta e quindi selezionare **slotAppServiceSlotOne** la seconda volta.

1. Nel menu principale del portale di Azure selezionare **Gruppi di risorse**.

1. Selezionare **slotDemoResourceGroup**.

1. Selezionare **slotAppService** o **slotAppServiceSlotOne**.

1. Nella pagina di panoramica selezionare **URL**.

    ![Selezionare URL nella scheda di panoramica per eseguire il rendering dell'app](./media/terraform-slot-walkthru/resource-url.png)

> [!NOTE]
> Possono essere necessari diversi minuti prima che Azure compili e distribuisca il sito da GitHub.
>
>

Per l'app Web **slotAppService** verrà visualizzata una pagina blu con il titolo **Slot Demo App 1**. Per l'app Web **slotAppServiceSlotOne** verrà visualizzata una pagina verde con il titolo **Slot Demo App 2**.

![Visualizzare le app in anteprima per verificare che siano state distribuite correttamente](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Scambiare i due slot di distribuzione

Per testare lo scambio dei due slot di distribuzione, completare i passaggi seguenti:
 
1. Passare alla scheda del browser che esegue **slotAppService**, ovvero l'app con la pagina blu. 

1. Tornare al portale di Azure in una scheda separata.

1. Aprire Cloud Shell.

1. Passare alla directory **clouddrive/swap**.

    ```bash
    cd clouddrive/swap
    ```

1. Usare l'editor vi per creare un file denominato `swap.tf`.

    ```bash
    vi swap.tf
    ```

1. Attivare la modalità di inserimento con il tasto I.

1. Incollare il codice seguente nell'editor:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Premere ESC per uscire dalla modalità di inserimento.

1. Salvare il file e chiudere l'editor vi immettendo il comando seguente:

    ```bash
    :wq
    ```

1. Inizializzare Terraform.

    ```bash
    terraform init
    ```

1. Creare il piano Terraform.

    ```bash
    terraform plan
    ```

1. Effettuare il provisioning delle risorse definite nel file di configurazione `swap.tf`. Confermare l'azione immettendo `yes` al prompt.

    ```bash
    terraform apply
    ```

1. Quando Terraform ha completato lo scambio degli slot, tornare nel browser che esegue il rendering dell'app Web **slotAppService** e aggiornare la pagina. 

L'app Web nello slot di staging **slotAppServiceSlotOne** è stata scambiata con lo slot di produzione ed è ora visualizzata in verde. 

![Gli slot di distribuzione sono stati scambiati](./media/terraform-slot-walkthru/slots-swapped.png)

Per tornare alla versione di produzione originale dell'app, riapplicare il piano Terraform creato dal file di configurazione `swap.tf`.

```bash
terraform apply
```

Al termine dello scambio, verrà visualizzata la configurazione originale.