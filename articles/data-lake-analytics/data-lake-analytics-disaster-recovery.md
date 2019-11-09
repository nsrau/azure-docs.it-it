---
title: Linee guida per il ripristino di emergenza per Azure Data Lake Analytics
description: Informazioni su come pianificare il ripristino di emergenza per gli account Azure Data Lake Analytics.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f9b22e6b806f76189134ec63c83d48f48bf95587
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889769"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Linee guida per il ripristino di emergenza per Azure Data Lake Analytics

Azure Data Lake Analytics è un servizio per processi di analisi su richiesta che semplifica l'uso dei Big Data. Anziché distribuire, configurare e ottimizzare l'hardware, è possibile scrivere query per trasformare i dati ed estrarre informazioni di interesse. Con il servizio di analisi è possibile gestire processi di qualsiasi dimensione immediatamente definendo il livello e l'ambito necessari. Verrà addebitato un costo per il processo solo durante la sua esecuzione, per la massima convenienza. Questo articolo fornisce indicazioni su come proteggere i processi da interruzioni rare a livello di area o da eliminazioni accidentali.

## <a name="disaster-recovery-guidance"></a>Indicazioni sul ripristino di emergenza

Quando si usa Azure Data Lake Analytics, è essenziale preparare il proprio piano di ripristino di emergenza. Questo articolo consente di creare un piano di ripristino di emergenza. Sono disponibili risorse aggiuntive che consentono di creare un piano personalizzato:
- [Ripristino di emergenza e in caso di errori per le applicazioni Azure](/azure/architecture/reliability/disaster-recovery)
- [Indicazioni tecniche sulla resilienza di Azure](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>Procedure consigliate e indicazioni sugli scenari

È possibile eseguire un processo U-SQL ricorrente in un account Anna in un'area che legge e scrive le tabelle U-SQL, nonché i dati non strutturati.  Per preparare un'emergenza, seguire questa procedura:

1. Creare gli account Anna e ADLS nell'area secondaria che verranno usati durante un'interruzione del servizio.

   > [!NOTE]
   > Poiché i nomi degli account sono globalmente univoci, usare uno schema di denominazione coerente che indica quale account è secondario.

2. Per i dati non strutturati, fare riferimento alle [linee guida per il ripristino di emergenza dei dati in Azure Data Lake storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. Per i dati strutturati archiviati in tabelle e database Anna, è possibile creare copie degli elementi dei metadati, ad esempio database, tabelle, funzioni con valori di tabella e assembly. È necessario risincronizzare periodicamente questi artefatti quando si verificano modifiche nell'ambiente di produzione. Ad esempio, è necessario replicare i dati appena inseriti nell'area secondaria copiando i dati e inserendoli nella tabella secondaria.

   > [!NOTE]
   > Questi nomi di oggetto hanno come ambito l'account secondario e non sono globalmente univoci, quindi possono avere gli stessi nomi dell'account di produzione primario.

Durante un'interruzione, è necessario aggiornare gli script in modo che i percorsi di input puntino all'endpoint secondario. Quindi, gli utenti inviano i propri processi all'account Anna nell'area secondaria. L'output del processo verrà quindi scritto nell'account Anna e ADLS nell'area secondaria.

## <a name="next-steps"></a>Passaggi successivi

[Linee guida per il ripristino di emergenza dei dati in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
