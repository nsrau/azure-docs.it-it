---
title: Strumenti di sviluppo di Azure Confidential computing
description: Usare gli strumenti e le librerie per sviluppare applicazioni per l'elaborazione riservata
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 6bb3b8dbc7887419f7901a52b56c25f60c869abb
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997850"
---
# <a name="application-development-on-intel-sgx"></a>Sviluppo di applicazioni su Intel SGX 


L'infrastruttura di elaborazione riservata richiede strumenti e software specifici. Questa pagina illustra in modo specifico i concetti relativi allo sviluppo di applicazioni per le macchine virtuali di Azure Confidential computing in esecuzione su Intel SGX. Prima di leggere questa pagina, [leggere l'introduzione di macchine virtuali e enclavi Intel SGX](confidential-computing-enclaves.md). 

Per sfruttare le potenzialità delle enclavi e degli ambienti isolati, è necessario usare strumenti che supportano il confidential computing. Sono disponibili diversi strumenti che supportano lo sviluppo di applicazioni enclave. È ad esempio possibile usare questi framework open source: 

- [Open enclave Software Development Kit (OE SDK)](#oe-sdk)
- [CCF (Confidential Consortium Framework)](#ccf)

## <a name="overview"></a>Panoramica

Un'applicazione creata con enclavi viene partizionata in due modi:

1. Un componente "non attendibile" (l'host)
1. Un componente "attendibile" (l'enclave)


![Sviluppo di app](media/application-development/oe-sdk.png)


**L'host** è dove l'applicazione enclave è in esecuzione ed è un ambiente non attendibile. Il codice dell’enclave distribuito sull'host non è accessibile dall'host. 

**L'enclave** è il punto in cui viene eseguito il codice dell'applicazione e i relativi dati memorizzati nella cache/memoria. I calcoli protetti devono essere eseguiti nell'enclave per garantire la protezione continua di segreti e dati sensibili. 


Durante la progettazione dell'applicazione è importante identificare e determinare quale parte dell'applicazione deve essere eseguita nelle enclavi. Il codice che si sceglie di inserire nel componente attendibile viene isolato dal resto dell'applicazione. Una volta inizializzata l'enclave e caricato il codice in memoria, tale codice non può essere letto o cambiato dai componenti non attendibili. 

## <a name="open-enclave-software-development-kit-oe-sdk"></a>Open Enclave Software Development Kit (OE SDK) <a id="oe-sdk"></a>

Se si vuole scrivere codice da eseguire in un'enclave, usare una libreria o un framework supportato dal provider. [Open Enclave SDK](https://github.com/openenclave/openenclave) è un SDK open source che consente l'astrazione per diversi componenti hardware abilitati per il confidential computing. 

Open Enclave SDK è progettato come singolo livello di astrazione per qualsiasi componente hardware e per qualsiasi CSP. Open Enclave SDK può essere usato nelle macchine virtuali di confidential computing di Azure per creare ed eseguire applicazioni sulle enclavi.

## <a name="confidential-consortium-framework-ccf"></a>Framework di Consorzio riservato (CCF) <a id="ccf"></a>

[CCF](https://github.com/Microsoft/CCF) è una rete distribuita di nodi, ognuno dei quali esegue le proprie enclavi. La rete dei nodi attendibili consente di eseguire un Ledger distribuito. Il Ledger fornisce componenti sicuri e affidabili per il protocollo da usare. 

![Nodi CCF](media/application-development/ccf.png)

Questo framework open source garantisce una riservatezza elevata, con granularità fine e una governance del Consorzio per blockchain. Con ogni nodo che usa TEEs è possibile garantire il consenso sicuro e l'elaborazione delle transazioni.


## <a name="next-steps"></a>Passaggi successivi 
- [Distribuire una macchina virtuale della serie DCsv2 di calcolo riservata](quick-create-portal.md)
- [Scaricare e installare l'SDK di OE e iniziare a sviluppare applicazioni](https://github.com/openenclave/openenclave)