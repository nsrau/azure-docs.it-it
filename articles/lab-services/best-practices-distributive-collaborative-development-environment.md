---
title: Distributed sviluppo collaborativo di risorse di Azure DevTest Labs | Microsoft Docs
description: Fornisce le procedure consigliate per la configurazione di un ambiente di sviluppo distribuito e di collaborazione per lo sviluppo di risorse di DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 8ffc8ed3f84284ff69e9515cba0982790b823a37
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543776"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Le procedure consigliate per lo sviluppo distribuito e collaborazione delle risorse di Azure DevTest Labs
Sviluppo collaborativo distribuito consente diversi team o alle persone per sviluppare e gestire un codice di base. Per avere esito positivo, il processo di sviluppo dipende la possibilità di creare, condividere e integrazione delle informazioni. Questo principio allo sviluppo delle chiavi può essere utilizzato all'interno di Azure DevTest Labs. Esistono diversi tipi di risorse all'interno di un lab in genere distribuiti tra labs diversi all'interno dell'organizzazione. I diversi tipi di risorse sono incentrati in due aree:

- Risorse che sono archiviate internamente all'interno del lab (basata su lab)
- Le risorse che vengono archiviate nel [repository esterni che sono connessi al laboratorio](devtest-lab-add-artifact-repo.md) (codice basata su repository). 

Questo documento descrive alcune procedure consigliate che consentono la collaborazione e la distribuzione tra più team, garantendo la personalizzazione e la qualità a tutti i livelli.

## <a name="lab-based-resources"></a>Risorse basate su lab

### <a name="custom-virtual-machine-images"></a>Immagini di macchina virtuale personalizzata
È possibile avere un'origine comune di immagini personalizzate che vengono distribuiti ai laboratori ogni notte. Per informazioni dettagliate, vedere [factory di immagini](image-factory-create.md).    

### <a name="formulas"></a>Formule
[Le formule](devtest-lab-manage-formulas.md) sono specifici del laboratorio e non dispone di un meccanismo di distribuzione. I membri lab eseguire tutto lo sviluppo delle formule. 

## <a name="code-repository-based-resources"></a>Risorse basate su repository codice
Esistono due diverse funzionalità basate su ambienti, gli elementi e i repository di codice. Questo articolo illustra le funzionalità e su come impostare in modo più efficace i repository e del flusso di lavoro per consentire la possibilità di personalizzare gli elementi disponibili e ambienti a livello di organizzazione oppure a livello di team.  Questo flusso di lavoro si basa sullo standard [strategia di creazione rami di controllo del codice sorgente](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops). 

### <a name="key-concepts"></a>Concetti chiave
Le informazioni di origine per gli elementi sono inclusi metadati, gli script. Le informazioni di origine per gli ambienti includono i metadati e i modelli di Resource Manager con i file di supporto, ad esempio gli script di PowerShell, gli script DSC, con estensione zip e così via.  

### <a name="repository-structure"></a>Struttura del repository  
La configurazione più comune per il controllo codice sorgente (SCC) consiste nell'impostare una struttura a più livelli per l'archiviazione file di codice (modelli di Resource Manager, i metadati e gli script) che consentono di nel lab. In particolare, creare repository diversi per archiviare le risorse che sono gestite dai diversi livelli dell'azienda:   

- Risorse a livello aziendale.
- Risorse di livello di unità/divisione di Business
- Risorse specifiche del team.

Ognuno di questi livelli di collegamento a un altro repository in cui il ramo master deve essere di qualità di produzione. Il [rami](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) in ciascun repository sarà per lo sviluppo di tali risorse specifiche (elementi o i modelli). Questa struttura sia perfettamente allineata con DevTest Labs come è possibile connettersi facilmente più repository e più rami contemporaneamente a laboratori dell'organizzazione. Il nome del repository è incluso nell'interfaccia utente (UI) per evitare confusione quando sono presenti nomi identici, descrizione e server di pubblicazione.
     
Il diagramma seguente mostra due archivi: un repository aziendale che viene mantenuto per la divisione IT e un repository di divisione mantenuta per la divisione di R & D.

![Un ambiente di sviluppo collaborativo e distributive di esempio](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Questa struttura sovrapposta consente lo sviluppo, mantenendo un livello di qualità superiore al ramo master, mentre con più repository connesso a un lab consente una flessibilità maggiore.

## <a name="next-steps"></a>Passaggi successivi    
Vedere gli articoli seguenti:

- Aggiungere un repository in un lab tramite il [portale di Azure](devtest-lab-add-artifact-repo.md) o tramite [modello Azure Resource Manager](add-artifact-repository.md)
- [Elementi di DevTest Labs](devtest-lab-artifact-author.md)
- [Gli ambienti DevTest Labs](devtest-lab-create-environment-from-arm.md).
