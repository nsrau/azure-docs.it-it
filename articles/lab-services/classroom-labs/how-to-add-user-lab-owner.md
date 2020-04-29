---
title: Come aggiungere altri proprietari a un Lab in Azure Lab Services
description: Questo articolo illustra come un amministratore può aggiungere un utente come proprietario di un Lab in Azure Lab Services.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 9302cadf0b900c9faac382bad86b51cea7d828fb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77443517"
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