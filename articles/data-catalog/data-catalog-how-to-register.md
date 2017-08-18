---
title: Registrare le origini dati in Azure Data Catalog | Microsoft Docs
description: Questo articolo illustra come registrare le origini dati in Azure Data Catalog, inclusi i campi di metadati estratti durante la registrazione.
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: bab89906-186f-4d35-9ffd-61b1d903905d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 05/15/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: 532ff423ff53567b6ce40c0ea7ec09a689cee1e7
ms.openlocfilehash: 3f7eb053db6b6a545990f31f64628594f7f3e2c1
ms.contentlocale: it-it
ms.lasthandoff: 06/05/2017


---
# <a name="register-data-sources-in-azure-data-catalog"></a>Registrare le origini dati in Azure Data Catalog
## <a name="introduction"></a>Introduzione
Azure Data Catalog è un servizio cloud completamente gestito che funge da sistema di registrazione e di individuazione per le origini dati aziendali. In altre parole, Data Catalog permette agli utenti di trovare, comprendere e usare le origini dati e consente alle organizzazioni di ottenere maggior valore dai dati esistenti. Il primo passaggio per rendere individuabile un'origine dati in Data Catalog consiste nel registrare l'origine dati.

## <a name="register-data-sources"></a>Registrare le origini dati
La registrazione è il processo di estrazione dei metadati da un'origine dati e di copia dei dati nel servizio Data Catalog. I dati rimangono nella posizione in cui risiedono attualmente e sotto il controllo degli amministratori e dei criteri del sistema corrente.

Per registrare un'origine dati, seguire questa procedura:
1. Nel portale di Azure Data Catalog avviare lo strumento di registrazione delle origini dati di Data Catalog. 
2. Eseguire l'accesso con l'account aziendale o dell'istituto di istruzione con le stesse credenziali usate per accedere al portale.
3. Selezionare l'origine dati che si vuole registrare.

Per altre informazioni dettagliate, vedere [Introduzione ad Azure Data Catalog](data-catalog-get-started.md).

Dopo la registrazione dell'origine dati, il catalogo tiene traccia della posizione e ne indicizza i metadati. Gli utenti possono cercare, esplorare e trovare l'origine dati e quindi usarne la posizione per connettersi tramite l'applicazione o lo strumento preferito.

## <a name="supported-data-sources"></a>Origini dati supportate
Per un elenco di origini dati attualmente supportate, vedere [Riferimento per l'origine dati di Azure Data Catalog](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Metadati strutturali
Quando si registra un'origine dati, lo strumento di registrazione estrae le informazioni sulla struttura degli oggetti selezionati. Queste informazioni sono dette metadati strutturali.

Per tutti gli oggetti, questi metadati strutturali includono la posizione dell'oggetto, in modo che gli utenti che trovano i dati possano usare tali informazioni per connettersi all'oggetto negli strumenti client di propria scelta. Altri metadati strutturali includono il tipo e il nome dell'oggetto e il nome di colonna/attributo e il tipo di dati.

## <a name="descriptive-metadata"></a>Metadati descrittivi
Oltre ai metadati strutturali di base estratti dall'origine dati, lo strumento di registrazione dell'origine dati estrae i metadati descrittivi. Per SQL Server Analysis Services e SQL Server Reporting Services questi metadati vengono estratti dalle proprietà Description esposte da questi servizi. Per SQL Server vengono estratti i valori specificati con la proprietà estesa ms\_description. Per il database Oracle lo strumento di registrazione dell'origine dati estrae la colonna COMMENTS dalla vista ALL\_TAB\_COMMENTS.

Oltre ai metadati descrittivi estratti dall'origine dati, gli utenti possono immettere metadati descrittivi usando lo strumento di registrazione dell'origine dati. Gli utenti possono aggiungere tag e identificare esperti per gli oggetti in fase di registrazione. Tutti questi metadati descrittivi vengono copiati nel servizio Data Catalog con i metadati strutturali.

## <a name="include-previews"></a>Includere le anteprime
Per impostazione predefinita, solo i metadati vengono estratti dalle origini dati e copiati nel servizio Data Catalog, ma un'origine dati spesso è più facile da comprendere quando è possibile visualizzare un esempio dei dati che contiene.

Usando lo strumento di registrazione dell'origine dati di Data Catalog, è possibile includere un'anteprima dei dati in ogni tabella sotto forma di snapshot e una vista registrata. Se si sceglie di includere anteprime durante la registrazione, lo strumento di registrazione include un massimo di 20 record da ogni tabella e vista. Questo snapshot viene quindi copiato nel catalogo con i metadati strutturali e descrittivi.

> [!NOTE]
> Nell'anteprima delle tabelle di grandi dimensioni con un numero elevato di colonne potrebbero essere inclusi meno di 20 record.
>
>

## <a name="include-data-profiles"></a>Includere i profili dei dati
Esattamente come l'inclusione delle anteprime può offrire un contesto utile per gli utenti che cercano le origini dati in Data Catalog, includere un profilo dei dati può semplificare la comprensione delle origini dati trovate.

Usando lo strumento di registrazione dell'origine dati di Data Catalog, è possibile includere un profilo dei dati in ogni tabella e vista registrate. Se si sceglie di includere un profilo dei dati durante la registrazione, lo strumento di registrazione include statistiche aggregate sui dati in ogni tabella o vista, tra cui:

* Il numero di righe e le dimensioni dei dati nell'oggetto.
* La data dell'aggiornamento più recente dei dati e lo schema dell'oggetto.
* Il numero di record null e i valori distinti per colonne.
* I valori minimo, massimo, medio e deviazione standard per le colonne.

Queste statistiche vengono quindi copiate nel catalogo con i metadati strutturali e descrittivi.

> [!NOTE]
> Le colonne del testo e della data non includono le statistiche della media o della deviazione standard nel profilo dei dati.
>
>

## <a name="update-registrations"></a>Aggiornare le registrazioni
La registrazione di un'origine dati la rende individuabile in Data Catalog quando si usano i metadati e l'anteprima facoltativa estratti durante la registrazione. Se l'origine dati deve essere aggiornata nel catalogo (ad esempio, se lo schema di un oggetto è stato modificato, le tabelle escluse in origine devono essere incluse oppure si vuole aggiornare i dati inclusi nelle anteprime), è possibile eseguire di nuovo lo strumento di registrazione dell'origine dati.

La nuova registrazione di un'origine dati già registrata esegue un'operazione di unione "upsert": gli oggetti esistenti vengono aggiornati e i nuovi oggetti vengono creati. Tutti i metadati forniti dagli utenti tramite il portale di Data Catalog vengono mantenuti.

## <a name="summary"></a>Riepilogo
La registrazione dell'origine dati in Data Catalog, poiché copia i metadati strutturali e descrittivi da un'origine dati al servizio catalogo, rende più facile trovare e comprendere i dati. Dopo avere registrato l'origine dati, è possibile annotarla, gestirla e trovarla usando il portale di Data Catalog.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla registrazione delle origini dati, vedere l'esercitazione [Introduzione ad Azure Data Catalog](data-catalog-get-started.md).

