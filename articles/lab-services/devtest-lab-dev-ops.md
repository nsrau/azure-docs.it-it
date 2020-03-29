---
title: Integrazione di Azure DevTest Labs e DevOps Documenti Microsoft
description: Informazioni su come usare i lab di Azure DevTest Labs in una pipeline di integrazione continua (CI)/distribuzione continua (CD) in un ambiente aziendale.
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
DevOps è una metodologia di sviluppo software che integra lo sviluppo software (Dev) con le operazioni (Ops) per un sistema. Questo sistema può offrire nuove funzionalità, aggiornamenti e correzioni in linea con gli obiettivi aziendali. Questa metodologia comprende tutto, dalla progettazione di nuove funzionalità in base agli obiettivi, ai modelli di utilizzo e al feedback dei clienti; per correggere, ripristinare e indurire il sistema quando si verificano problemi. Una componente facilmente identificabile di questa metodologia è la pipeline di integrazione continua (CI)/consegna continua (CD). Una pipeline CI/CD accetta informazioni, codice e risorse da un commit tramite una serie di passaggi che includono la compilazione, il test e la distribuzione per produrre il sistema. Questo articolo è incentrato sui diversi modi per utilizzare in modo efficace i lab all'interno di una pipeline in un ambiente aziendale. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Vantaggi dell'utilizzo di laboratori nel flusso di lavoro DevOps 

### <a name="focused-access"></a>Accesso mirato 
L'utilizzo di un laboratorio come componente consente di associare un ecosistema specifico a un gruppo limitato di persone. In genere, a un team o a un gruppo che lavora in un'area comune o in una feature specifica verrà loro assegnato un lab.   

### <a name="infrastructure-replication-in-the-cloud"></a>Replica dell'infrastruttura nel cloud 
Uno sviluppatore può configurare rapidamente un ecosistema di sviluppo che include una casella di sviluppo con codice sorgente e strumenti. Lo sviluppatore può anche creare un ambiente quasi identico alla configurazione di produzione. Aiuta con lo sviluppo del ciclo interno più veloce. 

### <a name="pre-production"></a>Pre-produzione 
La presenza di un lab nella pipeline CI/CD semplifica l'esecuzione di più ambienti di pre-produzione diversi o di computer contemporaneamente per i test asincroni. È possibile distribuire e gestire diverse infrastrutture di supporto, ad esempio gli agenti di compilazione, all'interno di un lab. 

## <a name="devops-with-devtest-labs"></a>DevOps con DevTest Labs 

### <a name="development--operation"></a>Sviluppo / Funzionamento 
Un laboratorio deve essere incentrato su un team che lavora in un'area delle funzionalità. Questo obiettivo comune consente la condivisione di risorse specifiche dell'area, ad esempio strumenti, script o modelli di Resource Manager.This common focus allows for sharing of area-specific resources, like tools, scripts, or Resource Manager templates. Consente modifiche più veloci limitando gli effetti negativi a un gruppo più piccolo. Queste risorse condivise consentono allo sviluppatore di creare macchine virtuali di sviluppo con tutto il codice, gli strumenti e la configurazione necessari. Possono essere creati in modo dinamico o dispongono di un sistema che crea immagini di base con le personalizzazioni. Non solo lo sviluppatore può creare macchine virtuali, ma può anche creare ambienti DevTest Labs in base ai modelli necessari per creare le risorse di Azure appropriate nel lab. Eventuali modifiche o lavoro distruttivo possono essere eseguite contro l'ambiente di laboratorio senza influire su nessun altro. Si consideri lo scenario in cui il prodotto è un sistema autonomo installato nel computer del cliente. In questo scenario, DevTest Labs ha migliorato la creazione di macchine virtuali che include l'installazione di software aggiuntivo utilizzando gli elementi e pre-creazione di configurazioni del cliente per un test del ciclo interno più rapido del codice. 
  
## <a name="cicd-pipeline"></a>Pipeline CI/CD 
La pipeline CI/CD è uno dei componenti critici in DevOps che spostano il codice da una richiesta pull dello sviluppatore, lo integra con il codice esistente e lo distribuisce nell'ecosistema di produzione. Non è necessario che tutte le risorse si riperchino a un laboratorio. Ad esempio, un host Jenkins potrebbe essere configurato all'esterno del lab come risorsa più persistente. Di seguito sono riportati alcuni esempi specifici di integrazione di laboratori nella pipeline. 

### <a name="build"></a>Compilare 
La pipeline di compilazione è incentrata sulla creazione di un pacchetto di componenti che verranno testati insieme per essere passati alla pipeline di rilascio. I lab possono far parte della pipeline di compilazione come posizione per gli agenti di compilazione e altre risorse di supporto. La possibilità di creare dinamicamente l'infrastruttura consente un maggiore controllo. Con la possibilità di avere più ambienti in un lab, ogni compilazione può essere eseguita in modo asincrono durante l'utilizzo dell'ID di compilazione come parte delle informazioni sull'ambiente per identificare in modo univoco le risorse per la compilazione specifica.   

Per gli agenti di compilazione, la capacità del laboratorio di limitare l'accesso aumenta la sicurezza e riduce la possibilità di danneggiamenti accidentali.  

### <a name="test"></a>Test 
DevTest Labs consente a una pipeline CI/CD di automatizzare la creazione di ambienti di risorse di Azure che possono essere usate per il test automatico e manuale. Le macchine virtuali verrebbero create usando elementi o formule che usano le informazioni del processo di compilazione per creare le diverse configurazioni personalizzate necessarie per il test.   

### <a name="release"></a>Versione 
DevTest Labs viene comunemente usato per la verifica nella sezione relativa alla versione prima della distribuzione del codice. È simile ai test nella sezione Compilazione.It's similar to testing in the Build section. Le risorse di produzione non devono essere distribuite all'interno di DevTest Labs.Production resources should't be deployed within DevTest Labs. 

### <a name="customization"></a>Personalizzazione 
In DevOps di Azure sono disponibili attività esistenti che consentono la modifica di macchine virtuali e ambienti all'interno di lab specifici. Mentre i servizi DevOps di Azure sono un modo per gestire la pipeline CI/CD, è possibile integrare il lab in qualsiasi sistema che supporti la possibilità di chiamare API REST, eseguire script di PowerShell o usare l'interfaccia della riga di comando di Azure.While Azure DevOps Services are one way to manage the CI/CD pipeline, you can integrate the lab into any system that supports the ability to call REST APIs, execute PowerShell scripts, or use Azure CLI. 

Mentre alcuni gestori di pipeline CI/CD dispongono di plug-in open source esistenti in grado di gestire le risorse all'interno di Azure e DevTest Labs. Potrebbe essere necessario utilizzare alcuni script personalizzati per soddisfare le esigenze specifiche della pipeline.  Tenendo presente questo aspetto, quando si esegue un'attività, un'entità servizio viene usata con il ruolo appropriato per ottenere l'accesso al lab. L'entità servizio richiede in genere l'accesso al ruolo collaboratore al lab. Per altre informazioni, vedere [Integrare i laboratori DevTests di Azure nella pipeline di distribuzione e integrazione continua di Azure DevOps.For](devtest-lab-integrate-ci-cd-vsts.md)more information, see Integrate Azure DevTests Labs into your Azure DevOps continuous integration and delivery pipeline . 
 