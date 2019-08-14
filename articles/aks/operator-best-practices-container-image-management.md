---
title: Procedure consigliate per l'operatore - Gestione delle immagini del contenitore nel servizio Azure Kubernetes
description: Procedure consigliate per l'operatore del cluster per la gestione e la protezione delle immagini del contenitore nel servizio Azure Kubernetes
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mlearned
ms.openlocfilehash: 3feadaca361950df2a09f8da33fe380fc3763763
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67614826"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Procedure consigliate per la gestione e la sicurezza delle immagini del contenitore nel servizio Azure Kubernetes

La sicurezza dei contenitori e delle immagini che contengono è un fattore fondamentale da tenere in considerazione nello sviluppo e l'esecuzione di applicazioni nel servizio Azure Kubernetes. I contenitori che includono immagini di base non aggiornate o runtime applicazione senza patch introducono un rischio per la sicurezza e costituiscono un possibile vettore di attacco. Per ridurre al minimo questi rischi, è necessario integrare strumenti che siano in grado di cercare e correggere i problemi nei contenitori sia in fase di compilazione che di runtime. Prima vengono individuate le vulnerabilità o le immagini di base non aggiornate, più sicuro sarà il cluster. In questo articolo per *contenitori* si intendono sia le immagini del contenitore archiviate in un registro contenitori, sia i contenitori in esecuzione.

Questo articolo illustra in particolare come proteggere i contenitori nel servizio Azure Kubernetes. Si apprenderà come:

> [!div class="checklist"]
> * Cercare e correggere le vulnerabilità delle immagini
> * Attivare e ridistribuire automaticamente le immagini del contenitore quando un'immagine di base viene aggiornata

È anche possibile leggere le procedure consigliate per la [sicurezza del cluster][best-practices-cluster-security] e per la [sicurezza Pod][best-practices-pod-security].

## <a name="secure-the-images-and-run-time"></a>Proteggere le immagini e il runtime

**Indicazioni sulle procedure consigliate** - Analizzare le immagini del contenitore per verificare la presenza di vulnerabilità e distribuire solo le immagini che hanno superato la convalida. Aggiornare regolarmente le immagini di base e il runtime applicazione e ridistribuire i carichi di lavoro nel cluster AKS.

Un aspetto delicato dell'adozione dei carichi di lavoro basati su contenitore è la verifica della sicurezza delle immagini e del runtime usati per compilare le applicazioni. Come ci si assicura di non introdurre vulnerabilità per la sicurezza nelle proprie distribuzioni? Il flusso di lavoro di distribuzione deve includere un processo per analizzare le immagini del contenitore usando strumenti come [Twistlock][twistlock] o [Aqua][aqua], quindi consentire la distribuzione solo delle immagini verificate.

![Analizzare e correggere le immagini del contenitore, convalidarle e distribuirle](media/operator-best-practices-container-security/scan-container-images-simplified.png)

In un esempio reale è possibile usare una pipeline di integrazione continua e distribuzione continua (CI/CD) per automatizzare i processi di analisi, verifica e distribuzione delle immagini. Registro Azure Container include queste funzionalità di analisi delle vulnerabilità.

## <a name="automatically-build-new-images-on-base-image-update"></a>Compilare automaticamente nuove immagini all'aggiornamento dell'immagine di base

**Indicazioni sulle procedure consigliate** - Se si usano immagini di base per le immagini dell'applicazione, usare l'automazione per compilare nuove immagini quando l'immagine di base viene aggiornata. Poiché le immagini di base includono in genere correzioni per la sicurezza, aggiornare tutte le immagini del contenitore dell'applicazione downstream.

Ogni volta che un'immagine di base viene aggiornata, devono essere aggiornate anche tutte le immagini del contenitore downstream. Questo processo di compilazione deve essere integrato nelle pipeline di convalida e distribuzione, ad esempio [Azure Pipelines][azure-pipelines] o Jenkins. Queste pipeline assicurano che le applicazioni continuino a essere eseguite sulle immagini di base aggiornate. Una volta convalidate le immagini del contenitore dell'applicazione, è possibile aggiornare le distribuzioni del servizio Azure Kubernetes in modo che eseguano le immagini più recenti e sicure.

Le immagini del contenitore possono essere aggiornate automaticamente anche da Attività del Registro Azure Container quando l'immagine di base viene aggiornata. Questa funzionalità consente di compilare un numero limitato di immagini di base e di mantenerle regolarmente aggiornate con correzioni di bug e per la sicurezza.

Per altre informazioni sugli aggiornamenti delle immagini di base, vedere automatizzare le compilazioni di immagini [sull'aggiornamento di un'immagine di base con Azure container Registry attività][acr-base-image-update].

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato in particolare come proteggere i contenitori. Per implementare alcune di queste aree, vedere gli articoli seguenti:

* [Automatizzare le compilazioni di immagini sull'aggiornamento di un'immagine di base con le attività Container Registry][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
