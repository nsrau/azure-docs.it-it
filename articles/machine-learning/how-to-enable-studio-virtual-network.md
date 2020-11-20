---
title: Abilitare Azure Machine Learning Studio in una rete virtuale
titleSuffix: Azure Machine Learning
description: Informazioni su come configurare Azure Machine Learning Studio per accedere ai dati archiviati all'interno di una rete virtuale.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: df4d777ad78240b3ca84c51152b37861c4ccc486
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960003"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Usare Azure Machine Learning Studio in una rete virtuale di Azure

Questo articolo illustra come usare Azure Machine Learning Studio in una rete virtuale. Si apprenderà come:

> [!div class="checklist"]
> - Accedere a Studio da una risorsa all'interno di una rete virtuale.
> - Configurare endpoint privati per gli account di archiviazione.
> - Consentire a studio di accedere ai dati archiviati all'interno di una rete virtuale.
> - Informazioni sul modo in cui lo studio influisca sulla sicurezza dell'archiviazione.

Questo articolo è la parte cinque di una serie in cinque parti che illustra come proteggere un flusso di lavoro Azure Machine Learning. È consigliabile leggere prima di tutto la [parte 1: Panoramica di VNet](how-to-network-security-overview.md) per comprendere prima l'architettura complessiva. 

Vedere gli altri articoli di questa serie:

