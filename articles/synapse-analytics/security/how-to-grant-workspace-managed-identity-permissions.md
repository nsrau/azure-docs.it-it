---
title: Come concedere autorizzazioni all'identità gestita nell'area di lavoro di Azure SynapseHow to grant permissions to managed identity in Azure Synapse workspace
description: Un articolo che spiega come configurare le autorizzazioni per l'identità gestita nell'area di lavoro di Azure Synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9f519022fffe98c565c3b2d30f6578b9ebb70c57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428017"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>Concedere autorizzazioni all'identità gestita dell'area di lavoro (anteprima)Grant permissions to workspace managed identity (preview)

Questo articolo illustra come concedere autorizzazioni all'identità gestita nell'area di lavoro di protezione di Azure.This article teaches you how to grant permissions to the managed identity in Azure synapse workspace. Le autorizzazioni, a loro volta, consentono l'accesso ai pool SQL nell'area di lavoro e all'account di archiviazione ADLS gen2 tramite il portale di Azure.Permissions, in turn, allow access to SQL pools in the workspace and ADLS gen2 storage account through the Azure portal.

>[!NOTE]
>Questa identità gestita dell'area di lavoro verrà definita identità gestita tramite il resto di questo documento.

## <a name="grant-the-managed-identity--permissions-to-the-sql-pool"></a>Concedere le autorizzazioni di identità gestita al pool SQLGrant the managed identity permissions to the SQL pool

L'identità gestita concede autorizzazioni ai pool SQL nell'area di lavoro. Con le autorizzazioni concesse, è possibile orchestrare le pipeline che eseguono attività correlate al pool SQL. Quando si crea un'area di lavoro di Azure Synapse usando il portale di Azure, è possibile concedere le autorizzazioni CONTROL di identità gestite nei pool SQL.

Selezionare Sicurezza e rete quando si crea l'area di lavoro Synapse di Azure.Select **Security - networking** when you're creating your Azure Synapse workspace. Selezionare **quindi Concedi CONTROLLO all'identità gestita dell'area**di lavoro nei pool SQL.

