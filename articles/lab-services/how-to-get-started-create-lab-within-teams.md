---
title: Per iniziare, creare un Lab Azure Lab Services dai team
description: Scopri come iniziare a creare un Lab Azure Lab Services dai team.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: b585196fe61a09697cfa203aaa33f08afae2b427
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946741"
---
# <a name="get-started-and-create-a-lab-services-lab-from-teams"></a>Per iniziare, creare un Lab di Lab Services dai team

Questo articolo illustra come aggiungere un'app Azure Lab Services ai team. Quindi, come creare un Lab dai team.

## <a name="add-a-lab-services-app-to-teams"></a>Aggiungere un'app di Lab Services ai team

È possibile aggiungere Lab Services direttamente nei canali dei team, quindi l'app è disponibile per tutti gli utenti del team da usare. Eseguire questi tre passaggi:

1. Passare al canale teams in cui si vuole aggiungere l'app e selezionare **+** per aggiungere una scheda facendo clic su "..." dalla parte superiore della finestra a destra. 
1. Cercare **Azure Lab Services** dalla scheda Opzioni e aggiungere questa app. 

    > [!NOTE]
    > Solo i **proprietari** del team potranno creare laboratori per il team.
1. Selezionare un account di Lab Services che si desidera utilizzare per la creazione di Lab in aula in questo team. 

    Azure Lab Services USA Single Sign-On nel [sito web Azure Lab Services](https://labs.azure.com) ed esegue il pull di tutti gli account Lab a cui si ha accesso. 

    Vengono visualizzati gli account che si trovano nello stesso tenant dei team e per i quali si dispone dell'accesso **proprietario**, **collaboratore**o **autore** . 

   ![Introduzione a ALS](./media/integrate-with-teams/welcome.png) 
1. Premere **Save** e l'app viene aggiunta ai team e la scheda viene aggiunta al canale. 

    È ora possibile selezionare la scheda **Azure Lab Services** dal canale e iniziare a gestire i Lab come descritto nel passaggio seguente.

    La scheda, dopo che è stata aggiunta da un membro di un team, viene visualizzata da tutti nel canale. Tutti gli utenti che hanno accesso all'app ottengono l'accesso Single Sign-On con le credenziali usate per Microsoft Teams. Gli utenti che non hanno accesso all'app visualizzeranno la scheda in Teams, ma saranno bloccati finché non verranno loro concesse le autorizzazioni per l'app locale e la versione pubblicata nel portale di Azure dell'app.

## <a name="create-a-classroom-lab"></a>Creare un lab per le classi

Dopo aver selezionato l'account Lab, i proprietari del team saranno in grado di creare Lab per il team. L'intero processo di creazione del Lab e tutte le attività a livello di Lab possono essere eseguite all'interno dei team. Gli utenti avranno la possibilità di creare più Lab all'interno dello stesso team e del proprietario del team, con l'accesso appropriato a livello di account Lab, visualizzeranno solo i Lab associati al team specifico.

## <a name="giving-access-to-users-of-the-lab-account"></a>Concessione dell'accesso agli utenti dell'account Lab

Il provisioning dell'accesso agli utenti a livello di account lab deve essere eseguito nel portale di [Azure](https://ms.portal.azure.com/) .

1. Nella portale di Azure passare all'account di Azure Lab Services. 
1. Nella pagina **Account Lab** selezionare **Controllo di accesso (IAM)** , selezionare **+ Aggiungi** sulla barra degli strumenti e quindi selezionare **+ Aggiungi assegnazione di ruolo** sulla barra degli strumenti. 

    ![Fare clic su Controllo di accesso -> Aggiungi un'assegnazione di ruolo](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Nella pagina **Aggiungi un'assegnazione di ruolo** selezionare **Autore di lab** per **Ruolo**, scegliere l'utente che si vuole aggiungere a questo ruolo e fare clic su **Salva**. 

    ![Aggiungere l'autore del lab](./media/tutorial-setup-lab-account/add-lab-creator.png)

### <a name="creating-classroom-labs"></a>Creazione di Lab in aula

Il processo di creazione dei Lab in aula è lo stesso se si creano Lab da team o dal [sito Web di Lab Services](https://labs.azure.com). 

Per informazioni dettagliate sulla configurazione di questo articolo, il processo di creazione dei Lab: [Gestisci Lab in aula in Azure Lab Services](how-to-manage-classroom-labs.md).

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
- [Gestire un pool di macchine virtuali in Lab Services dai team](how-to-manage-vm-pool-within-teams.md)
- [Creare pianificazioni di Lab Services dai team](how-to-create-schedules-within-teams.md)
- [Accedere a una macchina virtuale (visualizzazione studente) in Lab Services dai team](how-to-access-vm-for-students-within-teams.md)