[1. Panoramica di VNet](how-to-network-security-overview.md)  >  [2. Proteggere l'area di lavoro](how-to-secure-workspace-vnet.md)  >  [3. Proteggere l'ambiente di training](how-to-secure-training-vnet.md)  >  [4. Proteggere l'ambiente di inferenza](how-to-secure-inferencing-vnet.md)  >  **5. Abilitare la funzionalità di studio**


> [!IMPORTANT]
> Se l'area di lavoro si trova in un __cloud sovrano__, ad esempio Azure per enti pubblici o Azure Cina 21ViaNet, i notebook integrati _non_ supportano l'uso dell'archiviazione che si trova in una rete virtuale. È invece possibile usare Jupyter Notebook da un'istanza di calcolo. Per altre informazioni, vedere la sezione [accedere ai dati in un notebook dell'istanza di calcolo](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook) .


## <a name="prerequisites"></a>Prerequisiti

+ Leggere la [Panoramica della sicurezza di rete](how-to-network-security-overview.md) per comprendere gli scenari comuni della rete virtuale e l'architettura complessiva della rete virtuale.

+ Una rete virtuale e una subnet preesistenti da usare.

+ Un' [area di lavoro Azure Machine Learning esistente con collegamento privato abilitato](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Un account di archiviazione di Azure esistente ha [aggiunto la rete virtuale](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints).

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Accedere a Studio da una risorsa all'interno di VNet

Se si accede a Studio da una risorsa all'interno di una rete virtuale, ad esempio un'istanza di calcolo o una macchina virtuale, è necessario consentire il traffico in uscita dalla rete virtuale a Studio. 

Ad esempio, se si usano gruppi di sicurezza di rete (NSG) per limitare il traffico in uscita, aggiungere una regola a una destinazione di __tag del servizio__ di __AzureFrontDoor. frontend__.

## <a name="access-data-using-the-studio"></a>Accedere ai dati tramite studio

Dopo aver aggiunto un account di archiviazione di Azure alla rete virtuale con un [endpoint del servizio](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) o un [endpoint privato](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints), è necessario configurare l'account di archiviazione in modo da usare l' [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per concedere a Studio l'accesso ai dati.

Se non si Abilita l'identità gestita, verrà visualizzato questo errore, `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` inoltre, verranno disabilitate le operazioni seguenti:

* Visualizzare in anteprima i dati in studio.
* Visualizza i dati nella finestra di progettazione.
* Inviare un esperimento AutoML.
* Avviare un progetto di assegnazione di etichette.

Studio supporta la lettura dei dati dai seguenti tipi di archivio dati in una rete virtuale:

* BLOB Azure
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* database SQL di Azure

### <a name="grant-workspace-managed-identity-__reader__-access-to-storage-private-link"></a>Concessione dell'accesso al __lettore__ di identità gestito dell'area di lavoro al collegamento privato di archiviazione

Questo passaggio è necessario solo se l'account di archiviazione di Azure è stato aggiunto alla rete virtuale con un [endpoint privato](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints). Per ulteriori informazioni, vedere ruolo predefinito [Reader](../role-based-access-control/built-in-roles.md#reader) .

### <a name="configure-datastores-to-use-workspace-managed-identity"></a>Configurare gli archivi dati per usare l'identità gestita dell'area di lavoro

Azure Machine Learning usa gli [archivi dati](concept-data.md#datastores) per connettersi agli account di archiviazione. Usare la procedura seguente per configurare gli archivi dati in modo da usare l'identità gestita. 

1. In Studio selezionare __datastores__.

1. Per creare un nuovo archivio dati, selezionare __+ nuovo archivio dati__.

    Per aggiornare un archivio dati esistente, selezionare l'archivio dati e selezionare __Aggiorna credenziali__.

1. Nelle impostazioni dell'archivio dati selezionare __Sì__ per  __Consenti Azure Machine Learning servizio di accedere allo spazio di archiviazione usando l'identità gestita dall'area di lavoro__.


Questa procedura consente di aggiungere l'identità gestita dall'area di lavoro come __lettore__ al servizio di archiviazione usando il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Accesso in __lettura__ consente all'area di lavoro di recuperare le impostazioni del firewall e assicurarsi che i dati non lascino la rete virtuale.

> [!NOTE]
> Per rendere effettive queste modifiche possono essere necessari fino a 10 minuti.

## <a name="technical-notes-for-managed-identity"></a>Note tecniche per l'identità gestita

L'uso dell'identità gestita per accedere ai servizi di archiviazione influisca su alcune considerazioni sulla sicurezza. Questa sezione descrive le modifiche per ogni tipo di account di archiviazione.

> [!IMPORTANT]
> Queste considerazioni sono univoche per il __tipo di account di archiviazione__ a cui si accede.

### <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

Per l' __archiviazione BLOB di Azure__, l'identità gestita dall'area di lavoro viene aggiunta anche come [lettore di dati BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) in modo che possa leggere i dati dall'archivio BLOB.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Controllo di accesso Azure Data Lake Storage Gen2

Per controllare l'accesso ai dati all'interno di una rete virtuale, è possibile usare gli elenchi di controllo di accesso (ACL) e gli elenchi di controllo di accesso di tipo POSIX.

Per usare il controllo degli accessi in base al ruolo di Azure, aggiungere l'identità gestita dall'area di lavoro al ruolo [lettore dati BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) Per altre informazioni, vedere [controllo degli accessi in base al ruolo di Azure](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

Per usare gli ACL, è possibile assegnare l'accesso all'identità gestita dall'area di lavoro in modo analogo a qualsiasi altro principio di sicurezza. Per altre informazioni, vedere [elenchi di controllo di accesso per file e directory](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="azure-data-lake-storage-gen1-access-control"></a>Controllo di accesso Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 supporta solo gli elenchi di controllo di accesso in stile POSIX. È possibile assegnare l'accesso di identità gestito dall'area di lavoro alle risorse come qualsiasi altro principio di sicurezza. Per ulteriori informazioni, vedere [controllo di accesso in Azure Data Lake storage Gen1](../data-lake-store/data-lake-store-access-control.md).

### <a name="azure-sql-database-contained-user"></a>Utente indipendente del database SQL di Azure

Per accedere ai dati archiviati in un database SQL di Azure tramite identità gestita, è necessario creare un utente indipendente di SQL che esegue il mapping all'identità gestita. Per altre informazioni sulla creazione di un utente da un provider esterno, vedere [creare utenti indipendenti con mapping a identità Azure ad](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Dopo aver creato un utente indipendente di SQL, concedere le autorizzazioni tramite il [comando T-SQL Grant](/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="azure-machine-learning-designer-default-datastore"></a>Archivio dati predefinito di Azure Machine Learning Designer

La finestra di progettazione USA l'account di archiviazione collegato all'area di lavoro per archiviare l'output per impostazione predefinita. Tuttavia, è possibile specificarlo per archiviare l'output in qualsiasi archivio dati a cui si ha accesso. Se l'ambiente USA reti virtuali, è possibile usare questi controlli per garantire che i dati rimangano protetti e accessibili.

Per impostare una nuova risorsa di archiviazione predefinita per una pipeline:

1. In una bozza di pipeline selezionare l' **icona dell'ingranaggio impostazioni** accanto al titolo della pipeline.
1. Selezionare l' **archivio dati predefinito**.
1. Consente di specificare un nuovo archivio dati.

È anche possibile eseguire l'override dell'archivio dati predefinito in base al modulo. Questo consente di controllare il percorso di archiviazione per ogni singolo modulo.

1. Selezionare il modulo di cui si desidera specificare l'output.
1. Espandere la sezione **impostazioni di output** .
1. Selezionare **Sostituisci impostazioni di output predefinite**.
1. Selezionare **imposta impostazioni di output**.
1. Consente di specificare un nuovo archivio dati.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo è una parte facoltativa di una serie di reti virtuali in quattro parti. Vedere il resto degli articoli per informazioni su come proteggere una rete virtuale:

* [Parte 1: Panoramica di rete virtuale](how-to-network-security-overview.md)
* [Parte 2: proteggere le risorse dell'area di lavoro](how-to-secure-workspace-vnet.md)
* [Parte 3: proteggere l'ambiente di training](how-to-secure-training-vnet.md)
* [Parte 4: proteggere l'ambiente di inferenza](how-to-secure-inferencing-vnet.md)