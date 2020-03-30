---
title: Approvare l'accesso just-in-time
description: Descrive in che modo i consumer delle applicazioni gestite di Azure approvano le richieste per l'accesso just-in-time a un'applicazione gestita.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651020"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Configurare e approvare l'accesso just-in-time per le applicazioni gestite di AzureConfigure and approve just-in-time access for Azure Managed Applications

In qualità di consumer di un'applicazione gestita, potrebbe non essere necessario fornire all'editore l'accesso permanente al gruppo di risorse gestite. Per concedere un maggiore controllo sulla concessione dell'accesso alle risorse gestite, le applicazioni gestite di Azure forniscono una funzionalità denominata accesso JIT (Just-In-Time), attualmente in anteprima. Consente di approvare quando e per quanto tempo l'editore ha accesso al gruppo di risorse. L'editore può effettuare gli aggiornamenti necessari durante tale periodo, ma al termine di tale periodo, l'accesso dell'editore scade.

Il flusso di lavoro per concedere l'accesso è:The work flow for granting access is:

1. L'editore aggiunge un'applicazione gestita al marketplace e specifica che l'accesso JIT è disponibile.

1. Durante la distribuzione, si abilita l'accesso JIT per l'istanza dell'applicazione gestita.

1. Dopo la distribuzione, è possibile modificare le impostazioni per l'accesso JIT.

1. L'editore invia una richiesta di accesso.

1. L'utente approva la richiesta.

Questo articolo è incentrato sulle azioni eseguite dai consumer per abilitare l'accesso JIT e approvare le richieste. Per informazioni sulla pubblicazione di un'applicazione gestita con accesso JIT, vedere [Richiedere l'accesso Just-In-Time nelle applicazioni gestite](request-just-in-time-access.md)di Azure.To learn about publishing a managed application with JIT access, see Request just-in-time access in Azure Managed Applications .

> [!NOTE]
> Per usare l'accesso just-in-time, è necessario disporre di una [licenza Azure Active Directory P2.](../../active-directory/privileged-identity-management/subscription-requirements.md)

## <a name="enable-during-deployment"></a>Abilita durante la distribuzione

1. Accedere al [portale](https://portal.azure.com)di Azure .

1. Trovare una voce del marketplace per un'applicazione gestita con JIT abilitato. Selezionare **Crea**.

1. Pur fornendo i valori per la nuova applicazione gestita, il passaggio **di configurazione JIT** consente di abilitare o disabilitare l'accesso JIT per l'applicazione gestita. Selezionare **Sì** per **Abilita accesso JIT**. Questa opzione è selezionata per impostazione predefinita per le applicazioni gestite definite con JIT abilitato nel marketplace.

   ![Configurare l'accesso](./media/approve-just-in-time-access/configure-jit-access.png)

   È possibile abilitare l'accesso JIT solo durante la distribuzione. Se si seleziona **No**, l'editore ottiene l'accesso permanente al gruppo di risorse gestite. Non è possibile abilitare l'accesso JIT in un secondo momento.

1. Per modificare le impostazioni di approvazione predefinite, selezionare **Personalizza configurazione JIT**.

   ![Personalizzare l'accesso](./media/approve-just-in-time-access/customize-jit-access.png)

   Per impostazione predefinita, un'applicazione gestita con JIT abilitato ha le seguenti impostazioni:

   * Modalità di approvazione - automatica
   * Durata massima dell'accesso – 8 ore
   * Responsabili approvazione – nessuno

   Quando la modalità di approvazione è impostata su **automatico**, gli approvatori ricevono una notifica per ogni richiesta, ma la richiesta viene approvata automaticamente. Se impostato su **manuale**, gli approvatori ricevono una notifica per ogni richiesta e uno di essi deve approvarla.

   La durata massima di attivazione specifica il periodo di tempo massimo per cui un editore può richiedere l'accesso al gruppo di risorse gestite.

   L'elenco dei responsabili approvazione è costituito dagli utenti di Azure Active Directory che possono approvare le richieste di accesso JIT. Per aggiungere un approvatore, selezionare **Aggiungi responsabile approvazione** e cercare l'utente.

   Dopo aver aggiornato l'impostazione, selezionare **Salva**.

## <a name="update-after-deployment"></a>Aggiornamento dopo la distribuzione

È possibile modificare i valori per la modalità di approvazione delle richieste. Tuttavia, se non è stato abilitato l'accesso JIT durante la distribuzione, non è possibile abilitarlo in un secondo momento.

Per modificare le impostazioni per un'applicazione gestita distribuita:

1. Nel portale selezionare l'applicazione di gestione.

1. Selezionare **Configurazione JIT** e modificare le impostazioni in base alle esigenze.

   ![Modificare le impostazioni di accesso](./media/approve-just-in-time-access/change-settings.png)

1. Al termine scegliere **Salva**.

## <a name="approve-requests"></a>Approvare le richieste

Quando l'editore richiede l'accesso, si riceve una notifica della richiesta. È possibile approvare le richieste di accesso JIT direttamente tramite l'applicazione gestita o in tutte le applicazioni gestite tramite il servizio Gestione identità con privilegi di Azure AD. Per usare l'accesso just-in-time, è necessario disporre di una [licenza Azure Active Directory P2.](../../active-directory/privileged-identity-management/subscription-requirements.md)

Per approvare le richieste tramite l'applicazione gestita:

1. Selezionare **Accesso JIT** per l'applicazione gestita e selezionare **Approva richieste**.

   ![Approvare le richieste](./media/approve-just-in-time-access/approve-requests.png)
 
1. Selezionare la richiesta da approvare.

   ![Seleziona richiesta](./media/approve-just-in-time-access/select-request.png)

1. Nel modulo specificare il motivo dell'approvazione e selezionare **Approva**.

Per approvare le richieste tramite Azure AD Privileged Identity Management:To approve requests through Azure AD Privileged Identity Management:

1. Selezionare **Tutti i servizi** e iniziare a cercare Azure AD **Privileged Identity Management**. Selezionarlo tra le opzioni disponibili.

   ![Cerca servizio](./media/approve-just-in-time-access/search.png)

1. Selezionare **Approva richieste**.

   ![Selezionare le richieste di approvazione](./media/approve-just-in-time-access/select-approve-requests.png)

1. Selezionare **Applicazioni gestite di Azure**e selezionare la richiesta da approvare.

   ![Seleziona richieste](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla pubblicazione di un'applicazione gestita con accesso JIT, vedere [Richiedere l'accesso Just-In-Time nelle applicazioni gestite](request-just-in-time-access.md)di Azure.To learn about publishing a managed application with JIT access, see Request just-in-time access in Azure Managed Applications .
