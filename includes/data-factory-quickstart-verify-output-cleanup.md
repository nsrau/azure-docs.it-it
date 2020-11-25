---
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
author: linda33wj
ms.author: jingwang
ms.openlocfilehash: 34848b638ff0c7f7b9d1a2f3e5894339f8310ccc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013361"
---
## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

La pipeline crea automaticamente la cartella di output nel contenitore BLOB adftutorial, quindi copia il file emp.txt dalla cartella di input a quella di output. 

1. Nella pagina del contenitore **adftutorial** del portale di Azure selezionare **Aggiorna** per visualizzare la cartella di output. 
    
    ![Screenshot che mostra la pagina del contenitore in cui è possibile aggiornare la pagina.](media/data-factory-quickstart-verify-output-cleanup/output-refresh.png)

2. Nell'elenco delle cartelle selezionare **output**. 

3. Verificare che **emp.txt** venga copiato nella cartella di output. 

    ![Screenshot che mostra il contenuto della cartella di output.](media/data-factory-quickstart-verify-output-cleanup/output-file.png)

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile eseguire la pulizia delle risorse create nel corso della guida introduttiva in due modi. Si può eliminare il [gruppo di risorse di Azure](../articles/azure-resource-manager/management/overview.md), in modo da includere tutte le risorse del gruppo. Se invece si vogliono mantenere intatte le altre risorse, eliminare solo la data factory creata in questa esercitazione.

Se si elimina un gruppo di risorse, vengono eliminate tutte le risorse in esso contenute, incluse le data factory. Eseguire il comando seguente per eliminare l'intero gruppo di risorse: 

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

> [!Note]
> L'eliminazione di un gruppo di risorse può richiedere tempo. Attendere il completamento del processo.

Per eliminare solo la data factory e non l'intero gruppo di risorse, eseguire il comando seguente: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```