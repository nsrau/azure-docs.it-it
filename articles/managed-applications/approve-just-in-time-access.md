---
title: Configurare e approvare l'accesso just-in-time per applicazioni gestite di Azure
description: Viene descritto come i consumer di applicazioni gestite di Azure approvare le richieste di accesso just-in-time a un'applicazione gestita.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 55fbf7292ab894cad3de3de9e96ddc96fe0b79b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481715"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Configurare e approvare l'accesso just-in-time per applicazioni gestite di Azure

I consumer di un'applicazione gestita, si potrebbe non essere familiarità fornendo il server di pubblicazione accesso permanente al gruppo di risorse gestite. Per offrirti un maggiore controllo sulla concessione dell'accesso alle risorse gestite, le applicazioni gestite di Azure offre una funzionalità denominata just-in-time (JIT) accesso, che è attualmente in anteprima. Consente di approvare quando e per quanto tempo il server di pubblicazione ha accesso al gruppo di risorse. Il server di pubblicazione possono eseguire aggiornamenti necessari durante tale periodo, ma al termine quel momento, scadenza dell'accesso del server di pubblicazione.

Il flusso di lavoro per la concessione dell'accesso è:

1. Il server di pubblicazione aggiunge un'applicazione gestita nel Marketplace e specifica che l'accesso JIT è disponibile.

1. Durante la distribuzione, si abilita l'accesso JIT per l'istanza dell'applicazione gestita.

1. Dopo la distribuzione, è possibile modificare le impostazioni per l'accesso JIT.

1. Il server di pubblicazione invia una richiesta di accesso.

1. Approvazione della richiesta.

Questo articolo illustra le azioni di consumer per abilitare l'accesso JIT e approvare le richieste. Per altre informazioni sulla pubblicazione di un'applicazione gestita con l'accesso JIT, vedere [richiedere l'accesso just-in-time in applicazioni gestite di Azure](request-just-in-time-access.md).

> [!NOTE]
> Per usare l'accesso just-in-time, è necessario disporre di un [licenza di Azure Active Directory P2](../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Abilitare durante la distribuzione

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Trovare una voce di marketplace per un'applicazione gestita con JIT abilitata. Selezionare **Create**.

1. Fornendo i valori per la nuova applicazione gestita, il **JIT configurazione** passaggio consente di abilitare o disabilitare l'accesso JIT per l'applicazione gestita. Selezionare **Yes** per **abilitare l'accesso JIT**. Questa opzione è selezionata per impostazione predefinita per le applicazioni gestite definito con JIT, abilitato nel marketplace.

   ![Configurare l'accesso](./media/approve-just-in-time-access/configure-jit-access.png)

   È possibile abilitare l'accesso JIT solo durante la distribuzione. Se si seleziona **No**, il server di pubblicazione ottiene accesso permanente al gruppo di risorse gestite. È possibile abilitare l'accesso JIT in un secondo momento.

1. Per modificare le impostazioni di approvazione predefinita, selezionare **personalizzare configurazione JIT**.

   ![Personalizzare l'accesso](./media/approve-just-in-time-access/customize-jit-access.png)

   Per impostazione predefinita, un'applicazione gestita con JIT abilitata con le seguenti impostazioni:

   * Modalità di approvazione-automatico
   * Durata massima accesso – 8 ore
   * I responsabili approvazione: nessuno

   Quando la modalità di approvazione è impostata su **automatica**, responsabili dell'approvazione riceve una notifica per ogni richiesta, ma la richiesta viene approvata automaticamente. Se impostato su **manuale**, responsabili dell'approvazione riceve una notifica per ogni richiesta e uno di essi deve approvarlo.

   La durata massima di attivazione specifica la quantità massima di tempo che possono richiedere un server di pubblicazione per l'accesso al gruppo di risorse gestite.

   L'elenco di responsabili approvazione è gli utenti di Azure Active Directory che possono approvare JIT richieste di accesso. Per aggiungere un responsabile approvazione, selezionare **responsabile approvazione aggiungere** e cercare l'utente.

   Dopo aver aggiornato l'impostazione, selezionare **salvare**.

## <a name="update-after-deployment"></a>Aggiornare dopo la distribuzione

È possibile modificare i valori per la modalità le richieste sono approvate. Tuttavia, se si non abilita l'accesso JIT durante la distribuzione, è non è possibile abilitarla in un secondo momento.

Per modificare le impostazioni per un'applicazione gestita distribuita:

1. Nel portale, selezionare l'applicazione di gestione.

1. Selezionare **JIT configurazione** e modificare le impostazioni in base alle esigenze.

   ![Modificare le impostazioni di accesso](./media/approve-just-in-time-access/change-settings.png)

1. Al termine scegliere **Salva**.

## <a name="approve-requests"></a>Approvare le richieste

Quando il server di pubblicazione richiede l'accesso, si riceverà una notifica della richiesta. È possibile approvare le richieste di accesso JIT direttamente tramite l'applicazione gestita o a tutte le applicazioni gestite tramite il servizio di Azure AD Privileged Identity Management. Per usare l'accesso just-in-time, è necessario disporre di un [licenza di Azure Active Directory P2](../active-directory/privileged-identity-management/subscription-requirements.md).

Per approvare le richieste tramite l'applicazione gestita:

1. Selezionare **accesso JIT** per l'applicazione gestita e selezionare **Approva richieste**.

   ![Approvare le richieste](./media/approve-just-in-time-access/approve-requests.png)
 
1. Selezionare le richieste da approvare.

   ![Selezionare richiesta](./media/approve-just-in-time-access/select-request.png)

1. Nel modulo, fornisce informazioni sul motivo per l'approvazione e selezionare **Approva**.

Per approvare le richieste tramite Azure AD Privileged Identity Management:

1. Selezionare **tutti i servizi** e iniziare cercando **Azure AD Privileged Identity Management**. Selezionarlo dalle opzioni disponibili.

   ![Ricerca per servizio](./media/approve-just-in-time-access/search.png)

1. Selezionare **approvare le richieste**.

   ![Selezionare Approva richieste](./media/approve-just-in-time-access/select-approve-requests.png)

1. Selezionare **applicazioni gestite di Azure**e selezionare le richieste da approvare.

   ![Selezionare le richieste](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla pubblicazione di un'applicazione gestita con l'accesso JIT, vedere [richiedere l'accesso just-in-time in applicazioni gestite di Azure](request-just-in-time-access.md).
