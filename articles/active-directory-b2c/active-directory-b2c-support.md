---
title: Supporto di Azure Active Directory B2C | Microsoft Docs
description: Come inviare richieste di supporto per Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f7f2217677b81c6f3e87eaa2612880adf3b499c2
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064934"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: Inviare richieste di supporto
È possibile archiviare le richieste di supporto per Azure Active Directory B2C (Azure AD B2C) nel portale di Azure usando la procedura seguente:

1. Passare dal tenant B2C in un altro tenant a cui è associata una sottoscrizione di Azure. In genere, questa è il tenant di dipendente o il tenant predefinito creato automaticamente quando è stata eseguita l'iscrizione per una sottoscrizione di Azure. Per altre informazioni, vedere la [relazione tra una sottoscrizione di Azure e Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

    ![portale di Azure con la selezione del tenant evidenziata](./media/active-directory-b2c-support/support-switch-dir.png)

1. Dopo avere cambiato tenant, fare clic su **Guida e supporto**.

    ![Riquadro Guida e supporto evidenziato in portale di Azure](./media/active-directory-b2c-support/support-support.png)

1. Fare clic su **Nuova richiesta di supporto**.

    ![Riquadro nuova richiesta di supporto evidenziato in portale di Azure](./media/active-directory-b2c-support/support-new.png)

1. Nel pannello **Informazioni di base** usare questi dettagli e fare clic su **Avanti**.

    * Il **Tipo di problema** è **Tecnico**.
    * Scegliere la **Sottoscrizione**appropriata.
    * Il **Servizio** è **Active Directory**.
    * Scegliere il **Piano di supporto**appropriato. Se non si ha un piano di supporto, è possibile iscriversi per ottenerne uno [qui](https://azure.microsoft.com/support/plans/).

     ![Pagina Nozioni di base con il pulsante Avanti evidenziato in portale di Azure](./media/active-directory-b2c-support/support-basics.png)

1. Nel pannello **Problema** usare questi dettagli e fare clic su **Avanti**.

    * Scegliere il livello di **Gravità** appropriato.
    * Il **Tipo di problema** è **B2C**.
    * Scegliere la **Categoria**appropriata.
    * Descrivere il problema nel campo **Dettagli** . Specificare dettagli quali il nome del tenant B2C, la descrizione del problema, i messaggi di errore, gli ID di correlazione (se disponibili) e così via.
    * Nel campo **Intervallo di tempo** specificare la data e l'ora, incluso il fuso orario, corrispondenti al momento in cui si è verificato il problema.
    * In **Caricamento file**caricare tutte le schermate e tutti i file che potrebbero risultare utili per la risoluzione del problema.

     ![Pagina problema con il pulsante Avanti evidenziato in portale di Azure](./media/active-directory-b2c-support/support-problem.png)

1. Nel pannello **Informazioni di contatto** aggiungere le proprie informazioni di contatto. Fare clic su **Create**(Crea).

    ![Pagina delle informazioni di contatto con il pulsante Crea evidenziato nel portale](./media/active-directory-b2c-support/support-contact.png)

1. Dopo aver inviato la richiesta di supporto, è possibile monitorarla facendo clic su **Guida e supporto** nella schermata iniziale, quindi su **Gestisci richieste di supporto**.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Problema noto: Invio di una richiesta di supporto nel contesto di un tenant B2C

Se non è stato svolto il passaggio 2 precedente e si tenta di creare una richiesta di supporto nel contesto del tenant B2C, verrà visualizzato il seguente errore.

> [!IMPORTANT]
> Non tentare di effettuare l'iscrizione per una nuova sottoscrizione di Azure nel tenant B2C.

![Non è presente un errore di sottoscrizione visualizzato in portale di Azure](./media/active-directory-b2c-support/support-no-sub.png)
