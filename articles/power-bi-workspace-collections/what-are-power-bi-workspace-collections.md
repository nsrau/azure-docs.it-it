---
title: Informazioni sulle raccolte di aree di lavoro di Power BI
description: "Power BI Embedded consente di integrare report di Power BI in applicazioni Web o applicazioni mobili ed elimina la necessità di compilare soluzioni personalizzate."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 7df172895bb926f1715370b941964e2c29ab393d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-power-bi-workspace-collections"></a>Informazioni sulle raccolte di aree di lavoro di Power BI

Le **raccolte di aree di lavoro di Power BI** consentono di integrare i report di Power BI direttamente nelle applicazioni Web o per dispositivi mobili.

![Diagramma applicazioni](media/what-are-power-bi-workspace-collections/what-is.png)

> [!IMPORTANT]
> Le raccolte di aree di lavoro di Power BI sono deprecate e sono disponibili fino a giugno 2018 o fino alla data specificata nel contratto. È consigliabile pianificare la migrazione a Power BI Embedded per evitare interruzioni nell'applicazione. Per informazioni su come eseguire la migrazione dei dati a Power BI Embedded, vedere [Come eseguire la migrazione del contenuto delle raccolte di aree di lavoro di Power BI a Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Le raccolte di aree di lavoro di Power BI sono un **servizio di Azure** che consente agli ISV e agli sviluppatori di app di sfruttare i vantaggi dell'esperienza dati di Power BI nelle proprie applicazioni. Gli sviluppatori creano applicazioni, ognuna delle quali ha utenti e set di funzionalità specifici. Queste app possono anche contenere alcuni elementi dati predefiniti, ad esempio grafici e report, ora supportati dalle raccolte di aree di lavoro di Microsoft Power BI. Per usare l'app non è necessario avere un account Power BI. È possibile continuare ad accedere all'applicazione come in precedenza, nonché visualizzare i report di Power BI e interagire con questi senza dover richiedere licenze aggiuntive.

## <a name="licensing-for-microsoft-power-bi-workspace-collections"></a>Licenze per le raccolte di aree di lavoro di Microsoft Power BI

Nel modello di utilizzo delle **raccolte di aree di lavoro di Microsoft Power BI** l'utente finale non è responsabile della gestione delle licenze di Power BI.  Le **sessioni** sono in realtà acquistate dallo sviluppatore dell'app che impiega gli oggetti visivi e sono addebitate alla sottoscrizione che comprende tali risorse. 

## <a name="microsoft-power-bi-workspace-collections-conceptual-model"></a>Modello concettuale delle raccolte di aree di lavoro di Microsoft Power BI

![Flusso applicazione con le raccolte di aree di lavoro](media/what-are-power-bi-workspace-collections/model.png)

