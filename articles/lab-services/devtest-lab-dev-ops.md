---
title: Integrazione di Azure DevTest Labs e DevOps | Microsoft Docs
description: Informazioni su come usare i Lab di Azure DevTest Labs all'interno di una pipeline di integrazione continua (CI)/recapito continuo (CD) in un ambiente aziendale.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 62c44bfea28d47d7c32aa7ef440a40d45c314683
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67078924"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integrazione di Azure DevTest Labs e Azure DevOps
DevOps è una metodologia di sviluppo software che integra lo sviluppo software (dev) con Operations (OPS) per un sistema. Questo sistema può fornire nuove funzionalità, aggiornamenti e correzioni in linea con gli obiettivi aziendali. Questa metodologia comprende tutto, dalla progettazione di nuove funzionalità in base agli obiettivi, ai modelli di utilizzo e ai commenti dei clienti; per correggere, ripristinare e finalizzare il sistema quando si verificano problemi. Un componente facilmente identificato di questa metodologia è la pipeline di integrazione continua (CI)/recapito continuo (CD). Una pipeline di integrazione continua/recapito continuo accetta informazioni, codice e risorse da un commit attraverso una serie di passaggi che includono la compilazione, il testing e la distribuzione, per produrre il sistema. Questo articolo è incentrato sui diversi modi per usare in modo efficace i Lab all'interno di una pipeline in un ambiente aziendale. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Vantaggi dell'uso dei Lab nel flusso di lavoro DevOps 

### <a name="focused-access"></a>Accesso con stato attivo 
L'uso di un Lab come componente consente a un ecosistema specifico di associarsi a un gruppo limitato di persone. In genere, a un team o a un gruppo che lavora in un'area comune o a una funzionalità specifica viene assegnato un Lab.   

### <a name="infrastructure-replication-in-the-cloud"></a>Replica dell'infrastruttura nel cloud 
Uno sviluppatore può configurare rapidamente un ecosistema di sviluppo che include una casella di sviluppo con codice sorgente e strumenti. Lo sviluppatore può anche creare un ambiente quasi identico alla configurazione di produzione. Consente di sviluppare più velocemente il ciclo interno. 

### <a name="pre-production"></a>Pre-produzione 
Avere un Lab nella pipeline di integrazione continua/recapito continuo semplifica l'esecuzione contemporanea di più ambienti o computer di pre-produzione diversi per il test asincrono. È possibile distribuire e gestire diverse infrastrutture di supporto come gli agenti di compilazione in un Lab. 

## <a name="devops-with-devtest-labs"></a>DevOps con DevTest Labs 

### <a name="development--operation"></a>Sviluppo/operazione 
Un Lab dovrebbe concentrarsi su un team che lavora in un'area funzionale. Questo obiettivo comune è consentire la condivisione di risorse specifiche dell'area, ad esempio strumenti, script o modelli Gestione risorse. Consente modifiche più veloci limitando gli effetti negativi a un gruppo più piccolo. Queste risorse condivise consentono allo sviluppatore di creare macchine virtuali di sviluppo con tutto il codice, gli strumenti e la configurazione necessari. Possono essere creati in modo dinamico o avere un sistema che crea immagini di base con le personalizzazioni. Non solo gli sviluppatori possono creare VM, ma possono anche creare ambienti DevTest Labs basati sui modelli necessari per creare le risorse di Azure appropriate nel Lab. Eventuali modifiche o attività distruttive possono essere eseguite sull'ambiente lab senza influire su altri utenti. Si consideri lo scenario in cui il prodotto è un sistema autonomo installato nel computer del cliente. In questo scenario, DevTest Labs ha migliorato la creazione della macchina virtuale che include l'installazione di software aggiuntivo usando gli artefatti e la pre-compilazione delle configurazioni dei clienti per un test più rapido del ciclo interno del codice. 
  
## <a name="cicd-pipeline"></a>Pipeline CI/CD 
La pipeline CI/CD è uno dei componenti fondamentali di DevOps che spostano il codice da una richiesta pull dello sviluppatore, lo integra con il codice esistente e lo distribuisce nell'ecosistema di produzione. Non è necessario che tutte le risorse si trovino all'interno di un Lab. Un host Jenkins, ad esempio, può essere configurato all'esterno del Lab come risorsa più persistente. Di seguito sono riportati alcuni esempi specifici di integrazione dei Lab nella pipeline. 

### <a name="build"></a>Compilare 
La pipeline di compilazione si concentra sulla creazione di un pacchetto di componenti che verranno testati insieme per essere passati alla pipeline di rilascio. I Lab possono far parte della pipeline di compilazione come posizione per gli agenti di compilazione e altre risorse di supporto. La possibilità di creare dinamicamente l'infrastruttura consente un maggiore controllo. Con la possibilità di avere più ambienti in un Lab, ogni compilazione può essere eseguita in modo asincrono usando l'ID compilazione come parte delle informazioni sull'ambiente per identificare in modo univoco le risorse per la compilazione specifica.   

Per gli agenti di compilazione, la capacità del Lab di limitare l'accesso aumenta la sicurezza e riduce la possibilità di danneggiamenti accidentali.  

### <a name="test"></a>Test 
DevTest Labs consente una pipeline di integrazione continua/recapito continuo per automatizzare la creazione di risorse di Azure (VM, ambienti) che possono essere usate per test automatizzati e manuali. Le macchine virtuali vengono create usando artefatti o formule che usano le informazioni del processo di compilazione per creare le diverse configurazioni personalizzate necessarie per il test.   

### <a name="release"></a>Versione 
DevTest Labs viene comunemente usato per la verifica nella sezione Release prima che il codice venga distribuito. È simile ai test nella sezione di compilazione. Le risorse di produzione non devono essere distribuite in DevTest Labs. 

### <a name="customization"></a>Personalizzazione 
In Azure DevOps sono disponibili attività che consentono di manipolare macchine virtuali e ambienti all'interno di Lab specifici. Anche se Azure DevOps Services sono un modo per gestire la pipeline CI/CD, è possibile integrare il Lab in qualsiasi sistema che supporta la possibilità di chiamare le API REST, eseguire script di PowerShell o usare l'interfaccia della riga di comando di Azure. 

Mentre alcuni gestori di pipeline CI/CD hanno plug-in open source esistenti che possono gestire le risorse in Azure e DevTest Labs. Potrebbe essere necessario usare alcuni script personalizzati per soddisfare le esigenze specifiche della pipeline.  Tenendo presente questo aspetto, quando si esegue un'attività, un'entità servizio viene utilizzata con il ruolo appropriato per ottenere l'accesso al Lab. L'entità servizio richiede in genere l'accesso al ruolo Collaboratore al Lab. Per altre informazioni, vedere [integrare Azure DevTests Labs nella pipeline di integrazione e recapito continua di Azure DevOps](devtest-lab-integrate-ci-cd-vsts.md). 
 