![Autorizzazione CONTROL sui pool SQL](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>Concedere le autorizzazioni di identità gestita all'account di archiviazione ADLS gen2Grant the managed identity permissions to ADLS gen2 storage account

Per creare un'area di lavoro di Azure Synapse, è necessario un account di archiviazione gen2 ADLS.An ADLS gen2 storage account is required to create an Azure Synapse workspace. Per avviare correttamente i pool Spark nell'area di lavoro di Azure Synapse, l'identità gestita di Azure Synapse richiede il ruolo *Collaboratore dati BLOB* di archiviazione in questo account di archiviazione. Anche l'orchestrazione della pipeline in Azure Synapse trae vantaggio da questo ruolo.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Concedere autorizzazioni all'identità gestita durante la creazione dell'area di lavoroGrant permissions to managed identity during workspace creation

Azure Synapse tenterà di concedere il ruolo Collaboratore dati BLOB di archiviazione all'identità gestita dopo aver creato l'area di lavoro di Azure Synapse tramite il portale di Azure.Azure Synapse will attempt to grant the Storage Blob Data Contributor role to the managed identity after you create the Azure Synapse workspace using Azure portal. I dettagli dell'account di archiviazione gen2 di ADLS vengono forniti nella scheda **Nozioni di base.**

![Scheda Nozioni di base nel flusso di creazione dell'area di lavoro](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Scegliere l'account di archiviazione gen2 e il file system di ADLS in **Nome account** e Nome **file system**.

![Fornire i dettagli di un account di archiviazione adLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Se l'autore dell'area di lavoro è anche **proprietario** dell'account di archiviazione gen2 di ADLS, Azure Synapse assegnerà il ruolo *Storage Blob Data Contributor* all'identità gestita. Verrà visualizzato il messaggio seguente sotto i dettagli dell'account di archiviazione immesso.

![Assegnazione del collaboratore dei dati BLOB di archiviazione riuscita](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Se l'autore dell'area di lavoro non è il proprietario dell'account di archiviazione gen2 di ADLS, Azure Synapse non assegna il ruolo *Storage Blob Data Contributor* all'identità gestita. Il messaggio visualizzato sotto i dettagli dell'account di archiviazione notifica all'autore dell'area di lavoro che non dispone di autorizzazioni sufficienti per concedere il ruolo *Collaboratore dati BLOB* di archiviazione all'identità gestita.

![Assegnazione di collaboratori ai dati BLOB di archiviazione non riuscitaUnsuccessful Storage BLOB Data Contributor assignment](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

Come indica il messaggio, non è possibile creare pool Spark a meno che il *collaboratore dei dati BLOB* di archiviazione non sia assegnato all'identità gestita.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Concedere autorizzazioni per l'identità gestita dopo la creazione dell'area di lavoroGrant permissions to managed identity after workspace creation

Durante la creazione dell'area di lavoro, se non si assegna il *collaboratore* ai dati BLOB di archiviazione all'identità gestita, il **proprietario** dell'account di archiviazione ADLS gen2 assegna manualmente tale ruolo all'identità. I passaggi seguenti ti aiuteranno a svolgere l'assegnazione manuale.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Passaggio 1: Passare all'account di archiviazione ADLS gen2 nel portale di AzureStep 1: Navigate to the ADLS gen2 storage account in Azure portal

Nel portale di Azure aprire l'account di archiviazione ADLS gen2 e selezionare **Panoramica** nel riquadro di spostamento sinistro. Sarà sufficiente assegnare il ruolo *Collaboratore dati BLOB di archiviazione* solo a livello di contenitore o file system. Selezionare **Contenitori**.  
![Panoramica dell'account di archiviazione gen2 di ADLSADLS gen2 storage account overview](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Passaggio 2: Selezionare il contenitoreStep 2: Select the container

L'identità gestita deve disporre dell'accesso ai dati al contenitore (file system) fornito al momento della creazione dell'area di lavoro. È possibile trovare questo contenitore o file system nel portale di Azure.You can find this container or file system in Azure portal. Aprire l'area di lavoro Synapse di Azure nel portale di Azure e selezionare la scheda **Panoramica** nel riquadro di spostamento sinistro.
![Contenitore di account di archiviazione gen2 di ADLSADLS gen2 storage account container](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Selezionare lo stesso contenitore o file system per concedere il ruolo *Collaboratore dati BLOB di archiviazione* all'identità gestita.
![Selezione del contenitore dell'account di archiviazione gen2 di ADLS](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>Passaggio 3: Passare al controllo di accessoStep 3: Navigate to Access control

Selezionare **Controllo di accesso (IAM)**.

![Controllo accessi (IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>Passaggio 4: Aggiungere una nuova assegnazione di ruoloStep 4: Add a new role assignment

Selezionare **+ Aggiungi**.

![Aggiungere una nuova assegnazione di ruoloAdd new role assignment](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-rbac-role"></a>Passaggio 5: Selezionare il ruolo Controllo degli accessi in base al ruoloStep 5: Select the RBAC role

Selezionare il ruolo **Collaboratore dati BLOB di archiviazione.**

![Selezionare il ruolo Controllo degli accessi in base al ruolo](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>Passaggio 6: Selezionare l'entità di sicurezza di Azure ADStep 6: Select the Azure AD security principal

Selezionare Utente, gruppo o entità servizio di Azure AD dall'elenco a discesa Assegna accesso a.Select **Azure AD user, group,** or service principal from the **Assign access to** drop down.

![Selezionare l'entità di sicurezza AADSelect AAD security principal](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>Passaggio 7: Cercare l'identità gestitaStep 7: Search for the managed identity

Il nome dell'identità gestita è anche il nome dell'area di lavoro. Cercare l'identità gestita immettendo il nome dell'area di lavoro Di Azure Synapse in **Seleziona**. Verrà visualizzata l'identità gestita nell'elenco.

![Trovare l'identità gestitaFind the managed identity](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>Passaggio 8: Selezionare l'identità gestitaStep 8: Select the managed identity

Selezionare l'identità gestita per i **membri selezionati**. Selezionare **Salva** per aggiungere l'assegnazione di ruolo.

![Selezionare l'identità gestita](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Passaggio 9: Verificare che il ruolo Collaboratore dati BLOB di archiviazione sia assegnato all'identità gestitaStep 9: Verify that the Storage Blob Data Contributor role is assigned to the managed identity

Selezionare **Controllo di accesso (IAM)** e quindi **Assegnazioni di**ruolo .

![Verificare l'assegnazione dei ruoli](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

L'identità gestita dovrebbe essere elencata nella sezione **Collaboratore dati BLOB di archiviazione** con il ruolo *Collaboratore dati BLOB* di archiviazione assegnato. 
![Selezione del contenitore dell'account di archiviazione gen2 di ADLS](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni [sull'identità gestita dell'area di lavoro](./synapse-workspace-managed-identity.md)
