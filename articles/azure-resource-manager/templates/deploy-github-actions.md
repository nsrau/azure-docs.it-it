---
title: Distribuire modelli di Gestione risorse usando le azioni di GitHub
description: Viene descritto come distribuire modelli di Azure Resource Manager usando le azioni di GitHub.
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: 5fe147a9c42e83d5e644b0c08dfa67de88ec05c0
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82875194"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>Distribuire modelli di Azure Resource Manager usando le azioni di GitHub

Le [azioni di GitHub](https://help.github.com/en/actions) consentono di creare flussi di lavoro del ciclo di vita di sviluppo software personalizzati direttamente nel repository GitHub in cui vengono archiviati i modelli di Azure Resource Manager (ARM). Un [flusso di lavoro](https://help.github.com/actions/reference/workflow-syntax-for-github-actions) è definito da un file YAML. I flussi di lavoro dispongono di uno o più processi con ogni processo contenente una serie di passaggi che eseguono singole attività. I passaggi possono eseguire comandi o usare un'azione. È possibile creare azioni personalizzate o usare azioni condivise dalla community di [GitHub](https://github.com/marketplace?type=actions) e personalizzarle in base alle esigenze. Questo articolo illustra come usare l' [azione dell'interfaccia](https://github.com/marketplace/actions/azure-cli-action) della riga di comando di Azure per distribuire modelli di gestione risorse.

L'azione dell'interfaccia della riga di comando di Azure include due azioni dipendenti

- **[Checkout](https://github.com/marketplace/actions/checkout)**: estrarre il repository in modo che il flusso di lavoro possa accedere a qualsiasi modello di gestione risorse specificato.
- **[Accesso di Azure](https://github.com/marketplace/actions/azure-login)**: accedere con le credenziali di Azure

Un flusso di lavoro di base per la distribuzione di un modello di Gestione risorse può avere tre passaggi:

1. Estrarre un file modello.
2. Accedere ad Azure.
3. Distribuire un modello di Gestione risorse

## <a name="prerequisites"></a>Prerequisiti

Per archiviare i modelli di Gestione risorse e i file del flusso di lavoro, è necessario un repository GitHub. Per crearne uno, vedere [creazione di un nuovo repository](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository).

## <a name="configure-deployment-credentials"></a>Configurare le credenziali di distribuzione

L'azione di accesso di Azure usa un'entità servizio per l'autenticazione in Azure. Il principale di un flusso di lavoro di integrazione continua/recapito continuo necessita in genere del diritto collaboratore incorporato per distribuire le risorse di Azure.

Il seguente script dell'interfaccia della riga di comando di Azure illustra come generare un'entità servizio di Azure con autorizzazioni di collaboratore per un gruppo di risorse di Azure. Questo gruppo di risorse è il punto in cui il flusso di lavoro distribuisce le risorse definite nel modello di Gestione risorse.

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

Personalizzare il valore di **$ProjectName** e **$location** nello script. Il nome del gruppo di risorse è il nome del progetto seguito da **rg**. È necessario specificare il nome del gruppo di risorse nel flusso di lavoro.

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

Copiare l'output JSON e archiviarlo come segreto GitHub nel repository GitHub. Vedere [prerequisite](#prerequisites) se non è ancora presente un repository.

1. Dal repository GitHub selezionare la scheda **Impostazioni** .
1. Scegliere **Secret** dal menu a sinistra.
1. Immettere i valori seguenti:

    - **Nome**: AZURE_CREDENTIALS
    - **Valore**: (incollare l'output JSON)
1. Selezionare **Aggiungi segreto**.

È necessario specificare il nome del segreto nel flusso di lavoro.

## <a name="add-resource-manager-template"></a>Aggiungi modello di Gestione risorse

Aggiungere un modello di Gestione risorse al repository GitHub. Se non si dispone di un modello, è possibile usare il modello seguente. Il modello crea un account di archiviazione.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

È possibile inserire il file in un punto qualsiasi del repository. Nell'esempio di flusso di lavoro nella sezione successiva si presuppone che il file di modello sia denominato **file azuredeploy. JSON**e che sia archiviato in una cartella denominata **templates** nella radice del repository.

## <a name="create-workflow"></a>Crea flusso di lavoro

Il file del flusso di lavoro deve essere archiviato nella cartella **. github/Workflow** nella radice del repository. L'estensione del file del flusso di lavoro può essere **yml** o **YAML**.

È possibile creare un file del flusso di lavoro e quindi eseguire il push o il caricamento del file nel repository oppure utilizzare la procedura seguente:

1. Dal repository GitHub selezionare **azioni** dal menu in alto.
1. Selezionare **nuovo flusso di lavoro**.
1. Selezionare **configura un flusso di lavoro**.
1. Rinominare il file del flusso di lavoro se si preferisce un nome diverso da **Main. yml**. Ad esempio: **deployStorageAccount. yml**.
1. Sostituire il contenuto del file yml con quanto segue:

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

    - **nome**: il nome del flusso di lavoro.
    - **on**: il nome degli eventi di GitHub che attiva il flusso di lavoro. Il flusso di lavoro viene attivato quando si verifica un evento push nel ramo master, che modifica almeno uno dei due file specificati. I due file sono il file del flusso di lavoro e il file di modello.

        > [!IMPORTANT]
        > Verificare che i due file e i relativi percorsi corrispondano.
    - **processi**: un'esecuzione del flusso di lavoro è costituita da uno o più processi. È disponibile un solo processo denominato **deploy-storage-account-template**.  Questo processo prevede tre passaggi:

        - **Estrai codice sorgente**.
        - **Accedere ad Azure**.

            > [!IMPORTANT]
            > Verificare che il nome del segreto corrisponda a quello salvato nel repository. Vedere [configurare le credenziali di distribuzione](#configure-deployment-credentials).
        - **Distribuire il modello ARM**. Sostituire il valore di **resourceGroupName**.  Se è stato usato lo script dell'interfaccia della riga di comando di Azure in [configure deployment Credentials](#configure-deployment-credentials), il nome del gruppo di risorse generato è il nome del progetto con l'aggiunta di **RG** Verificare il valore di **templateLocation**.

1. Selezionare **Avvia commit**.
1. Selezionare **commit direttamente nel ramo master**.
1. Selezionare Esegui **commit nuovo file** (o **commit modifiche**).

Poiché il flusso di lavoro è configurato per essere attivato dal file del flusso di lavoro o dal file di modello da aggiornare, il flusso di lavoro viene avviato subito dopo il commit delle modifiche.

## <a name="check-workflow-status"></a>Controllare lo stato del flusso di lavoro

1. Selezionare la scheda **azioni** . Verrà visualizzato un flusso di lavoro **create deployStorageAccount. yml** . L'esecuzione del flusso di lavoro richiede 1-2 minuti.
1. Selezionare il flusso di lavoro per aprirlo.
1. Selezionare **deploy-storage-account-template** (nome processo) dal menu a sinistra. Il nome del processo è definito nel flusso di lavoro.
1. Selezionare **Distribuisci modello ARM** (Nome passaggio) per espanderlo. È possibile visualizzare la risposta dell'API REST.

## <a name="next-steps"></a>Passaggi successivi

Per un'esercitazione dettagliata che illustra il processo di creazione di un modello, vedere [esercitazione: creare e distribuire il primo modello ARM](template-tutorial-create-first-template.md).
