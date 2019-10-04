---
title: Panoramica del controllo di accesso in Data Lake Storage Gen1| Microsoft Docs
description: Informazioni sul funzionamento del controllo di accesso in Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 276e691351d852d6dcb0075d47bf33af6767fc10
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226089"
---
# <a name="access-control-in-azure-data-lake-storage-gen1"></a>Controllo di accesso in Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 implementa un modello di controllo di accesso derivante da HDFS, che a sua volta deriva dal modello di controllo di accesso POSIX. Questo articolo offre un riepilogo delle nozioni di base del modello di controllo di accesso per Data Lake Storage Gen1. 

## <a name="access-control-lists-on-files-and-folders"></a>Elenchi di controllo di accesso per file e cartelle

Esistono due tipologie di elenchi di controllo di accesso (ACL): **ACL di accesso** e **ACL predefiniti**.

* **ACL di accesso**: questi elenchi controllano l'accesso a un oggetto. Sia i file che le cartelle hanno ACL di accesso.

* **ACL predefiniti**: "modello" di ACL associato a una cartella che determina gli ACL di accesso per tutti gli elementi figlio creati in tale cartella. I file non hanno ACL predefiniti.


Sia gli ACL di accesso che gli ACL predefiniti presentano la stessa struttura.



> [!NOTE]
> La modifica dell'ACL predefinito per un elemento padre non influisce sull'ACL di accesso o sull'ACL predefinito degli elementi figlio già esistenti.
>
>

## <a name="permissions"></a>Autorizzazioni

Le autorizzazioni per un oggetto del file system sono **Lettura**, **Scrittura** ed **Esecuzione** e possono essere usate per file e cartelle come illustrato nella tabella seguente:

|            |    File     |   Cartella |
|------------|-------------|----------|
| **Lettura (R)** | È possibile leggere il contenuto di un file | Per elencare il contenuto della cartella sono necessarie le autorizzazioni di **Lettura** ed **Esecuzione**|
| **Scrittura (W)** | È possibile scrivere o aggiungere in un file | Per creare elementi figlio in una cartella sono necessarie le autorizzazioni di **Scrittura** ed **Esecuzione**. |
| **Esecuzione (X)** | Nessun valore nel contesto di Data Lake Storage Gen1 | È necessaria per attraversare gli elementi figlio di una cartella |

### <a name="short-forms-for-permissions"></a>Forme brevi per le autorizzazioni

La forma **RWX** viene usata per indicare **Lettura + Scrittura + Esecuzione**. Esiste una forma numerica ridotta in cui **Lettura=4**, **Scrittura=2** ed **Esecuzione=1** e la cui somma rappresenta le autorizzazioni. Di seguito sono riportati alcuni esempi.

| Forma numerica | Forma breve |      Significato     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Lettura + Scrittura + Esecuzione |
| 5            | `R-X`        | Lettura + Esecuzione         |
| 4            | `R--`        | Lettura                   |
| 0            | `---`        | Nessuna autorizzazione         |


### <a name="permissions-do-not-inherit"></a>Non ereditarietà delle autorizzazioni

Nel modello di tipo POSIX usato da Data Lake Storage Gen1, le autorizzazioni per un elemento vengono archiviate nell'elemento stesso. In altri termini, le autorizzazioni per un elemento non sono ereditabili dagli elementi padre.

## <a name="common-scenarios-related-to-permissions"></a>Scenari comuni correlati alle autorizzazioni

Di seguito sono riportati alcuni scenari comuni che consentono di comprendere quali autorizzazioni sono necessarie per eseguire determinate operazioni su un account Data Lake Storage Gen1.

| Operazione | Object              |    /      | Seattle/   | Portland/   | Data.txt       |
|-----------|---------------------|-----------|------------|-------------|----------------|
| Lettura      | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Accoda a | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Eliminare    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Create    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| List      | /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| List      | /Seattle/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| List      | /Seattle/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


> [!NOTE]
> Se vengono soddisfatte le due condizioni precedenti, non sono necessarie autorizzazioni di scrittura per il file per eliminarlo.
>
>


## <a name="users-and-identities"></a>Utenti e identità

Ogni file e cartella ha autorizzazioni distinte per le entità seguenti:

* Utente proprietario
* Gruppo proprietario
* Utenti non anonimi
* Gruppi con nome
* Tutti gli altri utenti

Le identità di utenti e gruppi sono identità di Azure Active Directory (Azure AD). Se non viene specificato diversamente, quindi, nel contesto di Data Lake Storage Gen1 un "utente" può essere un utente di Azure AD o un gruppo di sicurezza di Azure AD.

### <a name="the-super-user"></a>Utente con privilegi avanzati

Un utente con privilegi avanzati ha diritti superiori rispetto a qualsiasi altro utente nell'account Data Lake Storage Gen1. Un utente con privilegi avanzati:

