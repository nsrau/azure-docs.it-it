---
title: Risolvere i problemi relativi ai progetti di Azure Migrate
description: Consente di risolvere i problemi relativi alla creazione e alla gestione di progetti di Azure Migrate.
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: b1fc4bce988b13a9ff76fd961d524ce945876054
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535401"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Risolvere i problemi relativi ai progetti di Azure Migrate

Questo articolo consente di risolvere i problemi relativi alla creazione e alla gestione di progetti [di Azure Migrate.This](migrate-services-overview.md) article helps you troubleshoot issues when creating and managing Azure Migrate projects.

## <a name="how-to-add-new-project"></a>Come aggiungere un nuovo progetto?

È possibile avere più progetti di Azure Migrate in una sottoscrizione. [Informazioni su come](how-to-add-tool-first-time.md) creare un progetto per la prima volta o [aggiungere altri](create-manage-projects.md#create-additional-projects) progetti.

## <a name="what-azure-permissions-are-needed"></a>Quali autorizzazioni di Azure sono necessarie?

Per creare un progetto di Azure Migrate, sono necessarie autorizzazioni di collaboratore o proprietario.

## <a name="cant-find-a-project"></a>Impossibile trovare un progetto

La ricerca di un progetto Azure Migrate esistente varia a seconda che si stia usando la versione corrente o precedente di Azure Migrate.Finding an existing Azure Migrate project depends on whether you're using the current or old version of Azure Migrate. [Seguire](create-manage-projects.md#find-a-project).


## <a name="cant-find-a-geography"></a>Non riesco a trovare un'area geografica

È possibile creare un progetto di Azure Migrate nelle aree geografiche supportate per i cloud [pubblici](migrate-support-matrix.md#supported-geographies-public-cloud) e [governativi.](migrate-support-matrix.md#supported-geographies-azure-government)

## <a name="what-are-vm-limits"></a>Che cosa sono i limiti delle macchine virtuali?

È possibile valutare fino a 35.000 macchine virtuali VMware o fino a 35.000 macchine virtuali Hyper-V in un singolo progetto. Un progetto può includere sia macchine virtuali VMware che macchine virtuali Hyper-V, fino ai limiti di valutazione.

## <a name="can-i-upgrade-old-project"></a>Posso aggiornare il vecchio progetto?

I progetti della versione precedente di Azure Migrate non possono essere aggiornati. È necessario [creare un nuovo progetto](how-to-add-tool-first-time.md)e aggiungervi strumenti.

## <a name="cant-create-a-project"></a>Impossibile creare un progetto

Se si tenta di creare un progetto e si verifica un errore di distribuzione:If you try to create a project and encounter a deployment error:

- Provare a creare nuovamente il progetto nel caso in cui si tratti di un errore temporaneo. In **Distribuzioni**fare clic su **Ridistribuisci** per riprovare.
- Verificare di disporre delle autorizzazioni Collaboratore o Proprietario nella sottoscrizione.
- Se si esegue la distribuzione in un'area geografica appena aggiunta, attendere un breve periodo di tempo e riprovare.
- Se viene visualizzato l'errore "Le richieste devono contenere intestazioni di identità utente", ciò potrebbe indicare che non si ha accesso al tenant di Azure Active Directory (Azure AD) dell'organizzazione. In questo caso:
    - Quando si viene aggiunti a un tenant di Azure AD per la prima volta, si riceve un invito tramite posta elettronica per partecipare al tenant.
    - Accettare l'invito da aggiungere al tenant.
    - Se non riesci a vedere il messaggio di posta elettronica, contatta un utente con accesso al tenant e chiedigli di [inviare di nuovo l'invito all'utente.](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)
    - Dopo aver ricevuto l'e-mail di invito, aprirla e selezionare il collegamento per accettare l'invito. Quindi, disconnettersi dal portale di Azure e accedere di nuovo. (l'aggiornamento del browser non funzionerà.) È quindi possibile iniziare a creare il progetto di migrazione.

## <a name="how-do-i-delete-a-project"></a>Come si elimina un progetto

[Seguire queste istruzioni](create-manage-projects.md#delete-a-project) per eliminare un progetto. Si noti che quando si elimina un progetto, vengono eliminati sia il progetto che i metadati relativi ai computer individuati nel progetto.

## <a name="added-tools-dont-show"></a>Gli strumenti aggiunti non vengono visualizzati

Assicurati di aver selezionato il progetto giusto. Nell'hub Azure Migrate > **server** o in **Database**fare clic su **Cambia** accanto a Esegui migrazione **progetto (Modifica)** nell'angolo superiore destro dello schermo. Scegliere la sottoscrizione corretta e il nome del progetto > **OK**. La pagina deve essere aggiornata con gli strumenti aggiunti del progetto selezionato.

## <a name="next-steps"></a>Passaggi successivi

Aggiungere strumenti di valutazione o migrazione ai progetti di Azure Migrate.Add [assessment](how-to-assess.md) or [migration](how-to-migrate.md) tools to Azure Migrate projects.