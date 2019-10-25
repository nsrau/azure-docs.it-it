---
title: Crea back-end di applicazioni per dispositivi mobili senza server con funzioni di Azure e altri servizi
description: Informazioni sui servizi di calcolo per la creazione di back-end di applicazioni per dispositivi mobili solide e senza server.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b8378be675b86ada10b8442c40a54b635ee115a9
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795861"
---
# <a name="build-mobile-backend-components-with-compute-services"></a>Creazione di componenti back-end per dispositivi mobili con servizi di calcolo
Ogni applicazione per dispositivi mobili necessita di un back-end responsabile dell'archiviazione dei dati, della logica di business e della sicurezza. La gestione dell'infrastruttura per ospitare ed eseguire codice back-end richiede la dimensione, il provisioning e la scalabilità di un gruppo di server, la gestione degli aggiornamenti del sistema operativo e dell'hardware necessari, l'applicazione di patch di sicurezza e il monitoraggio di tutti questi componenti dell'infrastruttura per le prestazioni disponibilità e tolleranza di errore. Si tratta di un'architettura senza server utile in quanto gli sviluppatori non dispongono di server da gestire, nessun sistema operativo o aggiornamenti software/hardware correlati da gestire. Consente di risparmiare molto tempo e costo per gli sviluppatori, il che significa tempi di immissione sul mercato più rapidi e risorse mirate alla creazione di applicazioni.

## <a name="benefits-of-compute"></a>Vantaggi del calcolo
- Astrazione dei server: non è necessario preoccuparsi di ospitare, applicare patch e sicurezza, consentendo agli sviluppatori di concentrarsi solo sul codice.
- La scalabilità immediata ed efficiente assicura che le risorse vengano sottoposte a provisioning automatico o su richiesta in base alla scalabilità necessaria.
- Disponibilità elevata e tolleranza di errore.
- La fatturazione micro garantisce che vengano addebitati solo i casi in cui il codice è effettivamente in esecuzione.
- Scrivi il codice che viene eseguito nel cloud nel linguaggio che preferisci.

Usare i servizi seguenti per abilitare le funzionalità di calcolo senza server nelle app per dispositivi mobili.

