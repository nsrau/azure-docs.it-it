---
title: Connettere app e integrare dati con i flussi di lavoro - App per la logica di Azure | Documentazione Microsoft
description: Creare flussi di lavoro e automatizzare i processi connettendo app e integrando dati con App per la logica di Azure.
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 07765c05-72a6-4169-a8ab-f6420bfbaf07
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/23/2017
ms.author: klam
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 64af585f81d39daaa5373d7cf080404ee5f1b037
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017


---
<a id="what-are-logic-apps" class="xliff"></a>

# Cosa sono le app per la logica?
Le app per la logica consentono di semplificare e implementare flussi di lavoro e integrazioni scalabili nel cloud. Offrono una finestra di progettazione visiva per modellare e automatizzare il processo come una serie di passaggi definita flusso di lavoro.  Nel cloud e in locale sono disponibili [numerosi connettori](../connectors/apis-list.md) per una rapida integrazione in servizi e protocolli.  Un'app per la logica viene avviata con un trigger (corrispondente ad esempio all'aggiunta di un account a Dynamics CRM) e dopo l'attivazione può avviare molte combinazioni di azioni, conversioni e logica condizionale.

Ecco alcuni vantaggi dell'uso delle app per la logica:  

* Risparmio di tempo nella progettazione di processi complessi tramite strumenti di progettazione di facile comprensione
* Facile implementazione di modelli e flussi di lavoro altrimenti difficili da implementare nel codice
* Possibilità di iniziare subito la progettazione dai modelli
* Personalizzazione dell'app per la logica con API, azioni e codice personalizzati
* Connessione e sincronizzazione di sistemi diversi in locale e nel cloud
* Possibilità di sfruttare BizTalk Server, Gestione API, Funzioni di Azure e il bus di servizio di Azure con supporto ottimale dell'integrazione

Le app per la logica sono un servizio iPaaS (integration Platform as a Service) completamente gestito che consente agli sviluppatori di evitare di occuparsi di hosting, scalabilità, disponibilità e gestione.  Le prestazioni delle app per la logica aumenteranno automaticamente per soddisfare le richieste.

![Finestra di progettazione del flusso di app](media/logic-apps-what-are-logic-apps/LogicAppCapture2.png)

Come indicato, le app per la logica consentono di automatizzare i processi aziendali. Ecco alcuni esempi:  

* Spostamento di file caricati su un server FTP in Archiviazione di Azure
* Elaborazione e instradamento di ordini in sistemi locali e cloud
* Monitoraggio di tutti i tweet su un determinato argomento, analisi del sentiment e creazione di avvisi e attività per gli elementi che richiedono completamento

Tutti questi scenari possono essere configurati tramite la finestra di progettazione visiva e senza scrivere una singola riga di codice. È possibile iniziare subito a [creare la propria app per la logica][create].  Dopo che è stata scritta, un'app per la logica può essere [distribuita e riconfigurata rapidamente](../logic-apps/logic-apps-create-deploy-template.md) in più ambienti e aree.

<a id="why-logic-apps" class="xliff"></a>

## Perché usare le app per la logica?
Le app per la logica introducono velocità e scalabilità nell'ambito dell'integrazione aziendale.  La facilità d'uso della finestra di progettazione, l'ampia gamma di trigger e azioni disponibili e gli avanzati strumenti di gestione semplificano la centralizzazione delle API.  Con il passaggio delle aziende alla digitalizzazione, le app per la logica consentono di connettere tra loro sistemi legacy e all'avanguardia.

L'[account di integrazione aziendale][biztalk] consente anche di passare a scenari di integrazione avanzati con funzionalità di [messaggistica XML][xml], [gestione dei partner commerciali][tpm] e altro ancora.

* **Strumenti di progettazione facili da usare** : è possibile progettare le app per la logica interamente nel browser o con strumenti di Visual Studio. Iniziare da un trigger, ad esempio una semplice pianificazione o il momento in cui viene creato un problema in GitHub. Quindi, gestire un numero qualsiasi di azioni usando la raccolta completa di connettori.
* **Facile connessione di API**: anche le attività di composizione facili da descrivere sono difficili da implementare nel codice. Le app per la logica facilitano la connessione ai diversi sistemi. oppure si vuole connettere la propria soluzione di marketing cloud al sistema di fatturazione locale, Se si vuole centralizzare la messaggistica tra API e sistemi con un Enterprise Service Bus, ad esempio, le app per la logica sono il modo più rapido e affidabile per risolvere questi problemi.
* **Iniziare rapidamente con i modelli**: per iniziare subito è disponibile una [raccolta di modelli][templates] che consente di creare rapidamente alcune soluzioni comuni. Dalle soluzioni B2B avanzate alla semplice connettività SaaS e persino alcune soluzioni senza alcuno scopo particolare, la raccolta è il modo più veloce per iniziare a sfruttare le potenzialità delle app per la logica.
* **Estendibilità integrata** : se il connettore necessario non è disponibile, le app per la logica sono progettate per interagire con API e codice personalizzati. È possibile creare facilmente una propria app per le API da usare come connettore personalizzato oppure chiamare una [funzione di Azure](https://functions.azure.com) per eseguire frammenti di codice su richiesta. 
* **Straordinaria potenza di integrazione reale** : è possibile iniziare facilmente e svilupparsi secondo le necessità. Le app per la logica possono sfruttare facilmente la potenza di BizTalk, la soluzione di integrazione leader del settore offerta da Microsoft, che consente ai professionisti dell'integrazione di creare le soluzioni necessarie. Altre informazioni su [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).

<a id="logic-app-concepts" class="xliff"></a>

## Concetti delle app per la logica
Di seguito sono riportate alcune informazioni fondamentali per comprendere appieno l'esperienza delle app per la logica. 

* **Flusso di lavoro** : le app per la logica consentono di modellare graficamente i processi aziendali come una serie di passaggi, o flusso di lavoro.
* **Connettori gestiti** : le app per la logica devono avere accesso a dati e servizi. I connettori gestiti vengono creati in maniera specifica per assistere l'utente durante la connessione ai dati e la successiva elaborazione. Vedere i connettori attualmente disponibili nell'elenco dei [connettori gestiti][managedapis].
* **Trigger** : alcuni connettori gestiti possono fungere anche da trigger. Un trigger avvia una nuova istanza di un flusso di lavoro basata su un evento specifico, ad esempio l'arrivo di un messaggio di posta elettronica oppure una modifica nell'account di archiviazione di Azure.
* **Azioni** : ogni passaggio dopo il trigger in un flusso di lavoro viene chiamato azione. Ogni azione viene in genere mappata a un'operazione sul connettore gestito o app per le API personalizzate.
* **Enterprise Integration Pack** : per scenari di integrazione più avanzati, le app per la logica includono funzionalità di BizTalk. BizTalk è la piattaforma di integrazione leader del settore di Microsoft. Enterprise Integration Pack consente di includere facilmente la convalida, la trasformazione e molto altro nei flussi di lavoro dell'app per la logica.

<a id="getting-started" class="xliff"></a>

## Introduzione
* Per iniziare a usare le app per la logica, seguire l'esercitazione [Creare una nuova app per la logica][create].  
* [Visualizzare esempi e scenari comuni](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Le app per la logica consentono di automatizzare i processi aziendali](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Informazioni su come integrare i sistemi correnti con le app per la logica](http://channel9.msdn.com/Events/Build/2016/P462)

[biztalk]: logic-apps-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: logic-apps-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: logic-apps-enterprise-integration-accounts.md
[xml]: logic-apps-enterprise-integration-b2b.md
[templates]: logic-apps-use-logic-app-templates.md

