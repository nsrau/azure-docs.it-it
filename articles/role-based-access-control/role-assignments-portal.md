---
title: Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure | Microsoft Docs
description: Informazioni su come gestire l'accesso per utenti, gruppi, entità servizio e identità gestite tramite il controllo degli accessi in base al ruolo e al portale di Azure. Vengono presentate anche la visualizzazione, la concessione e la rimozione dell'accesso.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9f5d48a074f8069e243af5644f86ad3c3d8f559b
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634864"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure

[Il controllo degli accessi in base al ruolo](overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. Questo articolo descrive come gestire l'accesso per utenti, gruppi, entità servizio e identità gestite tramite il controllo degli accessi in base al ruolo e il portale di Azure.

## <a name="open-access-control-iam"></a>Controllo di accesso (IAM)

Il pannello del **Controllo di accesso (IAM)**, anche noto come gestione delle identità e degli accessi, è disponibile in tutto il portale. Per visualizzare o gestire l'accesso nel portale, è necessario innanzitutto aprire il pannello Controllo di accesso (IAM) e selezionare l'ambito degli elementi da visualizzare o modificare.

1. Nel portale di Azure selezionare **Tutti i servizi** e quindi l'ambito o la risorsa da visualizzare o gestire. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica da visualizzare o gestire.

1. Fare clic su **Controllo di accesso (IAM)**.

    Di seguito viene riportato un esempio del pannello Controllo di accesso (IAM) per una sottoscrizione.

    ![Pannello Controllo di accesso (IAM) per una sottoscrizione](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Visualizzare ruoli e autorizzazioni

Una definizione di ruolo è una raccolta di autorizzazioni usata per l'assegnazione dei ruoli. Azure ha oltre 70 [ruoli predefiniti](built-in-roles.md). Seguire questa procedura per visualizzare i ruoli e le autorizzazioni che possono essere eseguite sul piano di gestione e dati.

1. Aprire **Controllo di accesso (IAM)** sull'ambito, come gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa, in cui si intende visualizzare i ruoli e autorizzazioni.

1. Fare clic su **Ruoli** per visualizzare l'elenco di tutti i ruoli predefiniti e personalizzati.

   È possibile visualizzare il numero di utenti e gruppi assegnati a ogni ruolo nell'ambito.

   ![Elenco dei ruoli](./media/role-assignments-portal/roles-list.png)

1. Fare clic su un singolo ruolo per visualizzare a chi è stato assegnato e le relative autorizzazioni.

   ![Assegnazioni di ruolo](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Visualizzare le assegnazioni di ruolo

Quando si gestisce l'accesso, si ha l'esigenza di sapere chi ha accesso, quali sono le sue autorizzazioni e il relativo livello. Per elencare l'accesso per un utente, gruppo, entità servizio o identità gestita, vengono visualizzate le assegnazioni di ruolo.

### <a name="view-role-assignments-for-a-single-user"></a>Visualizzare le assegnazioni di ruolo di un singolo utente

Seguire questi passaggi per visualizzare l'accesso per un singolo utente, gruppo, entità servizio o identità gestita in un ambito specifico.

1. Aprire **Controllo di accesso (IAM)** sull'ambito, come gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa, in cui si intende visualizzare l'accesso.

1. Fare clic sulla scheda **Verifica l'accesso**.

    ![Controllo di accesso - Scheda Verifica l'accesso](./media/role-assignments-portal/access-control-check-access.png)

1. Nell'elenco **Trova**, selezionare il tipo di entità di sicurezza di cui si intende verificare l'accesso.

1. Nella casella di ricerca, immettere una stringa per eseguire ricerche nella directory in base ai nomi visualizzati, agli indirizzi di posta elettronica o agli identificatori di oggetto.

    ![Elenco di selezione per la verifica dell'accesso](./media/role-assignments-portal/check-access-select.png)

1. Fare clic sull'entità di sicurezza per aprire il riquadro **Assegnazioni**.

    ![Riquadro Assegnazioni](./media/role-assignments-portal/check-access-assignments.png)

    In questo riquadro è possibile visualizzare i ruoli assegnati all'entità di sicurezza e all'ambito selezionati. Se sono presenti assegnazioni negate in questo ambito o ereditate da questo ambito, verranno elencate.

### <a name="view-all-role-assignments-at-a-scope"></a>Visualizzare le assegnazioni di ruolo in un ambito

1. Aprire **Controllo di accesso (IAM)** sull'ambito, come gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa, in cui si intende visualizzare l'accesso.

1. Fare clic sulla scheda **Assegnazioni di ruolo** oppure sul pulsante **Visualizza** nel riquadro Visualizza assegnazioni di ruolo per visualizzare tutte le assegnazioni di ruolo in questo ambito.

   ![Controllo di accesso - Scheda Assegnazioni di ruolo](./media/role-assignments-portal/access-control-role-assignments.png)

   Nella scheda Assegnazioni di ruolo è possibile visualizzare chi ha accesso a questo ambito. Si noterà che l'ambito di alcuni ruoli è **Questa risorsa**, mentre quello di altri è **(Ereditato)** da un altro ambito. L'accesso viene assegnato in modo specifico alla risorsa oppure ereditato da un'assegnazione nell'ambito padre.

## <a name="add-a-role-assignment"></a>Aggiungi un'assegnazione di ruolo

Per concedere l'accesso in RBAC, assegnare un ruolo a un utente, gruppo, entità servizio o identità gestita. Eseguire questa procedura per concedere l'accesso ad ambiti diversi.

### <a name="assign-a-role-at-a-scope"></a>Assegnare un ruolo a un ambito

1. Aprire **Controllo di accesso (IAM)** sull'ambito, come gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa, in cui si intende concedere l'accesso.

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questo ambito.

1. Fare clic su **Aggiungi assegnazione di ruolo** per aprire il riquadro Aggiungi assegnazione di ruolo.

   Se non si dispone delle autorizzazioni per assegnare ruoli, l'opzione Aggiungi assegnazione di ruolo verrà disabilitata.

   ![RIquadro Aggiungi assegnazione di ruolo](./media/role-assignments-portal/add-role-assignment.png)

1. Nell'elenco a discesa **Ruolo** selezionare un ruolo, ad esempio **Collaboratore Macchina virtuale**.

1. Nell'elenco **Seleziona** selezionare un utente, gruppo, entità servizio o identità gestita. Se l'entità di sicurezza non è visualizzata nell'elenco, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati, gli indirizzi e-mail e gli identificatori di oggetto.

1. Fare clic su **Salva** per assegnare un ruolo.

   Dopo qualche istante, all'entità di sicurezza verrà assegnato il ruolo per l'ambito selezionato.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Assegnare un utente come amministratore di una sottoscrizione

Per rendere un utente amministratore per una sottoscrizione di Azure, assegnargli il ruolo di [Proprietario](built-in-roles.md#owner) nell'ambito della sottoscrizione. Questo ruolo garantisce all'utente l'accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti. Questi passaggi sono gli stessi di qualsiasi altra assegnazione di ruoli.

1. Nel portale di Azure fare clic su **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Fare clic sulla sottoscrizione in cui si intende concedere l'accesso.

1. Fare clic su **Controllo di accesso (IAM)**.

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questa sottoscrizione.

1. Fare clic su **Aggiungi assegnazione di ruolo** per aprire il riquadro Aggiungi assegnazione di ruolo.

   Se non si dispone delle autorizzazioni per assegnare ruoli, l'opzione Aggiungi assegnazione di ruolo verrà disabilitata.

   ![RIquadro Aggiungi assegnazione di ruolo](./media/role-assignments-portal/add-role-assignment.png)

1. Nell’elenco a discesa **Ruolo**, selezionare il ruolo **Proprietario.**

1. Nell'elenco **Selezione**, selezionare un utente. Se nell'elenco l'utente non è visualizzato, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati e gli indirizzi e-mail.

1. Fare clic su **Salva** per assegnare un ruolo.

   Dopo qualche istante, all’utente viene assegnato il ruolo di Proprietario per l'ambito della sottoscrizione.

## <a name="remove-role-assignments"></a>Rimuovi assegnazioni di ruolo

Per rimuovere un accesso mediante il controllo degli accessi in base al ruolo, si rimuove un'assegnazione di ruolo. Eseguire la procedura seguente per rimuovere l'accesso.

1. Aprire **Controllo di accesso (IAM)** sull'ambito, come gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa, in cui si intende rimuovere l'accesso.

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questa sottoscrizione.

1. Nell'elenco delle assegnazioni di ruolo aggiungere un segno di spunta accanto all'entità di sicurezza con l'assegnazione di ruolo che si vuole rimuovere.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Fare clic su **Rimuovi**.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-portal/remove-role-assignment.png)

1. Nella finestra con il messaggio di rimozione dell'assegnazione di ruolo fare clic su **Sì**.

    Le assegnazioni di ruolo ereditate non possono essere rimosse. Per rimuovere un'assegnazione di ruolo ereditata, è necessario eseguire questa operazione nell'ambito di creazione dell'assegnazione di ruolo. Nella colonna **Ambito**, accanto a **(ereditato)** è presente un collegamento che consente di passare all'ambito in cui è stato assegnato questo ruolo. Passare all'ambito indicato nell'elenco per rimuovere l'assegnazione di ruolo.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Concedere l'accesso a un utente tramite il controllo degli accessi in base al ruolo e il portale di Azure](quickstart-assign-role-user-portal.md)
* [Esercitazione: Concedere l'accesso a un utente tramite il controllo degli accessi in base al ruolo e Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Ruoli predefiniti](built-in-roles.md)
* [Organizzare le risorse con i gruppi di gestione di Azure ](../azure-resource-manager/management-groups-overview.md)
