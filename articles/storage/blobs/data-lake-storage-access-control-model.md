---
title: Modello di controllo di accesso per Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni su come configurare l'accesso a livello di contenitore, directory e file in account che dispongono di uno spazio dei nomi gerarchico.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/10/2020
ms.author: normesta
ms.openlocfilehash: 65d1ef76ffae113a4b526eec75301abbfea751e7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017713"
---
# <a name="access-control-model-in-azure-data-lake-storage-gen2"></a>Modello di controllo di accesso in Azure Data Lake Storage Gen2

Data Lake Storage Gen2 supporta i meccanismi di autorizzazione seguenti:

- Autorizzazione chiave condivisa
- Autorizzazione di firma di accesso condiviso (SAS)
- Controllo degli accessi in base al ruolo (RBAC di Azure)
- Elenchi di controllo di accesso (ACL)

La [chiave condivisa e l'autorizzazione SAS](#shared-key-and-shared-access-signature-sas-authorization) concedono l'accesso a un utente o a un'applicazione senza che sia necessario disporre di un'identità in Azure Active Directory (Azure ad). Con queste due forme di autenticazione, gli ACL e il controllo degli accessi in base al ruolo non hanno effetto.

Il controllo degli accessi in base al ruolo e all'ACL di Azure richiede che l'utente (o l'applicazione) abbia un'identità in Azure AD  Il controllo degli accessi in base al ruolo di Azure consente di concedere l'accesso "grossolano" ai dati dell'account di archiviazione, ad esempio l'accesso in lettura o scrittura a tutti i dati in un account di archiviazione, mentre **gli** ACL consentono di concedere l'accesso con granularità fine, ad esempio l'accesso in scrittura a una directory o a un file specifico.  

Questo articolo è incentrato sul controllo degli accessi in base al ruolo e sugli ACL di Azure e su come il sistema li valuta insieme per prendere decisioni di autorizzazione per le risorse dell'account

<a id="role-based-access-control"></a>

## <a name="role-based-access-control-azure-rbac"></a>Controllo degli accessi in base al ruolo (RBAC di Azure)

Il controllo degli accessi in base al ruolo di Azure usa assegnazioni di ruolo per applicare i set di autorizzazioni alle [entità](../../role-based-access-control/overview.md#security-principal) Un'entità di sicurezza è un oggetto che rappresenta un utente, un gruppo, un'entità servizio o un'identità gestita definita in Azure Active Directory (AD). Un set di autorizzazioni può concedere a un'entità di sicurezza un livello di accesso a granularità grossolana, ad esempio l'accesso in lettura o scrittura a **tutti** i dati in un account di archiviazione o **tutti** i dati in un contenitore. 

I ruoli seguenti consentono a un'entità di sicurezza di accedere ai dati in un account di archiviazione. 

|Ruolo|Descrizione|
|--|--|
| [Proprietario dei dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Accesso completo ai contenitori e ai dati di archiviazione BLOB. Questo accesso consente all'entità di sicurezza di impostare il proprietario di un elemento e di modificare gli ACL di tutti gli elementi. |
| [Collaboratore ai dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Accesso in lettura, scrittura ed eliminazione ai contenitori e ai BLOB di archiviazione BLOB. Questo accesso non consente all'entità di sicurezza di impostare la proprietà di un elemento, ma può modificare l'ACL degli elementi di proprietà dell'entità di sicurezza. |
| [Lettore dei dati del BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) | Leggere ed elencare i BLOB e i contenitori di archiviazione BLOB. |

I ruoli, ad esempio [proprietario](../../role-based-access-control/built-in-roles.md#owner), [collaboratore](../../role-based-access-control/built-in-roles.md#contributor), [lettore](../../role-based-access-control/built-in-roles.md#reader)e [collaboratore account di archiviazione](../../role-based-access-control/built-in-roles.md#storage-account-contributor) , consentono a un'entità di sicurezza di gestire un account di archiviazione, ma non di fornire l'accesso ai dati all'interno di tale account. Tuttavia, questi ruoli (escluso **Reader**) possono ottenere l'accesso alle chiavi di archiviazione, che possono essere usate in diversi strumenti client per accedere ai dati.

## <a name="access-control-lists-acls"></a>Elenchi di controllo di accesso (ACL)

Gli ACL offrono la possibilità di applicare un livello di accesso più preciso a directory e file. Un *ACL* è un costrutto di autorizzazione che contiene una serie di *voci ACL*. Ogni voce ACL associa un'entità di sicurezza a un livello di accesso.  Per altre informazioni, vedere [elenchi di controllo di accesso (ACL) in Azure Data Lake storage Gen2](data-lake-storage-access-control.md).

## <a name="how-permissions-are-evaluated"></a>Modalità di valutazione delle autorizzazioni

Durante l'autorizzazione basata su entità di sicurezza, le autorizzazioni vengono valutate nell'ordine seguente.

: una: &nbsp; &nbsp; le assegnazioni di ruolo di Azure vengono valutate per prime e hanno la priorità sulle assegnazioni ACL.

: due: &nbsp; &nbsp; se l'operazione è completamente autorizzata in base all'assegnazione di ruolo di Azure, gli ACL non vengono valutati.

: tre: &nbsp; &nbsp; se l'operazione non è completamente autorizzata, vengono valutati gli ACL.

> [!div class="mx-imgBorder"]
> ![flusso delle autorizzazioni di archiviazione di data Lake](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-flow.png)

A causa del modo in cui le autorizzazioni di accesso vengono valutate dal sistema, **non è possibile** utilizzare un ACL per **limitare** l'accesso che è già stato concesso da un'assegnazione di ruolo. Questo perché il sistema valuta prima le assegnazioni di ruolo di Azure e, se l'assegnazione concede autorizzazioni di accesso sufficienti, gli ACL vengono ignorati. 

Il diagramma seguente illustra il flusso di autorizzazioni per tre operazioni comuni: elencare il contenuto della directory, leggere un file e scrivere un file.

> [!div class="mx-imgBorder"]
> ![esempio di flusso di autorizzazioni di archiviazione di data Lake](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-example.png)

## <a name="permissions-table-combining-azure-rbac-and-acl"></a>Tabella delle autorizzazioni: combinazione di controllo RBAC e ACL di Azure

La tabella seguente illustra come combinare i ruoli di Azure e le voci ACL in modo che un'entità di sicurezza possa eseguire le operazioni elencate nella colonna **Operation** . Questa tabella mostra una colonna che rappresenta ogni livello di una gerarchia di directory fittizia. Esiste una colonna per la directory radice del contenitore ( `/` ), una sottodirectory denominata **Oregon**, una sottodirectory della directory Oregon denominata **Portland** e un file di testo nella directory Portland denominata **Data.txt**. Le rappresentazioni in [formato breve](data-lake-storage-access-control.md#short-forms-for-permissions) della voce ACL richieste per concedere le autorizzazioni sono visualizzate in tali colonne. **N/** d (_non applicabile_) viene visualizzato nella colonna se non è necessaria una voce ACL per eseguire l'operazione.

|    Operazione             | Ruolo di Azure assegnato               |    /        | Oregon/     | Portland/ | Data.txt |             
|--------------------------|----------------------------------|-------------|-------------|-----------|----------|
| Read Data.txt            |   Proprietario dei dati del BLOB di archiviazione        | N/D      | N/D      | N/D       | N/D    |  
|                          |   Collaboratore ai dati del BLOB di archiviazione  | N/D      | N/D      | N/D       | N/D    |
|                          |   Lettore dei dati del BLOB di archiviazione       | N/D      | N/D      | N/D       | N/D    |
|                          |   nessuno                           | `--X`    | `--X`    | `--X`     | `R--`  |
| Append to Data.txt       |   Proprietario dei dati del BLOB di archiviazione        | N/D      | N/D      | N/D       | N/D    |
|                          |   Collaboratore ai dati del BLOB di archiviazione  | N/D      | N/D      | N/D       | N/D    |
|                          |   Lettore dei dati del BLOB di archiviazione       | `--X`    | `--X`    | `--X`     | `-W-`  |
|                          |   nessuno                           | `--X`    | `--X`    | `--X`     | `RW-`  |
| Delete Data.txt          |   Proprietario dei dati del BLOB di archiviazione        | N/D      | N/D      | N/D       | N/D    |
|                          |   Collaboratore ai dati del BLOB di archiviazione  | N/D      | N/D      | N/D       | N/D    |
|                          |   Lettore dei dati del BLOB di archiviazione       | `--X`    | `--X`    | `-WX`     | N/D    |
|                          |   nessuno                           | `--X`    | `--X`    | `-WX`     | N/D    |
| Create Data.txt          |   Proprietario dei dati del BLOB di archiviazione        | N/D      | N/D      | N/D       | N/D    |
|                          |   Collaboratore ai dati del BLOB di archiviazione  | N/D      | N/D      | N/D       | N/D    |
|                          |   Lettore dei dati del BLOB di archiviazione       | `--X`    | `--X`    | `-WX`     | N/D    |
|                          |   nessuno                           | `--X`    | `--X`    | `-WX`     | N/D    |
| Elenco /                   |   Proprietario dei dati del BLOB di archiviazione        | N/D      | N/D      | N/D       | N/D    |
|                          |   Collaboratore ai dati del BLOB di archiviazione  | N/D      | N/D      | N/D       | N/D    |
|                          |   Lettore dei dati del BLOB di archiviazione       | N/D      | N/D      | N/D       | N/D    |
|                          |   nessuno                           | `R-X`    | N/D      | N/D       | N/D    |
| Elencare /Oregon/            |   Proprietario dei dati del BLOB di archiviazione        | N/D      | N/D      | N/D       | N/D    |
|                          |   Collaboratore ai dati del BLOB di archiviazione  | N/D      | N/D      | N/D       | N/D    |
|                          |   Lettore dei dati del BLOB di archiviazione       | N/D      | N/D      | N/D       | N/D    |
|                          |   nessuno                           | `--X`    | `R-X`    | N/D       | N/D    |
| Elencare /Oregon/Portland/   |   Proprietario dei dati del BLOB di archiviazione        | N/D      | N/D      | N/D       | N/D    |
|                          |   Collaboratore ai dati del BLOB di archiviazione  | N/D      | N/D      | N/D       | N/D    |
|                          |   Lettore dei dati del BLOB di archiviazione       | N/D      | N/D      | N/D       | N/D    |
|                          |   nessuno                           | `--X`    | `--X`    | `R-X`     | N/D    |


> [!NOTE] 
> Per visualizzare il contenuto di un contenitore in Azure Storage Explorer, le entità di sicurezza devono [accedere Storage Explorer tramite Azure ad](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#add-a-resource-via-azure-ad)e, come minimo, hanno accesso in lettura (R--) alla cartella radice ( `\` ) di un contenitore. Questo livello di autorizzazione offre la possibilità di elencare il contenuto della cartella radice. Se non si desidera che il contenuto della cartella radice sia visibile, è possibile assegnare il ruolo [lettore](../../role-based-access-control/built-in-roles.md#reader) . Con questo ruolo, saranno in grado di elencare i contenitori nell'account, ma non il contenuto del contenitore. È quindi possibile concedere l'accesso a directory e file specifici usando gli ACL.   

## <a name="security-groups"></a>Gruppi di sicurezza

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)]

## <a name="limits-on-azure-role-assignments-and-acl-entries"></a>Limiti per le assegnazioni di ruolo e le voci ACL di Azure

Usando i gruppi, è meno probabile che superino il numero massimo di assegnazioni di ruolo per sottoscrizione e il numero massimo di voci ACL per ogni file o directory. Nella tabella seguente vengono descritti questi limiti.

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

## <a name="shared-key-and-shared-access-signature-sas-authorization"></a>Autorizzazione di chiave condivisa e firma di accesso condiviso (SAS)

Azure Data Lake Storage Gen2 supporta anche la [chiave condivisa](/rest/api/storageservices/authorize-with-shared-key) e i metodi [SAS](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) per l'autenticazione. Una caratteristica di questi metodi di autenticazione è che nessuna identità è associata al chiamante e pertanto non è possibile eseguire l'autorizzazione basata sull'autorizzazione dell'entità di sicurezza.

Nel caso della chiave condivisa, il chiamante acquisisce efficacemente l'accesso "super-utente", ovvero l'accesso completo a tutte le operazioni su tutte le risorse, inclusi i dati, l'impostazione del proprietario e la modifica degli ACL.

I token di firma di accesso condiviso includono le autorizzazioni consentite come parte del token. Le autorizzazioni incluse nel token di firma di accesso condiviso vengono di fatto applicate a tutte le decisioni di autorizzazione, ma non vengono eseguiti altri controlli ACL.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sugli elenchi di controllo di accesso, vedere  [elenchi di controllo di accesso (ACL) in Azure Data Lake storage Gen2](data-lake-storage-access-control.md).