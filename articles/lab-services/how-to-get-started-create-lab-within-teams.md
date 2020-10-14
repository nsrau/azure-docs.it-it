---
title: Per iniziare, creare un Lab Azure Lab Services dai team
description: Scopri come iniziare a creare un Lab Azure Lab Services dai team.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 0604e2934ff6b011acfa9dd4a4b25fa58193e69b
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044446"
---
# <a name="get-started-and-create-a-lab-services-lab-from-teams"></a>Per iniziare, creare un Lab di Lab Services dai team

Questo articolo illustra come aggiungere l'app **Azure Lab Services** a un team e come creare un Lab nell'ambiente MS teams.

## <a name="prerequisites"></a>Prerequisiti

In questa esercitazione viene configurato un Lab con macchine virtuali per il team. Per configurare un Lab in un account Lab, è necessario essere un membro di uno di questi ruoli nell'account Lab: Owner, Lab Creator o collaboratore. L'account usato per creare un account del lab viene aggiunto automaticamente al ruolo di Proprietario. Quindi, è possibile usare l'account utente usato per creare un account Lab per creare un Lab.

Di seguito è riportato il flusso di lavoro tipico quando si usa Azure Lab Services nei team

1. L'utente [Crea un account Lab](tutorial-setup-lab-account.md#create-a-lab-account) nel portale di Azure.
1. Un [creatore di account Lab aggiunge altri utenti](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) al ruolo **Lab Creator** . Ad esempio, l'autore/amministratore dell'account lab aggiunge docenti al ruolo di **Autore del laboratorio** in modo che possano creare lab per le classi.
1. Quindi, i docenti creano Labs, pre-configura la macchina virtuale del modello e pubblica il Lab per eseguire il provisioning delle macchine virtuali a tutti gli utenti del team.
1. Dopo la pubblicazione del Lab, una macchina virtuale viene assegnata a tutti gli utenti nell'elenco di appartenenza al team nel primo accesso per Azure Lab Services, facendo clic sulla scheda contenente **Azure Lab Services** app all'interno di Team (SSO) o accedendo al [sito Web Labs](https://labs.azure.com). Gli utenti possono quindi usare la macchina virtuale per svolgere le attività e i compiti.

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>Aggiungere Azure Lab Services app come scheda a un team

Il proprietario del team può aggiungere **Azure Lab Services** app direttamente nei canali dei team, quindi l'app è disponibile per l'uso da parte di tutti gli utenti del team. Attenersi ai tre passaggi seguenti:

1. Passare al canale teams in cui si vuole aggiungere l'app e selezionare **+** per aggiungere una scheda. 
1. Cercare **Azure Lab Services** dalla scheda Opzioni e aggiungere questa app. 

    > [!NOTE]
    > Solo i **proprietari** del team potranno creare laboratori per il team.
1. Selezionare un account di Lab Services che si desidera utilizzare per la creazione di Lab in aula in questo team. 

    Azure Lab Services USA Single Sign-On nel [sito web Azure Lab Services](https://labs.azure.com) ed esegue il pull di tutti gli account Lab a cui si ha accesso. 

    Vengono visualizzati gli account che si trovano nello stesso tenant dei team e per i quali si dispone dell'accesso **proprietario**, **collaboratore**o **autore** . 

   ![Introduzione a ALS](./media/integrate-with-teams/welcome.png) 
1. Premere **Save** e la scheda viene aggiunta al canale.

    È ora possibile selezionare la scheda **Azure Lab Services** dal canale e iniziare a gestire i Lab come descritto nel passaggio seguente.

Dopo aver selezionato l'account Lab, i proprietari del team saranno in grado di creare Lab per il team. L'intero processo di creazione del Lab e tutte le attività a livello di Lab possono essere eseguite all'interno dei team. Gli utenti avranno la possibilità di creare più Lab all'interno dello stesso team e del proprietario del team, con l'accesso appropriato a livello di account Lab, visualizzeranno solo i Lab associati al team specifico.

## <a name="deleting-classroom-labs"></a>Eliminazione di Lab in aula

Un Lab creato in teams può essere eliminato nel [sito Web di Lab Services](https://labs.azure.com) eliminando direttamente il Lab, come descritto in gestire i Lab [della classe in Azure Lab Services](how-to-manage-classroom-labs.md). 

L'eliminazione del Lab viene attivata anche quando il team viene eliminato. Se il team in cui viene creato il Lab viene eliminato, Lab verrebbe eliminato automaticamente 24 ore dopo l'attivazione della sincronizzazione automatica degli elenchi di utenti. 

L'eliminazione della scheda o la disinstallazione dell'app non comporterà l'eliminazione del Lab. Se la scheda viene eliminata, gli utenti nell'elenco di appartenenza al team potranno comunque accedere alle macchine virtuali nel [sito Web di Lab Services](https://labs.azure.com) , a meno che l'eliminazione del Lab non venga attivata in modo esplicito eliminando il Lab nel sito Web o eliminando il team. 

## <a name="next-steps"></a>Passaggi successivi

Quando viene creato un Lab all'interno dei team, l'elenco degli utenti del Lab viene automaticamente popolato e sincronizzato con l'appartenenza al team. Tutti i membri del team, inclusi i proprietari, i membri e i guest, verranno aggiunti automaticamente all'elenco degli utenti del Lab. Azure Lab Services manterrà una sincronizzazione con l'appartenenza al team e viene attivata una sincronizzazione automatica ogni 24 ore. Per informazioni dettagliate, vedere:

[Gestire gli elenchi utenti di Lab Services dai team](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Vedere anche

Vedere anche gli articoli seguenti:

- [Usare Azure Lab Services in teams Overview](lab-services-within-teams-overview.md)
- [Gestire gli elenchi degli utenti del Lab nei team](how-to-manage-user-lists-within-teams.md)
- [Gestisci il pool di macchine virtuali del Lab in team](how-to-manage-vm-pool-within-teams.md)
- [Creare e gestire pianificazioni Lab all'interno di Team](how-to-create-schedules-within-teams.md)
- [Accedere a una macchina virtuale all'interno di teams-visualizzazione studente](how-to-access-vm-for-students-within-teams.md)

