---
title: Pulsante Deploy to Azure per la distribuzione in Azure
description: Usare il pulsante per distribuire modelli di Azure Resource Manager da un repository GitHub.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 25ec5fd7a0c5b356097412ab6f1765cb0886522a
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555264"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Usare un pulsante di distribuzione per distribuire i modelli dal repository GitHub

Questo articolo descrive come usare il pulsante **Distribuisci in Azure** per distribuire i modelli da un repository GitHub. È possibile aggiungere il pulsante direttamente al file README.md nel repository GitHub. In alternativa, è possibile aggiungere il pulsante a una pagina Web che fa riferimento al repository.

L'ambito di distribuzione è determinato dallo schema del modello. Per altre informazioni, vedere:

* [gruppi di risorse](deploy-to-resource-group.md)
* [sottoscrizioni](deploy-to-subscription.md)
* [gruppi di gestione](deploy-to-management-group.md)
* [tenant](deploy-to-tenant.md)

## <a name="use-common-image"></a>Usa immagine comune

Per aggiungere il pulsante alla pagina Web o al repository, usare l'immagine seguente:

```markdown
![Deploy to Azure](https://aka.ms/deploytoazurebutton)
```

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

L'immagine viene visualizzata come segue:

![Pulsante Deploy to Azure per la distribuzione in Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Crea URL per la distribuzione del modello

Per creare l'URL per il modello, iniziare con l'URL non elaborato del modello nel repository. Per visualizzare l'URL non elaborato, selezionare **RAW**.

:::image type="content" source="./media/deploy-to-azure-button/select-raw.png" alt-text="Seleziona RAW":::

Il formato dell'URL è:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Convertire quindi l'URL in un valore con codifica URL. È possibile usare un codificatore online o eseguire un comando. L'esempio di PowerShell seguente illustra come codificare un valore in URL.

```powershell
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
[uri]::EscapeDataString($url)
```

L'URL di esempio ha il valore seguente quando viene codificato l'URL.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Ogni collegamento inizia con lo stesso URL di base:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Aggiungere il collegamento al modello con codifica URL alla fine dell'URL di base.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Si dispone dell'URL completo per il collegamento.

In genere, il modello viene ospitato in un repository pubblico. Se si usa un repository privato, è necessario includere un token per accedere al contenuto non elaborato del modello. Il token generato da GitHub è valido solo per un breve periodo di tempo. È necessario aggiornare spesso il collegamento.

Se si usa [git con Azure Repos](/azure/devops/repos/git/) invece di un repository GitHub, è comunque possibile usare il pulsante Distribuisci in Azure. Verificare che il repository sia pubblico. Usare l' [operazione Items](/rest/api/azure/devops/git/items/get) per ottenere il modello. La richiesta deve avere il formato seguente:

```http
https://dev.azure.com/{organization-name}/{project-name}/_apis/git/repositories/{repository-name}/items?scopePath={url-encoded-path}&api-version=6.0
```

Codificare questo URL della richiesta.

## <a name="create-deploy-to-azure-button"></a>Pulsante Crea Deploy to Azure

Infine, inserire il collegamento e l'immagine insieme.

Per aggiungere il pulsante con Markdown nel file README.md nel repository GitHub o in una pagina Web, usare:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

Per HTML, usare:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

Per git con repository di Azure, il pulsante è nel formato:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fdev.azure.com%2Forgname%2Fprojectname%2F_apis%2Fgit%2Frepositories%2Freponame%2Fitems%3FscopePath%3D%252Freponame%252Fazuredeploy.json%26api-version%3D6.0)
```

## <a name="deploy-the-template"></a>Distribuire il modello

Per testare la soluzione completa, selezionare il pulsante seguente:

[![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

Nel portale viene visualizzato un riquadro che consente di specificare facilmente i valori dei parametri. I parametri vengono precompilati con i valori predefiniti del modello.

![Usare il portale per la distribuzione](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui modelli, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](template-syntax.md).
