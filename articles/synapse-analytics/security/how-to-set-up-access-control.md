---
title: Proteggere l'area di lavoro sinapsi (anteprima)
description: Questo articolo illustra come usare i ruoli e il controllo di accesso per controllare le attività e l'accesso ai dati nell'area di lavoro sinapsi.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 7ce011a34aed39429884dc03285a0848776ac008
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006077"
---
# <a name="secure-your-synapse-workspace-preview"></a>Proteggere l'area di lavoro sinapsi (anteprima)

In questo articolo viene illustrato come utilizzare i ruoli e il controllo di accesso per controllare le attività e l'accesso ai dati. Seguendo queste istruzioni, il controllo di accesso in Azure sinapsi Analytics è semplificato. È sufficiente aggiungere e rimuovere utenti a uno dei tre gruppi di sicurezza.

## <a name="overview"></a>Panoramica

Per proteggere un'area di lavoro sinapsi (anteprima), si seguirà un modello di configurazione degli elementi seguenti:

- I ruoli di Azure, ad esempio quelli predefiniti come proprietario, collaboratore e così via.
- Ruoli sinapsi: questi ruoli sono univoci per le sinapsi e non sono basati sui ruoli di Azure. Sono disponibili tre ruoli:
  - Amministratore dell'area di lavoro sinapsi
  - Amministratore SQL sinapsi
  - Amministrazione di sinapsi Spark
- Controllo di accesso per i dati in Azure Data Lake Storage generazione 2 (ADLSGEN2).
- Controllo di accesso per i database sinapsi SQL e Spark

## <a name="steps-to-secure-a-synapse-workspace"></a>Passaggi per la protezione di un'area di lavoro sinapsi

In questo documento vengono utilizzati nomi standard per semplificare le istruzioni. Sostituirli con i nomi scelti.

|Impostazione | Valore di esempio | Descrizione |
| :------ | :-------------- | :---------- |
| **Area di lavoro sinapsi** | WS1 |  Nome che avrà l'area di lavoro sinapsi. |
| **Account ADLSGEN2** | STG1 | Account ADLS da usare con l'area di lavoro. |
| **Contenitore** | CNT1 | Il contenitore in STG1 che l'area di lavoro utilizzerà per impostazione predefinita. |
| **Tenant di Active Directory** | contoso | nome del tenant di Active Directory.|
||||

## <a name="step-1-set-up-security-groups"></a>PASSAGGIO 1: configurare i gruppi di sicurezza

Creare e popolare tre gruppi di sicurezza per l'area di lavoro:

- **WS1\_WSAdmins** : per gli utenti che necessitano del controllo completo sull'area di lavoro
- **WS1\_SparkAdmins** : per gli utenti che devono avere il controllo completo sugli aspetti di Spark dell'area di lavoro
- **WS1\_sqladmins** : per gli utenti che necessitano del controllo completo sugli aspetti SQL dell'area di lavoro
- Aggiungere **WS1\_WSAdmins** a **WS1\_sqladmins**
- Aggiungere **WS1\_WSAdmins** a **WS1\_SparkAdmins**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>PASSAGGIO 2: preparare l'account Data Lake Storage Gen2

Identificare le informazioni relative all'archiviazione:

- Account ADLSGEN2 da usare per l'area di lavoro. In questo documento viene chiamato STG1.  STG1 è considerato l'account di archiviazione "primario" per l'area di lavoro.
- Il contenitore all'interno di WS1 che l'area di lavoro sinapsi utilizzerà per impostazione predefinita. In questo documento viene chiamato CNT1.  Questo contenitore viene usato per:
  - Archiviazione dei file di dati di backup per le tabelle Spark
  - Log di esecuzione per i processi Spark

- Usando il portale di Azure, assegnare i gruppi di sicurezza i ruoli seguenti in CNT1

  - Assegnare **WS1\_WSAdmins** al ruolo di **collaboratore dati BLOB di archiviazione**
  - Assegnare **WS1\_SparkAdmins** al ruolo di **collaboratore dati BLOB di archiviazione**
  - Assegnare **Sqladmins WS1\_** al ruolo di **collaboratore dati BLOB di archiviazione**

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>PASSAGGIO 3: creare e configurare l'area di lavoro sinapsi

