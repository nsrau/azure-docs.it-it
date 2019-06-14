---
title: Integrazione di Azure DevTest Labs e DevOps | Microsoft Docs
description: Informazioni su come usare i lab di Azure DevTest Labs all'interno di un'integrazione continua (CI) / pipeline di recapito continuo (CD) in un ambiente aziendale.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078924"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integrazione di Azure DevTest Labs e DevOps di Azure
DevOps è una metodologia di sviluppo software che si integra lo sviluppo del software (Dev) con operazioni (Ops) per un sistema. Questo sistema può distribuire nuove funzionalità, gli aggiornamenti e correzioni allineate agli obiettivi aziendali. Questa metodologia comprende tutti gli elementi dalla progettazione di nuove funzionalità in base a obiettivi, i modelli di utilizzo e suggerimenti dei clienti; la correzione, ripristino e protezione avanzata del sistema quando si verificano problemi. Un componente facilmente identificabile di questa metodologia è l'integrazione continua (CI) / recapito continuo (CD) della pipeline. Una pipeline di integrazione continua/distribuzione continua richiede informazioni, codice e le risorse da un commit attraverso una serie di passaggi che includono creazione, test e distribuzione, per produrre il sistema. Questo articolo è incentrato sulle diverse modalità disponibili per l'utilizzo efficiente lab all'interno di una pipeline in un ambiente aziendale. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Vantaggi dell'uso di laboratori nel flusso di lavoro DevOps 

### <a name="focused-access"></a>Accesso con lo stato attivo 
Uso di un lab come componente consente a un ecosistema specifico da associare a un gruppo limitato di persone. In genere, un team o un gruppo che viene eseguito in un'area comune o una funzionalità specifica avrà un laboratorio assegnato.   

### <a name="infrastructure-replication-in-the-cloud"></a>Replica di infrastruttura nel cloud 
Uno sviluppatore può configurare rapidamente un ecosistema di sviluppo che include una casella di sviluppo con il codice sorgente e strumenti. Lo sviluppatore può anche creare un ambiente che è quasi identico alla configurazione di produzione. Ciò risulta utile con lo sviluppo più veloce ciclo interno. 

### <a name="pre-production"></a>Pre-produzione 
Un lab nella pipeline CI/CD rende più semplice avere più diversi ambienti di pre-produzione o le macchine in esecuzione contemporaneamente per il test asincroni. Supporto diverse infrastrutture, ad esempio gli agenti di compilazione possono essere distribuite e gestite all'interno di un lab. 

## <a name="devops-with-devtest-labs"></a>DevOps con DevTest Labs 

### <a name="development--operation"></a>Sviluppo / operazione 
Un lab deve essere incentrato sulle funzioni in un'area funzionale dei team. Questo tipo di attenzione comune consente la condivisione delle risorse specifiche delle aree, ad esempio gli strumenti, script o modelli di Resource Manager. È consentita la modifica più veloce pur limitando gli effetti negativi da un gruppo più piccolo. Queste risorse condivise consentono allo sviluppatore di creare macchine virtuali di sviluppo con tutto il codice necessario, strumenti e configurazione. Essi devono essere creati dinamicamente o dispone di un sistema che consente di creare immagini di base con le personalizzazioni. Non solo se lo sviluppatore può creare macchine virtuali, ma è anche possibile creare ambienti DevTest Labs in base ai modelli necessari per creare le risorse di Azure appropriate nell'ambiente di laboratorio. Eventuali modifiche distruttiva lavoro può avvenire nell'ambiente lab senza influire su altri utenti. Si consideri lo scenario in cui il prodotto è un sistema autonomo è installato nel computer del cliente. In questo scenario DevTest Labs ha migliorato la creazione della macchina virtuale che include l'installazione di software aggiuntivo usando elementi e pre-compilazione di configurazioni del cliente per il ciclo interno rapido test del codice. 
  
## <a name="cicd-pipeline"></a>Pipeline CI/CD 
La pipeline di integrazione continua/recapito Continuo è uno dei componenti fondamentali di DevOps che gestiscono lo spostamento di codice da una richiesta pull dello sviluppatore, si integra con il codice esistente e la distribuisce all'ecosistema di produzione. Tutte le risorse non devono essere necessariamente all'interno di un lab. Ad esempio, un host di Jenkins può essere includere la configurazione all'esterno di lab come risorsa più persistente. Di seguito sono riportati alcuni esempi specifici dell'integrazione labs nella pipeline. 

### <a name="build"></a>Compilare 
La pipeline di compilazione è incentrata sulla creazione di un pacchetto di componenti che verrà testata insieme per essere passato alla pipeline di rilascio. Labs possono far parte della pipeline di compilazione come percorso per gli agenti di compilazione e altre risorse di supporto. La possibilità di creare dinamicamente l'infrastruttura consente un maggiore controllo. Con la possibilità di disporre di più ambienti in un lab, ogni compilazione può essere eseguito in modo asincrono quando si usa l'ID della compilazione come parte delle informazioni di ambiente per identificare le risorse per la compilazione specifica.   

Per gli agenti di compilazione, capacità del lab per limitare l'accesso aumenta la sicurezza e riduce la possibilità di danneggiamento accidentale.  

### <a name="test"></a>Test 
DevTest Labs consente a una pipeline di integrazione continua/distribuzione continua automatizzare la creazione della risorsa di Azure (macchine virtuali, ambienti) che può essere utilizzato per test automatizzati e manuali. Le macchine virtuali verranno create utilizzando gli elementi o formule che utilizzano le informazioni del processo di compilazione per creare le diverse configurazioni personalizzate necessarie per il test.   

### <a name="release"></a>Versione 
DevTest Labs viene comunemente usato per la verifica nell'area di rilascio prima di distribuita il codice. È simile all'esecuzione di test nella sezione della compilazione. Le risorse di produzione non devono essere distribuite all'interno di DevTest Labs. 

### <a name="customization"></a>Personalizzazione 
In DevOps di Azure, esistono attività esistenti che consentono la manipolazione delle macchine virtuali e ambienti all'interno di Lab specifici. Mentre servizi DevOps di Azure sono un modo per gestire le pipeline CI/CD, è possibile integrare i lab a qualsiasi sistema che supporta la possibilità di chiamare le API REST, eseguire gli script di PowerShell o usare Azure CLI. 

Mentre alcuni gestori di pipeline di integrazione continua/recapito Continuo hanno plug-in open source esistenti che possono gestire le risorse all'interno di Azure e DevTest Labs. Potrebbe essere necessario utilizzare alcuni script personalizzati per soddisfare le esigenze specifiche della pipeline.  Con tali presupposti, durante l'esecuzione di un'attività, un'entità servizio viene usata con il ruolo appropriato per ottenere l'accesso al lab. L'entità servizio deve in genere l'accesso del ruolo Collaboratore al lab. Per altre informazioni, vedere [integrare Azure DevTests Labs nella tua Azure continua integrazione e recapito pipeline DevOps](devtest-lab-integrate-ci-cd-vsts.md). 
 