* Ha autorizzazioni RWX per **tutti** i file e le cartelle.
* Può modificare le autorizzazioni per qualsiasi file o cartella.
* Può modificare l'utente o il gruppo proprietario di qualsiasi file o cartella.

Tutti i membri che fanno parte del ruolo **Proprietari** per un account Data Lake Storage Gen1 sono automaticamente utenti con privilegi avanzati.

### <a name="the-owning-user"></a>Utente proprietario

L'utente che ha creato l'elemento ne è automaticamente l'utente proprietario. Un utente proprietario può:

* Modificare le autorizzazioni di un file di sua proprietà.
* Modificare il gruppo proprietario di un file di sua proprietà, a condizione che l'utente proprietario sia anche membro del gruppo di destinazione.

> [!NOTE]
> L'utente proprietario *non può* modificare l'utente proprietario di un file o di una cartella. Solo i superuser possono modificare l'utente proprietario di un file o una cartella.
>
>

### <a name="the-owning-group"></a>Gruppo proprietario

**Background**

Negli ACL POSIX ogni utente è associato a un "gruppo primario". L'utente "alice", ad esempio, può appartenere al gruppo "finanza". Alice può anche appartenere a più gruppi, ma uno solo è sempre designato come il suo gruppo primario. Quando Alice crea un file in POSIX, come gruppo proprietario del file viene impostato il gruppo primario di Alice, in questo caso "finanza". Il gruppo proprietario si comporta in modo analogo alle autorizzazioni assegnate per altri utenti o gruppi.

Poiché non esiste alcun "gruppo primario" associato agli utenti in Data Lake Storage Gen1, il gruppo proprietario viene assegnato come mostrato di seguito.

**Assegnazione del gruppo proprietario per un nuovo file o cartella**

* **Caso 1**: cartella radice "/". Questa cartella viene creata al momento della creazione di un account Data Lake Storage Gen1. In questo caso, il gruppo proprietario viene impostato su un GUID costituito solo da zeri.  Questo valore non permette alcun accesso.  È un segnaposto fino a quando non viene assegnato un gruppo.
* **Caso 2** (qualsiasi altro caso): quando viene creato un nuovo elemento, il gruppo proprietario viene copiato dalla cartella padre.

**Modifica del gruppo proprietario**

Il gruppo proprietario può essere modificato da:
* Qualsiasi utente con privilegi avanzati.
* Utente proprietario, se è anche membro del gruppo di destinazione

> [!NOTE]
> Il gruppo proprietario *non può* modificare gli ACL di un file o di una cartella.
>
> Per gli account creati a o prima di settembre 2018, il gruppo proprietario è impostato sull'utente che ha creato l'account nel caso della cartella radice per **Caso 1**, come descritto sopra.  Poiché un singolo account utente non è valido per fornire autorizzazioni tramite il gruppo proprietario, non vengono concesse autorizzazioni tramite questa impostazione predefinita. È possibile assegnare questa autorizzazione a un gruppo di utenti valido.


## <a name="access-check-algorithm"></a>Algoritmo di controllo dell'accesso

Lo pseudocodice seguente rappresenta l'algoritmo di controllo dell'accesso per gli account Data Lake Storage Gen1.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or folder
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

  # Handle the owning user. Note that mask IS NOT used.
  entry = get_acl_entry( path, OWNER )
  if (user == entry.identity)
      return ( (desired_perms & entry.permissions) == desired_perms )

  # Handle the named users. Note that mask IS used.
  entries = get_acl_entries( path, NAMED_USER )
  for entry in entries:
      if (user == entry.identity ) :
          mask = get_mask( path )
          return ( (desired_perms & entry.permmissions & mask) == desired_perms)

  # Handle named groups and owning group
  member_count = 0
  perms = 0
  entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
  for entry in entries:
    if (user_is_member_of_group(user, entry.identity)) :
      member_count += 1
      perms | =  entry.permissions
  if (member_count>0) :
    return ((desired_perms & perms & mask ) == desired_perms)
 
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>La maschera

Come illustrato nell'algoritmo di controllo dell'accesso, la maschera limita l'accesso agli **utenti non anonimi**, il **gruppo proprietario**, e i **gruppi non anonimi**.  

> [!NOTE]
> Per un nuovo account Data Lake Storage Gen1, l'impostazione predefinita della maschera per l'elenco di controllo di accesso della cartella radice ("/") è RWX.
>
>

### <a name="the-sticky-bit"></a>Sticky bit

Lo sticky bit è una funzionalità più avanzata di un file system POSIX. Nel contesto di Data Lake Storage Gen1, è improbabile che lo sticky bit sia necessario. In breve, se lo sticky bit è abilitato in una cartella, un elemento figlio può essere solo eliminato o rinominato dall'utente proprietario dell'elemento figlio.

Lo sticky bit non viene visualizzato nel portale di Azure.

## <a name="default-permissions-on-new-files-and-folders"></a>Autorizzazioni predefinite per nuovi file e cartelle

