---
title: Gestire un pool di macchine virtuali in Azure Lab Services dai team
description: Informazioni su come gestire un pool di macchine virtuali in Azure Lab Services dai team.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: b838e0561bb48b20450e49aaef37baf3c9ecb4d0
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946761"
---
# <a name="manage-a-vm-pool-in-lab-services-from-teams"></a>Gestire un pool di macchine virtuali in Lab Services dai team

La creazione della macchina virtuale (VM) viene avviata non appena la VM del modello viene pubblicata per la prima volta. Verranno create macchine virtuali che equivalgono al numero di utenti nell'elenco degli utenti del Lab. Le macchine virtuali vengono assegnate automaticamente agli studenti al primo accesso per Azure Lab Services. 

## <a name="publish-a-template-and-manage-a-vm-pool"></a>Pubblicare un modello e gestire un pool di macchine virtuali

Per pubblicare il modello, passare alla finestra teams Lab Services, selezionare **template** tab-> **...**  ->  **Pubblicazione**.

Una volta configurata la VM del modello e quando l'educatore sceglie di pubblicare il modello, verrà creato il numero di macchine virtuali equivalenti al numero di utenti nell'elenco degli utenti del Lab. Dopo la pubblicazione del Lab e la creazione di macchine virtuali, gli utenti verranno registrati automaticamente nel Lab e le macchine virtuali verranno assegnate al primo accesso a Azure Lab Services, quando accedono per la prima volta alla scheda con **Azure Lab Services** app. 

Quando viene attivata la sincronizzazione di un elenco di utenti, la capacità del Lab (numero di macchine virtuali nel Lab) verrà aggiornata automaticamente in base alle modifiche apportate all'appartenenza del team. Verranno create nuove macchine virtuali con l'aggiunta di nuovi utenti e verranno eliminate anche le macchine virtuali assegnate agli utenti rimossi dal team. Per ulteriori informazioni [, vedere How to Manage Users with teams](how-to-manage-user-lists-within-teams.md). 

Gli educatori possono continuare ad accedere alle macchine virtuali degli studenti direttamente dalla scheda pool di macchine virtuali. E i docenti possono accedere alle VM assegnate a se stessi dalla scheda **pool di macchine virtuali** o facendo clic sul pulsante **macchine virtuali personali** (angolo superiore/destro dello schermo). 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-vm-pool-with-teams/vm-pool.png" alt-text="Pool di macchine virtuali":::

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti:

- [Usare Azure Lab Services in teams Overview](lab-services-within-teams-overview.md)
- [Per iniziare, creare un Lab di Lab Services dai team](how-to-get-started-create-lab-within-teams.md)
- [Gestire gli elenchi utenti di Lab Services dai team](how-to-manage-user-lists-within-teams.md)
- [Creare pianificazioni di Lab Services dai team](how-to-create-schedules-within-teams.md)
- [Accedere a una macchina virtuale (visualizzazione studente) in Lab Services dai team](how-to-access-vm-for-students-within-teams.md)


