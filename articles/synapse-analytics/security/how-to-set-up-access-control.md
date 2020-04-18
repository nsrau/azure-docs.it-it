---
title: Proteggi la tua area di lavoro Synapse (anteprima)
description: Questo articolo ti insegnerà come usare i ruoli e il controllo degli accessi per controllare le attività e l'accesso ai dati nell'area di lavoro Synapse.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: ae8be848b5d12e01865fe6bd3b394b460252aa3e
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606015"
---
# <a name="secure-your-synapse-workspace-preview"></a>Proteggi la tua area di lavoro Synapse (anteprima)

Questo articolo ti insegnerà come usare i ruoli e il controllo degli accessi per controllare le attività e l'accesso ai dati. Seguendo queste istruzioni, il controllo degli accessi in Azure Synapse Analytics è semplificato. È sufficiente aggiungere e rimuovere utenti in uno dei tre gruppi di sicurezza.

## <a name="overview"></a>Panoramica

Per proteggere un'area di lavoro Synapse (anteprima), seguire un modello di configurazione degli elementi seguenti:To secure a Synapse workspace (preview), you'll follow a pattern of configuring the following items:

- Ruoli di Azure (ad esempio quelli predefiniti come Proprietario, Collaboratore e così via)Azure roles (such as the built-in one like Owner, Contributor, etc.)
- Ruoli Synapse: questi ruoli sono univoci per Synapse e non sono basati sui ruoli di Azure.Synapse roles – these roles are unique to Synapse and aren't based on Azure roles. Esistono tre di questi ruoli:
  - Amministratore dell'area di lavoro Synapse
  - Amministratore SQL Synapse
  - Amministratore Synapse Spark
- Controllo di accesso per i dati in Azure Data Lake Storage Gen 2 (ADLSGEN2).
- Controllo degli accessi per i database Synapse SQL e SparkAccess control for Synapse SQL and Spark databases

## <a name="steps-to-secure-a-synapse-workspace"></a>Passaggi per proteggere un'area di lavoro Synapse

Questo documento utilizza nomi standard per semplificare le istruzioni. Sostituirli con qualsiasi nome di propria scelta.

|Impostazione | Valore di esempio | Descrizione |
| :------ | :-------------- | :---------- |
| **Area di lavoro Synapse** | WS1 |  Nome dell'area di lavoro Synapse. |
| **Account ADLSGEN2** | STG1 | Account ADLS da utilizzare con l'area di lavoro. |
| **Contenitore** | CNT1 | Contenitore in STG1 che verrà utilizzato dall'area di lavoro per impostazione predefinita. |
| **Tenant di Active Directory** | contoso | il nome del tenant di Active Directory.|
||||

## <a name="step-1-set-up-security-groups"></a>PASSAGGIo 1: Impostare i gruppi di sicurezzaSTEP 1: Set up security groups

Creare e popolare tre gruppi di sicurezza per l'area di lavoro:Create and populate three security groups for your workspace:

- **WS1\_WSAdmins** – per gli utenti che necessitano di un controllo completo sull'area di lavoro
- **WS1\_SparkAdmins** : per gli utenti che necessitano di un controllo completo sugli aspetti Spark dell'area di lavoro
- **WS1\_SQLAdmins** – per gli utenti che necessitano di un controllo completo sugli aspetti SQL dell'area di lavoro
- Aggiungere **WS1\_WSAdmins** a **WS1\_SQLAdmins**
- Aggiungere **\_WS1 WSAdmins** a **WS1\_SparkAdmins**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>PASSAGGIo 2: Preparare l'account Data Lake Storage Gen2

Identificare queste informazioni sullo spazio di archiviazione:Identify this information about your storage:

- Account ADLSGEN2 da utilizzare per l'area di lavoro. Questo documento lo chiama STG1.  STG1 è considerato l'account di archiviazione "primario" per l'area di lavoro.
- Contenitore all'interno di WS1 che verrà utilizzato per impostazione predefinita nell'area di lavoro Synapse. Questo documento lo chiama CNT1.  Questo contenitore viene utilizzato per:This container is used for:
  - Archiviazione dei file di dati di backup per le tabelle Spark
  - Registri di esecuzione per i processi SparkExecution logs for Spark jobs

