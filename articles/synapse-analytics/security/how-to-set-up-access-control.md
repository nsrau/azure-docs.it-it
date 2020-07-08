---
title: Proteggere l'area di lavoro Synapse (anteprima)
description: L'articolo illustra come usare i ruoli e il controllo di accesso per controllare le attività e accedere ai dati nell'area di lavoro Synapse.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 1a8023a700e83892fae9a16bb2bfbe2a2eb0b307
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85193414"
---
# <a name="secure-your-synapse-workspace-preview"></a>Proteggere l'area di lavoro Synapse (anteprima)

L'articolo illustra come usare i ruoli e il controllo di accesso per controllare le attività e accedere ai dati. Attenendosi alle istruzioni seguenti, il controllo di accesso in Azure Synapse Analytics risulta semplificato. È sufficiente aggiungere e rimuovere gli utenti in uno dei tre gruppi di sicurezza.

## <a name="overview"></a>Panoramica

Per proteggere un'area di lavoro Synapse (anteprima) è necessario seguire uno schema per la configurazione degli elementi seguenti:

- Ruoli di Azure (ad esempio quelli predefiniti come Proprietario, Collaboratore ecc.)
- Ruoli di Synapse: questi ruoli sono unici di Synapse e non si basano sui ruoli di Azure. Sono disponibili tre ruoli di questo tipo:
  - Amministratore dell'area di lavoro Synapse
  - Amministratore Synapse SQL
  - Amministratore Apache Spark per Azure Synapse Analytics
- Controllo di accesso per i dati in Azure Data Lake Storage Gen 2 (ADLSGEN2).
- Controllo di accesso per i database di Synapse SQL e Spark

## <a name="steps-to-secure-a-synapse-workspace"></a>Passaggi per proteggere un'area di lavoro Synapse

Per semplificare le istruzioni, in questa documentazione vengono usati nomi standard. Sostituirli con nomi a scelta.

|Impostazione | Valore di esempio | Descrizione |
| :------ | :-------------- | :---------- |
| **Area di lavoro Synapse** | WS1 |  Il nome che verrà assegnato all'area di lavoro Synapse. |
| **Account ADLSGEN2** | STG1 | L'account ADLS da usare con l'area di lavoro. |
| **Contenitore** | CNT1 | Il contenitore in STG1 che verrà usato nell'area di lavoro per impostazione predefinita. |
| **Tenant di Active Directory** | contoso | il nome del tenant di Active Directory.|
||||

## <a name="step-1-set-up-security-groups"></a>PASSAGGIO 1: configurare i gruppi di sicurezza

Creare e popolare tre gruppi di sicurezza per l'area di lavoro:

- **WS1\_WSAdmins**: per gli utenti che necessitano del controllo completo sull'area di lavoro
- **WS1\_SparkAdmins**: per gli utenti che necessitano del controllo completo sugli aspetti Spark dell'area di lavoro
- **WS1\_SQLAdmins**: per gli utenti che necessitano del controllo completo sugli aspetti SQL dell'area di lavoro
- Aggiungere **WS1\_WSAdmins** a **WS1\_SQLAdmins**
- Aggiungere **WS1\_WSAdmins** a **WS1\_SparkAdmins**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>PASSAGGIO 2: preparare l'account Data Lake Storage Gen2

Identificare le informazioni seguenti relative all'archiviazione:

- L'account ADLSGEN2 da usare per l'area di lavoro. In questa documentazione è denominato STG1.  STG1 è considerato l'account di archiviazione "principale" per l'area di lavoro.
- Il contenitore in WS1 che verrà usato nell'area di lavoro Synapse per impostazione predefinita. In questa documentazione è denominato CNT1.  Il contenitore viene usato per:
  - Archiviare i file di dati di backup per le tabelle Spark
  - Eseguire i log per i processi Spark

- Usando il portale di Azure, assegnare i gruppi di sicurezza ai ruoli seguenti in CNT1

  - Assegnare **WS1\_WSAdmins** al ruolo **Collaboratore ai dati del BLOB di archiviazione**
  - Assegnare **WS1\_SparkAdmins** al ruolo **Collaboratore ai dati del BLOB di archiviazione**
  - Assegnare **WS1\_SQLAdmins** al ruolo **Collaboratore ai dati del BLOB di archiviazione**

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>PASSAGGIO 3: creare e configurare l'area di lavoro Synapse

