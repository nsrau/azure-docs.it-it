---
title: Piattaforma di Data Lake Storage e WANdisco LiveData per Azure (anteprima)
description: Eseguire la migrazione dei dati Hadoop locali a Azure Data Lake Storage Gen2 usando la piattaforma WANdisco LiveData per Azure.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 11/06/2020
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 9da6ea7abf57ffecc900a6dbef065a8c6b123e61
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94810997"
---
# <a name="meet-demanding-migration-requirements-with-wandisco-livedata-platform-for-azure-preview"></a>Soddisfare i requisiti di migrazione complessi con la piattaforma WANdisco LiveData per Azure (anteprima)

Eseguire la migrazione dei dati Hadoop locali a Azure Data Lake Storage Gen2 usando [la piattaforma WANdisco LiveData per Azure](https://docs.wandisco.com/live-data-platform/docs/landing/). Questa piattaforma Elimina la necessità di tempi di inattività dell'applicazione, Elimina la possibilità di perdita di dati e garantisce la coerenza dei dati anche mentre le operazioni continuano in locale.  

> [!NOTE]
> La piattaforma WANdisco LiveData per Azure è in versione di anteprima pubblica. Per la disponibilità a livello di area, vedere [aree supportate](https://docs.wandisco.com/live-data-platform/docs/prereq#supported-regions).

La piattaforma è costituita da due servizi: [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) per eseguire la migrazione dei dati usati in modo attivo dagli ambienti locali ad archiviazione di Azure e dal [piano LiveData per Azure](https://www.wandisco.com/products/livedata-plane-for-azure) , che garantisce che tutti i dati modificati o i dati di inserimento vengano replicati in modo coerente. 

> [!div class="mx-imgBorder"]
> ![Illustrazione della panoramica della piattaforma dati dinamica](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

È possibile gestire entrambi i servizi usando il portale di Azure e l'interfaccia della riga di comando di Azure e seguire lo stesso modello di fatturazione con pagamento in base al consumo come tutti gli altri servizi di Azure. La piattaforma LiveData per il consumo di Azure verrà visualizzata nella stessa fattura mensile di Azure e fornirà un modo coerente e pratico per tenere traccia e monitorare l'utilizzo.

A differenza della migrazione dei dati _offline_ [copiando le informazioni statiche in Azure Data Box](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster)o usando strumenti Hadoop come [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), è possibile mantenere il funzionamento completo dei sistemi aziendali durante la migrazione in _linea_ con WANdisco LiveData per Azure. Mantieni gli ambienti di Big Data operativi anche durante lo trasferimento dei dati in Azure.

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>Funzionalità chiave della piattaforma WANdisco LiveData per Azure

La [piattaforma WANdisco LiveData per Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) usa un motore di consenso univoco per la rete a ampio spazio per ottenere la coerenza dei dati e per eseguire la replica dei dati su larga scala, mentre le applicazioni possono continuare a modificare i dati in fase di replica.  

Di seguito sono riportate le funzionalità principali della piattaforma:

- **Coerenza dei dati**: consente di risolvere i problemi di migrazione di volumi di dati di grandi dimensioni tra ambienti e di mantenere i dati coerenti tra la migrazione della velocità effettiva dei sistemi di archiviazione, anche se sono in continua evoluzione. Usa il motore di consenso esclusivo per la rete wide area che supporta WANdisco direttamente in Azure per ottenere la coerenza dei dati e per eseguire la migrazione dei dati con garanzie di coerenza in tutte le modifiche dei dati.

- **Gestire le operazioni**: poiché le applicazioni possono continuare a creare, modificare, leggere ed eliminare dati durante la migrazione, non è necessario interferire con le operazioni aziendali o introdurre una finestra di interruzione solo per eseguire la migrazione di Big data ad Azure. Continua a usare le applicazioni, l'infrastruttura di analisi, i processi di inserimento e altre elaborazioni.

- **Convalidare i risultati**: la convalida end-to-end che i dati possono essere usati in modo efficace una volta eseguita la migrazione in Azure richiede l'esecuzione di carichi di lavoro dell'applicazione di produzione. Solo un servizio LiveData fornisce questo senza introdurre il rischio di divergenza dei dati, mantenendo la coerenza dei dati indipendentemente dal fatto che la modifica avvenga nell'origine o nella destinazione della migrazione. Testare e convalidare il comportamento dell'applicazione senza rischi o modifiche ai processi e ai sistemi.

- **Riduzione della complessità**: eliminare la necessità di creare e gestire i processi pianificati per copiare i dati eseguendo la migrazione dei dati tramite l'automazione. Usa l'integrazione completa con Azure come piano di controllo per gestire e monitorare lo stato di avanzamento della migrazione, tra cui la replica dei dati selettiva, i metadati hive, la sicurezza dei dati e la riservatezza.

- **Efficienza**: mantenere una velocità effettiva e prestazioni elevate e ridimensionare facilmente i volumi Big Data. Con il controllo dell'utilizzo della larghezza di banda, è possibile verificare che sia possibile soddisfare gli obiettivi di migrazione senza influito sulle altre operazioni di sistema.

## <a name="migrate-big-data-faster-without-risk"></a>Eseguire la migrazione Big Data più veloce senza rischi

Il primo servizio della piattaforma WANdisco LiveData per Azure è [LiveData Migrator per Azure](https://www.wandisco.com/products/livedata-migrator-for-azure); soluzione per la migrazione di dati usati attivamente da ambienti locali ad archiviazione di Azure. Viene eseguito il provisioning e la gestione di LiveData Migrator per Azure interamente dal portale di Azure o dall'interfaccia della riga di comando di Azure e viene eseguita insieme al cluster Hadoop locale senza modifiche alla configurazione, modifiche dell'applicazione o riavvii del servizio per iniziare subito la migrazione dei dati.

> [!div class="mx-imgBorder"]
> ![LiveData Migrator per l'architettura di Azure](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture.png)

Le migrazioni di Big data possono essere complesse e impegnative. Lo stato di petabyte delle informazioni senza compromettere le operazioni aziendali è stato impossibile da ottenere con le tecnologie di copia dei dati offline. [LiveData Migrator per Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) offre una distribuzione semplice ed è in grado di stabilire un servizio LiveData con la migrazione e la replica continua dei dati, mentre le applicazioni leggono, scrivono e modificano i dati di cui viene eseguita la migrazione.

Eseguire una migrazione è semplice quanto questi tre passaggi:

1. Eseguire il provisioning dell'istanza LiveData Migrator dal portale di Azure al cluster Hadoop locale. Non sono necessarie modifiche o tempi di inattività del cluster e le applicazioni possono continuare a funzionare.

   > [!div class="mx-imgBorder"]
   >![Creare un'istanza di LiveData Migrator](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

2. Definire l'account di archiviazione di destinazione abilitato per la Azure Data Lake Storage Gen2.

   > [!div class="mx-imgBorder"]
   >![Creare una destinazione Migrator LiveData](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

3. Definire il percorso dei dati di cui si vuole eseguire la migrazione, ad esempio: `/user/hive/warehouse` , e avviare la migrazione.

   > [!div class="mx-imgBorder"]
   > ![Creare una migrazione di LiveData Migrator](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

Monitora l'avanzamento della migrazione tramite gli strumenti standard di Azure, tra cui l'interfaccia della riga di comando di Azure e portale di Azure, e continua a usare l'ambiente locale in tutto. Prima di iniziare, esaminare questi [prerequisiti](https://docs.wandisco.com/live-data-platform/docs/prereq/).

## <a name="replicate-data-under-active-change"></a>Replicare i dati in una modifica attiva

Per le migrazioni su larga scala dei data Lake locali in Azure è necessaria la convalida e il test delle applicazioni. La possibilità di eseguire questa operazione senza il rischio di introdurre modifiche ai dati che creeranno più origini di verità che non possono essere facilmente riconciliate è fondamentale per eliminare i rischi e ridurre al minimo il costo del passaggio ad Azure. Il [piano LiveData per Azure](https://www.wandisco.com/products/livedata-plane-for-azure) usa la tecnologia del motore di coordinamento di WANdisco per superare questi problemi.

> [!div class="mx-imgBorder"]
> ![Piano LiveData per l'architettura di Azure](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

Mantieni i tuoi dati coerenti nei cluster Hadoop locali e nell'archiviazione di Azure con il piano LiveData per Azure dopo la migrazione iniziale:

1. Eseguire il provisioning del piano LiveData per Azure in locale e in Azure, a partire dalla portale di Azure. Non sono necessarie modifiche all'applicazione.
2. Configurare le regole di replica che coprono i percorsi di dati che si desidera rendere coerenti, ad esempio: `/user/contoso/sales/region/WA` .
3. Eseguire applicazioni che accedono e modificano i dati in entrambe le posizioni come file system compatibili con Hadoop.

Il piano LiveData per Azure consente di mantenere i dati coerenti senza imporre un sovraccarico significativo sulle prestazioni dell'applicazione o del cluster. Modificare o inserire dati quando tutte le modifiche vengono replicate in modo coerente.

## <a name="next-steps"></a>Passaggi successivi

- La [piattaforma LiveData per Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) per Azure viene usata come qualsiasi altra risorsa di Azure ed è ora disponibile in anteprima. 

- Comprendere i [prerequisiti](https://docs.wandisco.com/live-data-platform/docs/prereq/), pianificare la migrazione e completare rapidamente una migrazione su larga scala con LiveData Migrator per Azure.

- Provare il Migrator di LiveData senza che sia necessario disporre di un cluster Hadoop locale usando il [sandbox HDFS](https://docs.wandisco.com/live-data-platform/docs/create-sandbox-intro/).

## <a name="see-also"></a>Vedere anche

- [LiveData Migrator per Azure in Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=Overview)

- [Piano LiveData per Azure in Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=Overview)

- [LiveData Migrator per piani e prezzi di Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=PlansAndPrice)

- [Piano LiveData per piani e prezzi di Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=PlansAndPrice) 

- [Piattaforma LiveData per le domande frequenti su Azure](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [Problemi noti relativi alla piattaforma LiveData per Azure](https://docs.wandisco.com/live-data-platform/docs/known-issues/)

- [Introduzione ad Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)