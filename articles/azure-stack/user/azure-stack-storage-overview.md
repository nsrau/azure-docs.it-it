---
title: Introduzione ad archiviazione di Azure Stack
description: Altre informazioni sull'archiviazione di Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d1baeb5ff32fcadaeca25244ce3167fe3fe4477a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249715"
---
# <a name="introduction-to-azure-stack-storage"></a>Introduzione ad archiviazione di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

## <a name="overview"></a>Panoramica

Archiviazione di Azure Stack è un set di servizi di archiviazione cloud che include i BLOB, tabelle e code che siano coerenti con i servizi di archiviazione di Azure.

## <a name="azure-stack-storage-services"></a>Servizi di archiviazione di Stack di Azure

Archiviazione di Azure Stack offre i tre servizi seguenti:

- **Archiviazione BLOB**

    L'archiviazione BLOB archivia dati non strutturati. Un BLOB può essere qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione.

- **Archiviazione tabelle**

    Nell'archivio tabelle sono archiviati set di dati strutturati. L'archiviazione delle tabelle utilizza un archivio dati chiave-attributo NoSQL, che consente lo sviluppo e l'accesso rapido a grandi quantità di dati.

- **Archiviazione code**

    L'archiviazione di accodamento offre un sistema di messaggistica affidabile per l'elaborazione del flusso di lavoro e per la comunicazione tra i componenti dei servizi cloud.

Un account di archiviazione di Azure Stack è un account sicuro che consente di accedere a servizi in archiviazione di Azure Stack. L'account di archiviazione offre uno spazio dei nomi univoco per le risorse di archiviazione. Il diagramma seguente mostra le relazioni tra le risorse di archiviazione di Azure Stack in un account di archiviazione:

![Panoramica di archiviazione di Stack di Azure](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

### <a name="blob-storage"></a>Archiviazione BLOB

Per gli utenti con una grande quantità di dati non strutturati da archiviare nel cloud, archiviazione blob offre una soluzione efficace e scalabile. È possibile usare archiviazione blob per archiviare il contenuto, ad esempio:

- Documenti
- Dati dei social network come foto, video, musica e blog
- Backup di file, computer, database e dispositivi
- Immagini e testo per applicazioni Web
- Dati di configurazione per applicazioni cloud
- Big Data, come log e altri set di dati di grandi dimensioni

Ogni BLOB è organizzato in un contenitore. I contenitori sono inoltre una soluzione utile per assegnare criteri di sicurezza a gruppi di oggetti. Un account di archiviazione può contenere un numero qualsiasi di contenitori e un contenitore può contenere qualsiasi numero di BLOB, fino al limite dell'account di archiviazione.

Archiviazione BLOB offre tre tipi di BLOB:

- **BLOB in blocchi**

    I BLOB in blocchi sono ottimizzati per lo streaming e l'archiviazione di oggetti cloud e sono un'ottima scelta per l'archiviazione dei documenti, file multimediali, backup e così via.

- **I BLOB di Accodamento**

    I BLOB di accodamento sono simili ai BLOB in blocchi, ma ottimizzati per le operazioni di Accodamento. Un BLOB di accodamento può essere aggiornato solo aggiungendo un nuovo blocco alla fine. I BLOB di accodamento sono una buona scelta per scenari quali la registrazione, dove i nuovi dati devono essere scritti solo fino alla fine del BLOB.

- **BLOB di pagine**

    I BLOB di pagine sono ottimizzati per la rappresentazione di dischi IaaS e supporto casuali scrive che è di dimensioni fino a 1 TB. IaaS disco è un disco rigido virtuale archiviato come blob di pagine collegato a una macchina virtuale di Azure Stack.

### <a name="table-storage"></a>Archiviazione tabelle

Le applicazioni moderne spesso richiedono l'utilizzo di archivi dati con scalabilità e flessibilità maggiori di quelli delle versioni precedenti del software. Il servizio di archiviazione tabelle offre un archivio a disponibilità elevata e altamente scalabile, per consentire la scalabilità automatica dell'applicazione in base alle richieste degli utenti. Archiviazione tabelle è l'archivio di chiavi/attributi NoSQL di Microsoft, ha una struttura senza schema, che lo rende diverso dai database relazionali tradizionali. Con un archivio dati senza schema è facile adattare i dati con il variare delle esigenze dell'applicazione. L'archiviazione tabelle è semplice da usare e consente di velocizzare lo sviluppo e la creazione di applicazioni.

Archiviazione tabelle è un archivio chiave-attributo, il che significa che ogni valore in una tabella viene archiviato con un nome di proprietà tipizzato. Archiviazione tabelle è un archivio chiave-attributo, ovvero ogni valore di una tabella è archiviato con un nome di proprietà tipizzato che può essere usato per filtrare i dati e specificare i criteri di selezione. Una raccolta di proprietà con i rispettivi valori costituisce un'entità. Poiché l'archiviazione tabelle è senza schema, due entità nella stessa tabella possono contenere raccolte diverse di proprietà e tali proprietà possono essere di tipi diversi.

È possibile usare l'archiviazione tabelle per archiviare set di dati flessibili, ad esempio i dati utente per le applicazioni web, rubriche, informazioni sul dispositivo e qualsiasi altro tipo di metadati richiesti dal servizio. Per le applicazioni basate su Internet di oggi, i database NoSQL come l'archivio tabelle offrono una comoda alternativa ai database relazionali tradizionali.

Un account di archiviazione può contenere un numero qualsiasi di tabelle e una tabella può contenere qualsiasi numero di entità, fino al limite di capacità dell'account di archiviazione.

### <a name="queue-storage"></a>Archiviazione code

Durante la progettazione di applicazioni scalabili, i componenti dell'applicazione vengono spesso separati, per poter essere scalati in modo indipendente. Archiviazione code offre una soluzione di messaggistica affidabile per la comunicazione asincrona tra i componenti dell'applicazione, che siano in esecuzione nel cloud, sul desktop, in un server in locale o in un dispositivo mobile. Supporta inoltre la gestione di attività asincrone e la creazione di flussi di lavoro dei processi.

Un account di archiviazione può contenere qualsiasi numero di code e una coda può contenere qualsiasi numero di messaggi, fino al limite di capacità dell'account di archiviazione. I singoli messaggi possono avere una dimensione massima di 64 KB.

## <a name="next-steps"></a>Passaggi successivi

- [Archiviazione coerenti con Azure: differenze e considerazioni](azure-stack-acs-differences.md)

- Per altre informazioni sull'archiviazione di Azure, vedere [Introduzione ad archiviazione di Microsoft Azure](../../storage/common/storage-introduction.md)
