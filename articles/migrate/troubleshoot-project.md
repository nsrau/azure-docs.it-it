---
title: Risolvere i problemi relativi ai progetti di Azure Migrate
description: Consente di risolvere i problemi relativi alla creazione e alla gestione di progetti Azure Migrate.
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: f2245e13f348706f662055408196ea7a26484811
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92314651"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Risolvere i problemi relativi ai progetti di Azure Migrate

Questo articolo consente di risolvere i problemi durante la creazione e la gestione di progetti [Azure migrate](migrate-services-overview.md) .

## <a name="how-to-add-new-project"></a>Come aggiungere un nuovo progetto

È possibile che una sottoscrizione contenga più progetti Azure Migrate. [Informazioni su come](how-to-add-tool-first-time.md) creare un progetto per la prima volta o [aggiungere altri](create-manage-projects.md#create-additional-projects) progetti.

## <a name="what-azure-permissions-are-needed"></a>Quali sono le autorizzazioni di Azure necessarie?

Per creare un progetto Azure Migrate, è necessario disporre dell'autorizzazione di collaboratore o proprietario a livello di sottoscrizione.

## <a name="cant-find-a-project"></a>Impossibile trovare un progetto

La ricerca di un progetto di Azure Migrate esistente dipende dal fatto che si stia utilizzando la versione corrente o precedente di Azure Migrate. [Seguire](create-manage-projects.md#find-a-project).


## <a name="cant-find-a-geography"></a>Non è possibile trovare una geografia

È possibile creare un progetto Azure Migrate in aree geografiche supportate per i cloud [pubblici](migrate-support-matrix.md#supported-geographies-public-cloud) e [governativi](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="what-are-vm-limits"></a>Che cosa sono i limiti delle VM?

È possibile valutare fino a 35.000 VM VMware o fino a 35.000 macchine virtuali Hyper-V in un singolo progetto. Un progetto può includere sia macchine virtuali VMware che macchine virtuali Hyper-V, fino ai limiti di valutazione.

## <a name="can-i-upgrade-old-project"></a>È possibile aggiornare il progetto precedente?

Non è possibile aggiornare i progetti della versione precedente di Azure Migrate. È necessario [creare un nuovo progetto](how-to-add-tool-first-time.md)e aggiungervi strumenti.

## <a name="cant-create-a-project"></a>Non è possibile creare un progetto

Se si tenta di creare un progetto e si verifica un errore di distribuzione:

- Provare a creare di nuovo il progetto in caso di errore temporaneo. In **distribuzioni**fare clic su **Ridistribuisci** per riprovare.
- Verificare di disporre delle autorizzazioni Collaboratore o proprietario nella sottoscrizione.
- Se si esegue la distribuzione in una geografia appena aggiunta, attendere un breve periodo di tempo e riprovare.
- Se viene visualizzato l'errore "le richieste devono contenere intestazioni di identità utente", questo potrebbe indicare che non si ha accesso al tenant Azure Active Directory (Azure AD) dell'organizzazione. In questo caso:
    - Quando si viene aggiunti a un tenant di Azure AD per la prima volta, si riceve un messaggio di posta elettronica di invito per l'aggiunta al tenant.
    - Accettare l'invito da aggiungere al tenant.
    - Se il messaggio di posta elettronica non è visibile, contattare un utente con accesso al tenant e chiedere di [inviare di nuovo l'invito](../active-directory/external-identities/add-users-administrator.md#resend-invitations-to-guest-users) .
    - Dopo la ricezione del messaggio di posta elettronica di invito, aprirlo e selezionare il collegamento per accettare l'invito. Quindi, disconnettersi dalla portale di Azure ed eseguire di nuovo l'accesso. (l'aggiornamento del browser non funziona). È quindi possibile iniziare a creare il progetto di migrazione.

## <a name="how-do-i-delete-a-project"></a>Ricerca per categorie eliminare un progetto

[Seguire queste istruzioni](create-manage-projects.md#delete-a-project) per eliminare un progetto. Si noti che quando si elimina un progetto, vengono eliminati sia il progetto che i metadati relativi alle macchine virtuali individuate nel progetto.

## <a name="added-tools-dont-show"></a>Gli strumenti aggiunti non vengono visualizzati

Assicurarsi che sia selezionato il progetto appropriato. Nell'hub Azure Migrate > **Server** o in **database**fare clic su **modifica** accanto a **Esegui migrazione progetto (modifica)** nell'angolo superiore destro dello schermo. Scegliere la sottoscrizione e il nome del progetto corretti > **OK**. La pagina verrà aggiornata con gli strumenti aggiunti del progetto selezionato.

## <a name="next-steps"></a>Passaggi successivi

Aggiungere strumenti di [valutazione](how-to-assess.md) o [migrazione](how-to-migrate.md) ai progetti Azure migrate.