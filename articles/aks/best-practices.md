---
title: Procedure consigliate per il servizio Azure Kubernetes
description: Raccolta delle procedure consigliate per sviluppatori e operatori del cluster per la creazione e la gestione di applicazioni nel servizio Azure Kubernetes
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 12/07/18
ms.author: iainfou
ms.openlocfilehash: ef7dabc8bf792893380b80458d5fb20aa15cd909
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256469"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Procedure consigliate per sviluppatori e operatori del cluster per la creazione e la gestione di applicazioni nel servizio Azure Kubernetes

Per creare ed eseguire correttamente applicazioni nel servizio Azure Kubernetes, è importante comprendere e implementare alcune considerazioni fondamentali. Queste aree includono le funzionalità di multi-tenancy e pianificazione, la sicurezza di cluster e pod o la continuità aziendale e ripristino di emergenza. Le procedure consigliate descritte di seguito sono raggruppate in modo da aiutare gli operatori di cluster e gli sviluppatori a comprendere le considerazioni relative a ognuna di queste aree e a implementare le funzionalità appropriate.

Queste procedure consigliate e i relativi articoli concettuali sono stati scritti insieme al gruppo del prodotto, ai team di progettazione e ai team sul campo del servizio Azure Kubernetes, inclusi i Global Black Belt (GBB).

## <a name="cluster-operator-best-practices"></a>Procedure consigliate per gli operatori di cluster

Si consiglia agli operatori di cluster di collaborare con i proprietari di applicazioni e gli sviluppatori per comprendere le proprie esigenze. È quindi possibile usare le procedure consigliate seguenti per configurare i cluster del servizio Azure Kubernetes (AKS) in base a queste esigenze.

**Multi-tenancy**

* [Procedure consigliate per l'isolamento del cluster](operator-best-practices-cluster-isolation.md)
    * Principali componenti multi-tenant e isolamento logico con spazi dei nomi.
* [Procedure consigliate per le funzionalità di base dell'utilità di pianificazione](operator-best-practices-scheduler.md)
    * Quote di risorse e budget di interruzione dei pod.
* [Procedure consigliate per le funzionalità avanzate dell'utilità di pianificazione](operator-best-practices-advanced-scheduler.md)
    * Uso di taint e tolleranze, selettori di nodi e affinità, affinità fra pod e anti-affinità.
* [Procedure consigliate per autenticazione e autorizzazione](operator-best-practices-identity.md)
    * Integrazione con Azure Active Directory, uso del controllo degli accessi in base al ruolo e identità dei pod.

**Sicurezza**

* [Procedure consigliate per la sicurezza e gli aggiornamenti del cluster](operator-best-practices-cluster-security.md)
    * Protezione dell'accesso al server API, limitazione dell'accesso ai contenitori e gestione degli aggiornamenti e dei riavvii dei nodi.
* [Procedure consigliate per la gestione e la sicurezza delle immagini del contenitore](operator-best-practices-container-image-management.md)
    * Protezione di immagini e runtime, uso di registri attendibili e compilazioni automatizzate sugli aggiornamenti dell'immagine di base.
* [Procedure consigliate per la sicurezza dei pod](developer-best-practices-pod-security.md)
    * Protezione dell'accesso alle risorse, limitazione dell'esposizione delle credenziali e uso di identità dei pod e insiemi di credenziali delle chiavi digitali.

**Rete e di archiviazione**

* [Procedure consigliate per la connettività di rete](operator-best-practices-network.md)
    * Diversi modelli di rete, uso di controller del traffico in ingresso e web application firewall (WAF) e protezione dell'accesso SSH ai nodi.
* [Procedure consigliate per archiviazione e backup](operator-best-practices-storage.md)
    * Scelta del tipo di archiviazione e delle dimensioni dei nodi appropriati, provisioning dinamico dei volumi e backup dei dati.

**Esecuzione di carichi di lavoro di livello aziendale**

* [Procedure consigliate su continuità aziendale e ripristino di emergenza](operator-best-practices-multi-region.md)
    * Uso di coppie di aree, più cluster con Gestione traffico di Azure e replica geografica delle immagini del contenitore.

## <a name="developer-best-practices"></a>Procedure consigliate per lo sviluppatore

Gli sviluppatori o i proprietari di applicazioni possono semplificare l'esperienza di sviluppo e definire le esigenze di prestazioni delle applicazioni.

* [Procedure consigliate per gli sviluppatori di applicazioni per la gestione delle risorse](developer-best-practices-resource-management.md)
    * Definizione delle richieste di risorse dei pod e dei relativi limiti, configurazione degli strumenti di sviluppo e controllo dei problemi delle applicazioni.
* [Procedure consigliate per la sicurezza dei pod](developer-best-practices-pod-security.md)
    * Protezione dell'accesso alle risorse, limitazione dell'esposizione delle credenziali e uso di identità dei pod e insiemi di credenziali delle chiavi digitali.

## <a name="kubernetes--aks-concepts"></a>Concetti relativi a Kubernetes e al servizio Azure Kubernetes

Per comprendere meglio alcuni dei componenti e delle funzionalità di queste procedure consigliate, vedere anche i seguenti articoli concettuali per i cluster nel servizio Azure Kubernetes:

* [Concetti principali di Kubernetes](concepts-clusters-workloads.md)
* [Accesso e identità](concepts-identity.md)
* [Concetti relativi alla sicurezza](concepts-security.md)
* [Concetti di rete](concepts-network.md)
* [Opzioni di archiviazione](concepts-storage.md)
* [Opzioni di scalabilità](concepts-scale.md)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni introduttive sul servizio Azure Kubernetes, vedere una delle guide di avvio rapido per la distribuzione di un cluster del servizio Azure Kubernetes (AKS) tramite l'[interfaccia della riga di comando di Azure](kubernetes-walkthrough.md) o il [portale di Azure](kubernetes-walkthrough-portal.md).
