---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 57368331e83111836e13b6c3796cacad9816e318
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521274"
---
Per visualizzare e copiare le chiavi di accesso o la stringa di connessione dell'account di archiviazione dal portale di Azure:To view and copy your storage account access keys or connection string from the Azure portal:

1. Passare al [portale di Azure](https://portal.azure.com).
2. Individuare l'account di archiviazione.
3. In **Impostazioni**selezionare **Chiavi di accesso**. Verranno visualizzate le chiavi di accesso dell'account, con la stringa di connessione completa per ogni chiave.
4. Trovare il valore **Chiave** in **key1** e fare clic sul pulsante **Copia** per copiare la chiave dell'account.
5. In alternativa, è possibile copiare l'intera stringa di connessione. Trovare il valore **Stringa di connessione** in **key1** e fare clic sul pulsante **Copia** per copiare la stringa di connessione.

    ![Screenshot che mostra come visualizzare le chiavi di accesso nel portale di Azure](media/storage-view-keys-include/portal-connection-string.png)

È possibile usare una delle due chiavi per accedere ad Archiviazione di Azure, ma in generale è consigliabile usare la prima chiave e riservare l'uso della seconda chiave per la rotazione delle chiavi.

Per visualizzare o leggere le chiavi di accesso di un account, l'utente deve essere un amministratore del servizio o deve essere assegnato un ruolo RBAC che include **Microsoft.Storage/storageAccounts/listkeys/action**. Alcuni ruoli RBAC predefiniti che includono questa azione sono i ruoli **Proprietario**, **Collaboratore**e **Ruolo ruolo servizio operatore chiave dell'account di archiviazione.** Per altre informazioni sul ruolo di amministratore del servizio, vedere Ruoli di amministratore della [sottoscrizione classica, ruoli di controllo degli accessi in](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)base al ruolo di Azure e ruoli di Azure AD. Per informazioni dettagliate sui ruoli predefiniti per Archiviazione di Azure, vedere la sezione Archiviazione in Ruoli predefiniti di Azure per Controllo degli accessi in base al ruolo di Azure.For detailed information about built-in roles for Azure Storage, see the **Storage** section in [Azure built-in roles for Azure RBAC.](../articles/role-based-access-control/built-in-roles.md#storage)