Nel portale di Azure, creare un'area di lavoro Synapse:

- Assegnare all'area di lavoro il nome WS1
- Selezionare STG1 come account di archiviazione
- Selezionare CNT1 come contenitore in uso come "filesystem".
- Aprire WS1 in Synapse Studio
- Selezionare **Gestisci** > **Controllo di accesso** per assegnare i gruppi di sicurezza ai ruoli Synapse seguenti.
  - Assegnare **WS1\_WSAdmins** agli amministratori dell'area di lavoro Synapse
  - Assegnare **WS1\_SparkAdmins** agli amministratori di Synapse Spark
  - Assegnare **WS1\_SQLAdmins** agli amministratori di Synapse SQL

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>PASSAGGIO 4: configurare Data Lake Storage Gen2 per l'uso con l'area di lavoro Synapse

L'area di lavoro Synapse deve accedere a STG1 e CNT1 per poter eseguire le pipeline e le attività di sistema.

- Aprire il portale di Azure
- Individuare STG1
- Passare a CNT1
- Verificare che l'identità del servizio gestita per WS1 sia assegnata al ruolo **Collaboratore ai dati del BLOB di archiviazione** in CNT1
  - Se il ruolo non è stato assegnato, eseguire l'operazione.
  - L'identità del servizio gestita presenta lo stesso nome dell'area di lavoro. In questo caso, si tratta di &quot;WS1&quot;.

## <a name="step-5-configure-admin-access-for-sql-pools"></a>PASSAGGIO 5: configurare l'accesso amministratore per i pool SQL

- Aprire il portale di Azure
- Passare a WS1
- In **Impostazioni**, fare clic su **Amministratore SQL Active Directory**
- Fare clic su **Imposta amministratore** e selezionare WS1\_SQLAdmins

## <a name="step-6-maintaining-access-control"></a>PASSAGGIO 6: mantenere il controllo di accesso

La configurazione è terminata.

Ora, per gestire l'accesso degli utenti, è possibile aggiungerli e rimuoverli nei tre gruppi di sicurezza.

Sebbene sia possibile assegnare manualmente gli utenti ai ruoli Synapse, scegliendo questo metodo la configurazione non sarà coerente. In alternativa, limitarsi ad aggiungere o rimuovere gli utenti nei gruppi di sicurezza.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>PASSAGGIO 7: verificare l'accesso per gli utenti nei ruoli

Gli utenti in ogni ruolo devono completare i passaggi seguenti:

|   | Passaggio | Amministratori dell'area di lavoro | Amministratori Spark | Amministratori SQL |
| --- | --- | --- | --- | --- |
| 1 | Caricare un file parquet in CNT1 | YES | YES | YES |
| 2 | Leggere il file parquet usando SQL su richiesta | YES | NO | YES |
| 3 | Creare un pool di Spark | SÌ [1] | SÌ [1] | NO  |
| 4 | Leggere il file parquet con un Notebook | YES | YES | NO |
| 5 | Creare una pipeline dal Notebook e attivare subito l'esecuzione della pipeline | YES | NO | NO |
| 6 | Creare un pool SQL ed eseguire uno script SQL come &quot;SELECT 1&quot; | SÌ [1] | NO | SÌ[1] |

> [!NOTE]
> [1] Per creare pool di Spark o SQL, l'utente deve avere almeno il ruolo di Collaboratore nell'area di lavoro Synapse.
> [!TIP]
>
> - Alcuni passaggi non saranno consentiti di proposito a seconda del ruolo.
> - Tenere presente che alcune attività potrebbero avere esito negativo se la sicurezza non è stata configurata completamente. Tali attività sono evidenziate nella tabella.

## <a name="step-8-network-security"></a>PASSAGGIO 8: Sicurezza di rete

