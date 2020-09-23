---
title: Contenitori riservati in Azure Kubernetes Service (AKS)
description: Informazioni sul supporto di contenitori non modificati nei contenitori riservati.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 9/22/2020
ms.author: amgowda
ms.service: container-service
ms.openlocfilehash: 528b843e24e1d63e4822c253b3636ef490e8fe8e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997805"
---
# <a name="confidential-containers"></a>Contenitori riservati

## <a name="overview"></a>Panoramica

Consentire agli sviluppatori di importare un' **applicazione Docker esistente (nuova o esistente)** ed eseguirla in modo sicuro in Azure Kubernetes Service (AKS) tramite il supporto dei nodi di calcolo riservati.

I contenitori riservati consentono di proteggere:

- integrità dei dati 
- riservatezza dei dati
- integrità del codice
- protezione del codice contenitore tramite crittografia
- assicurazioni basate su hardware
- Consenti l'esecuzione di app esistenti
- Crea radice hardware di attendibilità

Un ambiente di esecuzione attendibile basato su hardware è un componente importante usato per fornire garanzie sicure attraverso le misurazioni hardware e software dei componenti TCB (Trusted computing base). Le verifiche di queste misurazioni sono utili per la convalida del calcolo previsto e per verificare eventuali manomissioni delle app contenitore.

I contenitori riservati supportano applicazioni personalizzate sviluppate con **Python, Java, node js e così via o applicazioni software in pacchetto come Nginx, cache Redis, Memcache**e così via, da eseguire senza modifiche su AKS.

I contenitori riservati rappresentano il percorso più veloce per la riservatezza dei contenitori, inclusa la protezione del contenitore tramite la crittografia, che consente l'uso Lift-and-Shift senza modifiche minime alla logica di business.

![Conversione del contenitore riservato](./media/confidential-containers/conf-con-deploy-process.jpg)


## <a name="confidential-container-enablers"></a>Abilitatori contenitore riservati

Per eseguire un contenitore Docker esistente, le applicazioni nei nodi di elaborazione riservati richiedono un livello di astrazione o un software SGX per sfruttare il set di istruzioni della CPU speciale. Il software SGX consente inoltre di proteggere il codice delle applicazioni riservate e di creare un'esecuzione diretta sulla CPU per rimuovere il sistema operativo guest, il sistema operativo host o l'hypervisor. Questa protezione riduce la superficie di attacco globale e le vulnerabilità con i livelli del sistema operativo o dell'hypervisor.

I contenitori riservati sono completamente supportati su AKS e sono abilitati tramite i partner di Azure e i progetti open source software (OSS). Gli sviluppatori possono scegliere i provider di software in base alle funzionalità, all'integrazione con i servizi di Azure e al supporto degli strumenti.

## <a name="partner-enablers"></a>Abilitatori partner
> [!NOTE]
> Le soluzioni seguenti sono offerte tramite partner Azure e possono comportare costi di licenza. Verificare le condizioni del software per i partner in modo indipendente. 

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) offre agli sviluppatori la possibilità di scegliere tra un portale e un'esperienza basata sull'interfaccia della riga di comando per riunire le applicazioni in contenitori e coprirle in contenitori SGX idonei, senza dover modificare o ricompilare l'applicazione. Fortanix offre la flessibilità necessaria per l'esecuzione e la gestione del set più ampio di applicazioni, incluse le applicazioni esistenti, le nuove applicazioni native enclave e le applicazioni pre-incluse nel pacchetto. Gli utenti possono iniziare a usare l'interfaccia utente di [gestione enclave](https://em.fortanix.com/) o le [API REST](https://www.fortanix.com/api/em/) per creare contenitori riservati seguendo la Guida [avvio rapido](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) per il servizio Azure Kubernetes.

![Processo di distribuzione di Fortanix](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain)

[Scone](https://scontain.com/index.html?lang=en) supporta i criteri di sicurezza che possono generare certificati, chiavi e segreti e garantisce che siano visibili solo ai servizi attestati di un'applicazione. In questo modo, i servizi di un'applicazione si attestano automaticamente reciprocamente tramite TLS, senza la necessità di modificare le applicazioni e TLS. Questa operazione viene illustrata con l'aiuto di una semplice applicazione Flask: https://sconedocs.github.io/flask_demo/  

FOCACCINa può convertire la maggior parte dei file binari esistenti in applicazioni eseguite all'interno di enclave senza dover modificare l'applicazione o ricompilare l'applicazione. SCONE protegge inoltre linguaggi interpretati come Python crittografando sia i file di dati che i file di codice Python. Con l'ausilio di un criterio di sicurezza SCONE, è possibile proteggere i file crittografati da accessi non autorizzati, modifiche e rollback. Come "sconify" un'applicazione Python esistente è illustrata [qui](https://sconedocs.github.io/sconify_image/)

![Flusso Scontain](./media/confidential-containers/scone-workflow.png)

Le distribuzioni di Scone nei nodi di elaborazione riservati con AKS sono completamente supportate e integrate. Inizia a usare un'applicazione di esempio https://sconedocs.github.io/aks/

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) fornisce il software della piattaforma SGX che consente di eseguire contenitori non modificati su AKS. Scopri di più sulle funzionalità **imminenti** e il flusso utente [qui](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp).

Introduzione a una cache Redis di esempio e a un'applicazione personalizzata Python [qui](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)

![Processo Anjuna](./media/confidential-containers/anjuna-process-flow.png)

## <a name="oss-enablers"></a>Abilitatori OSS 
> [!NOTE]
> Le soluzioni seguenti sono offerte tramite progetti open source e non sono direttamente affiliate ad Azure Confidential Computing (ACC) o Microsoft.  

### <a name="graphene"></a>Grafene

[Grafene](https://grapheneproject.io/) è un sistema operativo guest leggero, progettato per l'esecuzione di una singola applicazione Linux con requisiti di host minimi. Grafene è in grado di eseguire applicazioni in un ambiente isolato con vantaggi paragonabili all'esecuzione di un sistema operativo completo ed è dotato di un supporto efficace per la conversione di un'applicazione contenitore Docker esistente in grafene schermate Containers (SGC).

Introduzione a un'applicazione di esempio e alla distribuzione in AKS [qui](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) è un sistema operativo di libreria a più processi e indipendente dalla memoria (LibOS) per Intel SGX. Consente l'esecuzione di applicazioni legacy in SGX con modifiche minime al codice sorgente. Occlum protegge in modo trasparente la riservatezza dei carichi di lavoro degli utenti, consentendo allo stesso tempo un semplice passaggio alle applicazioni Docker esistenti.

Occlum supporta le distribuzioni AKS. Seguire le istruzioni per la distribuzione con diverse app di esempio [qui](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)


## <a name="confidential-containers-demo"></a>Demo sui contenitori riservati
Visualizza la demo riservata sul settore sanitario con i contenitori riservati. Esempio è disponibile [qui](https://github.com/Azure-Samples/confidential-container-samples/blob/main/confidential-healthcare-scone-confinf-onnx/README.md). 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Entra in contatto

Hai domande con la tua implementazione o vuoi diventare un fattore di abilitazione? Inviare un messaggio di posta elettronica a acconaks@microsoft.com

## <a name="reference-links"></a>Collegamenti di riferimento

[Attestazione Microsoft Azure](../attestation/overview.md)

[Macchine virtuali DCsv2](virtual-machine-solutions.md)

[Servizio Azure Kubernetes](../aks/intro-kubernetes.md)
