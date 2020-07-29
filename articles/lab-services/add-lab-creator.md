---
title: Aggiungere un utente come creatore del Lab in Azure Lab Services
description: Questo articolo illustra come aggiungere un utente al ruolo Lab Creator per un account Lab in Azure Lab Services. Gli autori del Lab possono creare Lab all'interno di questo account Lab.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 34fbf9085f36d008607b648825585d3435cc2895
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85444285"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Aggiungi Lab Creators a un account Lab in Azure Lab Services
Questo articolo illustra come aggiungere utenti come autori di Lab a un account Lab in Azure Lab Services. Questi usi possono quindi creare Lab in aula nell'account Lab. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Aggiungere un account utente Microsoft al ruolo di autore del Lab
Per configurare un lab per le classi in un account del lab, l'utente deve essere un membro del ruolo **Autore di laboratori** nell'account del lab. L'account usato per creare l'account del lab viene automaticamente aggiunto a questo ruolo. Se si prevede di usare lo stesso account utente per creare un lab per le classi, è possibile ignorare questo passaggio. Per usare un altro account utente per creare un lab per le classi, procedere come segue: 

Per fornire ai docenti l'autorizzazione per creare lab per le proprie classi, aggiungerli al ruolo **Autore di laboratori**:

1. Nella pagina **Account Lab** selezionare **Controllo di accesso (IAM)** e fare clic su **+ Aggiungi assegnazione di ruolo** sulla barra degli strumenti. 

    ![Fare clic su Controllo di accesso -> Aggiungi un'assegnazione di ruolo](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Nella pagina **Aggiungi un'assegnazione di ruolo** selezionare **Autore di lab** per **Ruolo**, scegliere l'utente che si vuole aggiungere a questo ruolo e fare clic su **Salva**. 

    ![Aggiungere l'autore del lab](./media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Se si aggiunge un utente non account Microsoft come autore del Lab, vedere la sezione [aggiungere un utente non account Microsoft come autore del Lab](#add-a-non-microsoft-account-user-as-a-lab-creator) . 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Aggiungere un utente non account Microsoft come autore del Lab
Per aggiungere un utente come autore del Lab, è possibile usare gli account di posta elettronica. Potrebbero essere usati i tipi di account di posta elettronica seguenti:

- Un account di posta elettronica fornito dall'Ufficio 365 Azure Active Directory (AAD) dell'Università. 
- Un account di posta elettronica Microsoft, ad esempio `@outlook.com` ,, `@hotmail.com` `@msn.com` o `@live.com` .
- Un account di posta elettronica non Microsoft, ad esempio uno fornito da Yahoo o Google. Tuttavia, questi tipi di account devono essere collegati con un account Microsoft.
- Un account GitHub. Questo account deve essere collegato a un account Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Uso di un account di posta elettronica non Microsoft
Gli autori e gli insegnanti del Lab possono usare account di posta elettronica non Microsoft per registrarsi e accedere a un Lab della classe.  Tuttavia, l'accesso al portale di Lab Services richiede che i docenti creino prima un account Microsoft collegato al proprio indirizzo di posta elettronica non Microsoft.

Molti insegnanti potrebbero già avere un account Microsoft collegato ai propri indirizzi di posta elettronica non Microsoft. Ad esempio, gli insegnanti hanno già un account Microsoft se hanno usato il proprio indirizzo di posta elettronica con altri prodotti o servizi Microsoft, ad esempio Office, Skype, OneDrive o Windows.  

Quando gli insegnanti accedono al portale di Lab Services, viene richiesto di specificare l'indirizzo di posta elettronica e la password. Se il docente tenta di accedere con un non account Microsoft che non dispone di un account Microsoft collegato, l'insegnante riceverà il messaggio di errore seguente: 

![Messaggio di errore](./media/how-to-configure-student-usage/cant-find-account.png)

Per iscriversi a una account Microsoft, gli insegnanti devono passare a [http://signup.live.com](http://signup.live.com) .  


### <a name="using-a-github-account"></a>Uso di un account GitHub
Gli insegnanti possono anche usare un account GitHub esistente per registrarsi e accedere a un Lab della classe. Se l'insegnante dispone già di un account Microsoft collegato al proprio account GitHub, può accedere e fornire la password come illustrato nella sezione precedente. Se non hanno ancora collegato il proprio account GitHub a una account Microsoft, devono selezionare le **Opzioni di accesso**:

![Collegamento Opzioni di accesso](./media/how-to-configure-student-usage/signin-options.png)

Nella pagina **Opzioni di accesso** selezionare **Accedi con GitHub**.

![Accedi con collegamento a GitHub](./media/how-to-configure-student-usage/signin-github.png)

Infine, viene richiesto di creare un account Microsoft collegato al proprio account GitHub. Viene eseguita automaticamente quando l'insegnante seleziona **Avanti**.  L'insegnante viene quindi sottoscritto immediatamente e connesso al Lab della classe.


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)
