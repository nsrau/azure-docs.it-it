---
title: "Usare più cluster HDInsight con un account Azure Data Lake Store - Azure | Microsoft Docs"
description: "Informazioni su come usare più di un cluster HDInsight con un singolo account Data Lake Store"
keywords: archiviazione hdinsight, hdfs, dati strutturati, dati non strutturati, data lake store
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 08f860dcf0f1d6c69cee02261b2a4989fc5c694a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-store-account"></a>Usare più cluster HDInsight con un account Azure Data Lake Store

A partire da HDInsight versione 3.5, è possibile creare cluster HDInsight con account Azure Data Lake Store come file system predefinito.
Supportando l'archiviazione illimitata, Data Lake Store è ideale non solo per l'hosting di grandi quantità di dati, ma anche per l'hosting di più cluster HDInsight che condividono un unico account Data Lake Store. Per istruzioni su come creare un cluster HDInsight con Data Lake Store come risorsa di archiviazione, vedere [Creare cluster HDInsight con Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Questo articolo fornisce agli amministratori di Data Lake Store le informazioni necessarie per configurare un singolo account Data Lake Store condiviso, che possa essere usato in più cluster HDInsight **attivi**. Queste indicazioni valgono per l'hosting di più cluster Hadoop sicuri e non sicuri in un account Data Lake Store condiviso.


## <a name="data-lake-store-file-and-folder-level-acls"></a>ACL a livello di file e cartella di Data Lake Store

