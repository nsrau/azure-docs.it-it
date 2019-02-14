---
title: Migrazione tra aree di Azure Data Lake Storage Gen1 | Microsoft Docs
description: Informazioni sulla migrazione tra aree per Azure Data Lake Storage Gen1.
services: data-lake-store
documentationcenter: ''
author: swums
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: stewu
ms.openlocfilehash: 0bf0843314f38c0de28820c82e95b7921297bf40
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233049"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Eseguire la migrazione di Azure Data Lake Storage Gen1 tra aree

Man mano che Azure Data Lake Storage Gen1 viene reso disponibile in nuove aree, è possibile scegliere di eseguire una migrazione occasionale per sfruttare i vantaggi di una nuova area. Ecco gli aspetti da considerare per la pianificazione e il completamento della migrazione.

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Per altre informazioni, vedere [Crea subito il tuo account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/).
* **Un account Data Lake Storage Gen1 in due aree diverse**. Per altre informazioni, vedere [Iniziare a usare Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Azure Data Factory**. Per altre informazioni, vedere l'[introduzione ad Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Considerazioni sulla migrazione

Identificare prima di tutto la strategia di migrazione più appropriata per l'applicazione che scrive, legge o elabora i dati in Data Lake Storage Gen1. Quando si sceglie una strategia, considerare i requisiti di disponibilità dell'applicazione e il tempo di inattività che si verifica durante una migrazione. L'approccio più semplice potrebbe ad esempio consistere nell'uso del modello di migrazione cloud noto come "trasferire in modalità lift-and-shift". In questo approccio, è necessario sospendere l'applicazione nell'area esistente mentre tutti i dati vengono copiati nella nuova area. Al termine del processo di copia, è possibile riprendere l'applicazione nella nuova area e quindi eliminare l'account Data Lake Storage Gen1 precedente. Durante la migrazione è necessario del tempo di inattività.

Per ridurre il tempo di inattività, è possibile iniziare immediatamente a inserire nuovi dati nella nuova area. Quando sono disponibili i dati minimi necessari, eseguire l'applicazione nella nuova area. Continuare a copiare in background i dati precedenti dall'account Data Lake Storage Gen1 esistente al nuovo account Data Lake Storage Gen1 nella nuova area. Questo approccio consente di passare alla nuova area con un tempo di inattività ridotto. Al termine della copia di tutti i dati precedenti, eliminare l'account Data Lake Storage Gen1 precedente.

Ecco altri dettagli importanti da considerare durante la pianificazione della migrazione:

* **Volume dei dati**. Il volume dei dati (gigabyte, numero di file e cartelle e così via) influisce sul tempo e sulle risorse necessari per la migrazione.

* **Nome dell'account Data Lake Storage Gen1**. Il nome del nuovo account nella nuova area deve essere univoco a livello globale. Ad esempio, il nome dell'account Data Lake Storage Gen1 precedente in East US 2 potrebbe essere contosoeastus2.azuredatalakestore.net. Si potrebbe assegnare al nuovo account Data Lake Storage Gen1 in North EU il nome contosonortheu.azuredatalakestore.net.

* **Strumenti**. Per copiare i file di Data Lake Storage Gen1 è consigliabile usare l'[attività di copia di Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Data Factory supporta lo spostamento dei dati con prestazioni e affidabilità elevate. Tenere presente che Data Factory copia solo la gerarchia di cartelle e il contenuto dei file. Eventuali elenchi di controllo di accesso usati nell'account precedente devono essere applicati manualmente al nuovo account. Per altre informazioni, inclusi gli obiettivi di prestazioni per gli scenari ottimali, vedere la [Guida alle prestazioni dell'attività di copia e all'ottimizzazione](../data-factory/copy-activity-performance.md). Se si vuole che i dati vengano copiati più rapidamente, potrebbe essere necessario usare altre unità di spostamento dei dati nel cloud. Altri strumenti, come AdlCopy, non supportano la copia di dati tra aree.  

* **Costi per la larghezza di banda**. Vengono applicati [costi per la larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/) perché i dati vengono trasferiti all'esterno di un'area di Azure.

* **Elenchi di controllo di accesso applicati ai dati**. Proteggere i dati nella nuova area applicando elenchi di controllo di accesso ai file e alle cartelle. Per altre informazioni, vedere [Protezione dei dati archiviati in Azure Data Lake Storage Gen1](data-lake-store-secure-data.md). È consigliabile approfittare della migrazione per aggiornare e modificare gli elenchi di controllo di accesso. Se si vogliono usare impostazioni simili a quelle correnti, è possibile vedere gli elenchi di controllo di accesso applicati a qualsiasi file usando il portale di Azure, i [cmdlet di PowerShell](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission) o gli SDK.  

* **Posizione dei servizi di analisi**. Per prestazioni ottimali, i servizi di analisi, come Azure Data Lake Analytics o HDInsight, devono trovarsi nella stessa area dei dati.  

## <a name="next-steps"></a>Passaggi successivi
* [Panoramica di Azure Data Lake Storage Gen1](data-lake-store-overview.md)
