---
title: Indicazioni sulla migrazione tra aree per Azure Data Lake Store | Microsoft Docs
description: Indicazioni sulla migrazione tra aree per Azure Data Lake Store
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/27/2017
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: 3c83a76c589178d5a2a01ed4edd208e42fb2e83e
ms.openlocfilehash: 785f97a28c060f88a0e0f3a5aa9cf6555ed2b5a4
ms.lasthandoff: 02/22/2017


---
# <a name="guidance-to-migrate-azure-data-lake-store-across-regions"></a>Indicazioni per la migrazione di Azure Data Lake Store tra aree

Quando Azure Data Lake Store viene reso disponibile in nuove aree, è possibile che si decida di eseguire una migrazione occasionale per sfruttare i vantaggi di una nuova area.  Ecco alcune indicazioni sugli elementi da prendere in considerazione durante la pianificazione e l'esecuzione della migrazione.

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account Azure Data Lake Store in due aree diverse**. Per istruzioni su come crearne uno, vedere [Introduzione ad Azure Data Lake Store](data-lake-store-get-started-portal.md).
* **Azure Data Factory**.  Per altre informazioni, vedere [Introduzione ad Azure Data Factory](../data-factory/data-factory-introduction.md).


## <a name="guidance-for-migration"></a>Indicazioni per la migrazione

È consigliabile identificare prima di tutto la strategia di migrazione appropriata per l'applicazione che scrive, legge o elabora i dati in Azure Data Lake Store. Quando si sceglie una strategia, è necessario considerare i requisiti di disponibilità dell'applicazione, ad esempio il tempo di inattività. L'approccio più semplice consiste quindi nel trasferire i dati in modalità lift-and-shift,  ovvero sospendere le applicazioni nell'area precedente mentre tutti i dati vengono copiati nella nuova area.  Al termine del processo di copia, è possibile riprendere l'applicazione nella nuova area ed eliminare l'account Azure Data Lake Store precedente.  La migrazione comporta tuttavia del tempo di inattività.

In alternativa, per ridurre il tempo di inattività, è possibile iniziare a inserire immediatamente nuovi dati nella nuova area e iniziare ad eseguire le applicazioni nella nuova area non appena sono disponibili i dati minimi necessari.  In background è possibile copiare i dati precedenti dall'account Azure Data Lake Store precedente al nuovo account Azure Data Lake Store account nella nuova area.  Ciò consente di passare alla nuova area con tempi di inattività ridotti.  Al termine della copia di tutti i dati precedenti, è possibile eliminare l'account Azure Data Lake Store precedente.

Ecco altri dettagli importanti da considerare durante la pianificazione della migrazione:

* **Volume dei dati**: il volume dei dati (GB, numero di file e cartelle e così via) influirà sul tempo e sulle risorse necessari per lo spostamento.

* **Nome dell'account Azure Data Lake Store**: il nome del nuovo account nella nuova area dovrà essere univoco a livello globale. Ad esempio, se contosoeastus2.azuredatalakestore.net è il nome dell'account di archiviazione precedente in Stati Uniti orientali 2, è possibile assegnare all'account di archiviazione il nome contosonortheu.azuredatalakestore.net in Europa settentrionale.

* **Scelta degli strumenti**: per copiare i file di Azure Data Lake Store è consigliabile usare l'[attività di copia di Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md).   Azure Data Factory supporta lo spostamento dei dati con prestazioni e affidabilità elevate.  Occorre ricordare che Azure Data Factory copierà solo la gerarchia di cartelle e il contenuto dei file. Eventuali elenchi di controllo di accesso applicati dovranno essere copiati manualmente nel nuovo account.  Le [indicazioni sull'ottimizzazione delle prestazioni di Azure Data Factory](../data-factory/data-factory-copy-activity-performance.md) sono un riferimento utile e offrono obiettivi di prestazioni per gli scenari ottimali.  Se si vuole copiare i dati in modo più veloce, potrebbe essere necessario usare unità di spostamento dati cloud aggiuntive.  Si noti che altri strumenti, ad esempio ADLCopy, non supportano la copia dei dati tra le aree.  

* **[Addebiti per la larghezza di banda](https://azure.microsoft.com/en-us/pricing/details/bandwidth/)** Saranno applicabili addebiti perché i dati verranno trasferiti all'esterno di un'area di Azure.

* **Elenchi di controllo di accesso applicati ai dati**: proteggere i dati nella nuova area applicando elenchi di controllo di accesso ai file e alle cartelle.  Per indicazioni, vedere [qui](data-lake-store-secure-data.md).  È consigliabile approfittare della migrazione per aggiornare e modificare gli elenchi di controllo di accesso .  Se si vogliono usare impostazioni simili, è possibile vedere gli elenchi di controllo di accesso applicati a qualsiasi file tramite il portale, i [cmdlet di PowerShell](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.datalakestore/v3.1.0/get-azurermdatalakestoreitempermission) o gli SDK.  

* **Posizione dei servizi di analisi**: per prestazioni ottimali, i servizi di analisi, ad esempio Data Lake Analytics o HDInsight, devono essere situati nella stessa area dei dati.  

## <a name="see-also"></a>Vedere anche
* [Panoramica dell’Archivio Data Lake di Azure](data-lake-store-overview.md)

