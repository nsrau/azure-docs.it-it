---
title: Per iniziare, creare un Lab Azure Lab Services all'interno di Team
description: Scopri come iniziare a creare un Lab Azure Lab Services all'interno di un team.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: cc4ad604bdf250cc6e4ba2c50c2f7143c921e906
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433972"
---
# <a name="get-started-and-create-a-lab-services-lab-within-teams"></a>Introduzione e creazione di un Lab di Lab Services nei team

Questo articolo illustra come aggiungere l'app **Azure Lab Services** a un team e come creare un Lab nell'ambiente MS teams.

## <a name="prerequisites"></a>Prerequisiti

In questa esercitazione viene configurato un Lab con macchine virtuali per il team. Per configurare un Lab in un account Lab, è necessario essere un membro di uno di questi ruoli nell'account Lab: Owner, Lab Creator o collaboratore. L'account usato per creare un account del lab viene aggiunto automaticamente al ruolo di Proprietario. Quindi, è possibile usare l'account utente usato per creare un account Lab per creare un Lab.

Di seguito è riportato il flusso di lavoro tipico quando si usa Azure Lab Services nei team

1. L'utente [Crea un account Lab](tutorial-setup-lab-account.md#create-a-lab-account) nel portale di Azure.
1. Un [creatore di account Lab aggiunge altri utenti](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) al ruolo **Lab Creator** . Ad esempio, l'autore/amministratore dell'account lab aggiunge docenti al ruolo di **Autore del laboratorio** in modo che possano creare lab per le classi.
1. Quindi, i docenti creano Labs, pre-configura la macchina virtuale del modello e pubblica il Lab per eseguire il provisioning delle macchine virtuali a tutti gli utenti del team.
1. Dopo la pubblicazione del Lab, una macchina virtuale viene assegnata a tutti gli utenti nell'elenco di appartenenza al team nel primo accesso per Azure Lab Services, facendo clic sulla scheda contenente **Azure Lab Services** app all'interno di Team (SSO) o accedendo al [sito Web Labs](https://labs.azure.com). Gli utenti possono quindi usare la macchina virtuale per svolgere le attività e i compiti.

> [!IMPORTANT]
> Azure Lab Services può essere usato all'interno di team solo se gli account Lab vengono creati nello stesso tenant dei team.

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>Aggiungere Azure Lab Services app come scheda a un team

Il proprietario del team può aggiungere **Azure Lab Services** app direttamente nei canali dei team, quindi l'app è disponibile per l'uso da parte di tutti gli utenti del team. Attenersi ai tre passaggi seguenti:

1. Passare al canale teams in cui si vuole aggiungere l'app e selezionare **+** per aggiungere una scheda. 
1. Cercare **Azure Lab Services** dalla scheda Opzioni e aggiungere questa app. 

    > [!NOTE]
    > Solo i **proprietari** del team potranno creare laboratori per il team.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/add.png" alt-text="Aggiungere una scheda":::
1. Selezionare un account di Lab Services che si desidera utilizzare per la creazione di Lab in questo team. 

    Azure Lab Services USA Single Sign-On nel [sito web Azure Lab Services](https://labs.azure.com) ed esegue il pull di tutti gli account Lab a cui si ha accesso. 

    Vengono visualizzati gli account che si trovano nello stesso tenant dei team e per i quali si dispone dell'accesso **proprietario**, **collaboratore** o **autore** . 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/welcome.png" alt-text="Introduzione a ALS":::
1. Premere **Save** e la scheda viene aggiunta al canale.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/created.png" alt-text="Creazione della scheda ALS":::

    È ora possibile selezionare la scheda **Azure Lab Services** dal canale e iniziare a gestire i Lab come descritto negli articoli seguenti.

Dopo aver selezionato l'account Lab, i proprietari del team saranno in grado di creare Lab per il team. L'intero processo di creazione del Lab e tutte le attività a livello di Lab possono essere eseguite all'interno dei team. Gli utenti avranno la possibilità di creare più Lab all'interno dello stesso team e del proprietario del team, con l'accesso appropriato a livello di account Lab, visualizzeranno solo i Lab associati al team specifico.

## <a name="next-steps"></a>Passaggi successivi

Quando viene creato un Lab all'interno dei team, l'elenco degli utenti del Lab viene automaticamente popolato e sincronizzato con l'appartenenza al team. Tutti i membri del team, inclusi i proprietari, i membri e i guest, verranno aggiunti automaticamente all'elenco degli utenti del Lab. Azure Lab Services manterrà una sincronizzazione con l'appartenenza al team e viene attivata una sincronizzazione automatica ogni 24 ore. Per informazioni dettagliate, vedere:

[Gestire gli elenchi utenti di Lab Services nei team](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Vedi anche

Vedere anche gli articoli seguenti:

- [Usare Azure Lab Services in teams Overview](lab-services-within-teams-overview.md)
- [Gestisci il pool di macchine virtuali del Lab in team](how-to-manage-vm-pool-within-teams.md)
- [Creare e gestire pianificazioni Lab all'interno di Team](how-to-create-schedules-within-teams.md)
- [Accedere a una macchina virtuale all'interno di teams-visualizzazione studente](how-to-access-vm-for-students-within-teams.md)
- [Elimina Lab all'interno di Team](how-to-delete-lab-within-teams.md)
