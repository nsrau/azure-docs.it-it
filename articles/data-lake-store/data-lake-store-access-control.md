---
title: Panoramica del controllo di accesso in Data Lake Store | Documentazione Microsoft
description: Informazioni sul funzionamento del controllo di accesso in Azure Data Lake Store
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 756d51e41c43be2e80f8fce1c3965e1723f7cec8
ms.openlocfilehash: 7cd0c60b3eaaf6c2fd702d4e4b580299cb71c5fc
ms.lasthandoff: 02/16/2017


---
# <a name="access-control-in-azure-data-lake-store"></a>Controllo di accesso in Azure Data Lake Store

Azure Data Lake Store implementa un modello di controllo di accesso derivante da HDFS, che a sua volta deriva dal modello di controllo di accesso POSIX. Questo articolo offre un riepilogo delle nozioni di base del modello di controllo di accesso per Data Lake Store. Per altre informazioni sul modello di controllo di accesso HDFS, vedere [HDFS Permissions Guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)(Guida alle autorizzazioni HDFS).

## <a name="access-control-lists-on-files-and-folders"></a>Elenchi di controllo di accesso per file e cartelle

Esistono due tipologie di elenchi di controllo di accesso (ACL): **ACL di accesso** e **ACL predefiniti**.

* **ACL di accesso**: questi elenchi controllano l'accesso a un oggetto. Sia i file che le cartelle hanno ACL di accesso.

* **ACL predefiniti**: "modello" di ACL associato a una cartella che determina gli ACL di accesso per tutti gli elementi figlio creati in tale cartella. I file non hanno ACL predefiniti.

![ACL in Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-1.png)

Sia gli ACL di accesso che gli ACL predefiniti presentano la stessa struttura.

![ACL in Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-2.png)



> [!NOTE]
> La modifica dell'ACL predefinito per un elemento padre non influisce sull'ACL di accesso o sull'ACL predefinito degli elementi figlio già esistenti.
>
>

## <a name="users-and-identities"></a>Utenti e identità

Ogni file e cartella ha autorizzazioni distinte per le entità seguenti:

* Utente proprietario del file
* Gruppo proprietario
* Utenti non anonimi
* Gruppi con nome
* Tutti gli altri utenti

Le identità di utenti e gruppi sono identità di Azure Active Directory (Azure AD). Se non viene specificato diversamente, quindi, nel contesto di Data Lake Store un "utente" può essere un utente di AD Azure o un gruppo di sicurezza di Azure AD.

## <a name="permissions"></a>Autorizzazioni

Le autorizzazioni per un oggetto del file system sono **Lettura**, **Scrittura** ed **Esecuzione** e possono essere usate per file e cartelle come illustrato nella tabella seguente:

|            |    File     |   Cartella |
|------------|-------------|----------|
| **Lettura (R)** | È possibile leggere il contenuto di un file | Per elencare il contenuto della cartella sono necessarie le autorizzazioni di **Lettura** ed **Esecuzione**|
| **Scrittura (W)** | È possibile scrivere o aggiungere in un file | Per creare elementi figlio in una cartella sono necessarie le autorizzazioni di **Scrittura** ed **Esecuzione**. |
| **Esecuzione (X)** | Nessun valore nel contesto di Data Lake Store | È necessaria per attraversare gli elementi figlio di una cartella |

### <a name="short-forms-for-permissions"></a>Forme brevi per le autorizzazioni

La forma **RWX** viene usata per indicare **Lettura + Scrittura + Esecuzione**. Esiste una forma numerica ridotta in cui **Lettura=4**, **Scrittura=2** ed **Esecuzione=1** e la cui somma rappresenta le autorizzazioni. Di seguito sono riportati alcuni esempi.

| Forma numerica | Forma breve |      Significato     |
|--------------|------------|------------------------|
| 7            | RWX        | Lettura + Scrittura + Esecuzione |
| 5            | R-X        | Lettura + Esecuzione         |
| 4            | R--        | Lettura                   |
| 0            | ---        | Nessuna autorizzazione         |


### <a name="permissions-do-not-inherit"></a>Non ereditarietà delle autorizzazioni

