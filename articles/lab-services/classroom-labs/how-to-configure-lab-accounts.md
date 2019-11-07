---
title: Configurare gli account Lab in Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare un account Lab dopo che è stato creato.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 9faf7de25b8bf227dd4c7f3588972f98f7493439
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73583975"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Configurare gli account Lab in Azure Lab Services 
In Azure Lab Services, un account Lab è un contenitore per i tipi Lab gestiti, ad esempio Lab in aula. Un amministratore configura un account lab con Azure Lab Services e fornisce l'accesso ai proprietari del lab autorizzati a creare lab nell'account. Questo articolo descrive come creare un account lab, visualizzare tutti gli account lab o eliminare un account lab.

## <a name="connect-with-a-peer-virtual-network"></a>Connettersi con una rete virtuale peer
Per connettere una rete virtuale come rete peer alla rete virtuale del Lab, seguire questa procedura:

1. Nella pagina **account Lab** selezionare Lab **Configuration** nel menu a sinistra.

    ![Pagina di configurazione Labs](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. Per **rete virtuale peer**selezionare **abilitato** o **disabilitato**. Il valore predefinito è **disabled**. Per abilitare la rete virtuale peer, seguire questa procedura: 
    1. Selezionare **Enabled**.
    2. Selezionare **VNet** dall'elenco a discesa. 
3. Sulla barra degli strumenti selezionare **Salva**. 

I Lab creati in questo account sono connessi alla rete virtuale selezionata. Possono accedere alle risorse nella rete virtuale selezionata. Per altre informazioni, vedere [connettere la rete del Lab a una rete virtuale peer in Azure Lab Services](how-to-connect-peer-virtual-network.md).

Quando si seleziona una rete virtuale per il campo **rete virtuale peer** , l'opzione **Consenti al creatore Lab di selezionare il percorso Lab** è disabilitata. Il motivo è che i Lab nell'account lab devono trovarsi nella stessa area dell'account Lab per connettersi alle risorse nella rete virtuale peer. 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>Consenti a Lab Creator di selezionare la posizione per il Lab
È possibile consentire a Lab Creator di creare Lab in un percorso diverso rispetto alla posizione dell'account Lab attenendosi alla procedura seguente: 

1. Nella pagina **account Lab** selezionare Lab **Configuration** nel menu a sinistra.
2. Per consentire al Lab **Creator di**selezionare la posizione del Lab, selezionare **abilitato** se si vuole che l'autore del Lab possa selezionare un percorso per il Lab. Se è disabilitato, i Lab vengono creati automaticamente nella stessa posizione in cui è presente l'account Lab. 
    
    Questo campo è disabilitato quando si seleziona una rete virtuale per il campo **rete virtuale peer** . Il motivo è che i Lab nell'account lab devono trovarsi nella stessa area dell'account Lab per accedere alle risorse nella rete virtuale peer. 
1. Sulla barra degli strumenti selezionare **Salva**. 

    ![Configurare l'impostazione del percorso Lab](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Specificare un intervallo di indirizzi per le macchine virtuali nel Lab
La procedura seguente include i passaggi per specificare un intervallo di indirizzi per le macchine virtuali nel Lab. Se si aggiorna l'intervallo specificato in precedenza, l'intervallo di indirizzi modificato si applica solo alle macchine virtuali create dopo che è stata apportata la modifica. 

Di seguito sono riportate alcune restrizioni per specificare l'intervallo di indirizzi da tenere in considerazione. 

- Il prefisso deve essere minore o uguale a 23. 
- Se una rete virtuale viene associata all'account Lab, l'intervallo di indirizzi specificato non può sovrapporsi all'intervallo di indirizzi dalla rete virtuale con peering.

1. Nella pagina **account Lab** selezionare Lab **Configuration** nel menu a sinistra.
2. Per il campo **intervallo di indirizzi** specificare l'intervallo di indirizzi per le macchine virtuali che verranno create nel Lab. L'intervallo di indirizzi deve essere nella notazione CIDR (Inter-Domain Routing) di classe (ad esempio: 10.20.0.0/23). Le macchine virtuali nel lab verranno create in questo intervallo di indirizzi.
3. Sulla barra degli strumenti selezionare **Salva**. 

    ![Configura intervallo di indirizzi](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Aggiungere un utente al ruolo di creatore di lab
Per configurare un lab per le classi in un account del lab, l'utente deve essere un membro del ruolo **Autore di laboratori** nell'account del lab. L'account usato per creare l'account del lab viene automaticamente aggiunto a questo ruolo. Se si prevede di usare lo stesso account utente per creare un lab per le classi, è possibile ignorare questo passaggio. Per usare un altro account utente per creare un lab per le classi, procedere come segue: 

Per fornire ai docenti l'autorizzazione per creare lab per le proprie classi, aggiungerli al ruolo **Autore di laboratori**:

1. Nella pagina **Account Lab** selezionare **Controllo di accesso (IAM)** e fare clic su **+ Aggiungi assegnazione di ruolo** sulla barra degli strumenti. 

    ![Fare clic su Controllo di accesso -> Aggiungi un'assegnazione di ruolo](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Nella pagina **Aggiungi un'assegnazione di ruolo** selezionare **Autore di lab** per **Ruolo**, scegliere l'utente che si vuole aggiungere a questo ruolo e fare clic su **Salva**. 

    ![Aggiungere l'autore del lab](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Se si aggiunge un utente non account Microsoft come autore del Lab, vedere la sezione [aggiungere un utente non account Microsoft come autore del Lab](#add-a-non-microsoft-account-user-as-a-lab-creator) . 

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Specificare le immagini del Marketplace disponibili per gli autori di lab
In quanto proprietario di un account del lab, è possibile specificare le immagini del Marketplace che gli autori di lab possono usare per creare lab nell'account del lab. 

1. Selezionare **Immagini del Marketplace** nel menu a sinistra. Per impostazione predefinita, verrà visualizzato l'elenco completo delle immagini (abilitate e disabilitate). È possibile filtrare l'elenco per visualizzare solo le immagini abilitate o disabilitate selezionando l'opzione **Enabled only**/**Disabled only** (Solo abilitate, Solo disabilitate) nell'elenco a discesa nella parte superiore. 
    
    ![Pagina Immagini del Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    Nell'elenco vengono visualizzate solo le immagini del Marketplace che soddisfano le condizioni seguenti:
        
    - Crea una singola macchina virtuale.
    - Usa Azure Resource Manager per il provisioning di macchine virtuali
    - Non richiede l'acquisto di un piano di licenze aggiuntivo
2. Per **disabilitare** un'immagine del Marketplace che è stata abilitata, eseguire una delle azioni seguenti: 
    1. Selezionare **... (puntini di sospensione)** nell'ultima colonna e selezionare **Disable image** (Disabilita immagine). 

        ![Disabilitare un'immagine](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Selezionare una o più immagini nell'elenco selezionando le caselle di controllo prima dei nomi delle immagini nell'elenco e selezionare **Disable selected images** (Disabilita immagini selezionate). 

        ![Disabilitare più immagini](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. Analogamente, per **abilitare** un'immagine del Marketplace, eseguire una delle azioni seguenti: 
    1. Selezionare **... (puntini di sospensione)** nell'ultima colonna e selezionare **Enable image** (Abilita immagine). 
    2. Selezionare una o più immagini nell'elenco selezionando le caselle di controllo prima dei nomi delle immagini nell'elenco e selezionare **Enable selected images** (Abilita immagini selezionate). 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Aggiungere un utente non account Microsoft come autore del Lab
Per aggiungere un utente come autore del Lab, è possibile usare gli account di posta elettronica. Potrebbero essere usati i tipi di account di posta elettronica seguenti:

- Un account di posta elettronica fornito dall'Ufficio 365 Azure Active Directory (AAD) dell'Università. 
- Un account di posta elettronica Microsoft, ad esempio `@outlook.com`, `@hotmail.com`, `@msn.com`o `@live.com`.
- Un account di posta elettronica non Microsoft, ad esempio uno fornito da Yahoo o Google. Tuttavia, questi tipi di account devono essere collegati con un account Microsoft.
- Un account GitHub. Questo account deve essere collegato a un account Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Uso di un account di posta elettronica non Microsoft
Gli autori e gli insegnanti del Lab possono usare account di posta elettronica non Microsoft per registrarsi e accedere a un Lab della classe.  Tuttavia, l'accesso al portale di Lab Services richiede che i docenti creino prima un account Microsoft collegato al proprio indirizzo di posta elettronica non Microsoft.

Molti insegnanti potrebbero già avere un account Microsoft collegato ai propri indirizzi di posta elettronica non Microsoft. Ad esempio, gli insegnanti hanno già un account Microsoft se hanno usato il proprio indirizzo di posta elettronica con altri prodotti o servizi Microsoft, ad esempio Office, Skype, OneDrive o Windows.  

Quando gli insegnanti accedono al portale di Lab Services, viene richiesto di specificare l'indirizzo di posta elettronica e la password. Se il docente tenta di accedere con un non account Microsoft che non dispone di un account Microsoft collegato, l'insegnante riceverà il messaggio di errore seguente: 

![Messaggio di errore](../media/how-to-configure-student-usage/cant-find-account.png)

Per iscriversi a una account Microsoft, gli insegnanti devono passare a [http://signup.live.com](http://signup.live.com).  


### <a name="using-a-github-account"></a>Uso di un account GitHub
Gli insegnanti possono anche usare un account GitHub esistente per registrarsi e accedere a un Lab della classe. Se l'insegnante dispone già di un account Microsoft collegato al proprio account GitHub, può accedere e fornire la password come illustrato nella sezione precedente. Se non hanno ancora collegato il proprio account GitHub a una account Microsoft, devono selezionare le **Opzioni di accesso**:

![Collegamento Opzioni di accesso](../media/how-to-configure-student-usage/signin-options.png)

Nella pagina **Opzioni di accesso** selezionare **Accedi con GitHub**.

![Accedi con collegamento a GitHub](../media/how-to-configure-student-usage/signin-github.png)

Infine, viene richiesto di creare un account Microsoft collegato al proprio account GitHub. Viene eseguita automaticamente quando l'insegnante seleziona **Avanti**.  L'insegnante viene quindi sottoscritto immediatamente e connesso al Lab della classe.


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)
