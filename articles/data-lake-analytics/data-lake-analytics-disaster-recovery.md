---
title: Indicazioni per il ripristino di emergenza per Azure Data Lake AnalyticsDisaster recovery guidance for Azure Data Lake Analytics
description: Informazioni su come pianificare il ripristino di emergenza per gli account di Azure Data Lake Analytics.Learn how to plan disaster recovery for your Azure Data Lake Analytics accounts.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f9b22e6b806f76189134ec63c83d48f48bf95587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889769"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Indicazioni per il ripristino di emergenza per Azure Data Lake AnalyticsDisaster recovery guidance for Azure Data Lake Analytics

Azure Data Lake Analytics è un servizio per processi di analisi su richiesta che semplifica l'uso dei Big Data. Anziché distribuire, configurare e ottimizzare l'hardware, è possibile scrivere query per trasformare i dati ed estrarre informazioni di interesse. Con il servizio di analisi è possibile gestire processi di qualsiasi dimensione immediatamente definendo il livello e l'ambito necessari. Verrà addebitato un costo per il processo solo durante la sua esecuzione, per la massima convenienza. In questo articolo vengono fornite indicazioni su come proteggere i processi da rare interruzioni a livello di area o eliminazioni accidentali.

## <a name="disaster-recovery-guidance"></a>Indicazioni sul ripristino di emergenza

Quando si usa Azure Data Lake Analytics, è fondamentale preparare il proprio piano di ripristino di emergenza. Questo articolo guida l'utente a creare un piano di ripristino di emergenza. Sono disponibili risorse aggiuntive che consentono di creare un piano personalizzato:There are additional resources that can help you create your own plan:
- [Ripristino di emergenza e in caso di errori per le applicazioni Azure](/azure/architecture/reliability/disaster-recovery)
- [Indicazioni tecniche sulla resilienza di Azure](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>Procedure consigliate e indicazioni per gli scenari

È possibile eseguire un processo U-SQL ricorrente in un account ADLA in un'area che legge e scrive tabelle U-SQL, nonché dati non strutturati.  Prepararsi per un'emergenza attenendosi alla seguente procedura:

1. Creare account ADLA e ADLS nell'area secondaria che verranno utilizzati durante un'interruzione.

   > [!NOTE]
   > Poiché i nomi di account sono univoci a livello globale, utilizzare uno schema di denominazione coerente che indica quale account è secondario.

2. Per i dati non strutturati, fare riferimento alle indicazioni per il ripristino di [emergenza per i dati in Azure Data Lake Storage Gen1For](../data-lake-store/data-lake-store-disaster-recovery-guidance.md) unstructured data, reference Disaster recovery guidance for data in Azure Data Lake Storage Gen1

3. Per i dati strutturati archiviati in tabelle e database ADLA, creare copie degli elementi dei metadati, ad esempio database, tabelle, funzioni con valori di tabella e assembly. È necessario risincronizzare periodicamente questi elementi quando si verificano modifiche nell'ambiente di produzione. Ad esempio, i dati appena inseriti devono essere replicati nell'area secondaria copiando i dati e inserendoli nella tabella secondaria.

   > [!NOTE]
   > Questi nomi di oggetto hanno come ambito l'account secondario e non sono univoci a livello globale, pertanto possono avere gli stessi nomi dell'account di produzione primario.

Durante un'interruzione, è necessario aggiornare gli script in modo che i percorsi di input puntino all'endpoint secondario. Gli utenti inviano quindi i processi all'account ADLA nell'area secondaria. L'output del processo verrà quindi scritto nell'account ADLA e ADLS nell'area secondaria.

## <a name="next-steps"></a>Passaggi successivi

[Materiale sussidiario per il ripristino di emergenza dei dati in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
