---
title: Creare una factory di immagini in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come creare una factory di immagini personalizzate in Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: cf1bb31614c04d6073bc40c510fc43b2f8e4e189
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622636"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Creare una factory di immagini personalizzate in Azure DevTest Labs
Questo articolo illustra come configurare una factory di immagini personalizzate usando gli script di esempio disponibile nel [repository Git](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>Che cos'è una factory di immagini?
Una factory di immagini è una soluzione di configurazione come codice che compila e distribuisce le immagini automaticamente a intervalli regolari con tutte le configurazioni desiderate. Le immagini nella factory immagine siano sempre aggiornate e la manutenzione è quasi pari a zero dopo l'intero processo è automatizzato. E, poiché tutte le configurazioni necessarie sono già nell'immagine, Salva l'ora di configurare manualmente il sistema dopo aver creata una macchina virtuale con il sistema operativo di base.

L'acceleratore significativa per ottenere un desktop per gli sviluppatori a uno stato pronto in DevTest Labs Usa immagini personalizzate. Lo svantaggio di immagini personalizzate è che c'è qualcosa aggiuntivi per mantenere nell'ambiente di laboratorio. Ad esempio, le versioni di valutazione dei prodotti scadono nel corso del tempo (o) non sono applicati gli aggiornamenti di sicurezza rilasciato di recente, che forzare per aggiornare periodicamente l'immagine personalizzata. Con una factory di immagini, si ha una definizione dell'immagine archiviata nel controllo del codice sorgente e dispone di un processo automatizzato per produrre immagini personalizzate in base alla definizione.

La soluzione offre la velocità di creazione di macchine virtuali da immagini personalizzate eliminando i costi di manutenzione periodica aggiuntiva. Con questa soluzione, è possibile automaticamente creare immagini personalizzate, distribuirli ad altri DevTest Labs e ritirare le vecchie immagini. Nel video seguente illustra la factory di immagini e come viene implementato con DevTest Labs.  Tutti gli script di Azure Powershell sono liberamente disponibili e si trova qui: [ https://aka.ms/dtlimagefactory ](https://aka.ms/dtlimagefactory).

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Visualizzazione generale della soluzione
La soluzione offre la velocità di creazione di macchine virtuali da immagini personalizzate eliminando i costi di manutenzione periodica aggiuntiva. Con questa soluzione, è possibile creare immagini personalizzate automaticamente e distribuirli ad altri DevTest Labs. Si usa Azure DevOps (denominato in precedenza Visual Studio Team Services) come il motore di orchestrazione per l'automazione di tutte le operazioni in DevTest Labs.

![Visualizzazione generale della soluzione](./media/create-image-factory/high-level-view-of-solution.png)

È presente una [estensione di Visual Studio Team Services per DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) che consente di eseguire questi passaggi individuali:

- Creare un'immagine personalizzata
- Creare una macchina virtuale
- Elimina macchina virtuale
- Crea ambiente
- Elimina ambiente
- Popolare ambiente

Uso dell'estensione DevTest Labs è un modo semplice per iniziare a creare automaticamente le immagini personalizzate in DevTest Labs.

È un'implementazione alternativa usando script di PowerShell per uno scenario più complesso. Tramite PowerShell, è possibile automatizzare completamente una factory di immagini basata su DevTest Labs che possono essere utilizzati in di integrazione continua e recapito continuo (CI/CD) toolchain. Sono i principi di seguito in questa soluzione alternativa:

- Aggiornamenti comuni non devono richiedere alcuna modifica per la factory di immagini. (ad esempio, aggiungendo un nuovo tipo di immagine personalizzata, ritiro automaticamente immagini precedenti, aggiungendo un DevTest Labs per ricevere le immagini personalizzate e così via nuovo 'endpoint').
- Le modifiche più comuni sono supportate dal controllo del codice sorgente (infrastruttura come codice)
- DevTest Labs ricezione immagini personalizzate non siano nella stessa sottoscrizione di Azure (labs estendersi su più sottoscrizioni)
- Gli script di PowerShell devono essere riutilizzabili in modo che è possibile creare rapidamente le factory aggiuntive in base alle esigenze

## <a name="next-steps"></a>Passaggi successivi
Passare all'articolo successivo in questa sezione: [Eseguire una factory di immagini da Azure DevOps](image-factory-set-up-devops-lab.md)
