---
title: Distribuire modelli di Resource Manager tramite GitHub Actions
description: Viene illustrato come distribuire modelli di Azure Resource Manager tramite GitHub Actions.
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: f2e0d73c838d16c161605972b87d6f07ef8869b9
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869184"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>Distribuire modelli di Azure Resource Manager tramite GitHub Actions

Con [GitHub Actions](https://help.github.com/en/actions) è possibile creare flussi di lavoro personalizzati del ciclo di vita di sviluppo software direttamente nel repository GitHub in cui sono archiviati i modelli di Azure Resource Manager (ARM). Un [flusso di lavoro](https://help.github.com/actions/reference/workflow-syntax-for-github-actions) è definito da un file YAML. I flussi di lavoro dispongono di uno o più processi, ognuno dei quali contiene una serie di passaggi che eseguono singole attività. I passaggi possono eseguire comandi o usare un'azione. È possibile creare azioni personalizzate o usare azioni condivise dalla [community di GitHub](https://github.com/marketplace?type=actions) e personalizzarle in base alle esigenze. Questo articolo illustra come usare [Azure CLI Action](https://github.com/marketplace/actions/azure-cli-action) per distribuire modelli di Resource Manager.

Azure CLI Action include due azioni dipendenti:

- **[Checkout](https://github.com/marketplace/actions/checkout)** : Consente di eseguire il checkout del repository in modo che il flusso di lavoro possa accedere a qualsiasi modello di Resource Manager specificato.
- **[Accesso ad Azure](https://github.com/marketplace/actions/azure-login)** : Permette di accedere con le credenziali di Azure

Un flusso di lavoro di base per la distribuzione di un modello di Resource Manager può includere tre passaggi:

1. Eseguire il checkout di un file modello.
2. Accedere ad Azure.
3. Distribuire un modello di Resource Manager

## <a name="prerequisites"></a>Prerequisiti

Per archiviare i modelli di Resource Manager e i file del flusso di lavoro è necessario un repository GitHub. Per crearne uno, vedere [Creazione di un nuovo repository](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository).

## <a name="configure-deployment-credentials"></a>Configurare le credenziali di distribuzione

L'azione di accesso ad Azure usa un'entità servizio per l'autenticazione in Azure. L'entità di sicurezza di un flusso di lavoro CI/CD necessita in genere dei diritti di collaboratore incorporati per distribuire risorse di Azure.

Il seguente script dell'interfaccia della riga di comando di Azure illustra come generare un'entità servizio di Azure con autorizzazioni di collaboratore in un gruppo di risorse di Azure. Questo è il gruppo di risorse in cui il flusso di lavoro distribuisce le risorse definite nel modello di Resource Manager.

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

Personalizzare il valore di **$projectName** e **$location** nello script. Il nome del gruppo di risorse è il nome del progetto seguito da **rg**. È necessario specificare il nome del gruppo di risorse nel flusso di lavoro.

Lo script restituisce un oggetto JSON simile al seguente:

```json
{
   "clientId": "<GUID>",
   "clientSecret": "<GUID>",
   "subscriptionId": "<GUID>",
   "tenantId": "<GUID>",
   (...)
}
```

Copiare l'output JSON e archiviarlo come segreto GitHub nel repository GitHub. Se non si dispone ancora di un repository, vedere [ Prerequisiti](#prerequisites).

1. Dal repository GitHub, selezionare la scheda **Impostazioni**.
1. Selezionare **Segreti** nel menu di sinistra.
1. Immettere i valori seguenti:

    - **Name**: AZURE_CREDENTIALS
    - **Value**: (Incollare l'output JSON)
1. Selezionare **Aggiungi segreto**.

È necessario specificare il nome del segreto nel flusso di lavoro.

## <a name="add-resource-manager-template"></a>Aggiungere un modello di Resource Manager

Aggiungere un modello di Resource Manager al repository GitHub. Se non si dispone di un modello, è possibile usare il modello seguente. Il modello crea un account di archiviazione.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

È possibile inserire il file in un punto qualsiasi del repository. Nell'esempio di flusso di lavoro illustrato nella sezione successiva si presuppone che il file di modello, denominato **azuredeploy.json**, sia archiviato in una cartella denominata **templates** alla radice del repository.

## <a name="create-workflow"></a>Creare un flusso di lavoro

Il file del flusso di lavoro deve essere archiviato nella cartella **.github/workflow** alla radice del repository. Il file del flusso di lavoro può avere estensione **.yml** o **.yaml**.

È possibile creare un file del flusso di lavoro e quindi eseguire il push o caricare il file nel repository oppure usare la procedura seguente:

1. Dal repository GitHub, selezionare **Actions** dal menu in alto.
1. Selezionare **Nuovo flusso di lavoro**.
1. Selezionare **Set up a workflow yourself** (Configurare manualmente un flusso di lavoro).
1. Se si preferisce un nome diverso da **main.yml**, rinominare il file del flusso di lavoro. Ad esempio: **deployStorageAccount.yml**.
1. Sostituire il contenuto del file .yml con quanto segue:

    ```yml
    name: Deploy ARM Template

    on:
      push:
        branches:
          - master
        paths:
          - ".github/workflows/deployStorageAccount.yml"
          - "templates/azuredeploy.json"

    jobs:
      deploy-storage-account-template:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout source code
            uses: actions/checkout@master

          - name: Login to Azure
            uses: azure/login@v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}


          - name: Deploy ARM Template
            uses: azure/CLI@v1
            with:
              inlineScript: |
                az deployment group create --resource-group myResourceGroup --template-file ./templates/azuredeploy.json
    ```

    Il file del flusso di lavoro include tre sezioni:

    - **name**: Nome del flusso di lavoro.
    - **on**: Nome degli eventi GitHub che attivano il flusso di lavoro. Il flusso di lavoro viene attivato quando si verifica un evento push nel ramo master che modifica almeno uno dei due file specificati. I due file sono il file del flusso di lavoro e il file di modello.

        > [!IMPORTANT]
        > Verificare che i due file e i relativi percorsi corrispondano.
    - **jobs**: Un'esecuzione del flusso di lavoro è costituita da uno o più processi. È presente un solo processo denominato **deploy-storage-account-template**.  Questo processo prevede tre passaggi:

        - **Eseguire il checkout del codice sorgente**.
        - **Accedere ad Azure**.

            > [!IMPORTANT]
            > Verificare che il nome del segreto corrisponda a quello salvato nel repository. Vedere [Configurare le credenziali di distribuzione](#configure-deployment-credentials).
        - **Distribuire un modello di Resource Manager**. Sostituire il valore di **resourceGroupName**.  Se è stato usato lo script dell'interfaccia della riga di comando di Azure in [Configurare le credenziali di distribuzione](#configure-deployment-credentials), il nome del gruppo di risorse generato corrisponde al nome del progetto seguito da **rg**. Verificare il valore di **templateLocation**.

1. Selezionare **Start commit** (Avvia commit).
1. Selezionare **Commit directly to the master branch** (Esegui il commit direttamente nel ramo master).
1. Selezionare **Commit new file** (Commit nuovo file) (o **Commit modifiche**).

Dato che il flusso di lavoro è configurato per essere attivato dal file del flusso di lavoro o dal file di modello da aggiornare, il flusso di lavoro viene avviato subito dopo il commit delle modifiche.

## <a name="check-workflow-status"></a>Controllare lo stato del flusso di lavoro

1. Selezionare la scheda **Actions**. Verrà visualizzato un flusso di lavoro **Create deployStorageAccount.yml** elencato. L'esecuzione del flusso di lavoro richiede 1-2 minuti.
1. Selezionare il flusso di lavoro per aprirlo.
1. Selezionare **deploy-storage-account-template** (nome processo) dal menu a sinistra. Il nome del processo è definito nel flusso di lavoro.
1. Selezionare **Deploy ARM Template** (distribuisci modello di Resource Manager, nome passaggio) per espanderlo. È possibile visualizzare la risposta dell'API REST.

## <a name="next-steps"></a>Passaggi successivi

Per un'esercitazione dettagliata che illustra il processo di creazione di un modello, vedere [Esercitazione: Creare e distribuire il primo modello di Resource Manager](template-tutorial-create-first-template.md).
