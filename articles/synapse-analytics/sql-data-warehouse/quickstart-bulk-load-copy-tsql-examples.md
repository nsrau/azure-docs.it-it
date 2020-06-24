---
title: Meccanismi di autenticazione con l'istruzione COPY
description: Questo articolo descrive i meccanismi di autenticazione per il caricamento bulk dei dati
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 05/06/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: f5f6c6970ad8bb697ceb118b6725b37e93ca80b5
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213058"
---
# <a name="securely-load-data-using-synapse-sql"></a>Caricare i dati in modo sicuro tramite Synapse SQL

Questo articolo descrive i meccanismi di autenticazione sicura per l'[istruzione COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) e ne fornisce esempi. L'istruzione COPY rappresenta il modo più flessibile e sicuro per il caricamento bulk dei dati in Synapse SQL.
## <a name="supported-authentication-mechanisms"></a>Meccanismi di autenticazione supportati

La matrice seguente descrive i metodi di autenticazione supportati per ogni tipo di file e di account di archiviazione. Si applica alla posizione di archiviazione di origine e al percorso del file di errore.

|                      |                CSV                |              Parquet              |                ORC                |
| :------------------: | :-------------------------------: | :-------------------------------: | :-------------------------------: |
|  Archivio BLOB di Azure  | FIRMA DI ACCESSO CONDIVISO/IDENTITÀ DEL SERVIZIO GESTITA/ENTITÀ SERVIZIO/CHIAVE/AAD |              FIRMA DI ACCESSO CONDIVISO/CHIAVE              |              FIRMA DI ACCESSO CONDIVISO/CHIAVE              |
| Azure Data Lake Gen2 | FIRMA DI ACCESSO CONDIVISO/IDENTITÀ DEL SERVIZIO GESTITA/ENTITÀ SERVIZIO/CHIAVE/AAD | FIRMA DI ACCESSO CONDIVISO/IDENTITÀ DEL SERVIZIO GESTITA/ENTITÀ SERVIZIO/CHIAVE/AAD | FIRMA DI ACCESSO CONDIVISO/IDENTITÀ DEL SERVIZIO GESTITA/ENTITÀ SERVIZIO/CHIAVE/AAD |

## <a name="a-storage-account-key-with-lf-as-the-row-terminator-unix-style-new-line"></a>R. Chiave dell'account di archiviazione con LF come carattere di terminazione della riga (nuova riga in stile Unix)


```sql
--Note when specifying the column list, input field numbers start from 1
COPY INTO target_table (Col_one default 'myStringDefault' 1, Col_two default 1 3)
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='<Your_Account_Key>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='x6RWv4It5F2msnjelv3H4DA80n0QW0daPdw43jM0nyetx4c6CpDkdj3986DX5AHFMIf/YN4y6kkCnU8lb+Wx0Pj+6MDw=='),
    ,ROWTERMINATOR='0x0A' --0x0A specifies to use the Line Feed character (Unix based systems)
)
```
> [!IMPORTANT]
>
> - Usare il valore esadecimale (0x0A) per specificare il carattere di nuova riga/avanzamento riga. Si noti che l'istruzione COPY interpreta la stringa '\n' come '\r\n' (nuova riga di ritorno a capo).

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator-windows-style-new-line"></a>B. Firme di accesso condiviso (SAS) con CRLF come carattere di terminazione della riga (nuova riga in stile Windows)
```sql
COPY INTO target_table
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<Your_SAS_Token>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='?sv=2018-03-28&ss=bfqt&srt=sco&sp=rl&st=2016-10-17T20%3A14%3A55Z&se=2021-10-18T20%3A19%3A00Z&sig=IEoOdmeYnE9%2FKiJDSFSYsz4AkNa%2F%2BTx61FuQ%2FfKHefqoBE%3D'),
    ,ROWTERMINATOR='\n'-- COPY command automatically prefixes the \r character when \n (newline) is specified. This results in carriage return newline (\r\n) for Windows based systems.
)
```

> [!IMPORTANT]
>
> - Non specificare il TERMINATORE RIGA come '\r\n' perché viene interpretato come '\r\r\n' e può provocare problemi di analisi

## <a name="c-managed-identity"></a>C. Identità gestita

L'autenticazione dell'identità gestita è obbligatoria quando l'account di archiviazione è collegato a una rete virtuale. 

### <a name="prerequisites"></a>Prerequisiti

