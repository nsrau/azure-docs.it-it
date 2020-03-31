---
title: Come aggiungere altri proprietari a un lab in Azure Lab ServicesHow to add additional owners to a lab in Azure Lab Services
description: Questo articolo illustra come un amministratore può aggiungere un utente come proprietario a un lab in Azure Lab Services.This article shows you how an administrator can add a user as an owner to a lab in Azure Lab Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443517"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Come aggiungere altri proprietari a un lab esistente in Azure Lab ServicesHow to add additional owners to an existing lab in Azure Lab Services
In questo articolo viene illustrato come, in qualità di amministratore, è possibile aggiungere altri proprietari a un lab esistente.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Aggiungere un utente al ruolo lettore per l'account lab
Per aggiungere un utente come proprietario aggiuntivo a un lab esistente, è innanzitutto necessario assegnare all'utente le autorizzazioni di **lettura** per l'account lab.

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Scegliere **Tutti i servizi** dal menu a sinistra. Cercare **Lab Services**, quindi selezionarlo.
3. Selezionare **l'account** lab dall'elenco. 
2. Nella **pagina Account lab**selezionare Controllo di accesso **(IAM)** nel menu a sinistra. 
2. Nella pagina **Controllo di accesso (IAM)** selezionare **Aggiungi** sulla barra degli strumenti e quindi **Aggiungi assegnazione ruolo**.

    ![Assegnazione di ruolo per l'account lab ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Nella pagina **Aggiungi assegnazione ruolo** eseguire la procedura seguente:On the Add a role assignment page, do the following steps: 
    1. Selezionare **Lettore** per il **ruolo**. 
    2. Selezionare l'utente. 
    3. Selezionare **Salva**. 

        ![Aggiungere un utente al ruolo lettore per l'account lab ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Aggiungere un utente al ruolo di proprietario per il labAdd user to the owner role for the lab

1. Tornare alla pagina **Account lab,** selezionare **Tutti i lab** nel menu a sinistra.
2. Selezionare il **lab** a cui si desidera aggiungere l'utente come proprietario. 
    
    ![Selezionare il lab ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. Nella pagina **Lab** selezionare **Controllo di accesso (IAM)** nel menu a sinistra.
4. Nella pagina **Controllo di accesso (IAM)** selezionare **Aggiungi** sulla barra degli strumenti e quindi **Aggiungi assegnazione ruolo**.
5. Nella pagina **Aggiungi assegnazione ruolo** eseguire la procedura seguente:On the Add a role assignment page, do the following steps: 
    1. Selezionare **Proprietario** per il **ruolo**. 
    2. Selezionare l'utente. 
    3. Selezionare **Salva**. 

## <a name="next-steps"></a>Passaggi successivi
Verificare che l'utente visualizzi il lab al momento dell'accesso al portale di [Lab Services.](https://labs.azure.com)