## <a name="azure-functions"></a>Funzioni di Azure
[Funzioni di Azure](https://azure.microsoft.com/services/functions/) è un'esperienza di calcolo basata su eventi che consente di eseguire il codice, scritto nel linguaggio di programmazione preferito, senza doversi preoccupare dei server. Gli sviluppatori non devono gestire l'applicazione o l'infrastruttura per eseguirla. Le funzioni vengono ridimensionate su richiesta e si paga solo per il tempo di esecuzione del codice. Funzioni di Azure è un ottimo modo per implementare un'API per un'applicazione per dispositivi mobili, in quanto è molto semplice da implementare e gestire e accessibile tramite HTTP.

**Funzionalità principali**
- **Basati su eventi e scalabile,** in cui gli sviluppatori possono utilizzare **trigger e associazioni** per definire quando viene richiamata una funzione e i dati a cui si connette.
- **Trasferimento delle dipendenze** : Funzioni supporta NuGet e NPM, quindi è possibile usare le librerie preferite.
- La **sicurezza integrata** ti permette di proteggere le funzioni attivate da http con provider OAuth, ad esempio Azure Active Directory, Facebook, Google, Twitter e account Microsoft.
- **Integrazione semplificata** con diversi [servizi di Azure](/azure/azure-functions/functions-overview#integrations) e offerte SaaS (software-as-a-Service).
- **Lo sviluppo flessibile** ti permette di scrivere codice per le funzioni direttamente nel portale o di configurare l'integrazione continua e di distribuire il codice tramite GitHub, Azure DevOps Services e altri strumenti di sviluppo supportati.
- Il runtime di funzioni è **Open Source** ed è disponibile su [GitHub](https://github.com/azure/azure-webjobs-sdk-script).
-  **Esperienza di sviluppo migliorata** che consente agli sviluppatori di codificare, testare ed eseguire il debug in locale usando l'editor preferito o l'interfaccia Web di facile utilizzo con il monitoraggio con strumenti integrati e funzionalità DevOps predefinite.
- **Ampia gamma di linguaggi di programmazione e opzioni di hosting** : sviluppare usando C#, node. js, Java, JavaScript o Python.
- **Modello tariffario in base al consumo** : si paga solo per il tempo impiegato per l'esecuzione del codice.

**Riferimenti**
- [Azure portal](https://portal.azure.com)
- [Documentazione](/azure/azure-functions/)
- [Guida per gli sviluppatori di funzioni di Azure](/azure/azure-functions/functions-reference)
- [Guide introduttive](/azure/azure-functions/functions-create-first-function-vs-code)
- [Esempi](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="app-service"></a>Servizio app
[Servizio app Azure](https://azure.microsoft.com/services/app-service/) App Azure servizio ti permette di creare e ospitare app Web e API RESTful nel linguaggio di programmazione che preferisci senza gestire l'infrastruttura. Offre la scalabilità automatica e la disponibilità elevata, supporta sia Windows che Linux e consente distribuzioni automatiche da GitHub, Azure DevOps o qualsiasi repository Git.

**Funzionalità principali**
- Supporto per **più linguaggi e Framework** per ASP.NET, ASP.NET Core, Java, Ruby, node. js, php o Python. È anche possibile eseguire PowerShell e altri script o eseguibili come servizi in background.
- **Ottimizzazione di DevOps** : configurare l'integrazione e la distribuzione continue con Azure DevOps, GitHub, Bitbucket, Docker Hub o Azure container Registry. Gestisci le tue app nel servizio app usando Azure PowerShell o l'interfaccia della riga di comando multipiattaforma.
- **Scalabilità globale con disponibilità elevata** per la scalabilità orizzontale o automatica manuale o automatica.
- **Connessioni a piattaforme SaaS e dati locali** per scegliere tra più di 50 connettori per sistemi aziendali (ad esempio SAP), servizi SaaS (come Salesforce) e servizi Internet (come Facebook). Accedere ai dati locali usando Connessioni ibride e le reti virtuali di Azure.
- **Sicurezza e conformità** : il servizio app Azure è conforme a ISO, SOC e PCI. Autentica gli utenti con Azure Active Directory o con l'account di accesso di social networking (Google, Facebook, Twitter e Microsoft). Creare restrizioni degli indirizzi IP e gestire le identità del servizio.
- **Modelli di applicazione** per scegliere da un elenco completo di modelli di applicazioni in Azure Marketplace, ad esempio Wordpress, Joomla e Drupal.
- L' **integrazione di Visual Studio** con strumenti dedicati in Visual Studio semplifica il lavoro di creazione, distribuzione e debug.

**Riferimenti**
- [Azure portal](https://portal.azure.com/)
- [Documentazione](/azure/app-service/)
- [Guide introduttive](/azure/app-service/app-service-web-get-started-dotnet)
- [Esempi](/azure/app-service/samples-cli)
- [Esercitazioni](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service-aks"></a>Servizio Azure Kubernetes
Il [servizio Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) gestisce l'ambiente Kubernetes ospitato consentendo di distribuire e gestire applicazioni in contenitori in modo semplice e rapido senza competenze nell'orchestrazione di contenitori. Elimina anche l'impegno delle operazioni in corso e della manutenzione effettuando il provisioning, l'aggiornamento e il ridimensionamento delle risorse su richiesta, senza portare le applicazioni offline. 

**Funzionalità principali**
- Esegui con **facilità la migrazione delle applicazioni esistenti** ai contenitori ed eseguili in AKS.
- **Semplifica la distribuzione e la gestione** delle applicazioni basate su microservizi.
- **Proteggi DevOps per AKS** per ottenere un equilibrio tra velocità e sicurezza e fornire codice più velocemente su larga scala.
- **Scalabilità semplificata usando AKS e ACI** per eseguire il provisioning di pod all'interno di ACI che iniziano in pochi secondi.
- **Distribuzione e gestione dei dispositivi** Internet su richiesta.
- **Training del modello di Machine Learning** con l'uso di strumenti come TensorFlow e KubeFlow.

**Riferimenti**
- [Azure portal](https://portal.azure.com/)
- [Documentazione](/azure/aks/)
- [Guide introduttive](/azure/aks/kubernetes-walkthrough-portal)
- [Esercitazioni](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Istanze di Azure Container
[Istanze di contenitore di Azure](https://azure.microsoft.com/services/container-instances/) è un'ottima soluzione per qualsiasi scenario che può funzionare in contenitori isolati, tra cui semplici applicazioni, automazione delle attività e processi di compilazione. Sviluppare app velocemente senza gestire le macchine virtuali.

**Funzionalità principali**
- **Tempi di avvio rapidi** come ACI possono avviare contenitori in Azure in pochi secondi, senza la necessità di effettuare il provisioning e gestire le macchine virtuali.
- **Connettività IP pubblico e nome DNS personalizzato**.
- **Sicurezza a livello di hypervisor** che garantisce che l'applicazione sia isolata in un contenitore come in una macchina virtuale.
- **Dimensioni personalizzate** per un utilizzo ottimale, consentendo specifiche esatte di core CPU e memoria. Si paga in base alle risorse necessarie con fatturazione al secondo ed è così possibile ottimizzare la spesa in base alle esigenze effettive.
- **Archiviazione permanente** per recuperare e salvare lo stato, ACI consente il montaggio diretto di condivisioni di file di Azure.
- **Contenitori Linux e Windows** pianificati con la stessa API.

**Riferimenti**
- [Azure portal](https://portal.azure.com/)
- [Documentazione](/azure/container-instances/)
- [Guide introduttive](/azure/container-instances/container-instances-quickstart-portal)
- [Esempi](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Esercitazioni](/azure/container-instances/container-instances-tutorial-prepare-app)