---
title: Panoramica del controllo di accesso in Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni sul funzionamento del controllo di accesso in Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 5ad7ef714147616fe55a9b978d501b974323e251
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65949565"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Controllo di accesso in Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementa un modello di controllo di accesso che supporta il controllo degli accessi in base al ruolo di Azure (RBAC) sia elenchi di controllo di accesso di tipo POSIX (ACL). Questo articolo offre un riepilogo delle nozioni di base del modello di controllo di accesso per Data Lake Storage Gen2.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

RBAC Usa le assegnazioni di ruolo da applicare in modo efficace set di autorizzazioni agli *le entità di sicurezza*. Oggetto *entità di sicurezza* è un oggetto che rappresenta un utente, gruppo, l'entità servizio o identità gestita che viene definito in Azure Active Directory (AD) che richiede l'accesso alle risorse di Azure.

In genere, le risorse di Azure sono vincolate a risorse di primo livello (ad esempio: Account di archiviazione di Azure). Nel caso di Archiviazione di Azure, e di conseguenza di Azure Data Lake Storage Gen2, questo meccanismo è stato esteso alla risorsa del file system.

Per informazioni su come assegnare i ruoli per le entità di sicurezza nell'ambito dell'account di archiviazione, vedere [autentica l'accesso ad Azure di BLOB e code usando Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>L'impatto delle assegnazioni di ruolo sugli elenchi di controllo di accesso a livello di file e directory

Mentre tramite le assegnazioni di ruolo RBAC è un meccanismo potente per controllare le autorizzazioni di accesso, si tratta di un meccanismo molto grana con granularità rispetto all'ACL. La granularità più fine per il controllo degli accessi in base al ruolo è a livello di file system e ciò verrà valutato con una priorità più elevata degli elenchi di controllo di accesso. Pertanto, se si assegna un ruolo a un'entità di sicurezza nell'ambito di un file system, tale entità di sicurezza ha il livello di autorizzazione associato al ruolo per tutte le sottodirectory e file in tale sistema di file, indipendentemente dalle assegnazioni di ACL.

Quando un'entità di sicurezza verrà concesse le autorizzazioni di dati RBAC tramite un [ruolo incorporato](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues), o tramite un ruolo personalizzato, queste autorizzazioni vengono valutate per primi dell'autorizzazione di una richiesta. Se l'operazione richiesta è autorizzata da assegnazioni di RBAC dell'entità di sicurezza, quindi l'autorizzazione è risolti immediatamente e non altri vengono eseguiti controlli ACL. In alternativa, se l'entità di sicurezza non ha un'assegnazione di RBAC o operazione della richiesta non corrisponde l'autorizzazione assegnata, quindi ACL vengono eseguiti controlli per determinare se l'entità di sicurezza è autorizzato a eseguire l'operazione richiesta.

> [!NOTE]
> Se l'entità di sicurezza è stato assegnato l'assegnazione di ruolo predefiniti di proprietario dei dati di archiviazione Blob, quindi l'entità di sicurezza è considerata una *superuser* e viene concesso l'accesso completo a tutte le operazioni di mutazione, tra cui l'impostazione di proprietario di una directory o file, nonché gli ACL per le directory e file per cui non sono il proprietario. L'accesso utente con privilegi avanzati è il solo modo autorizzato di cambiare il proprietario di una risorsa.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Autenticazione chiave condivisa e firma di accesso condiviso (SAS)

Azure Data Lake Storage Gen2 supporta i metodi di autenticazione chiave condivisa e firma di accesso condiviso per l'autenticazione. Una caratteristica di questi metodi di autenticazione è che l'identità non è associata al chiamante e pertanto autorizzazione basata sull'autorizzazione dell'entità di sicurezza non è possibile eseguire.

Nel caso della chiave condivisa, il chiamante ottiene di fatto l'accesso "utente con privilegi avanzati", vale a dire l'accesso completo a tutte le operazioni su tutte le risorse, inclusa l'impostazione del proprietario e la modifica degli elenchi di controllo di accesso.

I token di firma di accesso condiviso includono le autorizzazioni consentite come parte del token. Le autorizzazioni incluse nel token di firma di accesso condiviso vengono di fatto applicate a tutte le decisioni di autorizzazione, ma non vengono eseguiti altri controlli ACL.

## <a name="access-control-lists-on-files-and-directories"></a>Elenchi di controllo di accesso per file e directory

È possibile associare un'entità di sicurezza con un livello di accesso per file e directory. Queste associazioni vengono acquisite in un *elenco di controllo di accesso (ACL)*. Ogni file e directory nell'account di archiviazione è un elenco di controllo di accesso.

Se è stato assegnato un ruolo a un'entità di sicurezza a livello di account di archiviazione, è possibile usare gli elenchi di controllo di accesso per concedere a che tale entità di protezione accesso con privilegi elevati per le directory e file specifici.

È possibile utilizzare gli elenchi di controllo di accesso per fornire un livello di accesso inferiore rispetto a un livello concesso da un'assegnazione di ruolo. Ad esempio, se si assegna il [collaboratore ai dati Blob di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview) negli elenchi del ruolo a un'entità, quindi non è possibile usare il controllo di accesso di sicurezza per impedire la scrittura in una directory di tale entità di sicurezza.

### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Set di file e directory le autorizzazioni a livello tramite elenchi di controllo di accesso

Per impostare le autorizzazioni a livello di file e directory, vedere uno degli articoli seguenti:

|Se si vuole usare questo strumento:    |Vedere questo articolo:    |
|--------|-----------|
|Esplora archivi Azure    |[Impostare autorizzazioni a livello di file e directory usando Azure Storage Explorer con Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)|
|API REST    |[Percorso - aggiornamento](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Se l'entità di sicurezza è un *servizio* dell'entità, è importante usare l'ID oggetto dell'entità servizio e non l'ID oggetto della registrazione dell'app correlata. Per ottenere l'ID oggetto dell'entità servizio open la CLI di Azure e quindi usare questo comando: `az ad sp show --id <Your App ID> --query objectId`. Assicurarsi di sostituire il `<Your App ID>` segnaposto con l'ID App della registrazione dell'app.

### <a name="types-of-access-control-lists"></a>Tipi di elenchi di controllo di accesso

Esistono due tipi di elenchi di controllo di accesso: *ACL di accesso* e *ACL predefiniti*.

gli elenchi ACL di accesso controllano l'accesso a un oggetto. Sia i file che le directory hanno ACL di accesso.

ACL predefiniti sono modelli di ACL associato a una directory che determinano gli ACL di accesso per tutti gli elementi figlio creati in tale directory. I file non hanno ACL predefiniti.

Sia gli ACL di accesso che gli ACL predefiniti presentano la stessa struttura.

Sia gli ACL di accesso che gli ACL predefiniti presentano la stessa struttura.

> [!NOTE]
> La modifica dell'ACL predefinito per un elemento padre non influisce sull'ACL di accesso o sull'ACL predefinito degli elementi figlio già esistenti.

### <a name="levels-of-permission"></a>Livelli di autorizzazione

Le autorizzazioni per un oggetto del file system sono **Lettura**, **Scrittura** ed **Esecuzione** e possono essere usate per file e directory come illustrato nella tabella seguente:

|            |    File     |   Directory |
|------------|-------------|----------|
| **Lettura (R)** | È possibile leggere il contenuto di un file | Per elencare il contenuto della directory, sono necessarie le autorizzazioni di **Lettura** ed **Esecuzione** |
| **Scrittura (W)** | È possibile scrivere o aggiungere in un file | Per creare elementi figlio in una directory, sono necessarie le autorizzazioni di **Scrittura** ed **Esecuzione** |
| **Esecuzione (X)** | Nessun valore nel contesto di Data Lake Storage Gen2 | È necessaria per attraversare gli elementi figlio di una directory |

#### <a name="short-forms-for-permissions"></a>Forme brevi per le autorizzazioni

La forma **RWX** viene usata per indicare **Lettura + Scrittura + Esecuzione**. Esiste una forma numerica ridotta in cui **Lettura=4**, **Scrittura=2** ed **Esecuzione=1** e la cui somma rappresenta le autorizzazioni. Di seguito sono riportati alcuni esempi.

| Forma numerica | Forma breve |      Significato     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Lettura + Scrittura + Esecuzione |
| 5            | `R-X`        | Lettura + Esecuzione         |
| 4            | `R--`        | Lettura                   |
| 0            | `---`        | Nessuna autorizzazione         |

#### <a name="permissions-inheritance"></a>Ereditarietà delle autorizzazioni

Nel modello di tipo POSIX usato da Data Lake Storage Gen2, le autorizzazioni per un elemento vengono archiviate nell'elemento stesso. In altre parole, le autorizzazioni per un elemento non possono essere ereditate dagli elementi padre se le autorizzazioni vengono impostate dopo che l'elemento figlio è già stato creato. Le autorizzazioni vengono ereditate solo se le autorizzazioni predefinite sono state impostate per gli elementi padre prima che gli elementi figlio siano stati creati.

### <a name="common-scenarios-related-to-permissions"></a>Scenari comuni correlati alle autorizzazioni

Nella tabella seguente sono elencati alcuni scenari comuni utili per comprendere quali autorizzazioni sono necessarie per eseguire determinate operazioni in un account di archiviazione.

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
- Nome identità gestite
- Tutti gli altri utenti

Le identità di utenti e gruppi sono identità di Azure Active Directory (Azure AD). In tal caso, se non diversamente specificato, un *utente*, nel contesto di Data Lake Storage Gen2, può fare riferimento a un utente di Azure AD, identità dell'entità, vengono gestiti, account o gruppo di sicurezza del servizio.

#### <a name="the-owning-user"></a>Utente proprietario

L'utente che ha creato l'elemento ne è automaticamente l'utente proprietario. Un utente proprietario può:

* Modificare le autorizzazioni di un file di sua proprietà.
* Modificare il gruppo proprietario di un file di sua proprietà, a condizione che l'utente proprietario sia anche membro del gruppo di destinazione.

> [!NOTE]
> L'utente proprietario *non può* modificare l'utente proprietario di un file o di una directory. Solo gli utenti con privilegi avanzati possono modificare l'utente proprietario di un file o una directory.

#### <a name="the-owning-group"></a>Gruppo proprietario

Negli ACL POSIX ogni utente è associato a un *gruppo primario*. Ad esempio, l'utente "Alice" potrebbe appartenere al gruppo "Finanza". Alice può anche appartenere a più gruppi, ma uno solo è sempre designato come gruppo primario. Quando Alice crea un file in POSIX, come gruppo proprietario del file viene impostato il gruppo primario di Alice, in questo caso "finanza". Il gruppo proprietario si comporta in modo analogo alle autorizzazioni assegnate per altri utenti o gruppi.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Assegnazione del gruppo proprietario per un nuovo file o directory

* **Caso 1**: directory radice "/". Questa directory viene creata quando viene creato un file system di Data Lake Storage Gen2. In questo caso il gruppo proprietario viene impostato sull'utente che ha creato il file system se l'operazione è stata eseguita usando OAuth. Se viene creato il file system usando chiave condivisa, un Account SAS o una firma di accesso condiviso del servizio, quindi il proprietario e il gruppo proprietario viene impostati su **$superuser**.
* **Caso 2** (qualsiasi altro caso): quando viene creato un nuovo elemento, il gruppo proprietario viene copiato dalla directory padre.

##### <a name="changing-the-owning-group"></a>Modifica del gruppo proprietario

Il gruppo proprietario può essere modificato da:
* Qualsiasi utente con privilegi avanzati.
* Utente proprietario, se è anche membro del gruppo di destinazione

> [!NOTE]
> Il gruppo proprietario non può modificare gli ACL di un file o di una directory.  Mentre il gruppo proprietario viene impostato sull'utente che ha creato l'account nel caso la directory radice, **caso 1** sopra, un singolo account utente non è valido per fornire autorizzazioni tramite il gruppo proprietario. È possibile assegnare questa autorizzazione a un gruppo utenti valido, se applicabile.

### <a name="access-check-algorithm"></a>Algoritmo di controllo dell'accesso

Lo pseudocodice seguente rappresenta l'algoritmo di controllo di accesso per gli account di archiviazione.

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
> Per un nuovo file system di Data Lake Storage Gen2, la maschera per l'ACL di accesso della directory radice ("/") è impostata sul valore predefinito 750 per le directory e 640 per i file. File non ricevono il bit X perché è irrilevante per i file in un sistema solo di archiviazione.
>
> La maschera può essere specificata in base alle singole chiamate. In questo modo sistemi diversi con un elevato utilizzo di risorse, ad esempio i cluster, possono avere maschere effettive diverse per le operazioni su file. Se una maschera viene specificata per una determinata richiesta, esegue l'override completo della maschera predefinita.

#### <a name="the-sticky-bit"></a>Sticky bit

Lo sticky bit è una funzionalità più avanzata di un file system POSIX. Nel contesto di Data Lake Storage Gen2, è improbabile che lo sticky bit sia necessario. In breve, se lo sticky bit è abilitato in una directory, un elemento figlio può essere solo eliminato o rinominato dall'utente proprietario dell'elemento figlio.

Lo sticky bit non viene visualizzato nel portale di Azure.

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

 No. Controllo di accesso tramite ACL è abilitato per un account di archiviazione, purché la gerarchica Namespace (HNS) funzionalità è attivata.

Se lo spazio dei nomi gerarchico è disattivato, vengono applicate le regole di autorizzazione del controllo degli accessi in base al ruolo Azure.

### <a name="what-is-the-best-way-to-apply-acls"></a>Qual è il modo migliore per applicare gli elenchi di controllo di accesso?

Usare sempre i gruppi di sicurezza di Azure AD come entità assegnate negli elenchi di controllo di accesso. Evitare di assegnare direttamente singoli utenti o entità servizio. L'uso di questa struttura consentirà di aggiungere e rimuovere utenti o entità servizio senza la necessità di riapplicare gli elenchi di controllo di accesso a un'intera struttura di directory. È invece sufficiente aggiungerli o rimuoverli dal gruppo di sicurezza di Azure AD appropriato. Tenere presente che gli elenchi di controllo di accesso non vengono ereditati e quindi per riapplicare gli elenchi di controllo di accesso, è necessario aggiornare l'elenco di controllo di accesso in ogni file e sottodirectory. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Quali autorizzazioni sono necessarie per eliminare in modo ricorsivo una directory e il relativo contenuto?

- Il chiamante ha le autorizzazioni di "utente con privilegi avanzati"

Oppure

- Sono necessarie autorizzazioni di Scrittura + Esecuzione per la directory padre.
- Sono necessarie autorizzazioni di Lettura + Scrittura + Esecuzione per la directory da eliminare e per ogni directory al suo interno.

> [!NOTE]
> Non sono necessarie autorizzazioni di Scrittura per eliminare i file nelle directory. La directory radice "/" non può mai essere eliminata.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Chi è il proprietario di un file o una directory?

Il creatore di un file o una directory ne diventa il proprietario. Nel caso della directory radice, è l'identità dell'utente che ha creato il file system.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Quale gruppo viene impostato come gruppo proprietario di un file o una directory al momento della creazione?

Il gruppo proprietario viene copiato da quello della directory padre in cui si crea il nuovo file o la nuova directory.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Se l'utente proprietario di un file non ha le autorizzazioni RWX di cui ha bisogno, Cosa devo fare?

L'utente proprietario può modificare le autorizzazioni del file in modo da assegnarsi tutte le autorizzazioni RWX necessarie.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Perché in alcuni casi vengono visualizzati GUID negli elenchi di controllo di accesso?

Viene visualizzato un GUID se la voce rappresenta un utente e tale utente non esiste più in Azure AD. In genere ciò si verifica se l'utente non fa più parte dell'azienda o l'account è stato eliminato in Azure AD. Inoltre, le entità servizio e i gruppi di sicurezza non hanno un nome dell'entità utente (UPN) per identificarli e vengono quindi rappresentati dall'attributo OID (un GUID).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Come si imposta gli ACL in modo corretto per un servizio principale?

Quando si definiscono gli ACL per le entità servizio, è importante usare l'ID di oggetto (OID) del *entità servizio* per la registrazione dell'app che è stato creato. È importante notare che le app registrate dispone di un'entità servizio separato in specifico tenant di Azure AD. Le app registrate hanno un identificatore di oggetto che è visibile nel portale di Azure, ma il *entità servizio* dispone di un altro OID (diverso).

Per ottenere l'OID per l'entità servizio che corrisponde a una registrazione dell'app, è possibile usare il `az ad sp show` comando. Specificare l'ID dell'applicazione come parametro. Di seguito è riportato un esempio su come ottenere l'OID per l'entità servizio che corrisponde a una registrazione dell'app con App ID = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Eseguire il comando seguente nell'interfaccia della riga di comando di Azure:

`az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
<<OID will be displayed>>`

Quando si ha l'OID corretto per l'entità servizio, passare a Storage Explorer **Gestisci accesso** pagina per aggiungere l'OID e assegnare le autorizzazioni appropriate per l'identificatore di oggetto. Assicurarsi di selezionare **salvare**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage Gen2 supporta l'ereditarietà degli elenchi di controllo di accesso?

Le assegnazioni con controllo degli accessi in base al ruolo di Azure ereditano. Le assegnazioni passano dalle risorse sottoscrizione, gruppo di risorse e account di archiviazione alla risorsa file system.

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
