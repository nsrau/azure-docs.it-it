---
title: Gestire le autorizzazioni per database di Esplora dati di Azure
description: Questo articolo descrive i controlli degli accessi in base al ruolo per i database e le tabelle in Esplora dati di Azure.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: fbbc5a199116e46aac0874f3dc6d6d9aa18c60cd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954022"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Gestire le autorizzazioni per database di Esplora dati di Azure

Esplora dati di Azure consente di controllare l'accesso ai database e alle tabelle, usando il modello *controllo degli accessi in base al ruolo*. In questo modello viene eseguito il mapping ai *ruoli* delle*entità di sicurezza* (utenti, gruppi e app). Le entità di sicurezza possono accedere alle risorse in base ai ruoli che vengono loro assegnati.

Questo articolo descrive i ruoli disponibili e come assegnare le entità di sicurezza a tali ruoli.

## <a name="roles-and-permissions"></a>Ruoli e autorizzazioni

Esplora dati di Azure presenta i seguenti ruoli:

|Ruolo                       |Autorizzazioni                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Amministratore database             |Può eseguire qualsiasi operazione nell'ambito di un determinato database.|
|Utente database              |Può leggere tutti i dati e metadati nel database. Inoltre, può creare tabelle (diventando l'amministratore di tabella per tale tabella) e funzioni nel database.|
|Visualizzatore database            |Può leggere tutti i dati e metadati nel database.|
|Inseritore database          |Può inserire dati per tutte le tabelle esistenti nel database, ma non una query sui dati.|
|Unrestrictedviewer database|Può eseguire query su tabelle con il criterio **RestrictedViewAccess** abilitato. Non può eseguire query su altre tabelle.|
|Monitoraggio del database           |Può eseguire comandi ".show..." nel contesto del database e le relative entità figlio.|
|Amministratore tabella                |Può eseguire qualsiasi operazione nell'ambito di una determinata tabella. |
|Inseritore tabella             |Può inserire dati nell'ambito di una determinata tabella, ma non una query sui dati.|

## <a name="manage-permissions-in-the-azure-portal"></a>Gestire le autorizzazioni nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Passare al cluster Esplora dati di Azure.

1. Nella sezione **Panoramica**, selezionare il database in cui si desidera gestire le autorizzazioni.

    ![Selezionare il database](media/manage-database-permissions/select-database.png)

1. Selezionare **Autorizzazioni** quindi**Aggiungi**.

    ![Autorizzazioni per il database](media/manage-database-permissions/database-permissions.png)

1. In **Aggiungere autorizzazioni per il database**, selezionare il ruolo che si desidera assegnare all'entità di sicurezza, quindi **Selezionare entità di sicurezza**.

    ![Aggiungere autorizzazioni database](media/manage-database-permissions/add-permission.png)

1. Cercare l'entità di sicurezza, selezionarla, quindi fare clic su **Seleziona**.

    ![Gestire le autorizzazioni nel portale di Azure](media/manage-database-permissions/new-principals.png)

1. Selezionare **Salva**.

    ![Gestire le autorizzazioni nel portale di Azure](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Gestire le autorizzazioni con i comandi di gestione

1. Accedere a [https://dataexplorer.azure.com](https://dataexplorer.azure.com) e aggiungere il cluster, se non è già disponibile.

1. Nel riquadro sinistro, selezionare il database appropriato.

1. Usare il comando `.add` per assegnare le entità di sicurezza ai ruoli: `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`. Per aggiungere un utente al ruolo di Utente database, eseguire il comando seguente, sostituendo il nome del database e l'utente.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    L'output del comando mostra l'elenco di utenti esistenti e i ruoli che vengono loro assegnati nel database.

## <a name="next-steps"></a>Passaggi successivi

[Scrivere query](write-queries.md)