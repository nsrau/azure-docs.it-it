---
title: 'Servizio contenitore di Azure: domande frequenti | Documentazione Microsoft'
description: Risposte alle domande frequenti sul servizio contenitore di Azure, che semplifica la creazione, la configurazione e la gestione di un cluster di macchine virtuali per l&quot;esecuzione di app contenitore Docker.
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Contenitori, Micro-servizi, Mesos, Azure, Kubernetes
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 3bb83f231d16819e5f5da7edbc9fc3f38baff011
ms.openlocfilehash: b0c5efa595b0377d7ae2d936d0394667356d18c9


---
# <a name="frequently-asked-questions-azure-container-service"></a>Domande frequenti: servizio contenitore di Azure


## <a name="orchestrators"></a>Agenti di orchestrazione

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Quali agenti di orchestrazione contenitore sono supportati nel servizio contenitore di Azure? 

È previsto il supporto per DC/OS open source, Docker Swarm e Kubernetes. Il supporto per DC/OS e Docker Swarm è disponibile a livello generale, mentre il supporto per Kubernetes è attualmente in anteprima. Per altre informazioni, vedere la [panoramica](container-service-intro.md).
 
### <a name="do-you-support-swarm-mode"></a>La modalità Swarm è supportata? 

La modalità Swarm attualmente non è supportata, ma è presente nella guida di orientamento al servizio. 

### <a name="does-azure-container-service-support-windows-containers"></a>Il servizio contenitore di Azure supporta i contenitori Windows?  

Attualmente sono supportati i contenitori Linux. Il supporto per i contenitori Windows con gli agenti di orchestrazione DC/OS, Docker Swarm e Kubernetes è presente nella guida di orientamento al servizio. 

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>È consigliabile un agente di orchestrazione specifico nel servizio contenitore di Azure? 
In genere non viene consigliato un agente di orchestrazione specifico. Se si ha già familiarità con uno degli agenti di orchestrazione supportati, è possibile servirsi di tale esperienza per il servizio contenitore di Azure. Le tendenze dei dati suggeriscono tuttavia che DC/OS è il prodotto collaudato per i carichi di lavoro di Big Data e IoT, Kubernetes è ideale per i carichi di lavoro nativi del cloud e Docker Swarm è noto per l'integrazione con gli strumenti di Docker e la facile curva di apprendimento.

A seconda dello scenario, è anche possibile compilare e gestire soluzioni contenitore personalizzate con altri servizi di Azure. Questi servizi includono [Macchine virtuali](../virtual-machines/virtual-machines-linux-azure-overview.md), [Service Fabric](../service-fabric/service-fabric-overview.md), [App Web](../app-service-web/app-service-web-overview.md) e [Batch](../batch/batch-technical-overview.md).  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Qual è la differenza tra il servizio contenitore di Azure e il motore ACS? 
Il servizio contenitore di Azure è un servizio di Azure supportato dal Contratto di servizio con funzionalità nel portale di Azure, strumenti da riga di comando di Azure e API di Azure. Il servizio consente di implementare rapidamente e di gestire i cluster che eseguono strumenti di orchestrazione contenitore standard con un numero relativamente basso di scelte di configurazione. 

