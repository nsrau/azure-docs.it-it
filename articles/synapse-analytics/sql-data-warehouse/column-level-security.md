---
title: Che cos'è la sicurezza a livello di colonna per Azure Synapse?
description: La sicurezza a livello di colonna consente ai clienti di controllare l'accesso alle colonne della tabella di database in base al contesto di esecuzione dell'utente o all'appartenenza al gruppo, semplificando la progettazione e la codifica della sicurezza nell'applicazione e consentendo di implementare restrizioni sull'accesso alle colonne.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 61a3e2eadaf79cdb30a931b31cff709298d0a22c
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631305"
---
# <a name="column-level-security"></a>Sicurezza a livello di colonna

La sicurezza a livello di colonna consente ai clienti di controllare l'accesso alle colonne della tabella in base al contesto di esecuzione dell'utente o all'appartenenza al gruppo.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Dal momento che questo video è stato pubblicato a livello di [riga Security](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) è diventato disponibile per Azure Synapse.

La sicurezza a livello di colonna semplifica la progettazione e la codifica della sicurezza nell'applicazione, consentendo di limitare l'accesso alle colonne per proteggere i dati sensibili. Assicurando, ad esempio, che utenti specifici possano accedere solo determinate colonne di una tabella relative al loro reparto. La logica di restrizione dell'accesso si trova sul livello del database e non su un altro livello applicazione lontano dai dati. Il database applica le restrizioni di accesso ogni volta che si tenta di accedere ai dati da qualsiasi livello. Questa restrizione rende la sicurezza più affidabile e affidabile riducendo l'area di superficie del sistema di sicurezza globale. Inoltre, la sicurezza a livello di colonna elimina anche la necessità di introdurre visualizzazioni per filtrare le colonne per imporre restrizioni di accesso agli utenti.

È possibile implementare la sicurezza a livello di colonna con l'istruzione [GRANT](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL. Con questo meccanismo sono supportate sia l'autenticazione SQL che quella di Azure Active Directory (AAD).

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

Nell'esempio seguente viene `TestUser` illustrato come `SSN` limitare `Membership` l'accesso alla colonna della tabella:

Creare `Membership` una tabella con la colonna SSN utilizzata per archiviare i numeri di previdenza sociale:Create table with SSN column used to store social security numbers:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Consentire `TestUser` l'accesso a tutte le colonne ad eccezione della colonna SSN, che contiene i dati sensibili:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Le query `TestUser` eseguite come avranno esito negativo se includono la colonna SSN:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Modalità di utilizzo comuni

Alcuni esempi di come viene utilizzata oggi la sicurezza a livello di colonna:Some examples of how column-level security is being used today:

- Una società di servizi finanziari consente solo agli account manager di accedere ai numeri di previdenza sociale (SSN), ai numeri di telefono e ad altre informazioni personali (PII).
- Un fornitore di assistenza sanitaria consente solo amedici e infermieri di avere accesso a cartelle cliniche sensibili, impedendo ai membri del reparto di fatturazione di visualizzare questi dati.