La parte rimanente di questo articolo presuppone una buona conoscenza degli ACL a livello di file e cartella in Azure Data Lake Store, descritte nel dettaglio in [Controllo di accesso in Azure Data Lake Store](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-store-setup-for-multiple-hdinsight-clusters"></a>Configurazione di Data Lake Store per più cluster HDInsight
Si userà una gerarchia di cartelle a due livelli per spiegare i suggerimenti relativi all'uso di più cluster HDInsight con un account Data Lake Store. Si consideri, ad esempio, di avere un account Data Lake Store con la struttura di cartelle **/clusters/finance**. Con questa struttura, tutti i cluster necessari all'organizzazione Finanza possono usare /clusters/finance come percorso di archiviazione. Se un'altra organizzazione, ad esempio Marketing, volesse creare in futuro cluster HDInsight usando lo stesso account Data Lake Store, potrà creare il percorso cluster/marketing. Per il momento si userà solo **/clusters/finance**.

Per far sì che questa struttura di cartelle venga usata dai cluster HDInsight in modo efficace, l'amministratore di Data Lake Store deve assegnare le autorizzazioni appropriate, come descritto nella tabella. Le autorizzazioni illustrate nella tabella corrispondono ad ACL di accesso, non ad ACL predefiniti. 


|Cartella  |Autorizzazioni  |utente proprietario  |gruppo proprietario  | Utente non anonimo | Autorizzazioni utente non anonimo | Gruppo non anonimo | Autorizzazioni gruppo non anonimo |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |admin |admin  |Entità servizio |--x  |FINGRP   |r-x         |
|/clusters | rwxr-x--x |admin |admin |Entità servizio |--x  |FINGRP |r-x         |
|/clusters/finance | rwxr-x--t |admin |FINGRP  |Entità servizio |rwx  |-  |-     |

Nella tabella,

- **admin** è l'autore e amministratore dell'account Data Lake Store.
- **Entità servizio** è l'entità servizio di Azure Active Directory (AAD) associata all'account.
- **FINGRP** è un gruppo di utenti creato in AAD in cui sono contenuti gli utenti dell'organizzazione Finance.

Per istruzioni su come creare un'applicazione AAD (che crea anche un'entità servizio), vedere [Creare un'applicazione Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Per istruzioni su come creare un gruppo di utenti in AAD, vedere [Gestione dei gruppi in Azure Active Directory](../active-directory/active-directory-accessmanagement-manage-groups.md).

Alcuni punti chiave di cui tener conto.

- La struttura di cartelle a due livelli (**/cluster/finance/**) deve essere creata e configurata con le autorizzazioni appropriate dall'amministratore di Data Lake Store **prima** di usare l'account di archiviazione per i cluster. Questa struttura non viene creata automaticamente durante la creazione dei cluster.
- Nell'esempio precedente si consiglia di impostare il gruppo proprietario di **/cluster/finance** come **FINGRP** e di fornire a FINGRP l'autorizzazione **r-x** per accedere all'intera gerarchia di cartelle, a partire dalla radice. In questo modo, i membri di FINGRP possono esplorare la struttura di cartelle a partire dalla radice.
- Nel caso in cui più entità servizio AAD possano creare cluster in **/cluster/finance**, lo sticky bit (se impostato sulla cartella **finance**) garantisce che le cartelle create da un'entità servizio non possano essere eliminate da altre entità servizio.
- Dopo aver impostato la struttura di cartelle e le relative autorizzazioni, il processo di creazione di cluster HDInsight crea un percorso di archiviazione specifico del cluster in **/clusters/finance/**. La risorsa di archiviazione per un cluster con nome fincluster01, ad esempio, può essere **/clusters/finance/fincluster01**. La tabella seguente elenca la proprietà e le autorizzazioni relative alle cartelle create dal cluster HDInsight.

    |Cartella  |Autorizzazioni  |utente proprietario  |gruppo proprietario  | Utente non anonimo | Autorizzazioni utente non anonimo | Gruppo non anonimo | Autorizzazioni gruppo non anonimo |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/ fincluster01 | rwxr-x---  |Entità servizio |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>Suggerimenti per i dati di input e di output del processo

È consigliabile che i dati di input di un processo e i dati di output del processo vengano archiviati in una cartella esterna a **/cluster**. In questo modo, anche se la cartella specifica del cluster viene eliminata per liberare spazio di archiviazione, gli input e gli output del processo rimangono disponibili per un utilizzo futuro. In tal caso, assicurarsi che la gerarchia di cartelle per l'archiviazione dei dati di input e di output del processo consenta il livello di accesso appropriato per l'entità servizio.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Limite di cluster che condividono un unico account di archiviazione

Il limite relativo al numero di cluster che possono condividere un singolo account Data Lake Store dipende dal carico di lavoro in esecuzione sui cluster. La presenza di troppi cluster o di carichi di lavoro molto elevati nei cluster che condividono un account di archiviazione può causare una limitazione del traffico in ingresso/uscita dall'account di archiviazione.

## <a name="support-for-default-acls"></a>Supporto per gli ACL predefiniti

Quando si crea un'entità servizio con accesso utente non anonimo (come illustrato nella tabella precedente), è consigliabile **non** aggiungere l'utente non anonimo con un ACL predefinito. L'assegnazione di un accesso utente non anonimo con ACL predefiniti determina l'assegnazione di 770 autorizzazioni per l'utente proprietario, il gruppo proprietario e altri membri. Sebbene non sottragga autorizzazioni all'utente proprietario (7) o al gruppo proprietario (7), il valore predefinito di 770 sottrae tutte le autorizzazioni agli altri membri (0). Questo genera un problema noto con un particolare caso d'uso analizzato in dettaglio nella sezione [Problemi noti e soluzioni alternative](#known-issues-and-workarounds).

## <a name="known-issues-and-workarounds"></a>Problemi noti e soluzioni alternative

In questa sezione sono elencati i problemi noti relativi all'uso di HDInsight con Data Lake Store e le relative soluzioni alternative.

### <a name="publicly-visible-localized-yarn-resources"></a>Risorse YARN localizzate visibili pubblicamente

Quando viene creato un nuovo account Azure Data Lake Store, la directory radice viene automaticamente configurata con i bit di autorizzazione dell'ACL di accesso impostati su 770. L'utente proprietario della cartella radice è impostato sull'utente che ha creato l'account (l'amministratore di Data Lake Store) e il gruppo proprietario è impostato sul gruppo principale dell'utente che ha creato l'account. Per gli altri membri non è previsto alcun tipo di acceso.

Queste impostazioni influiscono su uno specifico caso d'uso di HDInsight acquisito in [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Gli invii di processi possono avere esito negativo con un messaggio di errore simile al seguente:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Come indicato nel documento JIRA YARN citato in precedenza, durante la localizzazione delle risorse pubbliche il localizzatore verifica che tutte le risorse richieste siano realmente pubbliche controllandone le autorizzazioni sul file system remoto. Eventuali LocalResource che non soddisfano questa condizione vengono rifiutate per la localizzazione. Il controllo delle autorizzazioni include l'accesso in lettura al file per gli altri membri. Questo scenario non può essere applicato direttamente per l'hosting di cluster HDInsight in Azure Data Lake, poiché Azure Data Lake nega agli altri membri l'accesso a livello di cartella radice.

#### <a name="workaround"></a>Soluzione alternativa
Impostare le autorizzazioni di lettura ed esecuzione per gli **altri** membri attraverso la gerarchia, ad esempio in corrispondenza di **/**, **/clusters** e **/clusters/finance**, come illustrato nella tabella precedente.

## <a name="see-also"></a>Vedere anche

* [Creare un cluster HDInsight con Archivio Data Lake](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)


