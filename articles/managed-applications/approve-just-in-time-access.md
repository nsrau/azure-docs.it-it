---
title: Approva l'accesso just-in-Time-applicazione gestita di Azure
description: Descrive in che modo gli utenti delle applicazioni gestite di Azure approvano le richieste per l'accesso JIT a un'applicazione gestita.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: dd5c3f21b1a4b71d129ccbebeaa7ee66274a672f
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529118"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Configurare e approvare l'accesso just-in-time per le applicazioni gestite di Azure

In qualità di consumer di un'applicazione gestita, l'utente potrebbe non essere in grado di concedere all'autore l'accesso permanente al gruppo di risorse gestite. Per fornire un maggiore controllo sulla concessione dell'accesso alle risorse gestite, le applicazioni gestite di Azure forniscono una funzionalità denominata accesso JIT (just-in-Time), attualmente in anteprima. Consente di approvare quando e per quanto tempo il server di pubblicazione può accedere al gruppo di risorse. Il server di pubblicazione può effettuare gli aggiornamenti necessari durante tale periodo di tempo, ma, al termine di tale periodo di tempo, l'accesso dell'editore scade.

Il flusso di lavoro per la concessione dell'accesso è:

1. Il server di pubblicazione aggiunge un'applicazione gestita al Marketplace e specifica che è disponibile l'accesso JIT.

1. Durante la distribuzione, si Abilita l'accesso JIT per l'istanza dell'applicazione gestita.

1. Dopo la distribuzione, è possibile modificare le impostazioni per l'accesso JIT.

1. Il server di pubblicazione invia una richiesta di accesso.

1. La richiesta viene approvata.

Questo articolo è incentrato sulle azioni intraprese dagli utenti per abilitare l'accesso JIT e approvare le richieste. Per informazioni sulla pubblicazione di un'applicazione gestita con accesso JIT, vedere [richiedere l'accesso just-in-time alle applicazioni gestite di Azure](request-just-in-time-access.md).

> [!NOTE]
> Per utilizzare l'accesso just-in-time, è necessario disporre di una [licenza Azure Active Directory P2](../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Abilita durante la distribuzione

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Trovare una voce del Marketplace per un'applicazione gestita con JIT abilitato. Selezionare **Create** (Crea).

1. Quando si specificano valori per la nuova applicazione gestita, il passaggio di **configurazione JIT** consente di abilitare o disabilitare l'accesso JIT per l'applicazione gestita. Selezionare **Sì** per **abilitare l'accesso JIT**. Questa opzione è selezionata per impostazione predefinita per le applicazioni gestite che definiscono con JIT abilitato nel Marketplace.

   ![Configurare l'accesso](./media/approve-just-in-time-access/configure-jit-access.png)

   È possibile abilitare l'accesso JIT solo durante la distribuzione. Se si seleziona **No**, il server di pubblicazione Ottiene l'accesso permanente al gruppo di risorse gestite. Non è possibile abilitare l'accesso JIT in un secondo momento.

1. Per modificare le impostazioni di approvazione predefinite, selezionare **Personalizza configurazione JIT**.

   ![Personalizzare l'accesso](./media/approve-just-in-time-access/customize-jit-access.png)

   Per impostazione predefinita, un'applicazione gestita con JIT abilitata presenta le impostazioni seguenti:

   * Modalità approvazione-automatica
   * Durata massima di accesso: 8 ore
   * Responsabili approvazione-nessuno

   Quando la modalità di approvazione è impostata su **automatico**, i responsabili approvazione ricevono una notifica per ogni richiesta, ma la richiesta viene approvata automaticamente. Quando è impostato su **Manual**, i responsabili approvazione ricevono una notifica per ogni richiesta e uno di essi deve approvarlo.

   La durata massima dell'attivazione specifica la quantità massima di tempo che un server di pubblicazione può richiedere per accedere al gruppo di risorse gestite.

   L'elenco dei responsabili approvazione è il Azure Active Directory gli utenti che possono approvare le richieste di accesso JIT. Per aggiungere un responsabile approvazione, selezionare **Aggiungi responsabile approvazione** e cercare l'utente.

   Dopo aver aggiornato l'impostazione, selezionare **Salva**.

## <a name="update-after-deployment"></a>Aggiornamento dopo la distribuzione

È possibile modificare i valori per la modalità di approvazione delle richieste. Tuttavia, se non è stato abilitato l'accesso JIT durante la distribuzione, non è possibile abilitarlo in un secondo momento.

Per modificare le impostazioni di un'applicazione gestita distribuita:

1. Nel portale selezionare l'applicazione Gestisci.

1. Selezionare **configurazione JIT** e modificare le impostazioni in base alle esigenze.

   ![Modificare le impostazioni di accesso](./media/approve-just-in-time-access/change-settings.png)

1. Al termine scegliere **Salva**.

## <a name="approve-requests"></a>Approvare le richieste

Quando il server di pubblicazione richiede l'accesso, si riceve una notifica della richiesta. È possibile approvare le richieste di accesso JIT direttamente tramite l'applicazione gestita o in tutte le applicazioni gestite tramite il servizio Azure AD Privileged Identity Management. Per utilizzare l'accesso just-in-time, è necessario disporre di una [licenza Azure Active Directory P2](../active-directory/privileged-identity-management/subscription-requirements.md).

Per approvare le richieste tramite l'applicazione gestita:

1. Selezionare **accesso JIT** per l'applicazione gestita e selezionare **Approva richieste**.

   ![Approvare le richieste](./media/approve-just-in-time-access/approve-requests.png)
 
1. Selezionare la richiesta da approvare.

   ![Seleziona richiesta](./media/approve-just-in-time-access/select-request.png)

1. Nel modulo specificare il motivo dell'approvazione e selezionare **approva**.

Per approvare le richieste tramite Azure AD Privileged Identity Management:

1. Selezionare **tutti i servizi** e iniziare la ricerca di **Azure ad Privileged Identity Management**. Selezionarlo dalle opzioni disponibili.

   ![Cerca servizio](./media/approve-just-in-time-access/search.png)

1. Selezionare **Approva richieste**.

   ![Selezionare Approva richieste](./media/approve-just-in-time-access/select-approve-requests.png)

1. Selezionare **applicazioni gestite di Azure**e selezionare la richiesta da approvare.

   ![Seleziona richieste](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla pubblicazione di un'applicazione gestita con accesso JIT, vedere [richiedere l'accesso just-in-time alle applicazioni gestite di Azure](request-just-in-time-access.md).
