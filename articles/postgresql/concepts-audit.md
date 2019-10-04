---
title: Controllare la registrazione con pgAudit nel database di Azure per PostgreSQL-server singolo
description: 'Concetti per la registrazione di controllo di pgAudit nel database di Azure per PostgreSQL: singolo server.'
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 198ab5f567652a76d209168041f305b9da4d0b43
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147178"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Registrazione di controllo nel database di Azure per PostgreSQL-server singolo

Registrazione di controllo delle attività del database nel database di Azure per PostgreSQL: il server singolo è disponibile tramite l'estensione di controllo PostgreSQL: [pgAudit](https://www.pgaudit.org/). pgAudit fornisce la registrazione dettagliata della sessione e/o del controllo degli oggetti.

> [!NOTE]
> pgAudit è in anteprima nel database di Azure per PostgreSQL.
> L'estensione può essere abilitata solo nei server per utilizzo generico e con ottimizzazione per la memoria.

## <a name="usage-considerations"></a>Considerazioni sull'utilizzo
Per impostazione predefinita, le istruzioni di log di pgAudit vengono emesse insieme alle istruzioni log regolari usando la funzionalità di registrazione standard di postgres. In database di Azure per PostgreSQL è possibile scaricare questi file con estensione log tramite il portale di Azure o l'interfaccia della riga di comando. Lo spazio di archiviazione massimo per la raccolta di file è 1 GB e ogni file è disponibile per un massimo di sette giorni (il valore predefinito è tre giorni). Questo servizio è un'opzione di archiviazione a breve termine.

In alternativa, è possibile configurare tutti i log da emettere al servizio di log di diagnostica di monitoraggio di Azure. Se si Abilita la registrazione diagnostica di monitoraggio di Azure, i log verranno inviati automaticamente (in formato JSON) ad archiviazione di Azure, Hub eventi e/o log di monitoraggio di Azure, a seconda della scelta effettuata.

L'abilitazione di pgAudit genera un volume elevato di registrazione in un server, che influisca sulle prestazioni e sull'archiviazione dei log. Si consiglia di usare il servizio log di diagnostica di Azure, che offre opzioni di archiviazione a lungo termine, nonché le funzionalità di analisi e avviso. Si consiglia di disattivare la registrazione standard per ridurre l'effetto sulle prestazioni della registrazione aggiuntiva:

   1. Impostare il parametro `logging_collector` su disattivato. 
   2. Riavviare il server per applicare questa modifica.

Per informazioni su come configurare la registrazione in archiviazione di Azure, Hub eventi o log di monitoraggio di Azure, vedere la sezione log di diagnostica dell' [articolo log del server](concepts-server-logs.md).

## <a name="installing-pgaudit"></a>Installazione di pgAudit

Per installare pgAudit, è necessario includerlo nelle librerie di precaricamento condivise del server. Per rendere effettiva una modifica al `shared_preload_libraries` parametro Postgres è necessario riavviare il server. È possibile modificare i parametri usando il [portale di Azure](howto-configure-server-parameters-using-portal.md), l'interfaccia della riga di comando di [Azure](howto-configure-server-parameters-using-cli.md)o l' [API REST](/rest/api/postgresql/configurations/createorupdate).

Utilizzando la [portale di Azure](https://portal.azure.com):

   1. Selezionare il server di Database di Azure per PostgreSQL.
   2. Nella barra laterale selezionare **parametri server**.
   3. Cercare il `shared_preload_libraries` parametro.
   4. Selezionare **pgaudit**.
   5. Riavviare il server per applicare la modifica.

   6. Connettersi al server usando un client, ad esempio PSQL, e abilitare l'estensione pgAudit
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Se viene visualizzato un errore, confermare che il server è stato riavviato dopo `shared_preload_libraries`il salvataggio.

## <a name="pgaudit-settings"></a>impostazioni pgAudit

pgAudit consente di configurare la registrazione di controllo di una sessione o di un oggetto. La [registrazione di controllo della sessione](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) emette log dettagliati delle istruzioni eseguite. La [registrazione di controllo degli oggetti](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) è un ambito di controllo per relazioni specifiche. È possibile scegliere di configurare uno o entrambi i tipi di registrazione. 

> [!NOTE]
> le impostazioni pgAudit vengono specificate globale del e non possono essere specificate a livello di database o di ruolo.

Dopo aver [installato pgAudit](#installing-pgaudit), è possibile configurarne i parametri per avviare la registrazione. La [documentazione di pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) fornisce la definizione di ogni parametro. Testare prima i parametri e confermare che si sta ottenendo il comportamento previsto.

> [!NOTE]
> Se `pgaudit.log_client` si imposta su on, i log vengono reindirizzati a un processo client, ad esempio PSQL, anziché essere scritti nel file. Questa impostazione viene in genere disabilitata.

> [!NOTE]
> `pgaudit.log_level`viene abilitato solo quando `pgaudit.log_client` è on. Inoltre, nel portale di Azure, esiste attualmente un bug con `pgaudit.log_level`: viene visualizzata una casella combinata, che implica che è possibile selezionare più livelli. Tuttavia, deve essere selezionato un solo livello. 

> [!NOTE]
> In database di Azure per PostgreSQL `pgaudit.log` non è possibile impostare l' `-` uso di un collegamento di segno (meno), come descritto nella documentazione di pgAudit. Tutte le classi di istruzioni obbligatorie (lettura, scrittura e così via) devono essere specificate singolarmente.

### <a name="audit-log-format"></a>Formato del log di controllo
Ogni voce di controllo è indicata `AUDIT:` da vicino all'inizio della riga del log. Il formato del resto della voce è descritto in dettaglio nella [documentazione di pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Se sono necessari altri campi per soddisfare i requisiti di controllo, usare il parametro `log_line_prefix`postgres. `log_line_prefix`è una stringa che viene restituita all'inizio di ogni riga del log di postgres. Ad esempio, l'impostazione `log_line_prefix` seguente fornisce il timestamp, il nome utente, il nome del database e l'ID del processo:

```
t=%m u=%u db=%d pid=[%p]:
```

Per altre informazioni su `log_line_prefix`, vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Introduzione
Per iniziare rapidamente, impostare `pgaudit.log` su `WRITE`e aprire i log per esaminare l'output. 


## <a name="next-steps"></a>Passaggi successivi
- [Informazioni sulla registrazione in database di Azure per PostgreSQL](concepts-server-logs.md)
- Informazioni su come impostare i parametri usando il [portale di Azure](howto-configure-server-parameters-using-portal.md), l'interfaccia della riga di comando di [Azure](howto-configure-server-parameters-using-cli.md)o l' [API REST](/rest/api/postgresql/configurations/createorupdate).
