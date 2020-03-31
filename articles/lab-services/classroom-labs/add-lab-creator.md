---
title: Aggiungere un utente come creatore di lab in Azure Lab ServicesAdd a user as a lab creator in Azure Lab Services
description: Questo articolo illustra come aggiungere un utente al ruolo Creatore lab per un account lab in Azure Lab Services.This article shows how to add a user to the Lab Creator role for a lab account in Azure Lab Services. I creatori di lab possono creare lab all'interno di questo account lab.
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
ms.openlocfilehash: 0538747ec639b3fab1a7b38193796d80a7736170
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444771"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Aggiungere creatori di lab a un account lab in Azure Lab ServicesAdd lab creators to a lab account in Azure Lab Services
Questo articolo illustra come aggiungere utenti come creatori di lab a un account lab in Azure Lab Services.This article shows you how to add users as lab creators to a lab account in Azure Lab Services. Questi usi possono quindi creare laboratori di classe nell'account lab. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Aggiungere l'account utente Microsoft al ruolo Lab Creator
Per configurare un lab per le classi in un account del lab, l'utente deve essere un membro del ruolo **Autore di laboratori** nell'account del lab. L'account usato per creare l'account del lab viene automaticamente aggiunto a questo ruolo. Se si prevede di usare lo stesso account utente per creare un lab per le classi, è possibile ignorare questo passaggio. Per usare un altro account utente per creare un lab per le classi, procedere come segue: 

Per fornire ai docenti l'autorizzazione per creare lab per le proprie classi, aggiungerli al ruolo **Autore di laboratori**:

1. Nella pagina **Account Lab** selezionare **Controllo di accesso (IAM)** e fare clic su **+ Aggiungi assegnazione di ruolo** sulla barra degli strumenti. 

    ![Fare clic su Controllo di accesso -> Aggiungi un'assegnazione di ruolo](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Nella pagina **Aggiungi un'assegnazione di ruolo** selezionare **Autore di lab** per **Ruolo**, scegliere l'utente che si vuole aggiungere a questo ruolo e fare clic su **Salva**. 

    ![Aggiungere l'autore del lab](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Se si aggiunge un utente di account non Microsoft come creatore di lab, vedere la sezione Aggiungere un utente di [account non Microsoft come creatore](#add-a-non-microsoft-account-user-as-a-lab-creator) di lab. 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Aggiungere un utente di account non Microsoft come creatore di labAdd a non-Microsoft account user as a lab creator
Per aggiungere un utente come creatore del lab, utilizzare i relativi account di posta elettronica. Potrebbero essere utilizzati i seguenti tipi di account di posta elettronica:

- Un account di posta elettronica fornito da Office 365 Azure Active Directory (AAD) dell'università. 
- Un account di posta `@outlook.com` `@hotmail.com`elettronica `@msn.com`Microsoft, ad esempio , , o `@live.com`.
- Un account di posta elettronica non Microsoft, ad esempio uno fornito da Yahoo o Google. Tuttavia, questi tipi di account devono essere collegati a un account Microsoft.
- Un account GitHub. Questo account deve essere collegato a un account Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Utilizzo di un account di posta elettronica non Microsoft
I creatori/istruttori del lab possono usare account di posta elettronica non Microsoft per registrarsi e accedere a un laboratorio della classe.  Tuttavia, l'accesso al portale di Lab Services richiede innanzitutto che gli istruttori creino un account Microsoft collegato al proprio indirizzo di posta elettronica non Microsoft.However, the sign-in to the Lab Services portal requires that instructors first create a Microsoft account that's linked to their non-Microsoft email address.

Molti istruttori potrebbero già avere un account Microsoft collegato ai propri indirizzi di posta elettronica non Microsoft. Ad esempio, gli istruttori dispongono già di un account Microsoft se hanno usato il proprio indirizzo di posta elettronica con altri prodotti o servizi Microsoft, ad esempio Office, Skype, OneDrive o Windows.For example, instructors already have a Microsoft account if they have used their email address with Microsoft's other products or services, such as Office, Skype, OneDrive, or Windows.  

Quando gli istruttori accedono al portale di Lab Services, vengono richiesti per l'indirizzo di posta elettronica e la password. Se l'istruttore tenta di accedere con un account non Microsoft che non dispone di un account Microsoft collegato, l'istruttore riceverà il seguente messaggio di errore: 

![Messaggio di errore](../media/how-to-configure-student-usage/cant-find-account.png)

Per iscriversi a un account Microsoft, [http://signup.live.com](http://signup.live.com)gli istruttori devono accedere a .  


### <a name="using-a-github-account"></a>Utilizzo di un account GitHubUsing a GitHub Account
Gli istruttori possono anche usare un account GitHub esistente per registrarsi e accedere a un laboratorio della classe. Se l'istruttore ha già un account Microsoft collegato al proprio account GitHub, può accedere e fornire la password come illustrato nella sezione precedente. Se non hanno ancora collegato il proprio account GitHub a un account Microsoft, devono selezionare **Opzioni di accesso:**

![Collegamento Opzioni di accesso](../media/how-to-configure-student-usage/signin-options.png)

Nella pagina **Opzioni di** accesso selezionare Accedi **con GitHub**.

![Accedi con il collegamento GitHub](../media/how-to-configure-student-usage/signin-github.png)

Infine, viene richiesto di creare un account Microsoft collegato al proprio account GitHub.Finally, they are prompted to create a Microsoft account that's linked to their GitHub account. Succede automaticamente quando l'istruttore seleziona **Avanti**.  L'istruttore viene quindi immediatamente connesso e collegato al laboratorio della classe.


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)
