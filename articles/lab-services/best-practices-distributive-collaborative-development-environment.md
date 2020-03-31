---
title: Sviluppo collaborativo distribuito delle risorse di Azure DevTest LabsDistributed collaborative development of Azure DevTest Labs resources
description: Vengono illustrate le procedure consigliate per l'impostazione di un ambiente di sviluppo distribuito e collaborativo per sviluppare risorse DevTest Labs.
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9469591b1945adaffca973828d619d5d06655262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170113"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Procedure consigliate per lo sviluppo distribuito e collaborativo delle risorse di Azure DevTest LabsBest practices for distributed and collaborative development of Azure DevTest Labs resources
Lo sviluppo collaborativo distribuito consente a team o persone diverse di sviluppare e gestire una base di codice. Per avere successo, il processo di sviluppo dipende dalla capacità di creare, condividere e integrare le informazioni. Questo principio di sviluppo chiave può essere usato all'interno di Azure DevTest Labs.This key development principle can be used within Azure DevTest Labs. Esistono diversi tipi di risorse all'interno di un lab che vengono comunemente distribuiti tra laboratori diversi all'interno di un'azienda. I diversi tipi di risorse sono focalizzati su due aree:

- Risorse archiviate internamente all'interno del lab (in laboratorio)
- Risorse archiviate in [archivi esterni connessi al lab](devtest-lab-add-artifact-repo.md) (basato su repository di codice). 

In questo documento vengono descritte alcune procedure consigliate che consentono la collaborazione e la distribuzione tra più team garantendo al contempo personalizzazione e qualità a tutti i livelli.

## <a name="lab-based-resources"></a>Risorse basate su laboratorio

### <a name="custom-virtual-machine-images"></a>Immagini di macchine virtuali personalizzate
È possibile avere un'origine comune di immagini personalizzate che vengono distribuite nei laboratori ogni notte. Per informazioni dettagliate, vedere [Factory di immagini](image-factory-create.md).    

### <a name="formulas"></a>Formule
[Le formule](devtest-lab-manage-formulas.md) sono specifiche del lab e non dispongono di un meccanismo di distribuzione. I membri del laboratorio fanno tutto lo sviluppo di formule. 

## <a name="code-repository-based-resources"></a>Risorse basate sul repository di codiceCode repository-based resources
Esistono due caratteristiche diverse che si basano su archivi di codice, elementi e ambienti. Questo articolo illustra le funzionalità e illustra come configurare in modo più efficace i repository e il flusso di lavoro per consentire la possibilità di personalizzare gli elementi e gli ambienti disponibili a livello di organizzazione o team.  Questo flusso di lavoro è basato sulla strategia standard di [diramazione](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops)del controllo del codice sorgente. 

### <a name="key-concepts"></a>Concetti chiave
Le informazioni di origine per gli elementi includono metadati, script. Le informazioni di origine per gli ambienti includono metadati e modelli di Resource Manager con qualsiasi file di supporto come script di PowerShell, script DSC, file zip e così via.  

### <a name="repository-structure"></a>Struttura del repository  
La configurazione più comune per il controllo del codice sorgente (SCC) consiste nell'impostare una struttura a più livelli per l'archiviazione dei file di codice (modelli di Gestione risorse, metadati e script) utilizzati nei lab. In particolare, creare repository diversi per archiviare le risorse gestite dai diversi livelli dell'azienda:   

- Risorse a livello aziendale.
- Risorse a livello di Business Unit/Division
- Risorse specifiche del team.

Ognuno di questi livelli si collega a un repository diverso in cui il ramo master deve essere della qualità di produzione. I [rami](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) in ogni repository sarebbero per lo sviluppo di tali risorse specifiche (elementi o modelli). Questa struttura si allinea bene con DevTest Labs in quanto è possibile connettere facilmente più repository e più rami contemporaneamente ai laboratori dell'organizzazione. Il nome del repository è incluso nell'interfaccia utente (UI) per evitare confusione quando sono presenti nomi, descrizioni e server di pubblicazione identici.
     
Il diagramma seguente mostra due repository: un repository aziendale gestito dalla Divisione IT e un repository di divisione gestito dalla divisione R&D.

![Esempio di ambiente di sviluppo distributivo e collaborativo](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Questa struttura a più livelli consente lo sviluppo mantenendo un livello di qualità superiore nella succursale master pur avendo più repository connessi a un laboratorio consente una maggiore flessibilità.

## <a name="next-steps"></a>Passaggi successivi    
Vedere gli articoli seguenti:

- Aggiungere un repository a un lab usando il portale di Azure o il modello Gestione risorse di [AzureAdd](add-artifact-repository.md) a repository to a lab using either the Azure [portal](devtest-lab-add-artifact-repo.md) or via Azure Resource Management template
- [Elementi DevTest LabsDevTest Labs artifacts](devtest-lab-artifact-author.md)
- [Ambienti DevTest Labs](devtest-lab-create-environment-from-arm.md).
