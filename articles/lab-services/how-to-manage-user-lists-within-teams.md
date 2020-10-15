---
title: Gestire gli elenchi di utenti Azure Lab Services dai team
description: Informazioni su come gestire gli elenchi di utenti Azure Lab Services dai team.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: fa8f96a84be5c4c91e0153216e15963ec0e3d6f8
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946711"
---
# <a name="manage-lab-services-user-lists-from-teams"></a>Gestire gli elenchi utenti di Lab Services dai team

Quando viene creato un Lab all'interno dei team (vedere la pagina relativa all' [Introduzione e alla creazione di un Lab di Lab Services dai team](how-to-get-started-create-lab-within-teams.md)), l'elenco degli utenti del Lab viene automaticamente popolato e sincronizzato con l'appartenenza al team. Tutti gli utenti del team, inclusi i proprietari, i membri e i guest, verranno aggiunti automaticamente all'elenco degli utenti del Lab. Azure Lab Services mantiene una sincronizzazione con l'appartenenza al team e viene attivata una sincronizzazione automatica ogni 24 ore. 

## <a name="sync-users"></a>Sincronizza utenti

Gli educatori possono usare il pulsante **Sincronizza** per attivare una sincronizzazione manuale dopo l'aggiornamento dell'appartenenza al team. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-users-with-teams/sync-users.png" alt-text="Sincronizza utenti":::

Una volta completata la sincronizzazione automatica o manuale, il valore seguente è true a seconda che il Lab sia stato pubblicato.

* Se il Lab non è stato pubblicato almeno una volta:
    * Gli utenti verranno aggiunti o eliminati dall'elenco degli utenti del Lab in base alle modifiche apportate all'appartenenza al team. 
* Se il Lab è stato pubblicato almeno una volta, oltre ad aggiungere o eliminare utenti, la capacità del Lab verrà aggiornata automaticamente.
    * Se sono presenti nuove aggiunte al team, verranno create nuove macchine virtuali.
    * Se un utente è stato eliminato dal team, verrà eliminata anche la macchina virtuale associata.

## <a name="next-steps"></a>Passaggi successivi

Una volta configurata la VM del modello e quando l'educatore sceglie di pubblicare il modello, verrà creato il numero di macchine virtuali equivalenti al numero di utenti nell'elenco degli utenti del Lab. Dopo la pubblicazione del Lab e la creazione di macchine virtuali, gli utenti verranno registrati automaticamente nel Lab e le macchine virtuali verranno assegnate al primo accesso a Azure Lab Services, quando accedono per la prima volta alla scheda con **Azure Lab Services** app. 

Per pubblicare la macchina virtuale del modello, passare alla finestra teams Lab Services, selezionare **template** tab-> **...**  ->  **Pubblicazione**.

Per gestire i pool di macchine virtuali, vedere [gestire un pool di macchine virtuali in Lab Services dai team](how-to-manage-vm-pool-within-teams.md).

### <a name="also-review"></a>Verifica anche

Vedere gli articoli seguenti:

- [Usare Azure Lab Services in teams Overview](lab-services-within-teams-overview.md)
- [Per iniziare, creare un Lab di Lab Services dai team](how-to-get-started-create-lab-within-teams.md)
- [Creare pianificazioni di Lab Services dai team](how-to-create-schedules-within-teams.md)
- [Accedere a una macchina virtuale (visualizzazione studente) in Lab Services dai team](how-to-access-vm-for-students-within-teams.md)

