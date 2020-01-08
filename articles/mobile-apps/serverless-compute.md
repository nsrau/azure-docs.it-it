---
title: Creazione di un back-end di un'applicazione per dispositivi mobili senza server con funzioni di Azure e altri servizi
description: Informazioni sui servizi di calcolo usati per creare un back-end solido e senza server per applicazioni per dispositivi mobili.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: d59cca4b5c956134516aa2c8066894aa14e5d33d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453096"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Creazione di componenti di back-end per dispositivi mobili con servizi di calcolo
Ogni applicazione per dispositivi mobili necessita di un back-end responsabile dell'archiviazione dei dati, della logica di business e della sicurezza. La gestione dell'infrastruttura per ospitare ed eseguire il codice back-end richiede la dimensione, il provisioning e la scalabilità di più server. È anche necessario gestire gli aggiornamenti del sistema operativo e l'hardware richiesto e applicare le patch di sicurezza. Quindi, è necessario monitorare tutti questi componenti dell'infrastruttura per le prestazioni, la disponibilità e la tolleranza di errore. 

L'architettura senza server è utile per questo tipo di scenario, perché non ci sono server da gestire e nessun sistema operativo o aggiornamenti software o hardware correlati da gestire. L'architettura senza server consente di risparmiare tempo e costi per gli sviluppatori, il che significa tempi di immissione sul mercato più rapidi e concentrati sulla creazione di applicazioni

## <a name="benefits-of-compute"></a>Vantaggi del calcolo
- Astrazione dei server significa che non è necessario preoccuparsi di ospitare, applicare patch e sicurezza, che consente di concentrarsi esclusivamente sul codice.
- La scalabilità immediata ed efficiente assicura che le risorse vengano sottoposte a provisioning automatico o su richiesta a ogni scala necessaria.
- Disponibilità elevata e tolleranza di errore.
- La micro-fatturazione garantisce che vengano addebitati solo i casi in cui il codice è effettivamente in esecuzione.
- Il codice viene eseguito nel cloud scritto nel linguaggio scelto.

Usare i servizi seguenti per abilitare le funzionalità di calcolo senza server nelle app per dispositivi mobili.

