---
title: Procedure consigliate per l'operatore - Gestione delle immagini del contenitore nel servizio Azure Kubernetes
description: Procedure consigliate per l'operatore del cluster per la gestione e la protezione delle immagini del contenitore nel servizio Azure Kubernetes
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: 1cc91f55d3895f06176875cb9ae620685dc09a26
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53605552"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Procedure consigliate per la gestione e la sicurezza delle immagini del contenitore nel servizio Azure Kubernetes

La sicurezza dei contenitori e delle immagini che contengono è un fattore fondamentale da tenere in considerazione nello sviluppo e l'esecuzione di applicazioni nel servizio Azure Kubernetes. I contenitori che includono immagini di base non aggiornate o runtime applicazione senza patch introducono un rischio per la sicurezza e costituiscono un possibile vettore di attacco. Per ridurre al minimo questi rischi, è necessario integrare strumenti che siano in grado di cercare e correggere i problemi nei contenitori sia in fase di compilazione che di runtime. Prima vengono individuate le vulnerabilità o le immagini di base non aggiornate, più sicuro sarà il cluster. In questo articolo per *contenitori* si intendono sia le immagini del contenitore archiviate in un registro contenitori, sia i contenitori in esecuzione.

Questo articolo illustra in particolare come proteggere i contenitori nel servizio Azure Kubernetes. Si apprenderà come:

> [!div class="checklist"]
> * Cercare e correggere le vulnerabilità delle immagini
> * Usare un registro attendibile con immagini del contenitore firmate digitalmente
> * Attivare e ridistribuire automaticamente le immagini del contenitore quando un'immagine di base viene aggiornata

È anche possibile leggere le procedure consigliate per la [sicurezza dei cluster][best-practices-cluster-security] e la [sicurezza dei pod][best-practices-pod-security].

## <a name="secure-the-images-and-run-time"></a>Proteggere le immagini e il runtime

**Indicazioni sulle procedure consigliate** - Analizzare le immagini del contenitore per verificare la presenza di vulnerabilità e distribuire solo le immagini che hanno superato la convalida. Aggiornare regolarmente le immagini di base e il runtime applicazione e ridistribuire i carichi di lavoro nel cluster AKS.

Un aspetto delicato dell'adozione dei carichi di lavoro basati su contenitore è la verifica della sicurezza delle immagini e del runtime usati per compilare le applicazioni. Come ci si assicura di non introdurre vulnerabilità per la sicurezza nelle proprie distribuzioni? Il flusso di lavoro della distribuzione dovrebbe includere un processo che analizzi le immagini del contenitore usando uno strumento come [Twistlock][twistlock] o [Aqua][aqua] e quindi consenta la distribuzione delle sole immagini verificate.

![Analizzare e correggere le immagini del contenitore, convalidarle e distribuirle](media/operator-best-practices-container-security/scan-container-images-simplified.png)

In un esempio reale è possibile usare una pipeline di integrazione continua e distribuzione continua (CI/CD) per automatizzare i processi di analisi, verifica e distribuzione delle immagini. Registro Azure Container include queste funzionalità di analisi delle vulnerabilità.

## <a name="use-a-trusted-registry"></a>Usare un registro attendibile

**Indicazioni sulle procedure consigliate** - Limitare i registri di immagini che possono essere usati da pod e distribuzioni. Consentire solo i registri attendibili in cui si convalidano e si controllano le immagini disponibili.

Per una maggiore sicurezza, è anche possibile firmare digitalmente le immagini del contenitore come si fa con il codice dell'applicazione. Occorre quindi consentire al servizio Azure Kubernetes di distribuire solo le immagini firmate. Questo processo fornisce un ulteriore livello di sicurezza in quanto limita il servizio Azure Kubernetes a eseguire il pull delle sole immagini firmate digitalmente e considerate attendibili dall'utente, anziché semplicemente delle immagini che superano un controllo delle vulnerabilità. Si verifica anche che l'immagine del contenitore non sia stata manomessa e sostituita con un'immagine con lo stesso nome.

I registri attendibili che forniscono immagini del contenitore firmate digitalmente rendono più complesso l'ambiente, ma possono essere necessari per la conformità con determinati criteri o normative. Registro Azure Container supporta l'uso dei registri attendibili e delle immagini firmate.

Per altre informazioni sulle immagini firmate digitalmente, vedere [Attendibilità dei contenuti in Registro contenitori di Azure][acr-content-trust].

## <a name="automatically-build-new-images-on-base-image-update"></a>Compilare automaticamente nuove immagini all'aggiornamento dell'immagine di base

**Indicazioni sulle procedure consigliate** - Se si usano immagini di base per le immagini dell'applicazione, usare l'automazione per compilare nuove immagini quando l'immagine di base viene aggiornata. Poiché le immagini di base includono in genere correzioni per la sicurezza, aggiornare tutte le immagini del contenitore dell'applicazione downstream.

Ogni volta che un'immagine di base viene aggiornata, devono essere aggiornate anche tutte le immagini del contenitore downstream. Questo processo di compilazione deve essere integrato nelle pipeline di convalida e distribuzione come [Azure Pipelines][azure-pipelines] o Jenkins. Queste pipeline assicurano che le applicazioni continuino a essere eseguite sulle immagini di base aggiornate. Una volta convalidate le immagini del contenitore dell'applicazione, è possibile aggiornare le distribuzioni del servizio Azure Kubernetes in modo che eseguano le immagini più recenti e sicure.

Le immagini del contenitore possono essere aggiornate automaticamente anche da Attività del Registro Azure Container quando l'immagine di base viene aggiornata. Questa funzionalità consente di compilare un numero limitato di immagini di base e di mantenerle regolarmente aggiornate con correzioni di bug e per la sicurezza.

Per altre informazioni sugli aggiornamenti delle immagini di base, vedere [Automatizzare la compilazione di immagini in caso di aggiornamento dell'immagine di base con ACR Tasks][acr-base-image-update].

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato in particolare come proteggere i contenitori. Per implementare alcune di queste aree, vedere gli articoli seguenti:

* [Automatizzare la compilazione di immagini in caso di aggiornamento dell'immagine di base con ACR Tasks][acr-base-image-update]
* [Attendibilità dei contenuti in Registro contenitori di Azure][acr-content-trust]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-content-trust]: ../container-registry/container-registry-content-trust.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
