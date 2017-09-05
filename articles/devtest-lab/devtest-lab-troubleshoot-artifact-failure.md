---
title: Diagnosticare gli errori di elemento in una VM di Azure DevTest Labs | Microsoft Docs
description: Informazioni su come risolvere i problemi relativi a errori degli elementi in DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: e4f2946d0ba0756f36622aded0e8594acabb9527
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnosticare errori di elementi nel lab 
Dopo aver creato un elemento, è possibile verificare se l'esito è positivo o negativo. I log degli elementi in DevTest Labs forniscono informazioni che è possibile usare per diagnosticare un errore di elemento. Esistono due modi diversi per visualizzare le informazioni sul log degli elementi per una VM Windows.

> [!NOTE]
> Per garantire che gli errori vengano identificati e descritti correttamente, è importante che l'elemento sia strutturato in modo corretto. Per informazioni su come costruire correttamente un elemento, vedere [Creare elementi personalizzati](devtest-lab-artifact-author.md). Per vedere un esempio di elemento strutturato correttamente, consultare questo elemento [Testare i tipi di parametri](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

## <a name="troubleshoot-artifact-failures-using-the-azure-portal"></a>Risolvere i problemi relativi agli errori di elemento mediante il portale di Azure
Per usare il portale di Azure al fine di diagnosticare gli errori durante la creazione dell'elemento, seguire questi passaggi:

1. Nell'elenco delle risorse selezionare il proprio lab.

2. Scegliere la VM Windows che include l'elemento da analizzare.

3. Nel riquadro sinistro sotto **GENERALE** scegliere **Elementi**. Viene visualizzato un elenco di elementi associati a tale VM che indica il nome dell'elemento e il suo stato.

   ![Esempio di archivio git dell’elemento](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Scegliere un elemento che mostra lo stato **Failed**. L'elemento viene aperto e viene visualizzato un messaggio di estensione che include i dettagli sull'errore dell'elemento.

   ![Esempio di archivio git dell’elemento](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-vm"></a>Risoluzione dei problemi di elemento dall'interno della VM
Per visualizzare i log degli elementi all'interno della macchina virtuale, seguire questi passaggi:

1. Accedere alla VM che contiene l'elemento da analizzare.

2. Passare a C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.9\Status dove "1.9" è il numero di versione CSE.

   ![Esempio di archivio git dell’elemento](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Aprire il file **status** per visualizzare le informazioni che consentono di diagnosticare gli errori di elemento per la VM.




[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Post di blog correlati
* [Aggiungere una VM a un dominio di AD esistente usando un modello di Resource Manager in Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [Aggiungere un archivio Git a un lab](devtest-lab-add-artifact-repo.md).