## <a name="azure-functions"></a>Funzioni di Azure
[Funzioni di Azure](https://azure.microsoft.com/services/functions/) è un'esperienza di calcolo basata su eventi che è possibile usare per eseguire il codice, scritto nel linguaggio di programmazione preferito, senza doversi preoccupare dei server. Non è necessario gestire l'applicazione o l'infrastruttura per eseguirla. Le funzioni si ridimensionano su richiesta e si paga solo per il tempo di esecuzione del codice. Funzioni di Azure è un ottimo modo per implementare un'API per un'applicazione per dispositivi mobili. Sono facili da implementare e gestire e sono accessibili tramite HTTP.

**Caratteristiche principali**
- Scalabile e basato sugli eventi, in cui è possibile utilizzare trigger e associazioni per definire quando una funzione viene richiamata e a quali dati si connette.
- Usare le dipendenze perché funzioni supporta NuGet e NPM, quindi è possibile usare le librerie preferite.
- Sicurezza integrata che permette di proteggere le funzioni attivate da HTTP con provider OAuth, ad esempio Azure Active Directory, Facebook, Google, Twitter e account Microsoft.
- Integrazione semplificata con diverse offerte di [servizi di Azure](/azure/azure-functions/functions-overview#integrations) e software as a Service (SaaS).
- Sviluppo flessibile, che consente di codificare le funzioni direttamente nel portale di Azure o di configurare l'integrazione continua e distribuire il codice tramite GitHub, Azure DevOps Services e altri strumenti di sviluppo supportati.
- Il runtime di funzioni è open source ed è disponibile su [GitHub](https://github.com/azure/azure-webjobs-sdk-script).
- Esperienza di sviluppo avanzata in cui è possibile scrivere codice, testare ed eseguire il debug in locale usando l'editor preferito o l'interfaccia Web di facile utilizzo con il monitoraggio con strumenti integrati e funzionalità DevOps predefinite.
- Diversi linguaggi di programmazione e opzioni di hosting per lo sviluppo, C#ad esempio node. js, Java, JavaScript o Python.
- Il modello di determinazione prezzi con pagamento in base al consumo ti permette di pagare solo il tempo impiegato per l'esecuzione del codice.

**Riferimenti**
- [Azure portal](https://portal.azure.com)
- [Documentazione di Funzioni di Azure](/azure/azure-functions/)
- [Guida per gli sviluppatori di funzioni di Azure](/azure/azure-functions/functions-reference)
- [Guide introduttive](/azure/azure-functions/functions-create-first-function-vs-code)
- [Esempi](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Servizio app di Azure
Con [app Azure servizio](https://azure.microsoft.com/services/app-service/)è possibile creare e ospitare app Web e API RESTful nel linguaggio di programmazione preferito senza gestire l'infrastruttura. Offre la scalabilità automatica e la disponibilità elevata, supporta sia Windows che Linux e consente le distribuzioni automatiche da GitHub, Azure DevOps o qualsiasi repository git.

**Caratteristiche principali**
- Supporto di più linguaggi e Framework per ASP.NET, ASP.NET Core, Java, Ruby, node. js, PHP o Python. È anche possibile eseguire PowerShell e altri script o eseguibili come servizi in background.
- Ottimizzazione DevOps tramite l'integrazione e la distribuzione continue con Azure DevOps, GitHub, BitBucket, Docker Hub o Azure Container Registry. Gestisci le tue app nel servizio app usando Azure PowerShell o l'interfaccia della riga di comando multipiattaforma.
- Scalabilità globale con disponibilità elevata per la scalabilità orizzontale o automatica manuale o automatica.
- Connessioni a piattaforme SaaS e dati locali per scegliere tra più di 50 connettori per sistemi aziendali, ad esempio SAP, servizi SaaS come Salesforce e servizi Internet come Facebook. Accedere ai dati locali usando connessioni ibride e reti virtuali di Azure.
- App Azure servizio è conforme a ISO, SOC e PCI. Autenticare gli utenti con Azure Active Directory o con l'accesso per i social media, ad esempio Google, Facebook, Twitter e Microsoft. Creare restrizioni degli indirizzi IP e gestire le identità del servizio.
- Modelli di applicazione per scegliere da un elenco completo di modelli di applicazioni in Azure Marketplace, ad esempio WordPress, Joomla e Drupal.
- L'integrazione di Visual Studio con strumenti dedicati in Visual Studio semplifica il lavoro di creazione, distribuzione e debug.

**Riferimenti**
- [Azure portal](https://portal.azure.com/)
- [Documentazione del servizio app Azure](/azure/app-service/)
- [Guide introduttive](/azure/app-service/app-service-web-get-started-dotnet)
- [Esempi](/azure/app-service/samples-cli)
- [Esercitazioni](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Servizio Azure Kubernetes
Il [servizio Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) gestisce l'ambiente Kubernetes ospitato, consentendo di distribuire e gestire applicazioni in contenitori in modo semplice e rapido senza competenze nell'orchestrazione di contenitori. Elimina inoltre il carico delle operazioni e della manutenzione in corso. AKS effettua il provisioning, l'aggiornamento e la scalabilità delle risorse su richiesta, senza portare offline le applicazioni.

**Caratteristiche principali**
- Esegui con facilità la migrazione delle applicazioni esistenti ai contenitori ed eseguili in AKS.
- Semplifica la distribuzione e la gestione delle applicazioni basate su microservizi.
- Proteggi DevOps per AKS per ottenere un equilibrio tra velocità e sicurezza e fornire codice più velocemente su larga scala.
- Ridimensiona con facilità usando AKS e istanze di contenitore di Azure per eseguire il provisioning di pod all'interno di istanze di contenitore che iniziano in pochi secondi.
- Distribuisci e Gestisci i dispositivi su richiesta.
- Eseguire il training dei modelli di Machine Learning con l'uso di strumenti come TensorFlow e KubeFlow.

**Riferimenti**
- [Azure portal](https://portal.azure.com/)
- [Documentazione del servizio Azure Kubernetes](/azure/aks/)
- [Guide introduttive](/azure/aks/kubernetes-walkthrough-portal)
- [Esercitazioni](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Istanze di Azure Container
[Istanze di contenitore di Azure](https://azure.microsoft.com/services/container-instances/) è un'ottima soluzione per qualsiasi scenario che può funzionare in contenitori isolati, ad esempio applicazioni semplici, automazione delle attività e processi di compilazione. Sviluppare app velocemente senza gestire le macchine virtuali.

**Caratteristiche principali**
- Tempi di avvio rapidi come istanze di contenitore possono avviare contenitori in Azure in pochi secondi, senza dover eseguire il provisioning e gestire le macchine virtuali.
- Connettività IP pubblico e nome DNS personalizzato.
- Sicurezza a livello di hypervisor che garantisce che l'applicazione sia isolata in un contenitore come in una macchina virtuale.
- Dimensioni personalizzate per un utilizzo ottimale, consentendo specifiche esatte di core CPU e memoria. Si paga in base alle risorse necessarie con fatturazione al secondo ed è così possibile ottimizzare la spesa in base alle esigenze effettive.
- Archivio permanente per il recupero e la persistenza dello stato. Istanze di contenitore consente il montaggio diretto di condivisioni di File di Azure.
- Contenitori Linux e Windows pianificati con la stessa API.

**Riferimenti**
- [Azure portal](https://portal.azure.com/)
- [Documentazione di istanze di contenitore di Azure](/azure/container-instances/)
- [Guide introduttive](/azure/container-instances/container-instances-quickstart-portal)
- [Esempi](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Esercitazioni](/azure/container-instances/container-instances-tutorial-prepare-app)