Nella portale di Azure creare un'area di lavoro sinapsi:

- Denominare l'area di lavoro WS1
- Scegliere STG1 per l'account di archiviazione
- Scegliere CNT1 per il contenitore usato come "filesystem".
- Aprire WS1 in sinapsi Studio
- Selezionare **Gestisci** > **controllo di accesso** assegnare i gruppi di sicurezza ai ruoli sinapsi seguenti.
  - Assegnare **WS1\_WSAdmins** agli amministratori dell'area di lavoro sinapsi
  - Assegnare **WS1\_SparkAdmins** a sinapsi Spark Admins
  - Assegnare **Sqladmins WS1\_** agli amministratori di sinapsi SQL

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>PASSAGGIO 4: configurazione di Data Lake Storage Gen2 per l'uso da area di lavoro sinapsi

L'area di lavoro sinapsi deve accedere a STG1 e CNT1 in modo da poter eseguire le pipeline ed eseguire attività di sistema.

- Aprire il portale di Azure
- Individuare STG1
- Passare a CNT1
- Assicurarsi che l'identità del servizio gestito (identità del servizio gestita) per WS1 sia assegnata al ruolo di **collaboratore dati BLOB di archiviazione** in CNT1
  - Se non viene visualizzato, assegnarlo.
  - Il nome dell'identità del servizio gestito è identico a quello dell'area di lavoro. In questo caso, sarebbe &quot;WS1.&quot;

## <a name="step-5-configure-admin-access-for-sql-pools"></a>PASSAGGIO 5: configurare l'accesso amministrativo per i pool SQL

- Aprire il portale di Azure
- Passare a WS1
- In **Impostazioni**fare clic su **amministratore di SQL Active Directory**
- Fare clic su **imposta amministratore** e\_scegliere WS1 sqladmins

## <a name="step-6-maintaining-access-control"></a>PASSAGGIO 6: gestione del controllo di accesso

La configurazione è stata completata.

Per gestire l'accesso per gli utenti, è ora possibile aggiungere e rimuovere utenti ai tre gruppi di sicurezza.

Sebbene sia possibile assegnare manualmente gli utenti ai ruoli sinapsi, in caso contrario, non verranno configurati in modo coerente. Al contrario, aggiungere o rimuovere utenti solo nei gruppi di sicurezza.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>PASSAGGIO 7: verificare l'accesso per gli utenti nei ruoli

Gli utenti di ogni ruolo devono completare i passaggi seguenti:

|   | Passaggio | Amministratori dell'area di lavoro | Amministratori Spark | Amministratori SQL |
| --- | --- | --- | --- | --- |
| 1 | Caricare un file parquet in CNT1 | YES | YES | YES |
| 2 | Leggi il file parquet usando SQL su richiesta | YES | NO | YES |
| 3 | Creare un pool Spark | SÌ [1] | SÌ [1] | NO  |
| 4 | Legge il file parquet con un notebook | YES | YES | NO |
| 5 | Creare una pipeline dal notebook e attivare la pipeline per l'esecuzione | YES | NO | NO |
| 6 | Creare un pool SQL ed eseguire uno script SQL, ad &quot;esempio SELECT 1&quot; | SÌ [1] | NO | SÌ [1] |

> [!NOTE]
> [1] per creare pool SQL o Spark, l'utente deve avere almeno un ruolo Collaboratore nell'area di lavoro sinapsi.
> [!TIP]
>
> - Alcuni passaggi deliberatamente non saranno consentiti a seconda del ruolo.
> - Tenere presente che alcune attività possono avere esito negativo se la sicurezza non è stata configurata completamente. Queste attività sono indicate nella tabella.

## <a name="step-8-network-security"></a>PASSAGGIO 8: sicurezza di rete

