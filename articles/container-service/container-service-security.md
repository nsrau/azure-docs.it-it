---
title: Sicurezza del contenitore nel Servizio contenitore di Azure | Microsoft Docs
description: Considerazioni sulla protezione dei contenitori Docker distribuiti nel servizio contenitore di Azure e nei servizi di Azure correlati.
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Contenitori, Micro-servizi, Mesos, Azure, Kubernetes
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: saudas
ms.custom: 
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: cabcc35098fa2063812f5aef8593dba6dce48cc2
ms.lasthandoff: 03/31/2017


---
# <a name="securing-docker-containers-in-azure-container-service"></a>Protezione dei contenitori Docker nel servizio contenitore di Azure

Questo articolo introduce considerazioni e indicazioni per la sicurezza dei contenitori Docker distribuiti nel servizio contenitore di Azure. In genere molte di queste considerazioni si applicano ai contenitori Docker distribuiti in Azure o in altri ambienti. 

## <a name="image-security"></a>Sicurezza dell'immagine

I contenitori sono costituiti da immagini archiviati in una o più repository. Queste repository possono appartenere a registri di contenitori pubblici o privati. Un esempio di registro pubblico è [Hub Docker](https://hub.docker.com/). Un esempio di un registro privato è il [registro attendibile Docker](https://docs.docker.com/datacenter/dtr/2.0/), che può essere installato in locale o in un cloud privato virtuale. Sono inoltre disponibili servizi del registro contenitori privato basato su cloud che include [Registro contenitori di Azure](../container-registry/container-registry-intro.md).

### <a name="public-and-private-images"></a>Immagini pubbliche e private
In generale, come con qualsiasi pacchetto software pubblicato, un'immagine del contenitore pubblica non garantisce la sicurezza. Le immagini del contenitore sono costituite da più livelli di software e ogni livello di software potrebbe avere vulnerabilità. È importante comprendere l'origine dell'immagine del contenitore, tra cui il proprietario dell'immagine (per stabilire se è una fonte attendibile o meno), i livelli di software da cui è costituita e le versioni del software. 

Ad esempio, se si passa alla [repository nginx](https://hub.docker.com/_/nginx/) ufficiale nell'Hub Docker e poi alla scheda **Tag**, si notano vulnerabilità contraddistinte da colori in ogni immagine. Ogni colore indica la vulnerabilità di un livello software dell'immagine. Per altre informazioni sulle vulnerabilità dell'analisi in Hub Docker, vedere [Informazioni ufficiali sulle repository in Hub Docker](https://blog.docker.com/2015/06/understanding-official-repos-docker-hub/).

![Immagini di nginx in Hub Docker](./media/container-service-security/docker-hub-nginx.png)



Le aziende sono molto interessate alla sicurezza e per proteggersi da attacchi alla sicurezza devono archiviare e recuperare le immagini da un registro privato, ad esempio del Registro contenitori di Azure o del Registro attendibile Docker. Oltre a garantire un registro privato gestito, il Registro contenitori di Azure supporta [l'autenticazione basata su entità servizio](../container-registry/container-registry-authentication.md) tramite Azure Active Directory per i flussi di autenticazione di base, tra cui l'accesso basato sui ruoli per la sola lettura, la scrittura e le autorizzazioni del proprietario.


### <a name="image-security-scanning"></a>Analisi della sicurezza dell'immagine

Anche quando si usa un registro privato, è consigliabile usare soluzioni di analisi dell'immagine per una convalida di sicurezza aggiuntiva. Ogni livello di software in un'immagine del contenitore è potenzialmente soggetto a vulnerabilità indipendenti da altri livelli dell'immagine del contenitore. Man mano che sempre più aziende avviano la distribuzione dei carichi di lavoro di produzione basate sulle tecnologie con contenitori, l'analisi dell'immagine diventa fondamentale per prevenire le minacce alla sicurezza contro delle organizzazioni. 

Le soluzioni di monitoraggio e analisi, ad esempio le soluzioni di sicurezza [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry) e [Aqua Security](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry), tra le altre, possono essere usate per acquisire immagini del contenitore in un registro privato e identificare le potenziali vulnerabilità. È importante comprendere la profondità di analisi offerta dalle diverse soluzioni. Ad esempio, alcune soluzioni potrebbero eseguire dei controlli incrociati solo sui livelli dell'immagine rispetto alle vulnerabilità note. Queste soluzioni potrebbero non essere in grado di verificare il software a livello immagine creato tramite determinati software di gestione pacchetti. Altre soluzioni dispongono dell'integrazione di analisi più approfondite e possono rilevare vulnerabilità in qualsiasi software nel pacchetto.

### <a name="production-deployment-rules-and-audit"></a>Controllo e regole di distribuzione di produzione
Dopo la distribuzione dell'applicazione nell'ambiente di produzione, è essenziale impostare alcune regole per assicurarsi che le immagini usate negli ambienti di produzione siano sicure e che non contengano vulnerabilità.

* Di norma, le immagini con le vulnerabilità, anche secondarie, non dovrebbero essere eseguite in un ambiente di produzione. Inoltre, tutte le immagini distribuite nell'ambiente di produzione idealmente dovrebbero essere salvate in un registro privato accessibile a una cerchia ristretta di persone. È anche importante limitare il numero di immagini di produzione per assicurarsi che possano essere gestite in modo efficace.

* Poiché è difficile individuare l'origine del software da un'immagine del contenitore disponibile al pubblico, è buona norma creare immagini di origine per assicurarsi di conoscere l'origine del livello. Quando una vulnerabilità emerge in un'immagine del contenitore generata automaticamente, i clienti possono trovare un percorso più rapido per la risoluzione. Con un'immagine pubblica, i clienti dovranno trovare la radice di un'immagine pubblica per correggerla oppure ottenere un'altra immagine protetta dal server di pubblicazione.

* Per un'immagine digitalizzata completamente distribuita nell'ambiente di produzione non viene garantito l'aggiornamento per la durata dell'applicazione. È possibile che vengano segnalate vulnerabilità della sicurezza per i livelli dell'immagine precedentemente ignoti o che sono stati introdotti dopo la distribuzione di produzione. Il controllo periodico delle immagini distribuite nell'ambiente di produzione è necessario identificare le immagini che non sono state aggiornate in un determinato periodo di tempo. È possibile usare metodologie di distribuzione verde-blu e meccanismi di aggiornamento in sequenza per aggiornare immagini del contenitore senza tempi di inattività. L'analisi di immagine può essere eseguita con gli strumenti descritti nella sezione precedente. 

* Una pipeline di integrazione continua (CI) per creare immagini e analisi di sicurezza integrata può contribuire a mantenere i registri privati protetti con le immagini di un contenitore protetto. L'analisi delle vulnerabilità incorporata nella soluzione CI garantisce che le immagini che passano tutti i test vengano inserite nel registro privato dalla produzione da cui vengono distribuiti i carichi di lavoro della produzione. Un errore di pipeline CI garantisce che le immagini vulnerabili non vengano inserite nel registro privato usato per le distribuzioni del carico di lavoro della produzione. Inoltre consente di automatizzare l'analisi di sicurezza dell'immagine se è presente un numero significativo di immagini. Altrimenti il controllo manuale delle immagini per le vulnerabilità di sicurezza può risultare lungo e dettagliato e tendente all'errore.

## <a name="host-level-container-isolation"></a>Isolamento del contenitore a livello di host
Quando un cliente distribuisce le applicazioni del contenitore nelle risorse di Azure, queste vengono distribuite a livello di sottoscrizione nei gruppi di risorse e non sono multi-tenant. Ciò significa che se un cliente condivide una sottoscrizione con altri utenti, non è possibile inserire limiti tra le due distribuzioni nella stessa sottoscrizione. Pertanto, la sicurezza a livello di contenitore non viene garantita. 

È inoltre importante comprendere che i contenitori condividono il kernel e le risorse dell'host, che nel servizio contenitore di Azure è una macchina virtuale di Azure in un cluster. Pertanto, è necessario eseguire i contenitori in esecuzione nell'ambiente di produzione in modalità utente senza privilegi. L'esecuzione di un contenitore con privilegi radice può compromettere l'intero ambiente. Con accesso a livello di radice in un contenitore, un pirata informatico può accedere ai privilegi radice completi sull'host. Inoltre, è importante eseguire contenitori con file system di sola lettura. Ciò impedisce a un utente che ha accesso al contenitore di compromesso di scrivere script dannosi nel file system e di ottenere l'accesso ad altri file. Analogamente, è importante limitare le risorse, ad esempio memoria, CPU e larghezza di banda, allocate in un contenitore. Ciò consente di impedire ai pirati informatici di monopolizzare le risorse, sostenendo attività illegali, ad esempio frodi di carte di credito o data mining bitcoin, che potrebbero impedire l'esecuzione nell'host o nel cluster di altri contenitori.

## <a name="orchestrator-considerations"></a>Considerazioni sull'orchestrator

Ogni orchestrator disponibile nel servizio contenitore di Azure ha proprie considerazioni sulla sicurezza. Ad esempio, è consigliabile limitare l'accesso SSH diretto ai nodi di orchestrator nel servizio contenitore. È invece necessario usare ogni interfaccia utente dell'orchestrator o gli strumenti da riga di comando (ad esempio `kubectl` per Kubernetes) per gestire l'ambiente del contenitore senza accedere all'host. Per altre informazioni vedere [Stabilire una connessione remota a un cluster Kubernetes, DC/OS o Docker Swarm](container-service-connect.md).

Per altre informazioni sulla sicurezza specifica per l'orchestrator, vedere le risorse seguenti:

* **Kubernetes**: [Security Best Practices for Kubernetes Deployment](http://blog.kubernetes.io/2016/08/security-best-practices-kubernetes-deployment.html) (Pratiche consigliate di sicurezza per la distribuzione di Kubernetes)

* **DC/OS**: [Securing Your Cluster](https://dcos.io/docs/1.8/administration/securing-your-cluster/) (Protezione del cluster)

* **Docker Swarm**: [Docker Security](https://www.docker.com/docker-security) (Sicurezza Docker)



## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla sicurezza dell'architettura e del contenitore Docker, vedere [Introduction to Container Security](https://www.docker.com/sites/default/files/WP_IntrotoContainerSecurity_08.19.2016.pdf) (Introduzione alla sicurezza del contenitore).

* Visitare il [Centro sicurezza di Azure](https://www.microsoft.com/en-us/trustcenter/cloudservices/azure) per informazioni sulla sicurezza della piattaforma Azure.
