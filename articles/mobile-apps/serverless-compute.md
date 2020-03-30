---
title: Creare un back-end di un'applicazione per dispositivi mobili senza server con Funzioni di Azure e altri serviziBuild a serverless mobile application back-end with Azure Functions and other services
description: Informazioni sui servizi di calcolo utilizzati per creare un back-end di applicazioni per dispositivi mobili solido e senza server.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: d6a2bbc984a8808d2cd8a9b292e55d80f43f2e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240140"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Costruisci componenti back-end mobili con servizi di calcolo
Ogni applicazione per dispositivi mobili necessita di un back-end responsabile dell'archiviazione dei dati, della logica di business e della sicurezza. La gestione dell'infrastruttura per ospitare ed eseguire codice back-end richiede la dimensione, il provisioning e la scalabilità di più server. È inoltre necessario gestire gli aggiornamenti del sistema operativo e l'hardware coinvolto e applicare le patch di protezione. Quindi è necessario monitorare tutti questi componenti dell'infrastruttura per ottenere prestazioni, disponibilità e tolleranza di errore. 

L'architettura senza server è utile per questo tipo di scenario perché non si dispone di server da gestire e nessun sistema operativo o aggiornamenti software o hardware correlati da gestire. L'architettura senza server consente di risparmiare tempo e costi per gli sviluppatori, il che significa tempi di commercializzazione più rapidi e energia focalizzata sulla creazione di applicazioni.

## <a name="benefits-of-compute"></a>Vantaggi del calcolo
- L'astrazione dei server significa che non c'è bisogno di preoccuparsi di hosting, applicazione di patch e sicurezza, che consente di concentrarsi esclusivamente sul codice.
- La scalabilità immediata ed efficiente garantisce il provisioning automatico o su richiesta delle risorse su richiesta, su qualsiasi scala sia necessaria.
- Disponibilità elevata e tolleranza di errore.
- La microfatturazione garantisce la fatturazione solo quando il codice è effettivamente in esecuzione.
- Il codice viene eseguito nel cloud scritto nella lingua desiderata.

Usa i servizi seguenti per abilitare le funzionalità di calcolo senza server nelle tue app per dispositivi mobili.

