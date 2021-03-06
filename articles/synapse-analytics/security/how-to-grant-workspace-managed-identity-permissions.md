---
title: Concedere le autorizzazioni per l'identità gestita nell'area di lavoro sinapsi
description: Articolo che illustra come configurare le autorizzazioni per l'identità gestita nell'area di lavoro sinapsi di Azure.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 46fe27205a2c30d6cb2319bf620c6fe1ee34c31e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458998"
---
# <a name="grant-permissions-to-workspace-managed-identity"></a>Concedere autorizzazioni all'identità gestita dell'area di lavoro

Questo articolo illustra come concedere le autorizzazioni per l'identità gestita nell'area di lavoro di sinapsi di Azure. Le autorizzazioni consentono, a loro volta, di accedere ai pool SQL dedicati nell'area di lavoro e ADLS Gen2 account di archiviazione tramite la portale di Azure.

>[!NOTE]
>A questa identità gestita dell'area di lavoro verrà fatto riferimento come identità gestita nel resto di questo documento.

## <a name="grant-managed-identity-permissions-to-the-dedicated-sql-pool"></a>Concedere le autorizzazioni di identità gestite al pool SQL dedicato

L'identità gestita concede le autorizzazioni ai pool SQL dedicati nell'area di lavoro. Con le autorizzazioni concesse è possibile orchestrare le pipeline che eseguono attività relative al pool SQL dedicate. Quando si crea un'area di lavoro di Azure sinapsi usando portale di Azure, è possibile concedere le autorizzazioni di controllo identità gestite per i pool SQL dedicati.

Quando si crea un'area di lavoro di Azure sinapsi, selezionare **sicurezza** . Selezionare quindi **Consenti pipeline (in esecuzione come identità assegnata al sistema dell'area di lavoro) per accedere ai pool SQL.**

![Autorizzazione CONTROL per pool SQL dedicati](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>Concedere le autorizzazioni di identità gestite per ADLS Gen2 account di archiviazione

Per creare un'area di lavoro di Azure sinapsi, è necessario un account di archiviazione ADLS Gen2. Per avviare correttamente i pool Spark nell'area di lavoro sinapsi di Azure, l'identità gestita di Azure sinapsi richiede il ruolo di *collaboratore dati BLOB di archiviazione* per questo account di archiviazione. Anche l'orchestrazione della pipeline in Azure sinapsi trae vantaggio da questo ruolo.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Concedere le autorizzazioni all'identità gestita durante la creazione dell'area di lavoro

La sinapsi di Azure tenterà di concedere al ruolo di collaboratore dati BLOB di archiviazione l'identità gestita dopo avere creato l'area di lavoro di Azure sinapsi usando portale di Azure. Specificare i dettagli dell'account di archiviazione ADLS Gen2 nella scheda **nozioni di base** .

![Scheda nozioni fondamentali nel flusso di creazione dell'area di lavoro](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Scegliere il ADLS Gen2 account di archiviazione e filesystem in nome **account** e **nome file System**.

![Fornire i dettagli dell'account di archiviazione ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Se l'autore dell'area di lavoro è anche **proprietario** dell'account di archiviazione ADLS Gen2, la sinapsi di Azure assegnerà il ruolo di *collaboratore dati BLOB di archiviazione* all'identità gestita. Il messaggio seguente verrà visualizzato sotto i dettagli dell'account di archiviazione immesso.

![Assegnazione di collaboratore dati BLOB di archiviazione riuscita](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Se l'autore dell'area di lavoro non è il proprietario dell'account di archiviazione ADLS Gen2, la sinapsi di Azure non assegna il ruolo di *collaboratore dei dati BLOB di archiviazione* all'identità gestita. Il messaggio visualizzato sotto i dettagli dell'account di archiviazione notifica al creatore dell'area di lavoro che non dispongono di autorizzazioni sufficienti per concedere al ruolo di *collaboratore dati BLOB di archiviazione* l'identità gestita.

![Assegnazione di collaboratore dati BLOB di archiviazione non riuscita](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

Come indicato nel messaggio, non è possibile creare pool Spark, a meno che il *collaboratore dati BLOB di archiviazione* non sia assegnato all'identità gestita.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Concedere le autorizzazioni all'identità gestita dopo la creazione dell'area di lavoro

Durante la creazione dell'area di lavoro, se non si assegna il *collaboratore dati BLOB di archiviazione* all'identità gestita, il **proprietario** dell'account di archiviazione ADLS Gen2 assegna manualmente tale ruolo all'identità. I passaggi seguenti consentiranno di eseguire l'assegnazione manuale.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Passaggio 1: passare all'account di archiviazione ADLS Gen2 in portale di Azure

In portale di Azure aprire l'account di archiviazione ADLS Gen2 e selezionare **Panoramica** dal percorso di spostamento a sinistra. È necessario solo assegnare il ruolo di *collaboratore dei dati BLOB di archiviazione* a livello di contenitore o file System. Selezionare **Contenitori**.  
![Panoramica dell'account di archiviazione ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Passaggio 2: selezionare il contenitore

L'identità gestita deve avere accesso ai dati al contenitore (file system) fornito durante la creazione dell'area di lavoro. È possibile trovare questo contenitore o file system in portale di Azure. Aprire l'area di lavoro sinapsi di Azure in portale di Azure e selezionare la scheda **Panoramica** dal pannello di navigazione sinistro.
![Contenitore dell'account di archiviazione ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Selezionare lo stesso contenitore o file system per concedere al ruolo *collaboratore dati BLOB di archiviazione* l'identità gestita.
![Screenshot che mostra il contenitore o file system da selezionare.](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>Passaggio 3: passare a controllo di accesso

Selezionare **Controllo di accesso (IAM)** .

![Controllo di accesso (IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>Passaggio 4: aggiungere una nuova assegnazione di ruolo

Selezionare **+ Aggiungi**.

![Aggiungi nuova assegnazione di ruolo](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-azure-role"></a>Passaggio 5: selezionare il ruolo di Azure

Selezionare il ruolo **collaboratore dati BLOB di archiviazione** .

![Selezionare il ruolo di Azure](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>Passaggio 6: selezionare l'entità di sicurezza Azure AD

Selezionare **Azure ad utente, gruppo o entità servizio** nell'elenco **a discesa assegna accesso a** .

![Selezionare l'entità di sicurezza di AAD](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>Passaggio 7: ricerca dell'identità gestita

Il nome dell'identità gestita è anche il nome dell'area di lavoro. Per cercare l'identità gestita, immettere il nome dell'area di lavoro sinapsi di Azure in **Select**. Verrà visualizzata l'identità gestita elencata.

![Trovare l'identità gestita](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>Passaggio 8: selezionare l'identità gestita

Consente di selezionare l'identità gestita per i **membri selezionati**. Selezionare **Save (Salva** ) per aggiungere l'assegnazione di ruolo.

![Selezionare l'identità gestita](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Passaggio 9: verificare che il ruolo di collaboratore dati BLOB di archiviazione sia assegnato all'identità gestita

Selezionare **controllo di accesso (IAM)** , quindi selezionare **assegnazioni di ruolo**.

![Verificare l'assegnazione di ruolo](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

L'identità gestita dovrebbe essere elencata nella sezione **archiviazione dei dati BLOB di archiviazione** a cui è assegnato il ruolo di *collaboratore dei dati BLOB di archiviazione* . 
![Selezione del contenitore dell'account di archiviazione ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull' [identità gestita dell'area di lavoro](./synapse-workspace-managed-identity.md)
