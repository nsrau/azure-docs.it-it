---
title: Migrazione tra aree di Azure Data Lake Store | Microsoft Docs
description: Informazioni sulla migrazione tra aree per Azure Data Lake Store.
services: data-lake-store
documentationcenter: 
author: swums
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/27/2017
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a0eecbb50b001325c91d1d721bf8de7244f04674
ms.lasthandoff: 04/27/2017


---
# <a name="migrate-data-lake-store-across-regions"></a>Eseguire la migrazione di Data Lake Store tra aree

Man mano che Azure Data Lake Store viene reso disponibile in nuove aree, è possibile scegliere di eseguire una migrazione occasionale per sfruttare i vantaggi di una nuova area. Ecco gli aspetti da considerare per la pianificazione e il completamento della migrazione.

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Per altre informazioni, vedere [Crea subito il tuo account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/).
* **Un account Data Lake Store in due aree diverse**. Per altre informazioni, vedere l'[introduzione ad Azure Data Lake Store](data-lake-store-get-started-portal.md).
* **Azure Data Factory**. Per altre informazioni, vedere l'[introduzione ad Azure Data Factory](../data-factory/data-factory-introduction.md).


## <a name="migration-considerations"></a>Considerazioni sulla migrazione

Identificare prima di tutto la strategia di migrazione più appropriata per l'applicazione che scrive, legge o elabora i dati in Data Lake Store. Quando si sceglie una strategia, considerare i requisiti di disponibilità dell'applicazione e il tempo di inattività che si verifica durante una migrazione. L'approccio più semplice potrebbe ad esempio consistere nell'uso del modello di migrazione cloud noto come "trasferire in modalità lift-and-shift". In questo approccio, è necessario sospendere l'applicazione nell'area esistente mentre tutti i dati vengono copiati nella nuova area. Al termine del processo di copia, è possibile riprendere l'applicazione nella nuova area e quindi eliminare l'account Data Lake Store precedente. Durante la migrazione è necessario del tempo di inattività.

Per ridurre il tempo di inattività, è possibile iniziare immediatamente a inserire nuovi dati nella nuova area. Quando sono disponibili i dati minimi necessari, eseguire l'applicazione nella nuova area. Continuare a copiare in background i dati precedenti dall'account Data Lake Store esistente al nuovo account Data Lake Store nella nuova area. Questo approccio consente di passare alla nuova area con un tempo di inattività ridotto. Al termine della copia di tutti i dati precedenti, eliminare l'account Data Lake Store precedente.

Ecco altri dettagli importanti da considerare durante la pianificazione della migrazione:

* **Volume dei dati**. Il volume dei dati (gigabyte, numero di file e cartelle e così via) influisce sul tempo e sulle risorse necessari per la migrazione.

* **Nome dell'account Data Lake Store**. Il nome del nuovo account nella nuova area deve essere univoco a livello globale. Il nome dell'account Data Lake Store precedente nell'area Stati Uniti orientali 2 potrebbe ad esempio essere contosostatiunitiorientali2.azuredatalakestore.net. Per il nuovo account Data Lake Store nell'area Europa settentrionale è possibile usare il nome contosoeuropasettentrionale.azuredatalakestore.net.

* **Strumenti**. Per copiare i file di Data Lake Store è consigliabile usare l'[attività di copia di Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md). Data Factory supporta lo spostamento dei dati con prestazioni e affidabilità elevate. Tenere presente che Data Factory copia solo la gerarchia di cartelle e il contenuto dei file. Eventuali elenchi di controllo di accesso usati nell'account precedente devono essere applicati manualmente al nuovo account. Per altre informazioni, inclusi gli obiettivi di prestazioni per gli scenari ottimali, vedere la [Guida alle prestazioni dell'attività di copia e all'ottimizzazione](../data-factory/data-factory-copy-activity-performance.md). Se si vuole che i dati vengano copiati più rapidamente, potrebbe essere necessario usare altre unità di spostamento dei dati nel cloud. Altri strumenti, come AdlCopy, non supportano la copia di dati tra aree.  

* **Costi per la larghezza di banda**. Vengono applicati [costi per la larghezza di banda](https://azure.microsoft.com/en-us/pricing/details/bandwidth/) perché i dati vengono trasferiti all'esterno di un'area di Azure.

* **Elenchi di controllo di accesso applicati ai dati**. Proteggere i dati nella nuova area applicando elenchi di controllo di accesso ai file e alle cartelle. Per altre informazioni, vedere [Protezione dei dati archiviati in Azure Data Lake Store](data-lake-store-secure-data.md). È consigliabile approfittare della migrazione per aggiornare e modificare gli elenchi di controllo di accesso. Se si vogliono usare impostazioni simili a quelle correnti, è possibile vedere gli elenchi di controllo di accesso applicati a qualsiasi file usando il portale di Azure, i [cmdlet di PowerShell](/powershell/module/azurerm.datalakestore/get-azurermdatalakestoreitempermission) o gli SDK.  

* **Posizione dei servizi di analisi**. Per prestazioni ottimali, i servizi di analisi, come Azure Data Lake Analytics o HDInsight, devono trovarsi nella stessa area dei dati.  

## <a name="next-steps"></a>Passaggi successivi
* [Panoramica dell’Archivio Data Lake di Azure](data-lake-store-overview.md)