Quando si crea un nuovo file o una nuova cartella in una cartella esistente, l'ACL predefinito per la cartella padre determina quanto segue:

- ACL predefinito e ACL di accesso di una cartella figlio.
- ACL di accesso di un file figlio (i file non hanno un ACL predefinito).

### <a name="umask"></a>umask

Quando si crea un file o una cartella, la proprietà umask viene usata per modificare la modalità in cui gli ACL predefiniti vengono impostati sull'elemento figlio. umask è un valore a 9 bit per le cartelle padre che contiene un valore RWX per l' **utente proprietario**, il **gruppo proprietario**e **altro**.

Umask per Azure Data Lake Storage Gen1 è un valore costante impostato su 007. Questo valore viene convertito in

| componente umask     | Forma numerica | Forma breve | Significato |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Per l'utente proprietario, copiare l'ACL predefinito dell'elemento padre nell'ACL di accesso dell'elemento figlio | 
| umask.owning_group  |    0         |   `---`      | Per il gruppo proprietario, copiare l'ACL predefinito dell'elemento padre nell'ACL di accesso dell'elemento figlio | 
| umask.other         |    7         |   `RWX`      | Per altri, rimuovere tutte le autorizzazioni sull'ACL di accesso dell'elemento figlio |

Il valore umask usato efficacemente da Azure Data Lake Storage Gen1 significa che il valore per gli altri non viene mai trasmesso per impostazione predefinita sui nuovi elementi figlio, indipendentemente da ciò che indica l'ACL predefinito. 

Lo pseudocodice seguente illustra come viene applicata la proprietà umask quando si creano gli ACL per un elemento figlio.

```
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen1"></a>Domande frequenti sugli elenchi di controllo di accesso in Data Lake Storage Gen1

### <a name="do-i-have-to-enable-support-for-acls"></a>È necessario abilitare il supporto per gli ACL?

No. Il controllo di accesso tramite gli elenchi di controllo di accesso (ACL) è sempre attivo per un account Data Lake Storage Gen1.

### <a name="which-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>Quali autorizzazioni sono necessarie per eliminare in modo ricorsivo una cartella e il relativo contenuto?

* Sono necessarie autorizzazioni di **Scrittura + Esecuzione** per la cartella padre.
* Sono necessarie autorizzazioni di **Lettura + Scrittura + Esecuzione** per la cartella da eliminare e per ogni cartella al suo interno.

> [!NOTE]
> Non sono necessarie autorizzazioni di Scrittura per eliminare i file nelle cartelle. La cartella radice "/" non può **mai** essere eliminata.
>
>

### <a name="who-is-the-owner-of-a-file-or-folder"></a>Chi è il proprietario di un file o una cartella?

Il creatore di un file o una cartella ne diventa il proprietario.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>Quale gruppo viene impostato come gruppo proprietario di un file o una cartella al momento della creazione?

Il gruppo proprietario viene copiato da quello della cartella padre in cui si crea il nuovo file o la nuova cartella.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Se l'utente proprietario di un file non ha le autorizzazioni RWX di cui ha bisogno, Cosa devo fare?

L'utente proprietario può modificare le autorizzazioni del file in modo da assegnarsi tutte le autorizzazioni RWX necessarie.

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>La visualizzazione degli ACL nel portale di Azure mostra i nomi utente, mentre tramite le API vengono visualizzati i GUID. Per quale motivo?

Le voci negli ACL vengono archiviate come GUID che corrispondono agli utenti in Azure AD. Le API restituiscono i GUID così come sono. Il portale di Azure tenta di semplificare l'uso degli ACL traducendo i GUID in nomi descrittivi, quando è possibile.

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>Perché a volte negli ACL vengono visualizzati i GUID quando si usa il portale di Azure?

Il GUID viene visualizzato quando l'utente non esiste più in Azure AD. In genere ciò si verifica se l'utente non fa più parte dell'azienda o l'account è stato eliminato in Azure AD.

### <a name="does-data-lake-storage-gen1-support-inheritance-of-acls"></a>Data Lake Storage Gen1 supporta l'ereditarietà degli elenchi di controllo di accesso?

No, ma gli ACL predefiniti possono essere usati per impostare gli ACL per i file e le cartelle figlio appena creati nella cartella padre.  

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Dove è possibile reperire altre informazioni sul modello di controllo di accesso POSIX?

* [POSIX Access Control Lists on Linux](https://www.linux.com/news/posix-acls-linux) (Elenchi di controllo di accesso POSIX in Linux)
* [HDFS Permission Guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) (Guida alle autorizzazioni HDFS)
* [Domande frequenti su POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [ACL POSIX in Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL: Using Access Control Lists on Linux](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/) (ACL: uso di elenchi di controllo di accesso in Linux)

## <a name="see-also"></a>Vedere anche

* [Panoramica di Azure Data Lake Storage Gen1](data-lake-store-overview.md)
