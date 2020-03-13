---
title: Gestire account lab in Azure Lab Services | Microsoft Docs
description: Informazioni su come creare un account lab, visualizzare tutti gli account lab o eliminare un account lab in una sottoscrizione di Azure.
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
ms.openlocfilehash: c82b5d02ab3928eb0472f2a047cdca2238bf0b63
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284291"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Gestire account lab in Azure Lab Services 
In Azure Lab Services, un account Lab è un contenitore per i tipi Lab gestiti, ad esempio Lab in aula. Un amministratore configura un account lab con Azure Lab Services e fornisce l'accesso ai proprietari del lab autorizzati a creare lab nell'account. Questo articolo descrive come creare un account lab, visualizzare tutti gli account lab o eliminare un account lab.

## <a name="create-a-lab-account"></a>Creare un account lab
La procedura seguente illustra come usare il portale di Azure per creare un account lab con Azure Lab Services. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutti i servizi** dal menu a sinistra. Selezionare **account Lab** nella sezione **DevOps** . Se si seleziona l'asterisco (`*`) accanto ad **Account Lab**, l'opzione verrà aggiunta alla sezione **PREFERITI** nel menu a sinistra. Da questo momento in poi, selezionare **Account Lab** in **PREFERITI**.

    ![Tutti i servizi -> Account Lab](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Nella pagina **account Lab** selezionare **Aggiungi** sulla barra degli strumenti o **Crea account Lab** nella pagina. 

    ![Selezionare Aggiungi nella pagina Account Lab](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Nella scheda **Informazioni di base** della pagina **Crea un account lab** eseguire le operazioni seguenti: 
    1. In **Lab account name** (Nome account lab) immettere un nome. 
    2. Selezionare la **sottoscrizione di Azure** in cui creare l'account lab.
    3. In **Gruppo di risorse** selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse.
    4. In **Posizione** selezionare una posizione o un'area in cui si vuole creare l'account lab.
    5. Per il campo **Consenti all'autore del lab di selezionare la località del lab**, specificare se si vuole consentire agli autori di selezionare una località per il lab. L'opzione è disabilitata per impostazione predefinita. Quando l'opzione è disattivata, gli autori del lab non possono specificare una località per il lab che si sta creando. I lab vengono creati nella località geografica più vicina all'account lab. Quando l'opzione è abilitata, un autore può selezionare una località al momento della creazione di un lab. Per altre informazioni, vedere [Allow Lab Creators to pick location for the Lab](allow-lab-creator-pick-lab-location.md). 

        ![Crea account Lab-nozioni di base >](../media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Selezionare **Avanti: avanzate** nella parte inferiore della pagina per passare alla scheda **Avanzate** , quindi seguire questa procedura: 
    1. Selezionare una **raccolta immagini condivisa** esistente o crearne una. È possibile salvare la macchina virtuale modello nella raccolta immagini condivisa per poter essere riutilizzata da altri utenti. Per informazioni dettagliate sulle raccolte di immagini condivisa, vedere [Use a shared image gallery in Azure Lab Services](how-to-use-shared-image-gallery.md) (Usare una raccolta immagini condivisa in Azure Lab Services).
    2. Specificare se si desidera **arrestare automaticamente le macchine virtuali Windows** quando gli utenti si disconnettono da essi. Specificare per quanto tempo le macchine virtuali devono attendere che l'utente si riconnetta prima dell'arresto automatico. 
    3. Per **Rete virtuale peer** selezionare una rete virtuale peer per la rete lab. I lab creati in questo account sono connessi alla rete virtuale selezionata e hanno accesso alle risorse nella rete virtuale selezionata. Per altre informazioni, vedere [connettere la rete virtuale del Lab a una rete virtuale peer](how-to-connect-peer-virtual-network.md).    
    8. Specificare un **intervallo di indirizzi** per le macchine virtuali nel lab. L'intervallo di indirizzi deve essere nella notazione CIDR (Inter-Domain Routing) di classe (ad esempio: 10.20.0.0/23). Le macchine virtuali nel lab verranno create in questo intervallo di indirizzi. Per altre informazioni, vedere [specificare un intervallo di indirizzi per le macchine virtuali nel Lab](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab)  

        ![Crea account Lab-> Advanced](../media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Selezionare **Next: Tags** nella parte inferiore della pagina per passare alla scheda **tag** . aggiungere eventuali tag da associare all'account Lab. I tag sono coppie nome/valore che consentono di classificare le risorse e visualizzare la fatturazione consolidata applicando lo stesso tag a più risorse e gruppi di risorse. Per altre informazioni, vedere [Usare tag per organizzare le risorse di Azure](../../azure-resource-manager/management/tag-resources.md).

    ![Crea account Lab-Tag >](../media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Selezionare **Verifica + crea** nella parte inferiore della pagina per passare alla scheda **Verifica + crea** . 
4. Esaminare le informazioni di riepilogo in questa pagina e selezionare **Crea**. 

    ![Crea account Lab-Tag >](../media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Attendere il completamento della distribuzione, espandere **passaggi successivi**e selezionare **Vai alla risorsa** , come illustrato nell'immagine seguente: 

    È anche possibile selezionare l' **icona a campana** sulla barra degli strumenti (**notifiche**), verificare che la distribuzione abbia avuto esito positivo e quindi selezionare **Vai alla risorsa**. 

    In alternativa, selezionare **Aggiorna** nella pagina **Account Lab** e selezionare l'account lab creato. 

    ![Finestra Create a lab account (Crea un account lab)](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Verrà visualizzata la pagina dell'**account lab** seguente:

    ![Pagina dell'account lab](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Visualizzare gli account lab
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutte le risorse** dal menu. 
3. Selezionare **Account Lab** per il **tipo**. 
    È anche possibile filtrare per sottoscrizione, gruppo di risorse, posizioni e tag. 

    ![Tutte le risorse -> Account Lab](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Visualizzare e gestire i lab nell'account lab

1. Nella pagina **account Lab** selezionare tutti i **Lab** nel menu a sinistra.

    ![Lab nell'account](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Viene visualizzato un **elenco di lab** nell'account con le informazioni seguenti: 
    1. Nome del lab.
    2. Data di creazione del lab. 
    3. Indirizzo di posta elettronica dell'utente che ha creato il lab. 
    4. Numero massimo di utenti consentiti nel lab. 
    5. Stato del lab. 
    6. Assegnazioni di ruoli. 

## <a name="delete-a-lab-in-the-lab-account"></a>Eliminare un lab nell'account lab
Per visualizzare un elenco dei lab nell'account lab, seguire le istruzioni nella sezione precedente.

1. Selezionare **...** (puntini di sospensione) e quindi **Elimina**. 

    ![Pulsante Elimina un lab](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Selezionare **Sì** nel messaggio di avviso. 

    ![Conferma dell'eliminazione del lab](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Eliminare un account lab
Seguire le istruzioni della sezione precedente che visualizza gli account lab in un elenco. Per eliminare un account lab seguire questa procedura: 

1. Selezionare l'**account lab** da eliminare. 
2. Selezionare **Elimina** dalla barra degli strumenti. 

    ![Account Lab -> Pulsante Elimina](../media/how-to-manage-lab-accounts/delete-button.png)
1. Digitare **Sì** per confermare.
1. Selezionare **Elimina**. 

    ![Eliminazione dell'account lab - Conferma](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> È anche possibile usare il modulo di PowerShell AZ. LabServices (anteprima) per gestire gli account Lab. Per ulteriori informazioni, vedere [AZ. LabServices Home page su GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Consenti a Lab Creator di selezionare la posizione del Lab](allow-lab-creator-pick-lab-location.md)
- [Connettere la rete del Lab a una rete virtuale peer](how-to-connect-peer-virtual-network.md)
- [Alleghi una raccolta di immagini condivise a un Lab](how-to-attach-detach-shared-image-gallery.md)
- [Aggiungere un utente come proprietario del Lab](how-to-add-user-lab-owner.md)
- [Visualizzare le impostazioni del firewall per un Lab](how-to-configure-firewall-settings.md)
- [Configurare altre impostazioni per un Lab](how-to-configure-lab-accounts.md)