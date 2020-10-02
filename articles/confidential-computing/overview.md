---
title: Panoramica del confidential computing di Azure
description: Panoramica del confidential computing di Azure
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 16f45c39a329998f4b4da4ea89315683a0fab790
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90967594"
---
# <a name="confidential-computing-on-azure"></a>Confidential computing in Azure

Il confidential computing di Azure consente di isolare i dati sensibili durante l'elaborazione nel cloud. Il confidential computing viene usato in molti settori per:

- Proteggere i dati finanziari
- Proteggere le informazioni dei pazienti
- Eseguire processi di Machine Learning su informazioni riservate
- Eseguire algoritmi su set di dati crittografati provenienti da più origini


## <a name="overview"></a>Panoramica
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Sappiamo quanto sia importante proteggere i dati sul cloud e ascoltiamo le preoccupazioni dei nostri clienti. Ecco alcune domande che i nostri clienti possono porre quando trasferiscono carichi di lavoro sensibili nel cloud: 

- Come assicurarsi che Microsoft non possa accedere ai dati crittografati?
- Come è possibile prevenire le minacce alla sicurezza poste da amministratori con privilegi all'interno dell'azienda?
- In quali altri modi è possibile impedire a terze parti di accedere ai dati sensibili dei clienti?

Microsoft Azure aiuta a ridurre al minimo la superficie di attacco per rafforzare la protezione dei dati. Azure offre già molti strumenti per salvaguardare i [**dati inattivi**](../security/fundamentals/encryption-atrest.md) tramite modelli come la crittografia lato client e lato server. Inoltre, Azure include meccanismi per crittografare i [**data in transito**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) tramite protocolli sicuri come TLS e HTTPS. Questa pagina include un'introduzione a un terzo tipo di crittografia dei dati, la crittografia dei **dati in uso**.

## <a name="introduction-to-confidential-computing"></a>Introduzione al confidential computing  

Il termine confidential computing è stato definito nel settore dal [Confidential Computing Consortium](https://confidentialcomputing.io/) (CCC), una fondazione che si occupa di definire e accelerare l'adozione di questa metodologia. Il CCC definisce il confidential computing in questo modo: protezione dei dati in uso mediante l'esecuzione di calcoli in un ambiente TEE (Trusted Execution Environment) basato su hardware.

TEE è un ambiente che impone l'esecuzione esclusivamente di codice autorizzato. I dati inclusi nel TEE non possono essere letti o manomessi da codice all'esterno dell'ambiente. 

### <a name="lessen-the-need-for-trust"></a>Ridurre la necessità di fiducia
L'esecuzione di carichi di lavoro nel cloud richiede fiducia. Si considerano attendibili i vari provider che rendono disponibili i diversi componenti dell'applicazione.


**Fornitore di software delle app**: considerare attendibile il software tramite la distribuzione in locale, l'uso di open source o con lo sviluppo interno del software delle applicazioni.

**Fornitori di hardware**: considerare attendibile l'hardware usando hardware in locale o interno. 

**Provider di infrastruttura**: considerare attendibili i provider o gestire i propri data center in locale.


Con il confidential computing di Azure è più facile considerare attendibile il provider di servizi cloud per vari aspetti dell'infrastruttura di cloud computing. Il confidential computing di Azure riduce la necessità di considerare attendibili il kernel del sistema operativo host, l'hypervisor, l'amministratore delle VM e l'amministratore dell'host.

### <a name="reducing-the-attack-surface"></a>Riduzione della superficie di attacco
TCB (Trusted Computing Base) fa riferimento a tutti i componenti hardware, firmware e software di un sistema che costituiscono un ambiente sicuro. I componenti all'interno di TCB sono considerati "critici". Se un componente viene compromesso, potrebbe essere messa a rischio la sicurezza dell'intero sistema. 

Una quantità di componenti TCB più bassa implica una maggiore sicurezza. Implica infatti un rischio di esposizione minore a varie vulnerabilità, malware, attacchi e utenti malintenzionati. L'obiettivo del confidential computing di Azure è ridurre i componenti TCB per i carichi di lavoro sul cloud offrendo ambienti TEE. Gli ambienti TEE riducono la quantità di componenti TCB includendo solo binari di runtime, codice e librerie. Quando si usano l'infrastruttura e i servizi di Azure per il confidential computing, è possibile rimuovere tutti i componenti Microsoft da TCB.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Uso di Azure per il confidential computing basato sul cloud<a id="cc-on-azure"></a>

Il confidential computing di Azure consente di sfruttarne le funzionalità in un ambiente virtuale. È ora possibile usare gli strumenti, il software e l'infrastruttura cloud per creare soluzioni basate su hardware sicuro.  

**Impedire l'accesso non autorizzato**: gestire i dati sensibili nel cloud. Azure offre la migliore protezione possibile per i dati, senza la necessità di cambiare completamente le attuali procedure.

**Conformità alle normative**: eseguire la migrazione al cloud e mantenere il controllo completo dei dati per soddisfare le normative statali in merito alla protezione delle informazioni personali e alla protezione della proprietà intellettuale dell'organizzazione.

**Collaborazione sicura e non attendibile**: affrontare i problemi lavorativi a livello di settore combinando i dati tra organizzazioni, anche concorrenti, per ampliare l'analisi e ricavare informazioni più approfondite.

**Elaborazione isolata**: offrire una nuova categoria di prodotti che rimuovono la responsabilità sui dati privati con l'elaborazione nascosta. I dati degli utenti non possono essere recuperati neanche dal provider di servizi. 

## <a name="get-started"></a>Introduzione
### <a name="azure-compute"></a>Calcolo di Azure
Creare applicazioni basate sulle offerte IaaS di confidential computing di Azure.
- Macchine virtuali (VM): [serie DCsv2](confidential-computing-enclaves.md)
- Servizio Azure Kubernetes: [orchestrare i contenitori riservati](confidential-nodes-aks-overview.md)

### <a name="azure-security"></a>Sicurezza di Azure 
Assicurarsi che i carichi di lavoro siano protetti tramite metodi di verifica e gestione delle chiavi associate all'hardware. 
- Attestazione: [Attestazione di Microsoft Azure (anteprima)](https://docs.microsoft.com/azure/attestation/overview)
- Gestione delle chiavi: modulo di protezione hardware gestito (anteprima)

### <a name="develop"></a>Sviluppo
Iniziare a sviluppare applicazioni con riconoscimento dell'enclave e distribuire algoritmi riservati con il framework di inferenza dei dati riservati.
- Scrivere applicazioni da eseguire in macchine virtuali DCsv2: [Open-enclave SDK](https://github.com/openenclave/openenclave)
- Modelli di ML riservati nel runtime ONNX: [inferenza di dati riservati (beta)](https://aka.ms/confidentialinference)

## <a name="next-steps"></a>Passaggi successivi

Distribuire una macchina virtuale serie DCsv2 e installarvi Open Enclave SDK.

> [!div class="nextstepaction"]
> [Distribuire una VM di confidential computing in Azure Marketplace](quick-create-marketplace.md)
