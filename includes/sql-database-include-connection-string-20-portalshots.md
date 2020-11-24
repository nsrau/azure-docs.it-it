---
title: Ottenere la stringa di connessione dal portale di Azure
description: Ottenere la stringa di connessione dal portale di Azure
keywords: connessione SQL, stringa di connessione
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: b06004143d4a1ec5a2808e21b1f78833e410c195
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557116"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Ottenere la stringa di connessione dal portale di Azure
Usare il [portale di Azure](https://portal.azure.com/) per ottenere la stringa di connessione necessaria al programma client per interagire con il database SQL di Azure.

1. Selezionare **tutti i servizi**  >  **database SQL**.

2. Immettere il nome del database nella casella di testo di filtro vicino alla parte superiore sinistra del pannello **Database SQL**.

3. Selezionare la riga per il database.

4. Quando viene visualizzato il pannello del database, per una visualizzazione più chiara selezionare i pulsanti **Riduci a icona** per comprimere i pannelli usati per la ricerca e il filtro del database.

5. Nel pannello del database selezionare **Mostra stringhe di connessione del database**.

6. Copiare la stringa di connessione appropriata. Se ad esempio si prevede di usare la libreria di connessione ADO.NET, copiare la stringa appropriata dalla scheda **ADO.NET**.

    ![Copiare la stringa di connessione ADO per il database][20-CopyAdoConnectionString]

7. Modificare la stringa di connessione in base alle esigenze. Inserire la password nella stringa di connessione o rimuovere "@&lt;servername&gt;" dal nome utente se il nome utente o il nome server è troppo lungo.

8. In un formato o un altro, incollare le informazioni sulla stringa di connessione nel codice del programma client.

Per altre informazioni, vedere [stringhe di connessione e file di configurazione](/dotnet/framework/data/adonet/connection-strings-and-configuration-files).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->