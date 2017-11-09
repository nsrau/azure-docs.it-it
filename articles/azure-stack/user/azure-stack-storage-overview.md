---
title: Introduzione all'archiviazione di Azure Stack
description: Informazioni sull'archiviazione di Azure Stack
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 8777aa486a627cf8b2d8ba443e115638354d10da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-stack-storage"></a>Introduzione all'archiviazione di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

## <a name="overview"></a>Panoramica
Stack di archiviazione di Azure è un set di servizi di archiviazione cloud inclusi BLOB, tabelle e le code che siano coerenti con i servizi di archiviazione di Azure.

## <a name="azure-stack-storage-services"></a>Servizi di Stack archiviazione di Azure
Archiviazione di Azure Stack fornisce i tre servizi seguenti:

* **Archiviazione BLOB** 

    Archiviazione BLOB archivia i dati di oggetto non strutturati. Un BLOB può essere qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione.
* **Archiviazione tabelle** 

    Nell'archivio tabelle sono archiviati set di dati strutturati. L'archiviazione delle tabelle utilizza un archivio dati chiave-attributo NoSQL, che consente lo sviluppo e l'accesso rapido a grandi quantità di dati.
* **Archiviazione code** 

    L'archiviazione di accodamento offre un sistema di messaggistica affidabile per l'elaborazione del flusso di lavoro e per la comunicazione tra i componenti dei servizi cloud.

Un account di archiviazione di Azure Stack è un account sicuro che consente di accedere ai servizi dello Stack di archiviazione di Azure. L'account di archiviazione offre uno spazio dei nomi univoco per le risorse di archiviazione. Il diagramma seguente mostra le relazioni tra le risorse di archiviazione di Azure Stack in un account di archiviazione:

![Cenni preliminari su Stack archiviazione di Azure](media/azure-stack-storage-overview/AzureStackStorageOverview.png)


### <a name="blob-storage"></a>Archiviazione BLOB

Per gli utenti con una grande quantità di dati non strutturati oggetto per l'archiviazione nel cloud, archiviazione Blob offre una soluzione efficace e scalabile. È possibile usare gli archivi BLOB per archiviare contenuto come:

* Documenti
* Dati dei social network come foto, video, musica e blog
* Backup di file, computer, database e dispositivi
* Immagini e testo per applicazioni Web
* Dati di configurazione per applicazioni cloud
* Big Data, come log e altri set di dati di grandi dimensioni

Ogni BLOB è organizzato in un contenitore. I contenitori sono inoltre una soluzione utile per assegnare criteri di sicurezza a gruppi di oggetti. Un account di archiviazione può contenere qualsiasi numero di contenitori e un contenitore può contenere qualsiasi numero di BLOB, fino al limite dell'account di archiviazione.

Archiviazione BLOB offre tre tipi di BLOB: 
* **BLOB in blocchi** 

    I BLOB in blocchi sono ottimizzati per lo streaming e l'archiviazione di oggetti cloud e sono una soluzione adatta per l'archiviazione di documenti, file multimediali, backup e così via.
* **BLOB di aggiunta** 

    I BLOB di accodamento sono simili ai BLOB in blocchi, ma ottimizzati per le operazioni di Accodamento. Un BLOB di accodamento può essere aggiornato solo aggiungendo un nuovo blocco alla fine. I BLOB di accodamento sono una buona scelta per scenari quali la registrazione, dove i nuovi dati devono essere scritti solo fino alla fine del BLOB.
* **BLOB di pagine** 

    BLOB di pagine sono ottimizzati per che rappresentano i dischi di IaaS e supporto casuale scritture che è di dimensioni fino a 1 TB. Una macchina virtuale di Azure Stack collegato IaaS disco è un disco rigido virtuale archiviato come blob di pagine.


### <a name="table-storage"></a>Archiviazione tabelle
Le applicazioni moderne spesso richiedono l'utilizzo di archivi dati con scalabilità e flessibilità maggiori di quelli delle versioni precedenti del software. Il servizio di archiviazione tabelle offre un archivio a disponibilità elevata e altamente scalabile, per consentire la scalabilità automatica dell'applicazione in base alle richieste degli utenti. Archiviazione tabelle è l'archivio di chiavi/attributi NoSQL di Microsoft. Ha una struttura senza schema ed è quindi diverso dai database relazionali tradizionali. Con un archivio dati senza schema è facile adattare i dati con il variare delle esigenze dell'applicazione. L'archiviazione tabelle è semplice da usare e consente di velocizzare lo sviluppo e la creazione di applicazioni.

L'archiviazione tabelle è semplice da usare e consente di velocizzare lo sviluppo e la creazione di applicazioni. Archiviazione tabelle è un archivio chiave-attributo, ovvero ogni valore di una tabella è archiviato con un nome di proprietà tipizzato che può essere usato per filtrare i dati e specificare i criteri di selezione. Una raccolta di proprietà con i rispettivi valori costituisce un'entità. Poiché l'archiviazione tabelle non ha uno schema, due entità della stessa tabella possono contenere raccolte diverse di proprietà, che possono essere di tipi diversi.

È possibile usare l'archiviazione tabelle per archiviare set di dati flessibili, ad esempio i dati utente per le applicazioni Web, le rubriche, le informazioni sui dispositivi e altri tipi di metadati richiesti dal servizio. Per le attuali applicazioni basate su Internet, i database NoSQL come l'archivio tabelle offrono una comoda alternativa ai database relazionali tradizionali.

Un account di archiviazione può contenere un numero qualsiasi di tabelle e una tabella può contenere qualsiasi numero di entità, fino al limite di capacità dell'account di archiviazione.

### <a name="queue-storage"></a>Archiviazione code
Durante la progettazione di applicazioni scalabili, i componenti dell'applicazione vengono spesso separati, per poter essere scalati in modo indipendente. L'archiviazione di accodamento offre una soluzione di messaggistica affidabile per la comunicazione asincrona tra i componenti dell'applicazione, che possono essere eseguiti nel cloud, in un desktop, in un server locale o in un dispositivo mobile. Supporta inoltre la gestione di attività asincrone e la creazione di flussi di lavoro dei processi.

Un account di archiviazione può contenere qualsiasi numero di code e una coda può contenere qualsiasi numero di messaggi, fino al limite di capacità dell'account di archiviazione. I singoli messaggi possono avere una dimensione massima di 64 KB.

## <a name="next-steps"></a>Passaggi successivi
* [Archiviazione di Azure-coerente: considerazioni e le differenze](azure-stack-acs-differences.md)

* Per ulteriori informazioni sull'archiviazione di Azure, vedere [Introduzione all'archiviazione di Microsoft Azure](../../storage/common/storage-introduction.md)