Nel modello di tipo POSIX usato da Data Lake Store, le autorizzazioni per un elemento vengono archiviate nell'elemento stesso. In altri termini, le autorizzazioni per un elemento non sono ereditabili dagli elementi padre.

## <a name="common-scenarios-related-to-permissions"></a>Scenari comuni correlati alle autorizzazioni

Di seguito sono riportati alcuni scenari comuni che consentono di comprendere quali autorizzazioni sono necessarie per eseguire determinate operazioni su un account Data Lake Store.

### <a name="permissions-needed-to-read-a-file"></a>Autorizzazioni necessarie per la lettura di un file

![ACL in Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-3.png)

* Per il file da leggere, il chiamante deve avere autorizzazioni di **Lettura**.
* Per tutte le cartelle della struttura di cartelle contenenti il file, il chiamante deve avere autorizzazioni di **Esecuzione**.

### <a name="permissions-needed-to-append-to-a-file"></a>Autorizzazioni necessarie per l'aggiunta a un file

![ACL in Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-4.png)

* Per il file a cui si intende eseguire l'aggiunta, il chiamante deve avere autorizzazioni di **Scrittura**.
* Per tutte le cartelle contenenti il file, il chiamante deve avere autorizzazioni di **Esecuzione**.

### <a name="permissions-needed-to-delete-a-file"></a>Autorizzazioni necessarie per l'eliminazione di un file

![ACL in Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-5.png)

* Per la cartella padre, il chiamante deve avere autorizzazioni di **Scrittura + Esecuzione**.
* Per tutte le altre cartelle nel percorso del file, il chiamante deve avere autorizzazioni di **Esecuzione**.



> [!NOTE]
> Se vengono soddisfatte le due condizioni precedenti, non sono necessarie autorizzazioni di scrittura per il file per eliminarlo.
>
>

### <a name="permissions-needed-to-enumerate-a-folder"></a>Autorizzazioni necessarie per l'enumerazione di una cartella

![ACL in Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-6.png)

* Per la cartella di cui si intende eseguire l'enumerazione, il chiamante deve avere autorizzazioni di **Lettura + Esecuzione**.
* Per tutte le cartelle predecessore, il chiamante deve avere autorizzazioni di **Esecuzione**.

## <a name="viewing-permissions-in-the-azure-portal"></a>Visualizzazione delle autorizzazioni nel portale di Azure

Nel pannello **Esplora dati** dell'account Data Lake Store fare clic su **Accesso** per visualizzare gli ACL per un file o una cartella. Fare clic su **Accesso** per visualizzare gli ACL per la cartella **catalog** dell'account **mydatastore**.

![ACL in Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-1.png)

La sezione superiore di questo pannello mostra una panoramica delle autorizzazioni dell'utente, che nello screenshot seguente si chiama Bob. A seguire vengono mostrate le autorizzazioni di accesso. Successivamente, nel pannello **Accesso** fare clic su **Visualizzazione semplice** per passare alla visualizzazione semplificata.

![ACL in Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-simple-view.png)

Fare clic su **Vista avanzata** per visualizzare la vista più avanzata, che mostra i concetti relativi ad ACL predefiniti, maschera e utente con privilegi avanzati.

![ACL in Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-advance-view.png)

## <a name="the-super-user"></a>Utente con privilegi avanzati

Un utente con privilegi avanzati ha diritti superiori rispetto a qualsiasi altro utente di Data Lake Store. Un utente con privilegi avanzati:

* Ha autorizzazioni RWX per **tutti** i file e le cartelle.
* Può modificare le autorizzazioni per qualsiasi file o cartella.
* Può modificare l'utente o il gruppo proprietario di qualsiasi file o cartella.

In Azure un account Data Lake Store include diversi ruoli di Azure, tra cui:

* Proprietari
* Collaboratori
* Lettori

Tutti i membri del ruolo **Proprietari** per un account Data Lake Store sono automaticamente superuser per tale account. Per altre informazioni, vedere l'articolo relativo al [controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md).
Se si vuole creare un ruolo Controllo degli accessi in base al ruolo personalizzato con autorizzazioni di utente avanzato, è necessario avere le autorizzazioni seguenti:
- Microsoft.DataLakeStore/accounts/Superuser/action
- Microsoft.Authorization/roleAssignments/write