## <a name="azure-functions"></a>Funzioni di Azure
[Funzioni](https://azure.microsoft.com/services/functions/) di Azure è un'esperienza di calcolo basata su eventi che è possibile usare per eseguire il codice, scritto nel linguaggio di programmazione di propria scelta, senza preoccuparsi dei server. Non è necessario gestire l'applicazione o l'infrastruttura in cui eseguirla. Le funzioni vengono scalate su richiesta e si paga solo per il tempo di esecuzione del codice. Le funzioni di Azure sono un ottimo modo per implementare un'API per un'applicazione mobile. Sono facili da implementare e gestire e sono accessibili tramite HTTP.

**Caratteristiche principali**
- Basato su eventi e scalabile in cui è possibile usare trigger e associazioni per definire quando viene richiamata una funzione e a quali dati si connette.
- Portare le proprie dipendenze perché Funzioni supporta NuGet e NPM, in modo da poter utilizzare le librerie preferite.
- Sicurezza integrata in modo da poter proteggere le funzioni attivate da HTTP con provider OAuth come Azure Active Directory, Facebook, Google, Twitter e account Microsoft.
- Integrazione semplificata con diversi servizi e software [di Azure](/azure/azure-functions/functions-overview) come servizio (SaaS).
- Sviluppo flessibile in modo da poter codificare le funzioni direttamente nel portale di Azure o configurare l'integrazione continua e distribuire il codice tramite GitHub, i servizi DevOps di Azure e altri strumenti di sviluppo supportati.
- Il runtime delle funzioni è open source e disponibile in [GitHub](https://github.com/azure/azure-webjobs-sdk-script).
- Esperienza di sviluppo migliorata in cui è possibile codificare, testare ed eseguire il debug in locale usando l'editor preferito o l'interfaccia Web di facile utilizzo con il monitoraggio con strumenti integrati e funzionalità DevOps incorporate.
- Varietà di linguaggi di programmazione e opzioni di hosting per lo sviluppo, ad esempio C , Node.js, Java, JavaScript o Python.
- Il modello di determinazione dei prezzi con pagamento per uso significa che si paga solo per il tempo trascorso durante l'esecuzione del codice.

**Riferimenti**
- [Portale di Azure](https://portal.azure.com)
- [Documentazione di Funzioni di AzureAzure Functions documentation](/azure/azure-functions/)
- [Guida per sviluppatori di Funzioni di Azure](/azure/azure-functions/functions-reference)
- [Avvi rapidi](/azure/azure-functions/functions-create-first-function-vs-code)
- [Esempi](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Servizio app di Azure
Con [il servizio app](https://azure.microsoft.com/services/app-service/)di Azure è possibile compilare e ospitare app Web e API RESTful nel linguaggio di programmazione desiderato senza gestire l'infrastruttura. Offre scalabilità automatica e disponibilità elevata, supporta sia Windows che Linux e abilita distribuzioni automatizzate da GitHub, DevOps di Azure o qualsiasi repository Git.It offers autoscaling and high availability, supports both Windows and Linux, and enables automated deployments from GitHub, Azure DevOps, or any Git repo.

**Caratteristiche principali**
- Supporto di più linguaggi e framework per ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP o Python. È anche possibile eseguire PowerShell e altri script o eseguibili come servizi in background.
- DevOps optimization through continuous integration and deployment with Azure DevOps, GitHub, BitBucket, Docker Hub, or Azure Container Registry. e gestire le app nel servizio app con Azure PowerShell o l'interfaccia della riga di comando multipiattaforma.
- Scalabilità globale con disponibilità elevata per la scalabilità verticale o orizzontale manualmente o automaticamente.
- Connessioni alle piattaforme SaaS e ai dati locali per scegliere tra più di 50 connettori per sistemi aziendali come SAP, servizi SaaS come Salesforce e servizi Internet come Facebook. Accedere ai dati locali usando connessioni ibride e reti virtuali di Azure.Access on-premises data by using hybrid connections and Azure Virtual Networks.
- Il servizio app di Azure è conforme allo standard ISO, SOC e PCI. Autenticare gli utenti con Azure Active Directory o con l'accesso per i social media come Google, Facebook, Twitter e Microsoft. Creare restrizioni per gli indirizzi IP e gestire le identità del servizio.
- Application templates to choose from an extensive list of application templates in Azure Marketplace, such as WordPress, Joomla, and Drupal.
- L'integrazione di Visual Studio con strumenti dedicati in Visual Studio semplifica il lavoro di creazione, distribuzione e debug.

**Riferimenti**
- [Portale di Azure](https://portal.azure.com/)
- [Documentazione del servizio app di AzureAzure App Service documentation](/azure/app-service/)
- [Avvi rapidi](/azure/app-service/app-service-web-get-started-dotnet)
- [Esempi](/azure/app-service/samples-cli)
- [Esercitazioni](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Servizio Azure Kubernetes
Il [servizio Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) gestisce l'ambiente Kubernetes ospitato, consentendo di distribuire e gestire applicazioni in contenitori in modo semplice e rapido senza competenze nell'orchestrazione di contenitori. Elimina inoltre l'onere delle operazioni e della manutenzione in corso. AKS effettua provvede, aggiorna e scala le risorse su richiesta, senza disconnettere le applicazioni.

**Caratteristiche principali**
- Esegui facilmente la migrazione delle applicazioni esistenti ai contenitori ed eseguile all'interno di AKS.
- Semplifica la distribuzione e la gestione di applicazioni basate su microservizi.
- Proteggi DevOps per AKS per raggiungere l'equilibrio tra velocità e sicurezza e distribuire il codice più velocemente su larga scala.
- Scalabilità con facilità usando AKS e le istanze del contenitore di Azure per eseguire il provisioning di pod all'interno di istanze del contenitore che iniziano in pochi secondi.
- Distribuire e gestire dispositivi IoT su richiesta.
- Allena i modelli di apprendimento automatico con l'uso di strumenti come TensorFlow e KubeFlow.

**Riferimenti**
- [Portale di Azure](https://portal.azure.com/)
- [Documentazione del servizio Azure KubernetesAzure Kubernetes Service documentation](/azure/aks/)
- [Avvi rapidi](/azure/aks/kubernetes-walkthrough-portal)
- [Esercitazioni](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Istanze di Azure Container
[Le istanze del contenitore](https://azure.microsoft.com/services/container-instances/) di Azure è un'ottima soluzione per qualsiasi scenario in grado di operare in contenitori isolati, ad esempio applicazioni semplici, automazione delle attività e processi di compilazione. Sviluppa le app rapidamente senza gestire le macchine virtuali.

**Caratteristiche principali**
- Tempi di avvio rapidi poiché le istanze del contenitore possono avviare i contenitori in Azure in pochi secondi, senza la necessità di eseguire il provisioning e gestire le macchine virtuali.
- Connettività IP pubblica e nome DNS personalizzato.
- Sicurezza a livello di hypervisor che garantisce che l'applicazione sia isolata in un contenitore come in una macchina virtuale.
- Dimensioni personalizzate per un utilizzo ottimale consentendo specifiche esatte dei core e della memoria della CPU. Si paga in base alle risorse necessarie con fatturazione al secondo ed è così possibile ottimizzare la spesa in base alle esigenze effettive.
- Archiviazione permanente per recuperare e mantenere lo stato. Le istanze del contenitore offrono il montaggio diretto delle condivisioni File di Azure.Container Instances offers direct mounting of Azure Files shares.
- Contenitori Linux e Windows pianificati con la stessa API.

**Riferimenti**
- [Portale di Azure](https://portal.azure.com/)
- [Documentazione sulle istanze dei contenitori di AzureAzure Container Instances documentation](/azure/container-instances/)
- [Avvi rapidi](/azure/container-instances/container-instances-quickstart-portal)
- [Esempi](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Esercitazioni](/azure/container-instances/container-instances-tutorial-prepare-app)