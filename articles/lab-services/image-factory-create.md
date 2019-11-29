---
title: Creare una factory di immagini in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come configurare una factory di immagini personalizzata usando gli script di esempio disponibili nel repository git.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: 7779914d9681d0f80cab9568da6a20b15e3a2eb1
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74560011"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Creare una factory di immagini personalizzata in Azure DevTest Labs
Questo articolo illustra come configurare una factory di immagini personalizzata usando gli script di esempio disponibili nel [repository git](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>Che cos'è una factory di immagini?
Una factory di immagini è una soluzione di configurazione come codice che compila e distribuisce automaticamente le immagini a intervalli regolari con tutte le configurazioni desiderate. Le immagini nella Factory di immagini sono sempre aggiornate e la manutenzione continua è quasi zero una volta che l'intero processo viene automatizzato. Poiché tutte le configurazioni necessarie sono già presenti nell'immagine, viene risparmiato il tempo di configurazione manuale del sistema dopo la creazione di una macchina virtuale con il sistema operativo di base.

L'acceleratore significativo per ottenere un desktop dello sviluppatore in uno stato pronto in DevTest Labs USA immagini personalizzate. Il lato negativo delle immagini personalizzate è che esistono altri elementi da mantenere nel Lab. Ad esempio, le versioni di valutazione dei prodotti scadono nel tempo (o) gli aggiornamenti della sicurezza appena rilasciati non vengono applicati, che ci impone di aggiornare periodicamente l'immagine personalizzata. Con una factory di immagini è presente una definizione dell'immagine archiviata nel controllo del codice sorgente e un processo automatizzato per produrre immagini personalizzate in base alla definizione.

La soluzione consente la velocità di creazione di macchine virtuali da immagini personalizzate eliminando al tempo stesso costi di manutenzione aggiuntivi. Con questa soluzione è possibile creare automaticamente immagini personalizzate, distribuirle ad altri DevTest Labs e ritirare le immagini precedenti. Il video seguente illustra la factory di immagini e il modo in cui viene implementato con DevTest Labs.  Tutti gli script di Azure PowerShell sono disponibili gratuitamente e si trovano qui: [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory).

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Visualizzazione di alto livello della soluzione
La soluzione consente la velocità di creazione di macchine virtuali da immagini personalizzate eliminando al tempo stesso costi di manutenzione aggiuntivi. Con questa soluzione è possibile creare automaticamente immagini personalizzate e distribuirle ad altri DevTest Labs. Si usa Azure DevOps (in precedenza Visual Studio Team Services) come motore di orchestrazione per automatizzare tutte le operazioni in DevTest Labs.

![Visualizzazione di alto livello della soluzione](./media/create-image-factory/high-level-view-of-solution.png)

È disponibile un' [estensione VSTS per DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) che consente di eseguire questi singoli passaggi:

- Creare un'immagine personalizzata
- Creare una macchina virtuale
- Elimina macchina virtuale
- Crea ambiente
- Elimina ambiente
- Popola ambiente

L'uso dell'estensione DevTest Labs è un modo semplice per iniziare a creare automaticamente immagini personalizzate in DevTest Labs.

È disponibile un'implementazione alternativa che usa script di PowerShell per uno scenario più complesso. Usando PowerShell, è possibile automatizzare completamente una factory di immagini basata su DevTest Labs che può essere usata nell'ambiente di integrazione continua e distribuzione continua (CI/CD). I principi seguiti in questa soluzione alternativa sono:

- Per gli aggiornamenti comuni non è necessario apportare modifiche alla Factory di immagini. (ad esempio, l'aggiunta di un nuovo tipo di immagine personalizzata, il ritiro automatico delle immagini precedenti, l'aggiunta di nuovi Lab DevTest "endpoint" per la ricezione di immagini personalizzate e così via).
- Le modifiche comuni sono supportate dal controllo del codice sorgente (infrastruttura come codice)
- DevTest Labs che ricevono immagini personalizzate potrebbero non trovarsi nella stessa sottoscrizione di Azure (sottoscrizioni di Labs span)
- Gli script di PowerShell devono essere riutilizzabili, in modo da poter creare altre Factory in base alle esigenze

## <a name="next-steps"></a>Passaggi successivi
Passare all'articolo successivo in questa sezione: [eseguire una factory di immagini da Azure DevOps](image-factory-set-up-devops-lab.md)
