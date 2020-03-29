---
title: Creare una factory di immagini in Azure DevTest Labs. Documenti Microsoft
description: Questo articolo illustra come configurare una factory di immagini personalizzata usando script di esempio disponibili nel repository Git (Azure DevTest Labs).
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 2c5a44a9505d4a312be521cdc3219c5e4ce95a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759449"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Creare una factory di immagini personalizzata in Azure DevTest LabsCreate a custom image factory in Azure DevTest Labs
In questo articolo viene illustrato come configurare una factory di immagini personalizzata utilizzando script di esempio disponibili nel [repository Git](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>Cos'è una fabbrica di immagini?
Una factory di immagini è una soluzione di configurazione come codice che compila e distribuisce automaticamente le immagini in modo regolare con tutte le configurazioni desiderate. Le immagini nella fabbrica di immagini sono sempre aggiornate e la manutenzione in corso è quasi zero una volta automatizzato l'intero processo. Inoltre, poiché tutte le configurazioni necessarie sono già presenti nell'immagine, consente di risparmiare tempo dalla configurazione manuale del sistema dopo la creazione di una macchina virtuale con il sistema operativo di base.

L'acceleratore significativo per ottenere un desktop di sviluppo a uno stato pronto in DevTest Labs utilizza immagini personalizzate. Lo svantaggio delle immagini personalizzate è che c'è qualcosa in più da mantenere in laboratorio. Ad esempio, le versioni di prova dei prodotti scadono nel tempo (o) gli aggiornamenti di sicurezza appena rilasciati non vengono applicati, il che ci costringe ad aggiornare periodicamente l'immagine personalizzata. Con una factory di immagini, si dispone di una definizione dell'immagine archiviata nel controllo del codice sorgente e si dispone di un processo automatizzato per produrre immagini personalizzate in base alla definizione.

La soluzione consente di velocizzare la creazione di macchine virtuali da immagini personalizzate, eliminando al contempo ulteriori costi di manutenzione continua. Con questa soluzione, è possibile creare automaticamente immagini personalizzate, distribuirle ad altri laboratori DevTest e ritirare le immagini precedenti. Nel video seguente vengono apprese la factory di immagini e il modo in cui viene implementata con DevTest Labs.  Tutti gli script di Azure Powershell [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory)sono disponibili gratuitamente e si trovano qui: .

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Vista di alto livello della soluzione
La soluzione consente di velocizzare la creazione di macchine virtuali da immagini personalizzate, eliminando al contempo ulteriori costi di manutenzione continua. Con questa soluzione, è possibile creare automaticamente immagini personalizzate e distribuirle ad altri devTest Labs.With this solution, you can automatically create custom images and distribute them to other DevTest Labs. Utilizzare Azure DevOps (in precedenza Visual Studio Team Services) come motore di orchestrazione per l'automazione di tutte le operazioni in DevTest Labs.

![Vista di alto livello della soluzione](./media/create-image-factory/high-level-view-of-solution.png)

C'è [un'estensione VSTS per DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) che consente di eseguire questi singoli passaggi:There's a VSTS Extension for DevTest Labs that enables you to execute these individual steps:

- Creare un'immagine personalizzata
- Creare una macchina virtuale
- Eliminazione di una macchina virtuale
- Crea ambiente
- Elimina ambiente
- Popola ambiente

L'uso dell'estensione DevTest Labs è un modo semplice per iniziare a creare automaticamente immagini personalizzate in DevTest Labs.Using the DevTest Labs extension is an easy way to get started with automatically creating custom images in DevTest Labs.

Esiste un'implementazione alternativa che usa lo script di PowerShell per uno scenario più complesso. Tramite PowerShell, è possibile automatizzare completamente una factory di immagini basata su DevTest Labs che può essere usata nella catena di strumenti di integrazione continua e distribuzione continua (CI/CD). I principi seguiti in questa soluzione alternativa sono:

- Gli aggiornamenti comuni non devono richiedere modifiche alla factory di immagini. Ad esempio, l'aggiunta di un nuovo tipo di immagine personalizzata, il ritiro automatico delle vecchie immagini, l'aggiunta di un nuovo 'endpoint' DevTest Labs per ricevere immagini personalizzate e così via.
- Le modifiche comuni sono supportate dal controllo del codice sorgente (infrastruttura come codice)Common changes are backed by source code control (infrastructure as code)
- DevTest Labs che ricevono immagini personalizzate potrebbero non trovarsi nella stessa sottoscrizione di Azure (i lab si estendono su sottoscrizioni)
- Gli script di PowerShell devono essere riutilizzabili in modo da poter creare in modo da poter creare in modo necessario altre factory

## <a name="next-steps"></a>Passaggi successivi
Passare all'articolo successivo in questa sezione: Eseguire una factory di immagini da DevOps di AzureMove on the next article in this section: [Run an image factory from Azure DevOps](image-factory-set-up-devops-lab.md)
