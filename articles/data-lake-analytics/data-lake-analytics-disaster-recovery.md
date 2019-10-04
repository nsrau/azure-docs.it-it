---
title: Indicazioni sul ripristino di emergenza per Azure Data Lake Analitica
description: Informazioni su come pianificare il ripristino di emergenza per gli account Azure Data Lake Analitica.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ea1d4020aa9be23b4839690ae0b386d35bce8a23
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66498890"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Indicazioni sul ripristino di emergenza per Azure Data Lake Analitica

Azure Data Lake Analytics è un servizio per processi di analisi su richiesta che semplifica l'uso dei Big Data. Anziché distribuire, configurare e ottimizzare l'hardware, è possibile scrivere query per trasformare i dati ed estrarre informazioni di interesse. Con il servizio di analisi è possibile gestire processi di qualsiasi dimensione immediatamente definendo il livello e l'ambito necessari. Verrà addebitato un costo per il processo solo durante la sua esecuzione, per la massima convenienza. Questo articolo fornisce indicazioni su come proteggere i processi dalle rare interruzioni a livello di area o da eliminazioni accidentali.

## <a name="disaster-recovery-guidance"></a>Indicazioni sul ripristino di emergenza

Quando si usa Azure Data Lake Analitica, è fondamentale per preparare il proprio piano di ripristino di emergenza. Questo articolo fornisce istruzioni per creare un piano di ripristino di emergenza. Sono disponibili risorse aggiuntive che consentono di creare proprio piano:
- [Ripristino di emergenza e in caso di errori per le applicazioni Azure](/azure/architecture/reliability/disaster-recovery)
- [Indicazioni tecniche sulla resilienza di Azure](/azure/architecture/reliability)

## <a name="best-practices-and-scenario-guidance"></a>Le procedure consigliate e linee guida di scenario

È possibile eseguire un processo U-SQL ricorrente in un account Azure Data Lake Analytics in un'area che legge e scrive tabelle U-SQL, nonché i dati non strutturati.  Preparazione alle situazioni di emergenza seguendo questi passaggi:

1. Creare gli account Azure Data Lake Analytics e Azure Data Lake Store nell'area secondaria che verrà utilizzato durante un'interruzione del servizio.

   > [!NOTE]
   > Poiché i nomi degli account sono univoci a livello globale, usare uno schema di denominazione coerente che indica quale account viene secondario.

2. Per i dati non strutturati, fare riferimento a [indicazioni sul ripristino di emergenza per i dati in Azure Data Lake archiviazione Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. Per i dati strutturati archiviati nei database e tabelle di Azure Data Lake Analytics, creare copie degli elementi dei metadati, ad esempio database, tabelle, funzioni con valori di tabella e assembly. È necessario risincronizzare periodicamente questi elementi quando vengono apportate modifiche nell'ambiente di produzione. Ad esempio, dati appena inseriti deve essere replicata nell'area secondaria copiando i dati e inserimento nella tabella secondaria.

   > [!NOTE]
   > Questi nomi di oggetto sono limitati all'account secondario e non sono univoci a livello globale, pertanto possono avere gli stessi nomi di account di produzione primario.

Durante un'interruzione, è necessario aggiornare gli script in modo che i percorsi di input puntano all'endpoint secondario. Quindi gli utenti di inviano i processi all'account Azure Data Lake Analytics nell'area secondaria. L'output del processo verrà quindi essere scritto per l'account Azure Data Lake Analytics e Azure Data Lake Store nell'area secondaria.

## <a name="next-steps"></a>Passaggi successivi

[Indicazioni sul ripristino di emergenza per i dati in Azure Data Lake archiviazione Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