- Usando il portale di Azure, assegnare ai gruppi di sicurezza i ruoli seguenti in CNT1Using the Azure portal, assign the security groups the following roles on CNT1

  - Assegnare WS1 WSAdmins al ruolo **Collaboratore dati BLOB di archiviazioneAssign** **\_WS1 WSAdmins** to the Storage Blob Data Contributor role
  - Assegnare WS1 SparkAdmins al ruolo **Collaboratore dati BLOB di archiviazioneAssign** **\_WS1 SparkAdmins** to the Storage Blob Data Contributor role
  - Assegnare WS1 SQLAdmins al ruolo **Collaboratore dati BLOB di archiviazioneAssign** **\_WS1 SQLAdmins** to the Storage Blob Data Contributor role

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>FASE 3: Creare e configurare l'area di lavoro Synapse

Nel portale di Azure creare un'area di lavoro Synapse:In the Azure portal, create a Synapse workspace:

- Assegnare un nome all'area di lavoro WS1
- Scegliere STG1 per l'account di archiviazione
- Scegliere CNT1 per il contenitore utilizzato come "file system".
- Aprire WS1 in Synapse Studio
- Selezionare **Gestisci** > controllo di**accesso** assegnare i gruppi di sicurezza ai ruoli Synapse seguenti.
  - Assegnare **WS1 WSAdmins\_** agli amministratori dell'area di lavoro Synapse
  - Assegnare **WS1\_SparkAdmins** agli amministratori di Synapse Spark
  - Assegnare SQLAdmins WS1 agli amministratori SQL di SynapseAssign **\_WS1 SQLAdmins** to Synapse SQL admins

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>FASE 4: Configurazione di Data Lake Storage Gen2 per l'utilizzo da parte dell'area di lavoro Synapse

L'area di lavoro Synapse deve accedere a STG1 e CNT1 in modo che possa eseguire pipeline ed eseguire attività di sistema.

- Aprire il portale di Azure
- Individuare STG1
- Passare a CNT1
- Verificare che il file MSI (Managed Service Identity) per WS1 sia assegnato al ruolo **Collaboratore dati BLOB** di Azure in CNT1
  - Se non vedi che è stato assegnato, assegnalo.
  - Il file MSI ha lo stesso nome dell'area di lavoro. In questo caso, &quot;sarebbe&quot;WS1 .

## <a name="step-5-configure-admin-access-for-sql-pools"></a>PASSAGGIo 5: Configurare l'accesso di amministratore per i pool SQLSTEP 5: Configure admin access for SQL pools

- Aprire il portale di Azure
- Passare a WS1
- In **Impostazioni**fare clic su **Amministratore di Active Directory SQL**
- Fare clic su Imposta\_ **amministratore** e scegliere WS1 SQLAdmins

## <a name="step-6-maintaining-access-control"></a>Passaggio 6: Mantenimento del controllo di accessoStep 6: Maintaining access control

La configurazione è terminata.

A questo punto, per gestire l'accesso per gli utenti, è possibile aggiungere e rimuovere utenti ai tre gruppi di sicurezza.

Sebbene sia possibile assegnare manualmente gli utenti ai ruoli Synapse, in tal caso non verranno configurati in modo coerente. Al contrario, aggiungere o rimuovere utenti ai gruppi di sicurezza.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>PASSAGGIo 7: Verificare l'accesso per gli utenti nei ruoli

Gli utenti in ogni ruolo devono completare i passaggi seguenti:Users in each role need to complete the following steps:

