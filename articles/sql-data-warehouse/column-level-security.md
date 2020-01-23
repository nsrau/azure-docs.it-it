---
title: Informazioni sulla sicurezza a livello di colonna per SQL Data Warehouse
description: La sicurezza a livello di colonna consente ai clienti di controllare l'accesso alle colonne della tabella di database in base al contesto di esecuzione dell'utente o all'appartenenza al gruppo, semplificando la progettazione e la codifica della sicurezza nell'applicazione e consentendo di implementare restrizioni per la colonna accesso.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
ms.openlocfilehash: 344701989a753e17d8a026f6bb771a6030bdb71f
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513049"
---
# <a name="column-level-security"></a>Sicurezza a livello di colonna

La sicurezza a livello di colonna consente ai clienti di controllare l'accesso alle colonne della tabella in base al contesto di esecuzione dell'utente o all'appartenenza al gruppo.


> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Poiché il video è stato pubblicato, la [sicurezza a livello di riga](/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) è diventata disponibile per SQL data warehouse. 

La sicurezza a livello di colonna semplifica la progettazione e la codifica della sicurezza nell'applicazione, consentendo di limitare l'accesso alle colonne per proteggere i dati sensibili. Assicurando, ad esempio, che utenti specifici possano accedere solo determinate colonne di una tabella relative al loro reparto. La logica di restrizione dell'accesso si trova sul livello del database e non su un altro livello applicazione lontano dai dati. Il database applica le restrizioni di accesso ogni volta che si tenta di accedere ai dati da qualsiasi livello. Questa restrizione rende la sicurezza più affidabile e affidabile riducendo la superficie di attacco del sistema di sicurezza globale. Inoltre, la sicurezza a livello di colonna Elimina la necessità di introdurre viste per filtrare le colonne per l'imposizione delle restrizioni di accesso agli utenti.

È possibile implementare la sicurezza a livello di colonna con l'istruzione T-SQL [Grant](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) . Con questo meccanismo sono supportate sia l'autenticazione SQL che quella di Azure Active Directory (AAD).

![cls](./media/column-level-security/cls.png)

## <a name="syntax"></a>Sintassi

```sql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>Esempio
Nell'esempio seguente viene illustrato come limitare l'accesso `TestUser` alla colonna `SSN` della tabella `Membership`:

Creare `Membership` tabella con la colonna SSN usata per archiviare i numeri di previdenza sociale:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Consente `TestUser` di accedere a tutte le colonne ad eccezione della colonna SSN, che include i dati sensibili:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Le query eseguite come `TestUser` avranno esito negativo se includono la colonna SSN:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Casi d'uso

Di seguito sono riportati alcuni esempi di come viene utilizzata la sicurezza a livello di colonna:

- Una società di servizi finanziari consente solo agli account manager di accedere ai numeri di previdenza sociale (SSN), ai numeri di telefono e ad altre informazioni personali (PII).
- Un Health care provider consente solo a medici e infermieri di accedere a record medici sensibili, evitando al tempo stesso ai membri del reparto di fatturazione di visualizzare questi dati.
