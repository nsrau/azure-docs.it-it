---
title: Elenchi di controllo di accesso in Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni sul funzionamento degli elenchi di controllo di accesso degli ACL simili a POSIX in Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 2418a8813e7b9de603b7e7cdc11fc756d73ac2a4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350756"
---
# <a name="access-control-lists-acls-in-azure-data-lake-storage-gen2"></a>Elenchi di controllo di accesso (ACL) in Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementa un modello di controllo di accesso che supporta il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) e gli elenchi di controllo di accesso (ACL) simili a POSIX. Questo articolo descrive gli elenchi di controllo di accesso in Data Lake Storage Gen2. Per informazioni su come incorporare il controllo degli accessi in base al ruolo di Azure con ACL e su come viene valutato dal sistema per prendere decisioni relative alle autorizzazioni, vedere il [modello di controllo di accesso in Azure Data Lake storage Gen2](data-lake-storage-access-control-model.md).

<a id="access-control-lists-on-files-and-directories"></a>

## <a name="about-acls"></a>Informazioni sugli ACL

È possibile associare un' [entità di sicurezza](../../role-based-access-control/overview.md#security-principal) a un livello di accesso per file e directory. Queste associazioni vengono acquisite in un *elenco di controllo di accesso (ACL)*. Ogni file e directory nell'account di archiviazione dispone di un elenco di controllo di accesso. Quando un'entità di sicurezza tenta un'operazione su un file o una directory, un controllo ACL determina se l'entità di sicurezza (utente, gruppo, entità servizio o identità gestita) dispone del livello di autorizzazione corretto per eseguire l'operazione.

> [!NOTE]
> Gli ACL si applicano solo alle entità di sicurezza nello stesso tenant e non si applicano agli utenti che usano l'autenticazione con chiave condivisa o firma di accesso condiviso (SAS). Questo perché nessuna identità è associata al chiamante e pertanto non è possibile eseguire l'autorizzazione basata sulle autorizzazioni dell'entità di sicurezza.  

## <a name="how-to-set-acls"></a>Come impostare gli ACL

Per impostare le autorizzazioni a livello di file e directory, vedere gli articoli seguenti:

| Ambiente | Articolo |
|--------|-----------|
|Esplora archivi Azure |[Usare Azure Storage Explorer per gestire directory, file ed elenchi di controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-explorer.md#managing-access)|
|.NET |[Usare .NET per gestire directory, file e ACL in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md#manage-access-control-lists-acls)|
|Java|[Utilizzare Java per gestire directory, file e ACL in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-java.md#manage-access-control-lists-acls)|
|Python|[Usare Python per gestire directory, file e ACL in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md#manage-access-control-lists-acls)|
|PowerShell|[Usare PowerShell per gestire directory, file e ACL in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-powershell.md#manage-access-control-lists-acls)|
|Interfaccia della riga di comando di Azure|[Usare l'interfaccia della riga di comando di Azure per gestire directory, file ed elenchi di controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-cli.md#manage-access-control-lists-acls)|
|API REST |[Percorso-aggiornamento](/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Se l'entità di sicurezza è un'entità *servizio* , è importante usare l'ID oggetto dell'entità servizio e non l'ID oggetto della registrazione dell'app correlata. Per ottenere l'ID oggetto dell'entità servizio, aprire l'interfaccia della riga di comando di Azure, quindi usare il comando seguente: `az ad sp show --id <Your App ID> --query objectId` . Assicurarsi di sostituire il `<Your App ID>` segnaposto con l'ID app della registrazione dell'app.

## <a name="types-of-acls"></a>Tipi di ACL

Esistono due tipi di elenchi di controllo di accesso: *ACL di accesso* e *ACL predefiniti*.

gli elenchi ACL di accesso controllano l'accesso a un oggetto. Sia i file che le directory hanno ACL di accesso.

Gli ACL predefiniti sono modelli di ACL associati a una directory che determina gli ACL di accesso per tutti gli elementi figlio creati in tale directory. I file non hanno ACL predefiniti.

Sia gli ACL di accesso che gli ACL predefiniti presentano la stessa struttura.

> [!NOTE]
> La modifica dell'ACL predefinito per un elemento padre non influisce sull'ACL di accesso o sull'ACL predefinito degli elementi figlio già esistenti.

## <a name="levels-of-permission"></a>Livelli di autorizzazione

Le autorizzazioni per un oggetto contenitore sono di **lettura**, **scrittura** ed **esecuzione** e possono essere usate nei file e nelle directory, come illustrato nella tabella seguente:

|            |    File     |   Directory |
|------------|-------------|----------|
| **Lettura (R)** | È possibile leggere il contenuto di un file | Per elencare il contenuto della directory, sono necessarie le autorizzazioni di **Lettura** ed **Esecuzione** |
| **Scrittura (W)** | È possibile scrivere o aggiungere in un file | Per creare elementi figlio in una directory, sono necessarie le autorizzazioni di **Scrittura** ed **Esecuzione** |
| **Esecuzione (X)** | Nessun valore nel contesto di Data Lake Storage Gen2 | È necessaria per attraversare gli elementi figlio di una directory |

> [!NOTE]
> Se si concedono le autorizzazioni usando solo ACL (nessun controllo degli accessi in base al ruolo di Azure), per concedere a un'entità di sicurezza l'accesso in lettura o scrittura a un file, è necessario assegnare all'entità di sicurezza le autorizzazioni di **esecuzione** al contenitore e a ogni cartella nella gerarchia di cartelle che portano al file.

### <a name="short-forms-for-permissions"></a>Forme brevi per le autorizzazioni

La forma **RWX** viene usata per indicare **Lettura + Scrittura + Esecuzione**. Esiste una forma numerica ridotta in cui **Lettura=4**, **Scrittura=2** ed **Esecuzione=1** e la cui somma rappresenta le autorizzazioni. Di seguito sono riportati alcuni esempi.

| Forma numerica | Forma breve |      Significato     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Lettura + Scrittura + Esecuzione |
| 5            | `R-X`        | Lettura + Esecuzione         |
| 4            | `R--`        | Lettura                   |
| 0            | `---`        | Nessuna autorizzazione         |

### <a name="permissions-inheritance"></a>Ereditarietà delle autorizzazioni

Nel modello di tipo POSIX usato da Data Lake Storage Gen2, le autorizzazioni per un elemento vengono archiviate nell'elemento stesso. In altre parole, le autorizzazioni per un elemento non possono essere ereditate dagli elementi padre se le autorizzazioni vengono impostate dopo che l'elemento figlio è già stato creato. Le autorizzazioni vengono ereditate solo se le autorizzazioni predefinite sono state impostate per gli elementi padre prima che gli elementi figlio siano stati creati.

## <a name="common-scenarios-related-to-acl-permissions"></a>Scenari comuni relativi alle autorizzazioni ACL

Nella tabella seguente vengono illustrate le voci ACL necessarie per consentire a un'entità di sicurezza di eseguire le operazioni elencate nella colonna **Operation** . 

Questa tabella mostra una colonna che rappresenta ogni livello di una gerarchia di directory fittizia. Esiste una colonna per la directory radice del contenitore ( `\` ), una sottodirectory denominata **Oregon**, una sottodirectory della directory Oregon denominata **Portland** e un file di testo nella directory Portland denominata **Data.txt**. 

> [!IMPORTANT]
> Questa tabella presuppone che si stiano usando **solo** ACL senza assegnazioni di ruolo di Azure. Per visualizzare una tabella simile che combina il controllo degli accessi in base al ruolo di Azure con ACL, vedere la [tabella delle autorizzazioni: combinazione](data-lake-storage-access-control-model.md#permissions-table-combining-azure-rbac-and-acl)di controllo degli accessi

|    Operazione             |    /    | Oregon/ | Portland/ | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Read Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Append to Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Delete Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Create Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Elenco /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Elencare /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Elencare /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Purché vengano soddisfatte le due condizioni precedenti, non sono necessarie autorizzazioni di scrittura per il file per eliminarlo.

## <a name="users-and-identities"></a>Utenti e identità

Ogni file e directory ha autorizzazioni distinte per le entità seguenti:

- Utente proprietario
- Gruppo proprietario
- Utenti non anonimi
- Gruppi con nome
- Entità servizio denominate
- Identità gestite denominate
- Tutti gli altri utenti

Le identità di utenti e gruppi sono identità di Azure Active Directory (Azure AD). Se non diversamente specificato, un *utente*, nel contesto di data Lake storage Gen2, può fare riferimento a un utente Azure ad, a un'entità servizio, a un'identità gestita o a un gruppo di sicurezza.

### <a name="the-owning-user"></a>Utente proprietario

L'utente che ha creato l'elemento ne è automaticamente l'utente proprietario. Un utente proprietario può:

* Modificare le autorizzazioni di un file di sua proprietà.
* Modificare il gruppo proprietario di un file di sua proprietà, a condizione che l'utente proprietario sia anche membro del gruppo di destinazione.

> [!NOTE]
> L'utente proprietario *non può* modificare l'utente proprietario di un file o di una directory. Solo gli utenti con privilegi avanzati possono modificare l'utente proprietario di un file o una directory.

### <a name="the-owning-group"></a>Gruppo proprietario

Negli ACL POSIX ogni utente è associato a un *gruppo primario*. È ad esempio possibile che l'utente "Alice" appartenga al gruppo "Finance". Alice potrebbe anche appartenere a più gruppi, ma un gruppo viene sempre designato come gruppo primario. Quando Alice crea un file in POSIX, come gruppo proprietario del file viene impostato il gruppo primario di Alice, in questo caso "finanza". Il gruppo proprietario si comporta in modo analogo alle autorizzazioni assegnate per altri utenti o gruppi.

#### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Assegnazione del gruppo proprietario per un nuovo file o directory

* **Caso 1**: la directory radice "/". Questa directory viene creata quando viene creato un contenitore Data Lake Storage Gen2. In questo caso, il gruppo proprietario viene impostato sull'utente che ha creato il contenitore se è stato eseguito tramite OAuth. Se il contenitore viene creato usando una chiave condivisa, una firma di accesso condiviso dell'account o una firma di accesso condiviso del servizio, il proprietario e il gruppo proprietario sono impostati su **$superuser**.
* **Caso 2** (tutti gli altri casi): quando viene creato un nuovo elemento, il gruppo proprietario viene copiato dalla directory padre.

#### <a name="changing-the-owning-group"></a>Modifica del gruppo proprietario

Il gruppo proprietario può essere modificato da:
* Qualsiasi utente con privilegi avanzati.
* Utente proprietario, se è anche membro del gruppo di destinazione

> [!NOTE]
> Il gruppo proprietario non può modificare gli ACL di un file o di una directory.  Mentre il gruppo proprietario è impostato sull'utente che ha creato l'account nel caso della directory radice, **caso 1** precedente, un singolo account utente non è valido per fornire le autorizzazioni tramite il gruppo proprietario. È possibile assegnare questa autorizzazione a un gruppo utenti valido, se applicabile.

## <a name="access-check-algorithm"></a>Algoritmo di controllo dell'accesso

Lo pseudocodice seguente rappresenta l'algoritmo di controllo dell'accesso per gli account di archiviazione.

```console
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
  # Handle super users.
  if (is_superuser(user)) :
    return True

  # Handle the owning user. Note that mask isn't used.
  entry = get_acl_entry( path, OWNER )
  if (user == entry.identity)
      return ( (desired_perms & entry.permissions) == desired_perms )

  # Handle the named users. Note that mask IS used.
  entries = get_acl_entries( path, NAMED_USER )
  for entry in entries:
      if (user == entry.identity ) :
          mask = get_mask( path )
          return ( (desired_perms & entry.permissions & mask) == desired_perms)

  # Handle named groups and owning group
  member_count = 0
  perms = 0
  entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
  mask = get_mask( path )
  for entry in entries:
    if (user_is_member_of_group(user, entry.identity)) :
        if ((desired_perms & entry.permissions & mask) == desired_perms)
            return True 
        
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>La maschera

Come illustrato nell'algoritmo di controllo dell'accesso, la maschera limita l'accesso agli utenti non anonimi, al gruppo proprietario e ai gruppi non anonimi.  

Per un nuovo contenitore di Data Lake Storage Gen2, la maschera per l'ACL di accesso della directory radice ("/") viene impostata per impostazione predefinita su **750** per le directory e **640** per i file. La tabella seguente illustra la notazione simbolica di questi livelli di autorizzazione.

|Entità|Directory|File|
|--|--|--|
|utente proprietario|`rwx`|`r-w`|
|gruppo proprietario|`r-x`|`r--`|
|Altri|`---`|`---`|

File non ricevono il bit X perché è irrilevante per i file in un sistema solo di archiviazione. 

La maschera può essere specificata in base alle singole chiamate. In questo modo sistemi diversi con un elevato utilizzo di risorse, ad esempio i cluster, possono avere maschere effettive diverse per le operazioni su file. Se una maschera viene specificata per una determinata richiesta, esegue l'override completo della maschera predefinita.

### <a name="the-sticky-bit"></a>Sticky bit

Il bit appiccicoso è una funzionalità più avanzata di un contenitore POSIX. Nel contesto di Data Lake Storage Gen2, è improbabile che lo sticky bit sia necessario. In breve, se lo sticky bit è abilitato in una directory, un elemento figlio può essere solo eliminato o rinominato dall'utente proprietario dell'elemento figlio.

Il bit appiccicoso non viene visualizzato nella portale di Azure.

## <a name="default-permissions-on-new-files-and-directories"></a>Autorizzazioni predefinite per nuovi file e directory

Quando si crea un nuovo file o una nuova directory in una directory esistente, l'ACL predefinito per la directory padre determina quanto segue:

- ACL predefinito e ACL di accesso di una directory figlio.
- ACL di accesso di un file figlio (i file non hanno un ACL predefinito).

### <a name="umask"></a>umask

Quando si crea un file o una directory, la proprietà umask viene usata per modificare la modalità in cui gli ACL predefiniti vengono impostati sull'elemento figlio. La proprietà umask è un valore a 9 bit sulle directory padre, che contiene un valore RWX per **l'utente proprietario**, **il gruppo proprietario** e **altri**.

La proprietà umask per Azure Data Lake Storage Gen2 è un valore costante impostato su 007. Questo valore viene convertito in:

| componente umask     | Forma numerica | Forma breve | Significato |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Per l'utente proprietario, copiare l'ACL predefinito dell'elemento padre nell'ACL di accesso dell'elemento figlio | 
| umask.owning_group  |    0         |   `---`      | Per il gruppo proprietario, copiare l'ACL predefinito dell'elemento padre nell'ACL di accesso dell'elemento figlio | 
| umask.other         |    7         |   `RWX`      | Per altri, rimuovere tutte le autorizzazioni sull'ACL di accesso dell'elemento figlio |

Il valore umask usato da Azure Data Lake Storage Gen2 significa che il valore di **other** non viene mai trasmesso per impostazione predefinita nei nuovi elementi figlio, a meno che non sia stato definito un ACL predefinito nella directory padre. In tal caso, umask viene ignorato e le autorizzazioni definite dall'ACL predefinito vengono applicate all'elemento figlio. 

Lo pseudocodice seguente illustra come viene applicata la proprietà umask quando si creano gli ACL per un elemento figlio.

```console
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

## <a name="faq"></a>Domande frequenti

### <a name="do-i-have-to-enable-support-for-acls"></a>È necessario abilitare il supporto per gli ACL?

No. Il controllo di accesso tramite ACL è abilitato per un account di archiviazione, purché sia attivata la funzionalità di spazio dei nomi gerarchico (HNS).

Se HNS è disattivato, le regole di autorizzazione RBAC di Azure Azure sono comunque valide.

### <a name="what-is-the-best-way-to-apply-acls"></a>Qual è il modo migliore per applicare gli elenchi di controllo di accesso?

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)] 

### <a name="how-are-azure-rbac-and-acl-permissions-evaluated"></a>Come vengono valutate le autorizzazioni RBAC e ACL di Azure?

Per informazioni sul modo in cui il sistema valuta gli ACL e il controllo degli accessi in base al ruolo di Azure per prendere decisioni relative [alle autorizzazioni per](data-lake-storage-access-control-model.md#how-permissions-are-evaluated)le risorse dell'account di archiviazione, vedere

### <a name="what-are-the-limits-for-azure-role-assignments-and-acl-entries"></a>Quali sono i limiti per le assegnazioni di ruolo e le voci ACL di Azure?

La tabella seguente fornisce una panoramica dei limiti da considerare quando si usa il controllo degli accessi in base al ruolo di Azure per gestire le autorizzazioni "grossolane" (autorizzazioni valide per gli account di archiviazione o i contenitori) e l'uso degli ACL per gestire le autorizzazioni con granularità fine (autorizzazioni valide per file e directory). Usare i gruppi di sicurezza per le assegnazioni ACL. Usando i gruppi, è meno probabile che superino il numero massimo di assegnazioni di ruolo per sottoscrizione e il numero massimo di voci ACl per ogni file o directory. 

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

### <a name="does-data-lake-storage-gen2-support-inheritance-of-azure-rbac"></a>Data Lake Storage Gen2 supporta l'ereditarietà di RBAC di Azure?

Le assegnazioni di ruolo di Azure ereditano. Le assegnazioni scorrono dalle risorse di sottoscrizione, gruppo di risorse e account di archiviazione fino alla risorsa contenitore.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage Gen2 supporta l'ereditarietà degli elenchi di controllo di accesso?

Gli ACL predefiniti possono essere usati per impostare gli ACL per le nuove sottodirectory figlio e i file creati nella directory padre. Per aggiornare gli ACL per gli elementi figlio esistenti, è necessario aggiungere, aggiornare o rimuovere gli ACL in modo ricorsivo per la gerarchia di directory desiderata. Per altre informazioni, vedere [impostare elenchi di controllo di accesso (ACL) in modo ricorsivo per Azure Data Lake storage Gen2](recursive-access-control-lists.md). 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Quali autorizzazioni sono necessarie per eliminare in modo ricorsivo una directory e il relativo contenuto?

- Il chiamante ha autorizzazioni ' superuser ',

Or

- Sono necessarie autorizzazioni di Scrittura + Esecuzione per la directory padre.
- Sono necessarie autorizzazioni di Lettura + Scrittura + Esecuzione per la directory da eliminare e per ogni directory al suo interno.

> [!NOTE]
> Non sono necessarie autorizzazioni di Scrittura per eliminare i file nelle directory. La directory radice "/" non può mai essere eliminata.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Chi è il proprietario di un file o una directory?

Il creatore di un file o una directory ne diventa il proprietario. Nel caso della directory radice, si tratta dell'identità dell'utente che ha creato il contenitore.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Quale gruppo viene impostato come gruppo proprietario di un file o una directory al momento della creazione?

Il gruppo proprietario viene copiato da quello della directory padre in cui si crea il nuovo file o la nuova directory.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Sono l'utente proprietario di un file ma non ho le autorizzazioni RWX necessarie. Cosa devo fare?

L'utente proprietario può modificare le autorizzazioni del file in modo da assegnarsi tutte le autorizzazioni RWX necessarie.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Perché in alcuni casi vengono visualizzati GUID negli elenchi di controllo di accesso?

Viene visualizzato un GUID se la voce rappresenta un utente e tale utente non esiste più in Azure AD. In genere ciò si verifica se l'utente non fa più parte dell'azienda o l'account è stato eliminato in Azure AD. Inoltre, le entità servizio e i gruppi di sicurezza non hanno un nome dell'entità utente (UPN) per identificarli e vengono quindi rappresentati dall'attributo OID (un GUID).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Ricerca per categorie impostare gli ACL correttamente per un'entità servizio?

Quando si definiscono gli ACL per le entità servizio, è importante usare l'ID oggetto (OID) dell' *entità servizio* per la registrazione dell'app creata. È importante notare che le app registrate hanno un'entità servizio separata nel tenant di Azure AD specifico. Le app registrate hanno un OID visibile nel portale di Azure, ma l' *entità servizio* presenta un altro OID (diverso).

Per ottenere l'OID per l'entità servizio che corrisponde alla registrazione di un'app, è possibile usare il `az ad sp show` comando. Specificare l'ID applicazione come parametro. Di seguito è riportato un esempio su come ottenere l'OID per l'entità servizio che corrisponde a una registrazione dell'app con ID app = 18218b12-1895-43E9-AD80-6e8fc1ea88ce. Eseguire il comando seguente nell'interfaccia della riga di comando di Azure:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

OID verrà visualizzato.

Quando si dispone dell'OID corretto per l'entità servizio, passare alla pagina Storage Explorer **Gestisci accesso** per aggiungere l'OID e assegnare le autorizzazioni appropriate per l'OID. Assicurarsi di selezionare **Salva**.

### <a name="can-i-set-the-acl-of-a-container"></a>È possibile impostare l'ACL di un contenitore?

No. Un contenitore non dispone di un ACL. È tuttavia possibile impostare l'ACL della directory radice del contenitore. Ogni contenitore dispone di una directory radice e condivide lo stesso nome del contenitore. Se, ad esempio, il contenitore è denominato `my-container` , la directory radice sarà denominata `myContainer/` . 

L'API REST di archiviazione di Azure contiene un'operazione denominata [set Container ACL](/rest/api/storageservices/set-container-acl), ma tale operazione non può essere usata per impostare l'ACL di un contenitore o la directory radice di un contenitore. Questa operazione viene invece usata per indicare se è possibile [accedere pubblicamente](anonymous-read-access-configure.md)ai BLOB in un contenitore. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Dove è possibile reperire altre informazioni sul modello di controllo di accesso POSIX?

* [POSIX Access Control Lists on Linux](https://www.linux.com/news/posix-acls-linux) (Elenchi di controllo di accesso POSIX in Linux)
* [HDFS Permission Guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) (Guida alle autorizzazioni HDFS)
* [Domande frequenti su POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [ACL POSIX in Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL: Using Access Control Lists on Linux](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/) (ACL: uso di elenchi di controllo di accesso in Linux)

## <a name="see-also"></a>Vedi anche

- [Modello di controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md)