Come per qualsiasi altro servizio di Azure, il provisioning delle risorse per le raccolte di aree di lavoro di Power BI avviene tramite le [API di Azure Resource Manager](https://msdn.microsoft.com/library/mt712306.aspx). In questo caso la risorsa di cui si effettua il provisioning è una **raccolta di aree di lavoro di Power BI**.

## <a name="workspace-collection"></a>Raccolta di aree di lavoro

La **Raccolta di aree di lavoro** è il contenitore di Azure di primo livello per le risorse che contengono da zero a più **aree di lavoro**.  La **Raccolta di** **aree di lavoro** ha tutte le proprietà standard di Azure e gli elementi seguenti:

* **Chiavi di accesso**: chiavi usate per chiamare in modo sicuro le API di Power BI, descritte in una sezione successiva.
* **Utenti**: utenti di Azure Active Directory (AAD) con diritti di amministratore per gestire la raccolta di aree di lavoro di Power BI tramite il portale di Azure o l'API di Azure Resource Manager.
* **Area**: nell'ambito del provisioning di una **accolta di aree di lavoro** è possibile selezionare un'area in cui effettuare il provisioning. Per altre informazioni, vedere [Aree di Azure](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Area di lavoro

Un'**area di lavoro** è un contenitore di contenuto Power BI che può includere set di dati e report. L' **Area di lavoro** è vuota quando viene creata. Si crea il contenuto con Power BI Desktop e si distribuisce il file PBIX nell'area di lavoro a livello di codice con l'[API di importazione di Power BI](https://msdn.microsoft.com/library/mt711504.aspx). Invece di usare Power BI Desktop, è anche possibile creare il set di dati a livello di codice e quindi creare report nell'applicazione.

## <a name="using-workspace-collections-and-workspaces"></a>Uso delle raccolte di aree di lavoro e delle aree di lavoro

Le **raccolte di aree di lavoro** e le **aree di lavoro** sono contenitori di contenuto usate e organizzate nel modo più efficiente perché si adattino alla progettazione dell'applicazione che si sta compilando. I modi per organizzare il contenuto all'interno di questi elementi possono essere molteplici. È possibile scegliere di inserire tutto il contenuto all'interno di un'area di lavoro e di usare successivamente i token delle app per suddividere ulteriormente il contenuto tra i clienti. È possibile anche decidere di inserire tutti i clienti in aree di lavoro distinte in modo che rimangano separati. In alternativa è possibile organizzare gli utenti per area anziché per cliente. Questa progettazione flessibile consente di scegliere il modo migliore per organizzare il contenuto.

## <a name="cached-datasets"></a>Set di dati memorizzati nella cache

È possibile usare set di dati memorizzati nella cache.  Non è tuttavia possibile aggiornare i dati memorizzati nella cache dopo averli caricati all'interno di **raccolte di aree di lavoro di Microsoft Power BI**. Un set di dati memorizzato nella cache indica che si sono importati i dati in Power BI Desktop anziché usare DirectQuery.

## <a name="authentication-and-authorization-with-app-tokens"></a>Autenticazione e autorizzazione con token delle app

Le **raccolte di aree di lavoro di Microsoft Power BI** assegnano all'applicazione l'esecuzione di tutte le operazioni necessarie per l'autenticazione e l'autorizzazione utente. Non è necessario che gli utenti finali siano clienti di Azure Active Directory.  Sarà invece l'applicazione ad autorizzare il rendering di un report di Power BI nelle **raccolte di aree di lavoro di Microsoft Power BI** tramite **token di autenticazione dell'applicazione (token dell'app)**.  I **token dell'app** vengono creati nel momento in cui l'app intende eseguire il rendering di un report.

![Diagramma dell'utilizzo dei token dell'applicazione](media/what-are-power-bi-workspace-collections/app-tokens.png)

I **token di autenticazione dell'applicazione (token dell'app)** vengono usati per eseguire l'autenticazione nelle **raccolte di aree di lavoro di Microsoft Power BI**.  Esistono tre tipi di **token dell'app**:

1. Token di provisioning: si usano per il provisioning di una nuova **area di lavoro** in una **raccolta di aree di lavoro**
2. Token di sviluppo: si usano per chiamare direttamente le **API REST di Power BI**
3. Token d'incorporamento: sono usati quando si eseguono chiamate per il rendering di un report nell'iframe incorporato

Questi token vengono usati nelle varie fasi di interazione con le **raccolte di aree di lavoro di Microsoft Power BI**.  I token sono progettati in modo che sia possibile delegare le autorizzazioni dalla propria app a Power BI. Per altre informazioni, vedere [Flusso dei token delle app](app-token-flow.md).

## <a name="create-or-edit-reports-within-your-application"></a>Creare o modificare report nell'applicazione

È ora possibile modificare report esistenti o creare nuovi report direttamente nell'applicazione senza dover usare Power BI Desktop. A tale scopo è necessario che nell'area di lavoro sia presente un set di dati.

## <a name="see-also"></a>Vedere anche 

[Scenari comuni delle raccolte di aree di lavoro di Microsoft Power BI](scenarios.md)  
[Introduzione alle raccolte di aree di lavoro di Microsoft Power BI](get-started.md)  
[Esempio introduttivo](get-started-sample.md)  
[Incorporare un report](embed-report.md)  
[Autenticazione e autorizzazione con le raccolte di aree di lavoro di Power BI](app-token-flow.md)  
[Esempio di incorporamento con JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Repository Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Repository Git PowerBI-Node](https://github.com/Microsoft/PowerBI-Node)  

Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)
