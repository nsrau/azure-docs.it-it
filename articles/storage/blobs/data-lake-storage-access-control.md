---
title: Panoramica del controllo di accesso in Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni sul funzionamento del controllo di accesso in Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: a35cf935d990dbb61f440d2592d59d21f33a2ae8
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037233"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Controllo di accesso in Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementa un modello di controllo di accesso che supporta il controllo degli accessi in base al ruolo di Azure (RBAC) e gli elenchi di controllo di accesso (ACL) di tipo POSIX. Questo articolo offre un riepilogo delle nozioni di base del modello di controllo di accesso per Data Lake Storage Gen2.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

RBAC usa le assegnazioni di ruolo per applicare in modo efficace i set di autorizzazioni alle *entità di sicurezza*. Un' *entità di sicurezza* è un oggetto che rappresenta un utente, un gruppo, un'entità servizio o un'identità gestita definita in Azure Active Directory (ad) che richiede l'accesso alle risorse di Azure.

In genere, le risorse di Azure sono vincolate alle risorse di primo livello, ad esempio gli account di archiviazione di Azure. Nel caso di archiviazione di Azure e di conseguenza Azure Data Lake Storage Gen2 questo meccanismo è stato esteso alla risorsa contenitore (file system).

Per informazioni su come assegnare i ruoli alle entità di sicurezza nell'ambito dell'account di archiviazione, vedere [concedere l'accesso ai dati di Accodamento e BLOB di Azure con RBAC nel portale di Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>L'effetto delle assegnazioni di ruolo negli elenchi di controllo di accesso a livello di file e directory

Quando si utilizzano assegnazioni di ruolo RBAC è un meccanismo potente per controllare le autorizzazioni di accesso, si tratta di un meccanismo con granularità grossolana rispetto agli ACL. La granularità minima per il controllo degli accessi in base al ruolo è a livello di contenitore e verrà valutata con una priorità più alta rispetto agli ACL. Se pertanto si assegna un ruolo a un'entità di sicurezza nell'ambito di un contenitore, l'entità di sicurezza dispone del livello di autorizzazione associato a tale ruolo per tutte le directory e i file in tale contenitore, indipendentemente dalle assegnazioni ACL.

Quando a un'entità di sicurezza vengono concesse le autorizzazioni per i dati RBAC tramite un [ruolo predefinito](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues)o tramite un ruolo personalizzato, queste autorizzazioni vengono valutate per prime all'autorizzazione di una richiesta. Se l'operazione richiesta è autorizzata dalle assegnazioni RBAC dell'entità di sicurezza, l'autorizzazione viene immediatamente risolta e non vengono eseguiti controlli ACL aggiuntivi. In alternativa, se l'entità di sicurezza non dispone di un'assegnazione RBAC o se l'operazione della richiesta non corrisponde all'autorizzazione assegnata, vengono eseguiti i controlli ACL per determinare se l'entità di sicurezza è autorizzata a eseguire l'operazione richiesta.

> [!NOTE]
> Se all'entità di sicurezza è stata assegnata l'assegnazione di ruolo incorporata del proprietario dei dati del BLOB di archiviazione, l'entità di sicurezza viene considerata un *utente con privilegi avanzati* e viene concesso l'accesso completo a tutte le operazioni di mutazione, inclusa l'impostazione del proprietario di una directory o di un file, nonché gli ACL per le directory e i file per cui non sono proprietari. L'accesso utente con privilegi avanzati è il solo modo autorizzato di cambiare il proprietario di una risorsa.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Autenticazione con chiave condivisa e firma di accesso condiviso (SAS)

Azure Data Lake Storage Gen2 supporta la chiave condivisa e i metodi SAS per l'autenticazione. Una caratteristica di questi metodi di autenticazione è che nessuna identità è associata al chiamante e pertanto non è possibile eseguire l'autorizzazione basata sull'autorizzazione dell'entità di sicurezza.

Nel caso della chiave condivisa, il chiamante ottiene di fatto l'accesso "utente con privilegi avanzati", vale a dire l'accesso completo a tutte le operazioni su tutte le risorse, inclusa l'impostazione del proprietario e la modifica degli elenchi di controllo di accesso.

I token di firma di accesso condiviso includono le autorizzazioni consentite come parte del token. Le autorizzazioni incluse nel token di firma di accesso condiviso vengono di fatto applicate a tutte le decisioni di autorizzazione, ma non vengono eseguiti altri controlli ACL.

## <a name="access-control-lists-on-files-and-directories"></a>Elenchi di controllo di accesso per file e directory

È possibile associare un'entità di sicurezza a un livello di accesso per file e directory. Queste associazioni vengono acquisite in un *elenco di controllo di accesso (ACL)* . Ogni file e directory nell'account di archiviazione dispone di un elenco di controllo di accesso.

Se è stato assegnato un ruolo a un'entità di sicurezza a livello di account di archiviazione, è possibile usare gli elenchi di controllo di accesso per concedere a tale entità di sicurezza l'accesso con privilegi elevati a file e directory specifici.

Non è possibile usare gli elenchi di controllo di accesso per fornire un livello di accesso inferiore rispetto a un livello concesso da un'assegnazione di ruolo. Ad esempio, se si assegna il ruolo di [collaboratore dati BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) a un'entità di sicurezza, non è possibile usare gli elenchi di controllo di accesso per impedire che l'entità di sicurezza scriva in una directory.

### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Impostare le autorizzazioni a livello di file e directory usando gli elenchi di controllo di accesso

Per impostare le autorizzazioni a livello di file e directory, vedere gli articoli seguenti:

|Se si desidera utilizzare questo strumento:    |Vedere questo articolo:    |
|--------|-----------|
|Azure Storage Explorer    |[Impostare autorizzazioni a livello di file e directory usando Azure Storage Explorer con Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)|
|API REST    |[Percorso-aggiornamento](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Se l'entità di sicurezza è un'entità *servizio* , è importante usare l'ID oggetto dell'entità servizio e non l'ID oggetto della registrazione dell'app correlata. Per ottenere l'ID oggetto dell'entità servizio, aprire l'interfaccia della riga di comando di Azure, quindi usare il comando seguente: `az ad sp show --id <Your App ID> --query objectId`. Assicurarsi di sostituire il segnaposto `<Your App ID>` con l'ID app della registrazione dell'app.

### <a name="types-of-access-control-lists"></a>Tipi di elenchi di controllo di accesso

Esistono due tipi di elenchi di controllo di accesso: *ACL di accesso* e *ACL predefiniti*.

Gli elenchi ACL di accesso controllano l'accesso a un oggetto. Sia i file che le directory hanno ACL di accesso.

Gli ACL predefiniti sono modelli di ACL associati a una directory che determina gli ACL di accesso per tutti gli elementi figlio creati in tale directory. I file non hanno ACL predefiniti.

Sia gli ACL di accesso che gli ACL predefiniti presentano la stessa struttura.

> [!NOTE]
> La modifica dell'ACL predefinito per un elemento padre non influisce sull'ACL di accesso o sull'ACL predefinito degli elementi figlio già esistenti.

### <a name="levels-of-permission"></a>Livelli di autorizzazione

Le autorizzazioni per un oggetto contenitore sono di **lettura**, **scrittura**ed **esecuzione**e possono essere usate nei file e nelle directory, come illustrato nella tabella seguente:

|            |    File     |   Directory |
|------------|-------------|----------|
| **Lettura (R)** | È possibile leggere il contenuto di un file | Per elencare il contenuto della directory, sono necessarie le autorizzazioni di **Lettura** ed **Esecuzione** |
| **Scrittura (W)** | È possibile scrivere o aggiungere in un file | Per creare elementi figlio in una directory, sono necessarie le autorizzazioni di **Scrittura** ed **Esecuzione** |
| **Esecuzione (X)** | Nessun valore nel contesto di Data Lake Storage Gen2 | È necessaria per attraversare gli elementi figlio di una directory |

> [!NOTE]
> Se si concedono le autorizzazioni usando solo ACL (nessun controllo degli accessi in base al ruolo), per concedere a un'entità di sicurezza l'accesso in lettura o scrittura a un file, è necessario assegnare all'entità di sicurezza le autorizzazioni di **esecuzione** per il contenitore e a ogni cartella nella gerarchia di cartelle che portano al file.

#### <a name="short-forms-for-permissions"></a>Forme brevi per le autorizzazioni

La forma **RWX** viene usata per indicare **Lettura + Scrittura + Esecuzione**. Esiste una forma numerica ridotta in cui **Lettura=4**, **Scrittura=2** ed **Esecuzione=1** e la cui somma rappresenta le autorizzazioni. Di seguito sono riportati alcuni esempi.

| Forma numerica | Forma breve |      Significato     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Lettura + Scrittura + Esecuzione |
| 5            | `R-X`        | Lettura + Esecuzione         |
| 4            | `R--`        | Read                   |
| 0            | `---`        | Nessuna autorizzazione         |

#### <a name="permissions-inheritance"></a>Ereditarietà delle autorizzazioni

Nel modello di tipo POSIX usato da Data Lake Storage Gen2, le autorizzazioni per un elemento vengono archiviate nell'elemento stesso. In altre parole, le autorizzazioni per un elemento non possono essere ereditate dagli elementi padre se le autorizzazioni vengono impostate dopo che l'elemento figlio è già stato creato. Le autorizzazioni vengono ereditate solo se le autorizzazioni predefinite sono state impostate per gli elementi padre prima che gli elementi figlio siano stati creati.

### <a name="common-scenarios-related-to-permissions"></a>Scenari comuni correlati alle autorizzazioni

La tabella seguente elenca alcuni scenari comuni che consentono di comprendere quali autorizzazioni sono necessarie per eseguire determinate operazioni su un account di archiviazione.

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

### <a name="users-and-identities"></a>Utenti e identità

Ogni file e directory ha autorizzazioni distinte per le entità seguenti:

- Utente proprietario
- Gruppo proprietario
- Utenti non anonimi
- Gruppi con nome
- Entità servizio denominate
- Identità gestite denominate
- Tutti gli altri utenti

Le identità di utenti e gruppi sono identità di Azure Active Directory (Azure AD). Se non diversamente specificato, un *utente*, nel contesto di data Lake storage Gen2, può fare riferimento a un utente Azure ad, a un'entità servizio, a un'identità gestita o a un gruppo di sicurezza.

#### <a name="the-owning-user"></a>Utente proprietario

L'utente che ha creato l'elemento ne è automaticamente l'utente proprietario. Un utente proprietario può:

* Modificare le autorizzazioni di un file di sua proprietà.
* Modificare il gruppo proprietario di un file di sua proprietà, a condizione che l'utente proprietario sia anche membro del gruppo di destinazione.

> [!NOTE]
> L'utente proprietario *non può* modificare l'utente proprietario di un file o di una directory. Solo gli utenti con privilegi avanzati possono modificare l'utente proprietario di un file o una directory.

#### <a name="the-owning-group"></a>Gruppo proprietario

Negli ACL POSIX ogni utente è associato a un *gruppo primario*. È ad esempio possibile che l'utente "Alice" appartenga al gruppo "Finance". Alice potrebbe anche appartenere a più gruppi, ma un gruppo viene sempre designato come gruppo primario. Quando Alice crea un file in POSIX, come gruppo proprietario del file viene impostato il gruppo primario di Alice, in questo caso "finanza". Il gruppo proprietario si comporta in modo analogo alle autorizzazioni assegnate per altri utenti o gruppi.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Assegnazione del gruppo proprietario per un nuovo file o directory

* **Caso 1**: la directory radice "/". Questa directory viene creata quando viene creato un contenitore Data Lake Storage Gen2. In questo caso, il gruppo proprietario viene impostato sull'utente che ha creato il contenitore se è stato eseguito tramite OAuth. Se il contenitore viene creato usando una chiave condivisa, una firma di accesso condiviso dell'account o una firma di accesso condiviso del servizio, il proprietario e il gruppo proprietario sono impostati su **$superuser**.
* **Caso 2** (tutti gli altri casi): quando viene creato un nuovo elemento, il gruppo proprietario viene copiato dalla directory padre.

##### <a name="changing-the-owning-group"></a>Modifica del gruppo proprietario

Il gruppo proprietario può essere modificato da:
* Qualsiasi utente con privilegi avanzati.
* Utente proprietario, se è anche membro del gruppo di destinazione

> [!NOTE]
> Il gruppo proprietario non può modificare gli ACL di un file o di una directory.  Mentre il gruppo proprietario è impostato sull'utente che ha creato l'account nel caso della directory radice, **caso 1** precedente, un singolo account utente non è valido per fornire le autorizzazioni tramite il gruppo proprietario. È possibile assegnare questa autorizzazione a un gruppo utenti valido, se applicabile.

### <a name="access-check-algorithm"></a>Algoritmo di controllo dell'accesso

Lo pseudocodice seguente rappresenta l'algoritmo di controllo dell'accesso per gli account di archiviazione.

```
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

#### <a name="the-mask"></a>La maschera

Come illustrato nell'algoritmo di controllo dell'accesso, la maschera limita l'accesso agli utenti non anonimi, al gruppo proprietario e ai gruppi non anonimi.  

> [!NOTE]
> Per un nuovo contenitore di Data Lake Storage Gen2, la maschera per l'ACL di accesso della directory radice ("/") viene impostata per impostazione predefinita su 750 per le directory e 640 per i file. File non ricevono il bit X perché è irrilevante per i file in un sistema solo di archiviazione.
>
> La maschera può essere specificata in base alle singole chiamate. In questo modo sistemi diversi con un elevato utilizzo di risorse, ad esempio i cluster, possono avere maschere effettive diverse per le operazioni su file. Se una maschera viene specificata per una determinata richiesta, esegue l'override completo della maschera predefinita.

#### <a name="the-sticky-bit"></a>Sticky bit

Il bit appiccicoso è una funzionalità più avanzata di un contenitore POSIX. Nel contesto di Data Lake Storage Gen2, è improbabile che lo sticky bit sia necessario. In breve, se lo sticky bit è abilitato in una directory, un elemento figlio può essere solo eliminato o rinominato dall'utente proprietario dell'elemento figlio.

Il bit appiccicoso non viene visualizzato nella portale di Azure.

### <a name="default-permissions-on-new-files-and-directories"></a>Autorizzazioni predefinite per nuovi file e directory

Quando si crea un nuovo file o una nuova directory in una directory esistente, l'ACL predefinito per la directory padre determina quanto segue:

- ACL predefinito e ACL di accesso di una directory figlio.
- ACL di accesso di un file figlio (i file non hanno un ACL predefinito).

#### <a name="umask"></a>umask

Quando si crea un file o una directory, la proprietà umask viene usata per modificare la modalità in cui gli ACL predefiniti vengono impostati sull'elemento figlio. La proprietà umask è un valore a 9 bit sulle directory padre, che contiene un valore RWX per **l'utente proprietario**, **il gruppo proprietario** e **altri**.

La proprietà umask per Azure Data Lake Storage Gen2 è un valore costante impostato su 007. Questo valore viene convertito in:

| componente umask     | Forma numerica | Forma breve | Significato |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Per l'utente proprietario, copiare l'ACL predefinito dell'elemento padre nell'ACL di accesso dell'elemento figlio | 
| umask.owning_group  |    0         |   `---`      | Per il gruppo proprietario, copiare l'ACL predefinito dell'elemento padre nell'ACL di accesso dell'elemento figlio | 
| umask.other         |    7         |   `RWX`      | Per altri, rimuovere tutte le autorizzazioni sull'ACL di accesso dell'elemento figlio |

Il valore umask usato da Azure Data Lake Storage Gen2 significa di fatto che il valore per **other** non viene mai trasmesso per impostazione predefinita sui nuovi elementi figlio, indipendentemente da ciò che indica l'ACL predefinito. 

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

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Domande frequenti sugli elenchi di controllo di accesso in Data Lake Storage Gen2

### <a name="do-i-have-to-enable-support-for-acls"></a>È necessario abilitare il supporto per gli ACL?

No. Il controllo di accesso tramite ACL è abilitato per un account di archiviazione, purché sia attivata la funzionalità di spazio dei nomi gerarchico (HNS).

Se lo spazio dei nomi gerarchico è disattivato, vengono applicate le regole di autorizzazione del controllo degli accessi in base al ruolo Azure.

### <a name="what-is-the-best-way-to-apply-acls"></a>Qual è il modo migliore per applicare gli elenchi di controllo di accesso?

Usare sempre i gruppi di sicurezza di Azure AD come entità assegnate negli elenchi di controllo di accesso. Evitare di assegnare direttamente singoli utenti o entità servizio. L'uso di questa struttura consentirà di aggiungere e rimuovere utenti o entità servizio senza la necessità di riapplicare gli elenchi di controllo di accesso a un'intera struttura di directory. È invece sufficiente aggiungerli o rimuoverli dal gruppo di sicurezza di Azure AD appropriato. Tenere presente che gli elenchi di controllo di accesso non vengono ereditati e quindi per riapplicare gli elenchi di controllo di accesso, è necessario aggiornare l'elenco di controllo di accesso in ogni file e sottodirectory. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Quali autorizzazioni sono necessarie per eliminare in modo ricorsivo una directory e il relativo contenuto?

- Il chiamante ha le autorizzazioni di "utente con privilegi avanzati"

oppure

- Sono necessarie autorizzazioni di Scrittura + Esecuzione per la directory padre.
- Sono necessarie autorizzazioni di Lettura + Scrittura + Esecuzione per la directory da eliminare e per ogni directory al suo interno.

> [!NOTE]
> Non sono necessarie autorizzazioni di Scrittura per eliminare i file nelle directory. La directory radice "/" non può mai essere eliminata.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Chi è il proprietario di un file o una directory?

Il creatore di un file o una directory ne diventa il proprietario. Nel caso della directory radice, si tratta dell'identità dell'utente che ha creato il contenitore.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Quale gruppo viene impostato come gruppo proprietario di un file o una directory al momento della creazione?

Il gruppo proprietario viene copiato da quello della directory padre in cui si crea il nuovo file o la nuova directory.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Se l'utente proprietario di un file non ha le autorizzazioni RWX di cui ha bisogno, che cosa occorre fare?

L'utente proprietario può modificare le autorizzazioni del file in modo da assegnarsi tutte le autorizzazioni RWX necessarie.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Perché in alcuni casi vengono visualizzati GUID negli elenchi di controllo di accesso?

Viene visualizzato un GUID se la voce rappresenta un utente e tale utente non esiste più in Azure AD. In genere ciò si verifica se l'utente non fa più parte dell'azienda o l'account è stato eliminato in Azure AD. Inoltre, le entità servizio e i gruppi di sicurezza non hanno un nome dell'entità utente (UPN) per identificarli e vengono quindi rappresentati dall'attributo OID (un GUID).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Ricerca per categorie impostare gli ACL correttamente per un'entità servizio?

Quando si definiscono gli ACL per le entità servizio, è importante usare l'ID oggetto (OID) dell' *entità servizio* per la registrazione dell'app creata. È importante notare che le app registrate hanno un'entità servizio separata nel tenant di Azure AD specifico. Le app registrate hanno un OID visibile nel portale di Azure, ma l' *entità servizio* presenta un altro OID (diverso).

Per ottenere l'OID per l'entità servizio che corrisponde alla registrazione di un'app, è possibile usare il comando `az ad sp show`. Specificare l'ID applicazione come parametro. Di seguito è riportato un esempio su come ottenere l'OID per l'entità servizio che corrisponde a una registrazione dell'app con ID app = 18218b12-1895-43E9-AD80-6e8fc1ea88ce. Eseguire il comando seguente nell'interfaccia della riga di comando di Azure:

```
$ az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
<<OID will be displayed>>
```

Quando si dispone dell'OID corretto per l'entità servizio, passare alla pagina Storage Explorer **Gestisci accesso** per aggiungere l'OID e assegnare le autorizzazioni appropriate per l'OID. Assicurarsi di selezionare **Salva**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage Gen2 supporta l'ereditarietà degli elenchi di controllo di accesso?

Le assegnazioni con controllo degli accessi in base al ruolo di Azure ereditano. Le assegnazioni scorrono dalle risorse di sottoscrizione, gruppo di risorse e account di archiviazione fino alla risorsa contenitore.

Gli elenchi di controllo di accesso non ereditano. Gli ACL predefiniti tuttavia possono essere usati per impostare gli ACL per le sottodirectory e i file creati nella directory padre. 

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

* [Panoramica di Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)