## <a name="the-owning-user"></a>Utente proprietario

L'utente che ha creato l'elemento ne è automaticamente l'utente proprietario. Un utente proprietario può:

* Modificare le autorizzazioni di un file di sua proprietà.
* Modificare il gruppo proprietario di un file di sua proprietà, a condizione che l'utente proprietario sia anche membro del gruppo di destinazione.

> [!NOTE]
> L'utente proprietario *non può* modificare l'utente proprietario di un altro file. Solo i superuser possono modificare l'utente proprietario di un file o una cartella.
>
>

## <a name="the-owning-group"></a>Gruppo proprietario

Negli ACL POSIX ogni utente è associato a un "gruppo primario". L'utente "alice", ad esempio, può appartenere al gruppo "finanza". Alice può anche appartenere a più gruppi, ma uno solo è sempre designato come il suo gruppo primario. Quando Alice crea un file in POSIX, come gruppo proprietario del file viene impostato il gruppo primario di Alice, in questo caso "finanza".

Quando viene creato un nuovo elemento del file system, Data Lake Store assegna un valore al gruppo proprietario.

* **Caso 1**: cartella radice "/". Questa cartella viene creata al momento della creazione di un account Data Lake Store. In questo caso il gruppo proprietario viene impostato sull'utente che ha creato l'account.
* **Caso 2**: tutti gli altri casi. Quando viene creato un nuovo elemento, il gruppo proprietario viene copiato dalla cartella padre.

Il gruppo proprietario può essere modificato da:
* Qualsiasi utente con privilegi avanzati.
* Utente proprietario, se è anche membro del gruppo di destinazione

## <a name="access-check-algorithm"></a>Algoritmo di controllo dell'accesso

La figura seguente rappresenta l'algoritmo di controllo dell'accesso per gli account Data Lake Store.

![Algoritmo per gli ACL in Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-algorithm.png)


## <a name="the-mask-and-effective-permissions"></a>Proprietà mask e "autorizzazioni valide"

La **maschera** è un valore RWX usato per limitare l'accesso per **utenti non anonimi**, **gruppo proprietario** e **gruppi con nome** durante l'esecuzione dell'algoritmo di controllo dell'accesso. Di seguito sono descritti i concetti chiave relativi a mask.

* La maschera crea autorizzazioni valide. Vale a dire che modifica le autorizzazioni al momento del controllo di accesso.
* La maschera può essere modificata direttamente dal proprietario del file e da qualsiasi utente con privilegi avanzati.
* La maschera può rimuovere autorizzazioni per creare l'autorizzazione valida, ma *non può* aggiungere autorizzazioni all'autorizzazione valida.

Verranno ora esaminati alcuni esempi. Nell'esempio seguente la maschera è impostata su **RWX** e non rimuove quindi alcuna autorizzazione. Le autorizzazioni valide per l'utente non anonimo, il gruppo proprietario e il gruppo con nome non vengono modificate durante il controllo di accesso.

![ACL in Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-1.png)

Nell'esempio seguente la maschera è impostata su **R-X**. Ciò significa che **disabilita l'autorizzazione di scrittura** per l'**utente non anonimo**, il **gruppo proprietario** e il **gruppo con nome** al momento del controllo di accesso.

![ACL in Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-2.png)

Per riferimento, di seguito è illustrata la visualizzazione nel portale di Azure della maschera per un file o una cartella.

![ACL in Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-mask-view.png)

> [!NOTE]
> Per un nuovo account Data Lake Store, l'impostazione predefinita della maschera per l'ACL di accesso e l'ACL predefinito della cartella radice ("/") è RWX.
>
>

## <a name="permissions-on-new-files-and-folders"></a>Autorizzazioni per nuovi file e cartelle

Quando si crea un nuovo file o una nuova cartella in una cartella esistente, l'ACL predefinito per la cartella padre determina quanto segue:

- ACL predefinito e ACL di accesso di una cartella figlio.
- ACL di accesso di un file figlio (i file non hanno un ACL predefinito).

### <a name="the-access-acl-of-a-child-file-or-folder"></a>ACL di accesso di una cartella o un file figlio.

Quando si crea una cartella o un file figlio, l'ACL predefinito dell'elemento padre viene copiato come ACL di accesso della cartella o del file figlio. Se **altri** utenti hanno autorizzazioni RWX nell'ACL predefinito dell'elemento padre, vengono rimosse dall'ACL di accesso dell'elemento figlio.

![ACL in Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-1.png)

Nella maggior parte degli scenari le informazioni sopra riportate sono sufficienti a illustrare il modo in cui viene determinato l'ACL di accesso di un elemento figlio. Se si ha familiarità con i sistemi POSIX e si vuole comprendere in modo approfondito come viene ottenuta questa trasformazione, vedere la sezione [Ruolo di umask nella creazione dell'ACL di accesso per nuovi file e cartelle](#umasks-role-in-creating-the-access-acl-for-new-files-and-folders) più avanti in questo articolo.


### <a name="a-child-folders-default-acl"></a>ACL predefinito di una cartella figlio

Quando viene creata una cartella figlio in una cartella padre, l'ACL predefinito della cartella padre viene copiato, così com'è, nell'ACL predefinito della cartella figlio.

![ACL in Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-2.png)

## <a name="advanced-topics-for-understanding-acls-in-data-lake-store"></a>Argomenti avanzati per informazioni dettagliate sugli ACL in Data Lake Store

Di seguito sono riportati alcuni argomenti avanzati utili per comprendere il modo in cui vengono determinati gli ACL per i file e le cartelle di Data Lake Store.

### <a name="umasks-role-in-creating-the-access-acl-for-new-files-and-folders"></a>Ruolo di umask nella creazione dell'ACL di accesso per nuovi file e cartelle

In un sistema compatibile con POSIX, il concetto generale è che umask è un valore a 9 bit relativo alla cartella padre che viene usato per trasformare l'autorizzazione per l'**utente proprietario**, il **gruppo proprietario** e gli **altri** utenti nell'ACL di accesso di una nuova cartella o un nuovo file figlio. I bit di umask identificano i bit da disattivare nell'ACL di accesso dell'elemento figlio. L'opzione umask viene così usata per impedire in modo selettivo la propagazione delle autorizzazioni per l'**utente proprietario**, il **gruppo proprietario** e gli **altri** utenti.

In un sistema HDFS umask è in genere un'opzione di configurazione a livello di sito controllata dagli amministratori. Data Lake Store usa una proprietà **umask a livello di account** non modificabile. La tabella seguente illustra l'opzione umask per Data Lake Store.

| Gruppo utenti  | Impostazione | Effetto sull'ACL di accesso di un nuovo elemento figlio |
|------------ |---------|---------------------------------------|
| utente proprietario | ---     | Nessun effetto                             |
| gruppo proprietario| ---     | Nessun effetto                             |
| altri       | RWX     | Rimuovere Lettura + Scrittura + Esecuzione         |

La figura seguente illustra il funzionamento di questa proprietà umask. L'effetto in definitiva è la rimozione dell'autorizzazione **Lettura + Scrittura + Esecuzione** per gli **altri** utenti. Dato che in umask non sono specificati bit per l'**utente proprietario** e il **gruppo proprietario**, tali autorizzazioni non vengono trasformate.

![ACL in Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-umask.png)

### <a name="the-sticky-bit"></a>Sticky bit

Lo sticky bit è una funzionalità più avanzata di un file system POSIX. Nel contesto di Data Lake Store, è improbabile che lo sticky bit sia necessario.

La tabella seguente descrive il funzionamento dello sticky bit in Data Lake Store.

| Gruppo utenti         | File    | Cartella |
|--------------------|---------|-------------------------|
| Sticky bit **OFF** | Nessun effetto   | Nessun effetto           |
| Sticky bit **ON**  | Nessun effetto   | Impedisce a chiunque tranne agli **utenti con privilegi avanzati** e all'**utente proprietario** di un elemento figlio di eliminare o rinominare l'elemento figlio.               |

Lo sticky bit non viene visualizzato nel portale di Azure.

## <a name="common-questions-about-acls-in-data-lake-store"></a>Domande frequenti sugli ACL in Data Lake Store

Di seguito sono riportate alcune domande frequenti sugli ACL in Data Lake Store.

### <a name="do-i-have-to-enable-support-for-acls"></a>È necessario abilitare il supporto per gli ACL?

No. Il controllo di accesso tramite ACL è sempre attivo per un account Data Lake Store.

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

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Se l'utente proprietario di un file non ha le autorizzazioni RWX di cui ha bisogno, che cosa occorre fare?

L'utente proprietario può modificare le autorizzazioni del file in modo da assegnarsi tutte le autorizzazioni RWX necessarie.

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>La visualizzazione degli ACL nel portale di Azure mostra i nomi utente, mentre tramite le API vengono visualizzati i GUID. Per quale motivo?

Le voci negli ACL vengono archiviate come GUID che corrispondono agli utenti in Azure AD. Le API restituiscono i GUID così come sono. Il portale di Azure tenta di semplificare l'uso degli ACL traducendo i GUID in nomi descrittivi, quando è possibile.

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>Perché a volte negli ACL vengono visualizzati i GUID quando si usa il portale di Azure?

Il GUID viene visualizzato quando l'utente non esiste più in Azure AD. In genere ciò si verifica se l'utente non fa più parte dell'azienda o l'account è stato eliminato in Azure AD.

### <a name="does-data-lake-store-support-inheritance-of-acls"></a>Data Lake Store supporta l'ereditarietà degli ACL?

No.

### <a name="what-is-the-difference-between-mask-and-umask"></a>Qual è la differenza tra mask e umask?

| mask | umask|
|------|------|
| La proprietà **mask** è disponibile per ogni file e cartella. | **umask** è una proprietà dell'account Data Lake Store. Di conseguenza, in Data Lake Store esiste un unico valore umask.    |
| La proprietà mask per un file o una cartella può essere modificata dall'utente proprietario, dal gruppo proprietario di un file o da un superuser. | La proprietà umask non può essere modificata da alcun utente, inclusi gli utenti con privilegi avanzati. È un valore costante non modificabile.|
| La proprietà mask viene usata durante l'algoritmo di controllo dell'accesso in fase di esecuzione per determinare se un utente ha il diritto di eseguire un'operazione su un file o una cartella. Il ruolo di mask è creare "autorizzazioni valide" al momento del controllo dell'accesso. | L'opzione umask non viene usata durante il controllo dell'accesso. Viene usata per determinare l'ACL di accesso dei nuovi elementi figlio di una cartella. |
| La proprietà mask è un valore RWX a 3 bit applicato a un utente non anonimo, a un gruppo con nome e all'utente proprietario al momento del controllo dell'accesso.| Umask è un valore a 9 bit applicato all'utente proprietario, al gruppo proprietario e agli **altri** utenti di un nuovo elemento figlio.|

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Dove è possibile reperire altre informazioni sul modello di controllo di accesso POSIX?

* [POSIX Access Control Lists on Linux](http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.html) (Elenchi di controllo di accesso POSIX in Linux)

* [HDFS Permission Guide](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) (Guida alle autorizzazioni HDFS)

* [Domande frequenti su POSIX](http://www.opengroup.org/austin/papers/posix_faq.html)

* [POSIX 1003.1 2008](http://standards.ieee.org/findstds/standard/1003.1-2008.html)

* [POSIX 1003.1e 1997](http://users.suse.com/~agruen/acl/posix/Posix_1003.1e-990310.pdf)

* [ACL POSIX in Linux](http://users.suse.com/~agruen/acl/linux-acls/online/)

* [ACL: Using Access Control Lists on Linux](http://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/) (ACL: uso di elenchi di controllo di accesso in Linux)

## <a name="see-also"></a>Vedere anche

* [Panoramica dell’Archivio Data Lake di Azure](data-lake-store-overview.md)