|   | Passaggio | Amministratori dell'area di lavoro | Amministratori Spark | Amministratori SQL |
| --- | --- | --- | --- | --- |
| 1 | Caricare un file di parquet in CNT1 | YES | YES | YES |
| 2 | Leggere il file di parquet utilizzando SQL su richiesta | YES | NO | YES |
| 3 | Creare un pool Spark | SI [1] | SI [1] | NO  |
| 4 | Legge il file di parquet con un notebook | YES | YES | NO |
| 5 | Creare una pipeline dal blocco appunti e attivare la pipeline per l'esecuzione oraCreate a pipeline from the Notebook and Trigger the pipeline to run now | YES | NO | NO |
| 6 | Creare un pool SQL ed eseguire &quot;uno script SQL, ad esempio SELECT 1Create a SQL Pool and run a SQL script such as SELECT 1&quot; | SI [1] | NO | SI[1] |

> [!NOTE]
> [1] Per creare pool SQL o Spark, l'utente deve disporre almeno del ruolo Collaboratore nell'area di lavoro Synapse.
> [!TIP]
>
> - Alcuni passaggi non saranno deliberatamente consentiti a seconda del ruolo.
> - Tenere presente che alcune attività potrebbero non riuscire se la sicurezza non è stata completamente configurata. Queste attività sono annotate nella tabella.

## <a name="step-8-network-security"></a>FASE 8: Sicurezza di rete

Per configurare il firewall dell'area di lavoro, la rete virtuale e [il collegamento privato](../../sql-database/sql-database-private-endpoint-overview.md).

## <a name="step-9-completion"></a>FASE 9: Completamento

L'area di lavoro è completamente configurata e protetta.

## <a name="how-roles-interact-with-synapse-studio"></a>Come i ruoli interagiscono con Synapse Studio

Synapse Studio si comporterà in modo diverso in base ai ruoli utente. Alcuni elementi potrebbero essere nascosti o disabilitati se un utente non è assegnato a ruoli che forniscono l'accesso appropriato. Nella tabella seguente viene riepilogato l'effetto su Synapse Studio.

| Attività | Amministratori dell'area di lavoro | Amministratori Spark | Amministratori SQL |
| --- | --- | --- | --- |
| Open Synapse Studio | YES | YES | YES |
| Visualizza hub home | YES | YES | YES |
| Visualizza hub dati | YES | YES | YES |
| Hub dati / Vedere account e contenitori ADLSGen2 collegati | SI [1] | SI[1] | SI[1] |
| Hub dati / Vedere database | YES | YES | YES |
| Hub dati / Vedere oggetti nei database | YES | YES | YES |
| Hub dati / Dati di Access nei database del pool SQL | YES   | NO   | YES   |
| Hub dati / Dati di Access nei database SQL su richiesta | SI [2]  | NO  | SI [2]  |
| Hub dati / Dati di Access nei database Spark | SI [2] | SI [2] | SI [2] |
| Usare l'hub Sviluppo | YES | YES | YES |
| Sviluppa script SQL hub/autore | YES | NO | YES |
| Sviluppare le definizioni dei processi Spark Hub/autore | YES | YES | NO |
| Sviluppare blocchi appunti Hub/authorDevelop Hub / author Notebooks | YES | YES | NO |
| Sviluppa Hub / autore Dataflows | YES | NO | NO |
| Usare l'hub Orchestrate | YES | YES | YES |
| Orchestrate hub / utilizzare Pipelines | YES | NO | NO |
| Utilizzare l'hub di gestione | YES | YES | YES |
| Gestire i pool Hub/SQLManage Hub / SQL pools | YES | NO | YES |
| Gestire i pool Hub/Spark | YES | YES | NO |
| Gestisci hub / Trigger | YES | NO | NO |
| Gestire hub/servizi collegati | YES | YES | YES |
| Gestisci hub / Controllo di accesso (assegnare gli utenti ai ruoli dell'area di lavoro Synapse) | YES | NO | NO |
| Gestire i runtime Hub/IntegrationManage Hub / Integration runtimes | YES | YES | YES |

> [!NOTE]
> [1] L'accesso ai dati nei contenitori dipende dal controllo di accesso in ADLSGen2 [2] Le tabelle Sql OD e le tabelle Spark archiviano i dati in ADLSGen2 e l'accesso richiede le autorizzazioni appropriate su ADLSGen2.

## <a name="next-steps"></a>Passaggi successivi

Creazione di [un'area di lavoro silnella](../quickstart-create-workspace.md)
