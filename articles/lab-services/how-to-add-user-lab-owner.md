---
title: Come aggiungere altri proprietari a un Lab in Azure Lab Services
description: Questo articolo illustra come un amministratore può aggiungere un utente come proprietario di un Lab in Azure Lab Services.
ms.topic: article
ms.date: 09/04/2020
ms.openlocfilehash: 4b46dd08a3b9527cdfc7bdf766ee4976e67656d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89482670"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Come aggiungere altri proprietari a un lab esistente in Azure Lab Services
Questo articolo illustra come un amministratore può aggiungere altri proprietari a un lab esistente.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Aggiungere un utente al ruolo Reader per l'account Lab
Per aggiungere un utente come proprietario aggiuntivo a un lab esistente, è innanzitutto necessario concedere all'utente le autorizzazioni di **lettura** per l'account Lab.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutti i servizi** dal menu a sinistra. Cercare **Lab Services**, quindi selezionarlo.
3. Selezionare l' **account Lab** nell'elenco. 
2. Nella **pagina account Lab**selezionare controllo di **accesso (IAM)** nel menu a sinistra. 
2. Nella pagina **controllo di accesso (IAM)** selezionare **Aggiungi** sulla barra degli strumenti e selezionare **Aggiungi assegnazione ruolo**.

    ![Assegnazione di ruolo per l'account Lab ](./media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Nella pagina **Aggiungi un'assegnazione di ruolo** seguire questa procedura: 
    1. Selezionare **Reader** per il **ruolo**. 
    2. Selezionare l'utente. 
    3. Selezionare **Salva**. 

        ![Aggiungere un utente al ruolo Reader per l'account Lab ](./media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Aggiungere un utente al ruolo di proprietario per il Lab

> [!NOTE]
> Se l'utente ha solo l'accesso in lettura al Lab, il Lab non viene visualizzato in labs.azure.com.

1. Tornare alla pagina **account Lab** , selezionare **tutti i Lab** nel menu a sinistra.
2. Selezionare il **Lab** al quale si desidera aggiungere l'utente come proprietario. 
    
    ![Selezionare il lab ](./media/how-to-add-user-lab-owner/select-lab.png)    
3. Nella pagina **Lab** selezionare controllo di **accesso (IAM)** nel menu a sinistra.
4. Nella pagina **controllo di accesso (IAM)** selezionare **Aggiungi** sulla barra degli strumenti e selezionare **Aggiungi assegnazione ruolo**.
5. Nella pagina **Aggiungi un'assegnazione di ruolo** seguire questa procedura: 
    1. Selezionare **Owner (proprietario** ) per il **ruolo**. 
    2. Selezionare l'utente. 
    3. Selezionare **Salva**. 

## <a name="next-steps"></a>Passaggi successivi
Verificare che l'utente veda il Lab dopo aver effettuato l'accesso al [portale di Lab Services](https://labs.azure.com).