Per configurare il firewall, la rete virtuale e il [collegamento privato](../../azure-sql/database/private-endpoint-overview.md) dell'area di lavoro.

## <a name="step-9-completion"></a>PASSAGGIO 9: Completion

L'area di lavoro è ora completamente configurata e protetta.

## <a name="how-roles-interact-with-synapse-studio"></a>Modalità di interazione dei ruoli con Synapse Studio

Il comportamento di Synapse Studio varia in base ai ruoli utente. Alcuni elementi possono essere nascosti o disabilitati se un utente non è assegnato ai ruoli che forniscono l'accesso appropriato. La tabella seguente riassume l'effetto su Synapse Studio.

| Attività | Amministratori dell'area di lavoro | Amministratori Spark | Amministratori SQL |
| --- | --- | --- | --- |
| Aprire Synapse Studio | YES | YES | YES |
| Visualizzare l'hub Home | YES | YES | YES |
| Visualizzare l'hub di dati | YES | YES | YES |
| Hub di dati/Visualizzare account e contenitori ADLS Gen2 collegati | SÌ [1] | SÌ[1] | SÌ[1] |
| Hub di dati/Visualizzare i database | YES | YES | YES |
| Hub di dati/Visualizzare gli oggetti nei database | YES | YES | YES |
| Hub di dati/Accedere ai dati nei database di pool SQL | YES   | NO   | YES   |
| Hub di dati/Accedere ai dati nei database SQL su richiesta | SÌ [2]  | NO  | SÌ [2]  |
| Hub di dati/Accedere ai dati nei database Spark | SÌ [2] | SÌ [2] | SÌ [2] |
| Usare l'hub Sviluppo | YES | YES | YES |
| Hub Sviluppo/Creare script SQL | YES | NO | YES |
| Hub Sviluppo/Creare definizioni dei processi Spark | YES | YES | NO |
| Hub Sviluppo/Creare Notebook | YES | YES | NO |
| Hub Sviluppo/Creare flussi di dati | YES | NO | NO |
| Usare l'hub Orchestrazione | YES | YES | YES |
| Hub Orchestrazione/Usare le pipeline | YES | NO | NO |
| Usare l'hub Gestione | YES | YES | YES |
| Hub Gestione/Pool SQL | YES | NO | YES |
| Hub Gestione/Pool di Spark | YES | YES | NO |
| Hub Gestione/Trigger | YES | NO | NO |
| Hub Gestione/Servizi collegati | YES | YES | YES |
| Hub Gestione/Controllo di accesso (assegnare utenti ai ruoli dell'area di lavoro Synapse) | YES | NO | NO |
| Hub Gestione/Runtime di integrazione | YES | YES | YES |
| Usare l'hub Monitoraggio | YES | YES | YES |
| Hub Monitoraggio/Orchestrazione/Esecuzioni di pipeline  | YES | NO | NO |
| Hub Monitoraggio/Orchestrazione/Esecuzioni di trigger  | YES | NO | NO |
| Hub Monitoraggio/Orchestrazione/Runtime di integrazione  | YES | YES | YES |
| Hub Monitoraggio/Attività/Applicazioni Spark | YES | YES | NO  |
| Hub Monitoraggio/Attività/Richieste SQL | YES | NO | YES |
| Hub Monitoraggio/Attività/Pool di Spark | YES | YES | NO  |
| Hub Monitoraggio/Trigger | YES | NO | NO |
| Hub Gestione/Servizi collegati | YES | YES | YES |
| Hub Gestione/Controllo di accesso (assegnare utenti ai ruoli dell'area di lavoro Synapse) | YES | NO | NO |
| Hub Gestione/Runtime di integrazione | YES | YES | YES |


> [!NOTE]
> [1] L'accesso ai dati nei contenitori dipende dal controllo di accesso in ADLS Gen2. </br>
> [2] Le tabelle OD SQL e le tabelle Spark archiviano i relativi dati in ADLS Gen2. Per l'accesso sono richieste le autorizzazioni appropriate su ADLS Gen2.

## <a name="next-steps"></a>Passaggi successivi

Creare un'[area di lavoro Synapse](../quickstart-create-workspace.md)
