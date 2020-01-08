---
title: Come aggiungere un utente come proprietario del Lab in Azure Lab Services
description: Questo articolo illustra come un amministratore può aggiungere un utente come proprietario di un Lab.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 9e007ee6c95843b7e8ef9d7a701237034811489e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480853"
---
# <a name="how-to-add-a-user-as-an-owner-of-a-classroom-lab-in-azure-lab-services"></a>Come aggiungere un utente come proprietario di un Lab della classe in Azure Lab Services
Questo articolo illustra come aggiungere un utente come proprietario di un Lab in Azure Lab Services.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Aggiungere un utente al ruolo Reader per l'account Lab
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutti i servizi** dal menu a sinistra. Cercare **Lab Services**, quindi selezionarlo.
3. Selezionare l' **account Lab** nell'elenco. 
2. Nella **pagina account Lab**selezionare controllo di **accesso (IAM)** nel menu a sinistra. 
2. Nella pagina **controllo di accesso (IAM)** selezionare **Aggiungi** sulla barra degli strumenti e selezionare **Aggiungi assegnazione ruolo**.

    ![Assegnazione di ruolo per l'account Lab ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Nella pagina **Aggiungi assegnazione ruolo** eseguire le operazioni seguenti: 
    1. Selezionare **Reader** per il **ruolo**. 
    2. Selezionare l'utente. 
    3. Selezionare **Salva**. 

        ![Aggiungere un utente al ruolo Reader per l'account Lab ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Aggiungere un utente al ruolo di proprietario per il Lab

1. Tornare alla pagina **account Lab** , selezionare **tutti i Lab** nel menu a sinistra.
2. Selezionare il **Lab** al quale si desidera aggiungere l'utente come proprietario. 
    
    ![Selezionare il lab ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. Nella pagina **Lab** selezionare controllo di **accesso (IAM)** nel menu a sinistra.
4. Nella pagina **controllo di accesso (IAM)** selezionare **Aggiungi** sulla barra degli strumenti e selezionare **Aggiungi assegnazione ruolo**.
5. Nella pagina **Aggiungi assegnazione ruolo** eseguire le operazioni seguenti: 
    1. Selezionare **Owner (proprietario** ) per il **ruolo**. 
    2. Selezionare l'utente. 
    3. Selezionare **Salva**. 

## <a name="next-steps"></a>Passaggi successivi
Verificare che l'utente veda il Lab dopo aver effettuato l'accesso al [portale di Lab Services](https://labs.azure.com).