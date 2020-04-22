---
title: Controllare l'accesso agli account di archiviazione per SQL su richiesta (anteprima)
description: Questo articolo descrive il modo in cui SQL su richiesta (anteprima) accede ad Archiviazione di Azure e come è possibile controllare l'accesso agli account di archiviazione per SQL su richiesta in Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420055"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Controllare l'accesso agli account di archiviazione per SQL su richiesta (anteprima) in Azure Synapse Analytics

Una query di SQL su richiesta (anteprima) legge i file direttamente da Archiviazione di Azure. Poiché l'account di archiviazione è un oggetto esterno alla risorsa SQL su richiesta, sono necessarie le credenziali appropriate. L'utente deve avere le autorizzazioni applicabili concesse per l'uso delle credenziali necessarie. Questo articolo descrive i tipi di credenziali che è possibile usare e il modo in cui viene eseguita la ricerca di credenziali per gli utenti di SQL e Azure AD.

## <a name="supported-storage-authorization-types"></a>Tipi di autorizzazione supportati per l'archiviazione

Un utente connesso alla risorsa SQL su richiesta deve essere autorizzato ad accedere ed eseguire query sui file in Archiviazione di Azure. Sono supportati tre tipi di autorizzazione:

- [Firma di accesso condiviso](#shared-access-signature)
- [Identità gestita](#managed-identity)
- [Identità utente](#user-identity)

> [!NOTE]
> [Pass-through di Azure AD](#force-azure-ad-pass-through) è il comportamento predefinito quando si crea un'area di lavoro. Se si usa il pass-through, non è necessario creare credenziali per ogni account di archiviazione a cui si accede con gli account di accesso di AD. È possibile [disabilitare questo comportamento](#disable-forcing-azure-ad-pass-through).

Nella tabella seguente sono disponibili i diversi tipi di autorizzazione già supportati o che lo saranno prossimamente.

| Tipo di autorizzazione                    | *Utente SQL*    | *Utente di Azure AD*     |
| ------------------------------------- | ------------- | -----------    |
| [Firma di accesso condiviso](#shared-access-signature)       | Supportato     | Supportato      |
| [Identità gestita](#managed-identity) | Non supportate | Non supportate  |
| [Identità utente](#user-identity)       | Non supportate | Supportato      |

### <a name="shared-access-signature"></a>Firma di accesso condiviso

**Firma di accesso condiviso**: fornisce accesso delegato alle risorse in un account di archiviazione. Con una firma di accesso condiviso è possibile concedere ai client l'accesso alle risorse in un account di archiviazione, senza condividere le chiavi dell'account. La firma di accesso condiviso offre un controllo granulare sul tipo di accesso concesso ai client a cui viene assegnata, tra cui intervallo di validità, autorizzazioni concesse, intervallo di indirizzi IP accettabile e protocollo accettabile (HTTPS/HTTP).

Per ottenere un token di firma di accesso condiviso, passare a **Portale di Azure -> Account di archiviazione -> Firma di accesso condiviso -> Configurazione autorizzazioni -> Genera firma di accesso condiviso e stringa di connessione.**

> [!IMPORTANT]
> Quando viene generato, il token di firma di accesso condiviso è preceduto da un punto interrogativo ('?'). Per usare il token in SQL su richiesta, è necessario rimuovere il punto interrogativo ('?') durante la creazione di una credenziale. Ad esempio:
>
> Token di firma di accesso condiviso: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

### <a name="user-identity"></a>Identità utente

**Identità utente**: nota anche come "pass-through", è un tipo di autorizzazione in cui per concedere l'accesso ai dati viene usata l'identità dell'utente di Azure AD che ha eseguito l'accesso a SQL su richiesta. Prima di accedere ai dati, l'amministratore di Archiviazione di Azure deve concedere le apposite autorizzazioni all'utente di Azure AD. Come indicato nella tabella precedente, il tipo di utente SQL non è supportato.

> [!NOTE]
> Se si usa il [pass-through di Azure AD](#force-azure-ad-pass-through), non è necessario creare credenziali per ogni account di archiviazione a cui si accede con gli account di accesso di AD.

> [!IMPORTANT]
> Per accedere ai dati con l'identità utente, è necessario avere il ruolo di proprietario/collaboratore/lettore dei dati dei BLOB di archiviazione.
> Anche se si è proprietari di un account di archiviazione, è comunque necessario aggiungere se stessi in uno dei ruoli dei dati dei BLOB di archiviazione.
>
> Per altre informazioni sul controllo di accesso in Azure Data Lake Store Gen2, vedere l'articolo [Controllo di accesso in Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md).
>

### <a name="managed-identity"></a>Identità gestita

**Identità gestita**: nota anche come MSI, è una funzionalità di Azure Active Directory (Azure AD) che offre servizi di Azure per SQL su richiesta. Inoltre, distribuisce un'identità automaticamente gestita in Azure AD. Questa identità può essere usata per autorizzare la richiesta di accesso ai dati in Archiviazione di Azure.

Prima di accedere ai dati, l'amministratore di Archiviazione di Azure deve concedere le apposite autorizzazioni all'identità gestita. La concessione di autorizzazioni all'identità gestita viene eseguita allo stesso modo che per qualsiasi altro utente di Azure AD.

## <a name="create-credentials"></a>Creare le credenziali

Per eseguire query su un file in Archiviazione di Azure, l'endpoint SQL su richiesta deve avere una credenziale a livello di server che contiene le informazioni di autenticazione. Per aggiungere una credenziale, eseguire [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Sarà necessario specificare un argomento CREDENTIAL NAME, che deve corrispondere a una parte o all'intero percorso dei dati in Archiviazione (vedere di seguito).

> [!NOTE]
> L'argomento FOR CRYPTOGRAPHIC PROVIDER non è supportato.

Per tutti i tipi di autorizzazione supportati, le credenziali possono puntare a un account, a un contenitore, a qualsiasi directory (non radice) o a un singolo file.

CREDENTIAL NAME deve corrispondere al percorso completo del contenitore, della cartella o del file, nel formato seguente: `<prefix>://<storage_account_path>/<storage_path>`

| Origine dati esterna       | Prefisso | Percorso dell'account di archiviazione                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Archiviazione BLOB di Azure         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>.dfs.core.windows.net              |

 '<storage_path>' è un percorso all'interno dell'archiviazione che punta alla cartella o al file da leggere.

> [!NOTE]
> È disponibile un argomento CREDENTIAL NAME speciale, `UserIdentity`, che [forza il pass-through di Azure AD](#force-azure-ad-pass-through). Leggere informazioni sugli effetti che ha sulla [ricerca di credenziali](#credential-lookup) durante l'esecuzione di query.

Facoltativamente, per consentire a un utente di creare o eliminare una credenziale, l'amministratore può concedere, negare o alterare qualsiasi credenziale:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Tipi di autorizzazione e archiviazione supportati

È possibile usare le combinazioni seguenti di tipi di autorizzazione e tipi di archiviazione di Azure:

|                     | Archiviazione BLOB   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *Firma di accesso condiviso*               | Supportato      | Non supportato   | Supportato     |
| *Identità gestita* | Non supportate  | Non supportate    | Non supportate |
| *Identità utente*    | Supportato      | Supportato        | Supportato     |

### <a name="examples"></a>Esempi

A seconda del [tipo di autorizzazione](#supported-storage-authorization-types), è possibile creare credenziali usando la sintassi T-SQL riportata di seguito.

**Firma di accesso condiviso e archiviazione BLOB**

Sostituire <*mystorageaccountname*> con il nome dell'account di archiviazione effettivo e <*mystorageaccountcontainername*> con il nome del contenitore effettivo:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Identità utente e Azure Data Lake Storage Gen1**

Sostituire <*mystorageaccountname*> con il nome dell'account di archiviazione effettivo e <*mystorageaccountcontainername*> con il nome del contenitore effettivo:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**Identità utente e Azure Data Lake Storage Gen2**

Sostituire <*mystorageaccountname*> con il nome dell'account di archiviazione effettivo e <*mystorageaccountcontainername*> con il nome del contenitore effettivo:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>Forzare il pass-through di Azure AD

La forzatura del pass-through di Azure AD è un comportamento predefinito eseguito tramite un argomento CREDENTIAL NAME speciale, `UserIdentity`, creato automaticamente durante il provisioning dell'area di lavoro di Azure Synapse. Forza l'utilizzo di un pass-through di Azure AD per ogni query di ogni account di accesso di Azure AD, che si verificherà nonostante l'esistenza di altre credenziali.

> [!NOTE]
> Il pass-through di Azure AD è un comportamento predefinito. Non è necessario creare credenziali per ogni account di archiviazione a cui si accede con gli account di accesso di AD.

Se si [disabilita la forzatura del pass-through di Azure AD per ogni query](#disable-forcing-azure-ad-pass-through) e lo si vuole riabilitare, eseguire:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Per abilitare la forzatura del pass-through di Azure AD per un utente specifico, è possibile concedergli l'autorizzazione REFERENCE per la credenziale `UserIdentity`. L'esempio seguente abilita la forzatura del pass-through di Azure AD per user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Per altre informazioni su come SQL su richiesta trova le credenziali da usare, vedere [Ricerca di credenziali](#credential-lookup).

## <a name="disable-forcing-azure-ad-pass-through"></a>Disabilitare la forzatura del pass-through di Azure AD

È possibile disabilitare la [forzatura del pass-through di Azure AD per ogni query](#force-azure-ad-pass-through). Per disabilitarlo, eliminare la credenziale `Userdentity` usando:

```sql
DROP CREDENTIAL [UserIdentity];
```

Per riabilitarlo, vedere la sezione [Forzare il pass-through di Azure AD](#force-azure-ad-pass-through).

Per disabilitare la forzatura del pass-through di Azure AD per un utente specifico, è possibile negargli l'autorizzazione REFERENCE per la credenziale `UserIdentity`. L'esempio seguente disabilita la forzatura del pass-through di Azure AD per un nome utente:

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Per altre informazioni su come SQL su richiesta trova le credenziali da usare, vedere [Ricerca di credenziali](#credential-lookup).

## <a name="grant-permissions-to-use-credential"></a>Concedere le autorizzazioni per l'uso delle credenziali

Per usare le credenziali, l'utente deve avere l'autorizzazione REFERENCES per una credenziale specifica. Per concedere un'autorizzazione REFERENCES a un utente specifico per una credenziale specifica, eseguire:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Per garantire un'esperienza uniforme con il pass-through di Azure AD, per impostazione predefinita tutti gli utenti hanno il diritto per l'uso della credenziale `UserIdentity`. Per ottenere questo risultato, viene eseguita automaticamente l'istruzione seguente con il provisioning dell'area di lavoro di Azure Synapse:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>Ricerca di credenziali

Quando si autorizzano le query, la ricerca delle credenziali viene usata per accedere a un account di archiviazione e si basa sulle regole seguenti:

- L'utente è connesso con l'account di accesso di Azure AD

  - Se esiste una credenziale UserIdentity per cui l'utente ha autorizzazioni REFERENCE, verrà usato il pass-through di Azure AD; in caso contrario, [cercare le credenziali in base al percorso](#lookup-credential-by-path)

- L'utente è connesso con l'account di accesso SQL

  - Usare la [ricerca di credenziali in base al percorso](#lookup-credential-by-path)

### <a name="lookup-credential-by-path"></a>Cercare le credenziali in base al percorso

Se la forzatura del pass-through di Azure AD è disabilitata, la ricerca di credenziali sarà basata sul percorso di archiviazione (primo livello di profondità) e sull'esistenza di un'autorizzazione REFERENCES per le credenziali specifiche. Se è possibile usare più credenziali per accedere allo stesso file, SQL su richiesta userà quella più specifica.  

Di seguito è riportato un esempio di query eseguita sul percorso di file seguente: *account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX.ext*

La ricerca di credenziali verrà completata nell'ordine seguente:

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

Se un utente non ha l'autorizzazione REFERENCES per la credenziale numero 5, SQL su richiesta verificherà che l'utente abbia tale autorizzazione per una credenziale su un livello più alto e continuerà finché non individua quella corretta. Se non viene trovata alcuna autorizzazione di questo tipo, verrà restituito un messaggio di errore.

### <a name="credential-and-path-level"></a>Credenziali e livello del percorso

A seconda della forma del percorso desiderata, per l'esecuzione di query è necessario soddisfare i requisiti seguenti:

- Se la query è destinata a più file (cartelle, con o senza caratteri jolly), l'utente deve avere accesso a una credenziale almeno al livello della directory radice (livello di contenitore). Questo livello di accesso è necessario perché i file elencati sono relativi rispetto alla directory radice (limitazioni di Archiviazione di Azure)
- Se la query è destinata a un singolo file, l'utente deve avere accesso a una credenziale a qualsiasi livello, perché SQL su richiesta accede direttamente al file, ovvero senza elencare le cartelle.

|                  | *Account* | *Directory radice* | *Qualsiasi altra directory* | *File*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *Singolo file*    | Supportato | Supportato        | Supportato             | Supportato     |
| *Più file* | Supportato | Supportato        | Non supportate         | Non supportate |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come eseguire query su diversi tipi di file e cartelle e su come creare e usare le viste, vedere gli articoli seguenti:

- [Eseguire query su un singolo file CSV](query-single-csv-file.md)
- [Eseguire query su cartelle e più file CSV](query-folders-multiple-csv-files.md)
- [Eseguire query su file specifici](query-specific-files.md)
- [Eseguire query su file Parquet](query-parquet-files.md)
- [Creare e usare viste](create-use-views.md)
- [Eseguire query su file JSON](query-json-files.md)
- [Eseguire query su tipi annidati di Parquet](query-parquet-nested-types.md)
