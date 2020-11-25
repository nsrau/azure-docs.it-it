---
title: Gestire account lab in Azure Lab Services | Microsoft Docs
description: Informazioni su come creare un account lab, visualizzare tutti gli account lab o eliminare un account lab in una sottoscrizione di Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 33e5e42f65fdd34bb37b12947b5173700ad7970c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999502"
---
# <a name="create-and-manage-lab-accounts"></a>Creare e gestire account di lab
In Azure Lab Services un account lab è un contenitore per tipi di lab gestiti, ad esempio, laboratori di classe. Un amministratore configura un account lab con Azure Lab Services e fornisce l'accesso ai proprietari del lab autorizzati a creare lab nell'account. Questo articolo descrive come creare un account lab, visualizzare tutti gli account lab o eliminare un account lab.

## <a name="create-a-lab-account"></a>Creare un account lab
La procedura seguente illustra come usare il portale di Azure per creare un account lab con Azure Lab Services. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutti i servizi** dal menu a sinistra. Selezionare **Account Lab** nella sezione **DevOps**. Se si seleziona l'asterisco (`*`) accanto ad **Account Lab**, l'opzione verrà aggiunta alla sezione **PREFERITI** nel menu a sinistra. Da questo momento in poi, selezionare **Account Lab** in **PREFERITI**.

    ![Tutti i servizi -> Account Lab](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Nella pagina **Account Lab** selezionare **Aggiungi** sulla barra degli strumenti oppure selezionare **Crea un account lab** nella pagina. 

    ![Selezionare Aggiungi nella pagina Account Lab](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Nella scheda **Informazioni di base** della pagina **Crea un account lab** eseguire le operazioni seguenti: 
    1. In **Lab account name** (Nome account lab) immettere un nome. 
    2. Selezionare la **sottoscrizione di Azure** in cui creare l'account lab.
    3. In **Gruppo di risorse** selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse.
    4. In **Posizione** selezionare una posizione o un'area in cui si vuole creare l'account lab.
    5. Per il campo **Consenti all'autore del lab di selezionare la località del lab**, specificare se si vuole consentire agli autori di selezionare una località per il lab. L'opzione è disabilitata per impostazione predefinita. Quando l'opzione è disattivata, gli autori del lab non possono specificare una località per il lab che si sta creando. I lab vengono creati nella località geografica più vicina all'account lab. Quando l'opzione è abilitata, un autore può selezionare una località al momento della creazione di un lab. Per altre informazioni, vedere [Consentire all'autore del lab di selezionare la posizione per il lab](allow-lab-creator-pick-lab-location.md). 

        ![Crea un account lab > impostazioni di base](./media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Selezionare **Avanti: Avanzate** nella parte inferiore della pagina per passare alla scheda **Avanzate** e quindi seguire questa procedura: 
    1. Selezionare una **raccolta immagini condivisa** esistente o crearne una. È possibile salvare la macchina virtuale modello nella raccolta immagini condivisa per poter essere riutilizzata da altri utenti. Per informazioni dettagliate sulle raccolte di immagini condivisa, vedere [Use a shared image gallery in Azure Lab Services](how-to-use-shared-image-gallery.md) (Usare una raccolta immagini condivisa in Azure Lab Services).
    2. Specificare se si desidera **arrestare automaticamente le macchine virtuali Windows** quando gli utenti si disconnettono. Specificare per quanto tempo le macchine virtuali devono attendere che l'utente si riconnetta prima di arrestarsi automaticamente. 
    3. Per **Rete virtuale peer** selezionare una rete virtuale peer per la rete lab. I lab creati in questo account sono connessi alla rete virtuale selezionata e hanno accesso alle risorse nella rete virtuale selezionata. Per altre informazioni, vedere [Connettere la rete virtuale del lab con una rete virtuale peer](how-to-connect-peer-virtual-network.md).    
    8. Specificare un **intervallo di indirizzi** per le macchine virtuali nel lab. L'intervallo di indirizzi deve essere nella notazione CIDR (Classless Interdomain Routing). Ad esempio: 10.20.0.0/23. Le macchine virtuali nel lab verranno create in questo intervallo di indirizzi. Per altre informazioni, vedere [Specificare un intervallo di indirizzi per le macchine virtuali nel lab](how-to-connect-peer-virtual-network.md#specify-an-address-range-for-vms-in-the-lab-account)  

        > [!NOTE]
        > La proprietà relativa all'**intervallo di indirizzi** si applica solo se per il lab è abilitata una **rete virtuale peer**.

        ![Crea un account lab > impostazioni avanzate](./media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Selezionare **Avanti: Tag** nella parte inferiore della pagina per passare alla scheda **Tag**. Aggiungere i tag da associare all'account lab. I tag sono coppie nome-valore che consentono di classificare le risorse e visualizzare dati di fatturazione consolidati tramite l'applicazione dello stesso tag a più risorse e gruppi di risorse. Per altre informazioni, vedere [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md).

    ![Screenshot che mostra la pagina "Crea account Lab" con la scheda Tag evidenziata.](./media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Selezionare **Rivedi e crea** nella parte inferiore della pagina per passare alla scheda **Rivedi e crea**. 
4. Esaminare le informazioni di riepilogo in questa pagina e selezionare **Crea**. 

    ![Crea un account lab > Tag](./media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Attendere finché non è completata la distribuzione, espandere **Passaggi successivi** e selezionare **Vai alla risorsa**, come mostrato nell'immagine seguente: 

    È anche possibile selezionare l'**icona del campanello** sulla barra degli strumenti (**Notifiche**), verificare se la distribuzione è riuscita e quindi selezionare **Vai alla risorsa**. 

    In alternativa, selezionare **Aggiorna** nella pagina **Account Lab** e selezionare l'account lab creato. 

    ![Finestra Create a lab account (Crea un account lab)](./media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Verrà visualizzata la pagina dell'**account lab** seguente:

    ![Pagina dell'account lab](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Visualizzare gli account lab
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutte le risorse** dal menu. 
3. Selezionare **Account Lab** per il **tipo**. 
    È anche possibile filtrare per sottoscrizione, gruppo di risorse, posizioni e tag. 

    ![Tutte le risorse -> Account Lab](./media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>Eliminare un account lab
Seguire le istruzioni della sezione precedente che visualizza gli account lab in un elenco. Per eliminare un account lab seguire questa procedura: 

1. Selezionare l'**account lab** da eliminare. 
2. Selezionare **Elimina** dalla barra degli strumenti. 

    ![Account Lab -> Pulsante Elimina](./media/how-to-manage-lab-accounts/delete-button.png)
1. Digitare **Sì** per confermare.
1. Selezionare **Elimina**. 

    ![Eliminazione dell'account lab - Conferma](./media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Per gestire gli account lab è anche possibile usare il modulo di PowerShell Az.LabServices (anteprima). Per altre informazioni, vedere la [home page di Az.LabServices su GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli nella sezione del sommario **Guide pratiche** -> **Creare e configurare account lab (proprietario di account lab)** . 