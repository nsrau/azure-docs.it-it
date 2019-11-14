---
title: Sicurezza a livello di colonna
description: La sicurezza a livello di colonna (CLS) consente ai clienti di controllare l'accesso alle colonne della tabella del database in base al contesto di esecuzione o dell'appartenenza ai gruppi dell'utente. CLS semplifica la progettazione e la codifica della sicurezza nell'applicazione. CLS consente di implementare le limitazioni per l'accesso alle colonne.
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
ms.openlocfilehash: 34172e313b537d59287a424683ea1555cbba9b99
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049149"
---
# <a name="column-level-security"></a>Sicurezza a livello di colonna
La sicurezza a livello di colonna (CLS) consente ai clienti di controllare l'accesso alle colonne della tabella del database in base al contesto di esecuzione o dell'appartenenza ai gruppi dell'utente.
Aggiornamento al video riportato di seguito: poiché il video è stato pubblicato, la [sicurezza a livello di riga](https://docs.microsoft.com/en-us/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) è disponibile anche in SQL data warehouse. 
> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

CLS semplifica la progettazione e la codifica della sicurezza nell'applicazione. CLS consente di implementare le limitazioni per l'accesso alle colonne per proteggere dati sensibili. Assicurando, ad esempio, che utenti specifici possano accedere solo determinate colonne di una tabella relative al loro reparto. La logica di restrizione dell'accesso si trova nel livello database e non lontano dai dati in un altro livello applicazione. Il database applica le restrizioni di accesso a ogni tentativo di accesso ai dati da qualsiasi livello. Questa restrizione rende il sistema di sicurezza più affidabile e solido, grazie alla riduzione della superficie di attacco del sistema di sicurezza generale. CLS elimina anche l'esigenza di introduzione di visualizzazioni per filtrare le colonne per imporre limitazioni di accesso agli utenti.

È possibile implementare CLS con l'istruzione T-SQL [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql). Con questo meccanismo sono supportate sia l'autenticazione SQL che quella di Azure Active Directory (AAD).

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
L'esempio seguente illustra come escludere 'TestUser' dall'accesso alla colonna 'SSN' della tabella 'Membership':

Creare la tabella 'Membership' con la colonna SSN usata per archiviare i numeri di previdenza sociale:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Consentire a 'TestUser' di accedere a tutte le colonne a eccezione di SSN contenente dati sensibili:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Le query eseguite come 'TestUser' avranno esito negativo se includeranno la colonna SSN:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Casi di utilizzo
Alcuni esempi di come viene usato CLS:
- Una società di servizi finanziari consente solo agli account manager di accedere ai numeri di previdenza sociale (SSN), ai numeri di telefono e ad altre informazioni personali (PII).
- Un fornitore di assistenza sanitaria consente solo a medici e infermieri di avere accesso a cartelle cliniche sensibili mentre non consente ai membri del reparto contabilità di visualizzare questi dati.
