---
title: Pulsante Deploy to Azure per la distribuzione in Azure
description: Usare il pulsante per distribuire modelli di Azure Resource Manager da un repository GitHub.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 88436eac970b252d7b0bc7bccee4131e06e9e0cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77109040"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Usare un pulsante di distribuzione per distribuire i modelli dal repository GitHub

Questo articolo descrive come usare il pulsante **Distribuisci in Azure** per distribuire i modelli da un repository GitHub. È possibile aggiungere il pulsante direttamente al file README.md nel repository GitHub o a una pagina Web che fa riferimento al repository.

## <a name="use-common-image"></a>Usa immagine comune

Per aggiungere il pulsante alla pagina Web o al repository, usare l'immagine seguente:

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

L'immagine viene visualizzata come segue:

![Pulsante Deploy to Azure per la distribuzione in Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Crea URL per la distribuzione del modello

Per creare l'URL per il modello, iniziare con l'URL non elaborato del modello nel repository:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Quindi, l'URL lo codifica. È possibile usare un codificatore online o eseguire un comando. L'esempio di PowerShell seguente illustra come codificare un valore in URL.

```powershell
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

## <a name="deploy-the-template"></a>Distribuire il modello

Per testare la soluzione completa, selezionare il pulsante seguente:

[![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

Nel portale viene visualizzato un riquadro che consente di specificare facilmente i valori dei parametri. I parametri vengono precompilati con i valori predefiniti del modello.

![Usare il portale per la distribuzione](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui modelli, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](template-syntax.md).
