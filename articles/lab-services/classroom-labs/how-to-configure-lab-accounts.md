---
title: Configurare gli account del lab in Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare un account lab dopo averlo creato.
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
ms.openlocfilehash: ba469c038f04a31a57e798b97b5120bec573feae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65414050"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Configurare gli account del lab in Azure Lab Services 
In Azure Lab Services, un account del lab è un contenitore per i tipi di lab gestiti, ad esempio labs in aula. Un amministratore configura un account lab con Azure Lab Services e fornisce l'accesso ai proprietari del lab autorizzati a creare lab nell'account. Questo articolo descrive come creare un account lab, visualizzare tutti gli account lab o eliminare un account lab.

## <a name="connect-with-a-peer-virtual-network"></a>Connettersi con una rete virtuale peer
Per connettere una rete virtuale come rete di peer a rete virtuale del lab, seguire questi passaggi:

1. Nel **Account del Lab** pagina, selezionare **configurazione Labs** nel menu a sinistra.

    ![Pagina di configurazione del lab](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. Per la **rete virtuale Peer**, selezionare **Enabled** oppure **disabilitato**. Il valore predefinito è **disabilitato**. Per abilitare la rete virtuale peer, procedere come segue: 
    1. Selezionare **Enabled**.
    2. Selezionare il **VNet** nell'elenco a discesa. 
3. Sulla barra degli strumenti selezionare **Salva**. 

Lab creato in questo account connessi alla rete virtuale selezionata. Possono accedere alle risorse nella rete virtuale selezionata. Per altre informazioni, vedere [connettere la rete del lab con una rete virtuale peer in Azure Lab Services](how-to-connect-peer-virtual-network.md).

Quando si seleziona una rete virtuale per il **rete virtuale Peer** campo, il **Consenti creatore di lab selezionare percorsi lab** opzione è disabilitata. È perché lab con l'account del lab deve essere nella stessa area come account del lab per consentirle di connettersi con le risorse nella rete virtuale peer. 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>Consenti creatore di lab selezionare percorso per il lab
È possibile consentire creatore di lab da creare lab in un percorso diverso da quello dell'account del lab seguendo questa procedura: 

1. Nel **Account del Lab** pagina, selezionare **configurazione Labs** nel menu a sinistra.
2. Per il **Consenti creatore di lab selezionare percorsi lab**, selezionare **abilitato** se si desidera che il creatore di lab sia in grado di selezionare un percorso per l'ambiente lab. Se è disabilitato, i laboratori vengono creati automaticamente nello stesso percorso in cui si trova l'account del lab. 
    
    Questo campo viene disabilitato quando si seleziona una rete virtuale per il **rete virtuale Peer** campo. È perché lab con l'account del lab deve essere nella stessa area come account del lab per poter accedere alle risorse nella rete virtuale peer. 
1. Sulla barra degli strumenti selezionare **Salva**. 

    ![Configurare l'impostazione del percorso lab](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Specificare un intervallo di indirizzi per le macchine virtuali nel lab
La procedura seguente include i passaggi per specificare un intervallo di indirizzi per le macchine virtuali nel lab. Se si aggiorna l'intervallo è specificato in precedenza, l'intervallo di indirizzi modifica si applica solo alle macchine virtuali che vengono create dopo che è stata apportata la modifica. 

Ecco alcune limitazioni quando si specifica l'intervallo di indirizzi è necessario tenere in considerazione. 

- Il prefisso deve essere minore o uguale a 23. 
- Se viene eseguito il peering di una rete virtuale per l'account del lab, l'intervallo di indirizzi specificati non può sovrapporsi all'intervallo di indirizzi da rete virtuale con peering.

1. Nel **Account del Lab** pagina, selezionare **configurazione Labs** nel menu a sinistra.
2. Per il **intervallo di indirizzi** , specificare l'intervallo di indirizzi per le macchine virtuali che verranno create nell'ambiente di laboratorio. L'intervallo di indirizzi deve essere nella notazione CIDR (Classless Interdomain Routing). Ad esempio: 10.20.0.0/23. Le macchine virtuali nel lab verranno create in questo intervallo di indirizzi.
3. Sulla barra degli strumenti selezionare **Salva**. 

    ![Configurare l'intervallo di indirizzi](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Aggiungere un utente al ruolo di creatore di lab
Per configurare un lab per le classi in un account del lab, l'utente deve essere un membro del ruolo **Autore di laboratori** nell'account del lab. L'account usato per creare l'account del lab viene automaticamente aggiunto a questo ruolo. Se si prevede di usare lo stesso account utente per creare un lab per le classi, è possibile ignorare questo passaggio. Per usare un altro account utente per creare un lab per le classi, procedere come segue: 

Per fornire ai docenti l'autorizzazione per creare lab per le proprie classi, aggiungerli al ruolo **Autore di laboratori**:

1. Nella pagina **Account Lab** selezionare **Controllo di accesso (IAM)** e fare clic su **+ Aggiungi assegnazione di ruolo** sulla barra degli strumenti. 

    ![Fare clic su Controllo di accesso -> Aggiungi un'assegnazione di ruolo](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Nella pagina **Aggiungi un'assegnazione di ruolo** selezionare **Autore di lab** per **Ruolo**, scegliere l'utente che si vuole aggiungere a questo ruolo e fare clic su **Salva**. 

    ![Aggiungere l'autore del lab](../media/tutorial-setup-lab-account/add-lab-creator.png)

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




## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)