1. Installare Azure PowerShell usando questa [guida](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Se si dispone di un account di archiviazione BLOB o per utilizzo generico v1, prima è necessario eseguire l'aggiornamento all'utilizzo generico v2 usando questa [guida](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
3. È necessario avere attivato l'opzione **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione**  nel menu delle impostazioni **Firewall e reti virtuali** di tale account. Per altre informazioni, fare riferimento a [questa guida](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions).
#### <a name="steps"></a>Passaggi

1. In PowerShell **registrare il server SQL** con Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```

2. Creare **un account di archiviazione per utilizzo generico v2** usando questa [guida](../../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

   > [!NOTE]
   > Se si dispone di un account di archiviazione BLOB o per utilizzo generico v1, è necessario **prima eseguire l'aggiornamento a v2** usando questa [guida](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

3. Quando si è posizionati nell'account di archiviazione, passare a **Controllo di accesso (IAM)** e selezionare **Aggiungi un'assegnazione di ruolo**. Assegnare il ruolo **Proprietario, collaboratore o lettore dei dati dei BLOB di archiviazione** per il controllo degli accessi in base al ruolo al server SQL.

   > [!NOTE]
   > Solo i membri con il privilegio di proprietario possono eseguire questo passaggio. Per i vari ruoli predefiniti per le risorse di Azure, fare riferimento a questa [guida](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
   
4. È ora possibile eseguire l'istruzione COPY specificando "Identità gestita":

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV',
        CREDENTIAL = (IDENTITY = 'Managed Identity'),
    )
    ```

> [!IMPORTANT]
>
> - Specificare il ruolo Proprietario, collaboratore o lettore dei **dati dei BLOB** **di archiviazione** per il controllo degli accessi in base al ruolo. Questi ruoli sono diversi da quelli predefiniti di Azure di proprietario, collaboratore e lettore. 

## <a name="d-azure-active-directory-authentication-aad"></a>D. Autenticazione di Azure Active Directory (AAD)
#### <a name="steps"></a>Passaggi

1. Quando si è posizionati nell'account di archiviazione, passare a **Controllo di accesso (IAM)** e selezionare **Aggiungi un'assegnazione di ruolo**. Assegnare il ruolo **Proprietario, collaboratore o lettore dei dati dei BLOB di archiviazione** per il controllo degli accessi in base al ruolo all'utente AAD. 

2. Configurare l'autenticazione di Azure AD tramite la [documentazione](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#create-an-azure-ad-administrator-for-azure-sql-server) seguente. 

3. Connettersi al pool SQL tramite Active Directory in cui è ora possibile eseguire l'istruzione COPY senza specificare alcuna credenziale:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
    )
    ```

> [!IMPORTANT]
>
> - Specificare il ruolo Proprietario, collaboratore o lettore dei **dati dei BLOB** **di archiviazione** per il controllo degli accessi in base al ruolo. Questi ruoli sono diversi da quelli predefiniti di Azure di proprietario, collaboratore e lettore. 

## <a name="e-service-principal-authentication"></a>E. Autenticazione di un'entità servizio
#### <a name="steps"></a>Passaggi

1. [Creare un'applicazione Azure Active Directory (AAD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)
2. [Ottenere l'ID applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)
3. [Ottenere la chiave di autenticazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-a-new-application-secret)
4. [Ottenere l'endpoint di token OAuth 2.0 V1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. [Assegnare le autorizzazioni di lettura, scrittura ed esecuzione all'applicazione AAD](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder) nell'account di archiviazione
6. È ora possibile eseguire l'istruzione COPY:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder0/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
        ,CREDENTIAL=(IDENTITY= '<application_ID>@<OAuth_2.0_Token_EndPoint>' , SECRET= '<authentication_key>')
        --CREDENTIAL should look something like this:
        --,CREDENTIAL=(IDENTITY= '92761aac-12a9-4ec3-89b8-7149aef4c35b@https://login.microsoftonline.com/72f714bf-86f1-41af-91ab-2d7cd011db47/oauth2/token', SECRET='juXi12sZ6gse]woKQNgqwSywYv]7A.M')
    )
    ```

> [!IMPORTANT]
>
> - Usare la versione **V1** dell'endpoint di token OAuth 2.0

## <a name="next-steps"></a>Passaggi successivi

- Per la sintassi dettagliata, vedere l'[articolo sull'istruzione COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- Per le procedure consigliate per il caricamento, vedere l'articolo [Panoramica sul caricamento dei dati](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading#what-is-elt)