Per configurare il firewall dell'area di lavoro, la rete virtuale e il [collegamento privato](../../sql-database/sql-database-private-endpoint-overview.md).

## <a name="step-9-completion"></a>PASSAGGIO 9: completamento

L'area di lavoro è ora completamente configurata e protetta.

## <a name="how-roles-interact-with-synapse-studio"></a>Interazione dei ruoli con sinapsi Studio

Sinapsi studio si comporterà in modo diverso in base ai ruoli utente. Alcuni elementi possono essere nascosti o disabilitati se un utente non è assegnato ai ruoli che forniscono l'accesso appropriato. La tabella seguente riepiloga l'effetto su sinapsi Studio.

| Attività | Amministratori dell'area di lavoro | Amministratori Spark | Amministratori SQL |
| --- | --- | --- | --- |
| Apri sinapsi Studio | YES | YES | YES |
| Visualizza Hub Home page | YES | YES | YES |
| Visualizza hub dati | YES | YES | YES |
| Hub dati/vedere account ADLS Gen2 collegati e contenitori | SÌ [1] | SÌ [1] | SÌ [1] |
| Hub dati/vedere database | YES | YES | YES |
| Hub dati/Visualizza oggetti nei database | YES | YES | YES |
| Data Hub/dati di accesso nei database del pool SQL | YES   | NO   | YES   |
| Data Hub/dati di accesso nei database SQL su richiesta | SÌ [2]  | NO  | SÌ [2]  |
| Data Hub/dati di accesso nei database Spark | SÌ [2] | SÌ [2] | SÌ [2] |
| Usare l'hub di sviluppo | YES | YES | YES |
| Sviluppare script SQL per hub/autore | YES | NO | YES |
| Sviluppare definizioni di processi Spark per hub/autore | YES | YES | NO |
| Sviluppare notebook di hub/autore | YES | YES | NO |
| Sviluppare flussi di Data Hub/autore | YES | NO | NO |
| Usare l'hub orchestrazione | YES | YES | YES |
| Orchestrazione hub/USA pipeline | YES | NO | NO |
| Usare l'hub di gestione | YES | YES | YES |
| Gestire i pool Hub/SQL | YES | NO | YES |
| Gestisci pool Hub/Spark | YES | YES | NO |
| Gestire Hub/trigger | YES | NO | NO |
| Gestire Hub/servizi collegati | YES | YES | YES |
| Gestire il controllo dell'hub/accesso (assegnare utenti ai ruoli dell'area di lavoro sinapsi) | YES | NO | NO |
| Gestisci runtime di integrazione/Hub | YES | YES | YES |
| Usare l'hub di monitoraggio | YES | YES | YES |
| Monitorare le esecuzioni di hub/orchestrazione/pipeline  | YES | NO | NO |
| Monitorare le esecuzioni di hub/orchestrazione/trigger  | YES | NO | NO |
| Monitorare i runtime di hub/orchestrazione/integrazione  | YES | YES | YES |
| Monitorare le applicazioni Hub/Activities/Spark | YES | YES | NO  |
| Monitoraggio di hub/attività/richieste SQL | YES | NO | YES |
| Monitorare Hub/attività/pool Spark | YES | YES | NO  |
| Hub di monitoraggio/trigger | YES | NO | NO |
| Gestire Hub/servizi collegati | YES | YES | YES |
| Gestire il controllo dell'hub/accesso (assegnare utenti ai ruoli dell'area di lavoro sinapsi) | YES | NO | NO |
| Gestisci runtime di integrazione/Hub | YES | YES | YES |


> [!NOTE]
> [1] l'accesso ai dati nei contenitori dipende dal controllo di accesso in ADLS Gen2. </br>
> [2] le tabelle e le tabelle di Spark SQL e archiviano i dati in ADLS Gen2 e l'accesso richiede le autorizzazioni appropriate per ADLS Gen2.

## <a name="next-steps"></a>Passaggi successivi

Creare un' [area di lavoro sinapsi](../quickstart-create-workspace.md)