Il [motore ACS](http://github.com/Azure/acs-engine) è un progetto open source che consente agli utenti esperti di personalizzare la configurazione dei cluster a ogni livello. Essendo possibile modificare la configurazione sia dell'infrastruttura che del software, non è previsto nessun Contratto di servizio per il motore ACS. Il supporto viene gestito tramite il progetto open source in GitHub invece che tramite i canali Microsoft ufficiali. 

## <a name="cluster-management"></a>Gestione dei cluster

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>Come si creano le chiavi SSH per il cluster?

È possibile usare gli strumenti standard del sistema operativo per creare una coppia di chiavi pubblica e privata RSA SSH per l'autenticazione nelle macchine virtuali Linux per il cluster. Per i passaggi, vedere il materiale sussidiario per [OS X e Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) o per [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). 

Se si usano i [comandi dell'interfaccia della riga di comando di Azure 2.0 (anteprima)](container-service-create-acs-cluster-cli.md) per distribuire un cluster del servizio contenitore, le chiavi SSH possono venire generate automaticamente per il cluster.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Come si crea un'entità servizio per il cluster Kubernetes?

Per creare un cluster Kubernetes nel servizio contenitore di Azure, sono necessari anche l'ID e la password di un'entità servizio di Azure Active Directory. Per altre informazioni, vedere [Informazioni sull'entità servizio per un cluster Kubernetes](container-service-kubernetes-service-principal.md).


Se si usano i [comandi dell'interfaccia della riga di comando di Azure 2.0 (anteprima)](container-service-create-acs-cluster-cli.md) per distribuire un cluster Kubernetes, le credenziali dell'entità servizio possono venire generate automaticamente per il cluster.


### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>Come si aumenta il numero di master dopo la creazione di un cluster? 
Dopo la creazione del cluster, il numero di master è fisso e non può essere modificato. Durante la creazione del cluster, è consigliabile selezionare tre o cinque master per una disponibilità elevata.

> [!NOTE]
> Nell'anteprima un cluster Kubernetes nel servizio contenitore di Azure può avere solo un master.
>

### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>Come si aumenta il numero di agenti dopo la creazione di un cluster? 
È possibile ridimensionare il numero di agenti nel cluster usando il portale di Azure o gli strumenti da riga di comando. Vedere [Ridimensionare un cluster del servizio contenitore di Azure](container-service-scale.md).

> [!NOTE]
> Nell'anteprima, un cluster Kubernetes nel servizio contenitore di Azure ha un numero fisso di agenti. 
>

### <a name="what-are-the-urls-of-my-masters-and-agents"></a>Quali sono gli URL dei master e degli agenti? 
Gli URL delle risorse cluster nel servizio contenitore di Azure si basano sul prefisso del nome DNS specificato e sul nome dell'area di Azure scelto per la distribuzione. Ad esempio, il nome di dominio completo (FQDN) del nodo master presenta questo formato:

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

È possibile trovare gli URL di uso più frequente per il cluster nel portale di Azure, in Esplora risorse di Azure o in altri strumenti di Azure.
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>Dove si trova la stringa di connessione SSH al cluster?

È possibile trovare la stringa di connessione nel portale di Azure oppure usando gli strumenti da riga di comando di Azure. 

1. Nel portale passare al gruppo di risorse per la distribuzione del cluster.  

2. Fare clic su **Panoramica** e fare clic sul collegamento **Distribuzioni** in **Informazioni di base**. 

3. Nel pannello **Cronologia distribuzioni** fare clic sulla distribuzione con il nome che inizia con **microsoft-acs** seguito da una data di distribuzione. Esempio: microsoft-acs-201701310000.  

4. Nella pagina **Riepilogo** in **Output** risultano diversi collegamenti al cluster <provided></provided>. **SSHMaster0** fornisce una stringa di connessione SSH al primo master nel cluster del servizio contenitore. 

Come già osservato, è anche possibile usare gli strumenti di Azure per trovare il nome FQDN del master. Stabilire una connessione SSH al master usando il nome FQDN del master e il nome utente specificato durante la creazione del cluster. Ad esempio:

```bash
ssh userName@masterFQDN –A –p 22 
```

Per altre informazioni, vedere [Connettersi a un cluster del servizio contenitore di Azure](container-service-connect.md).




## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni](container-service-intro.md) sul servizio contenitore di Azure.
* Distribuire un cluster del servizio contenitore usando il [portale](container-service-deployment.md) o la [versione 2.0 (anteprima) dell'interfaccia della riga di comando di Azure](container-service-create-acs-cluster-cli.md).


<!--HONumber=Feb17_HO3-->


