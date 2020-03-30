---
title: Aggiornare le informazioni di Gruppi nel portale App personali - Azure ADUpdate your Groups info on the My Apps portal - Azure AD
description: Scopri come visualizzare e aggiornare le informazioni relative ai gruppi, tra cui la visualizzazione dei gruppi di cui sei proprietario, la creazione di nuovi gruppi, la visualizzazione dei gruppi di cui sei già membro e l'iscrizione ai gruppi di cui non fai già parte.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 00639462fce4e67561b8e50609264d4423872c82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022297"
---
# <a name="update-your-groups-info-on-the-my-apps-portal"></a>Aggiornare le informazioni di Gruppi nel portale delle app personali

È possibile usare l'account aziendale o dell'istituto di istruzione con il portale **App personali** basato sul Web per visualizzare e avviare molte delle app dell'organizzazione basate sul cloud, per aggiornare alcune informazioni dell'account e del profilo, per visualizzare le informazioni sui **gruppi** e per eseguire **verifiche di accesso** per le app e i gruppi. Se non si ha accesso al portale **App personali**, è necessario contattare il supporto tecnico per l'autorizzazione.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Questo contenuto è destinato agli utenti. Gli amministratori possono trovare altre informazioni su come configurare e gestire le app basate sul cloud nella [documentazione di Gestione applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="view-your-groups-information"></a>Visualizzare le informazioni sui gruppi

Se l'amministratore ha concesso l'autorizzazione per visualizzare il riquadro **Gruppi,** è possibile:

- **Come membro del gruppo.** Visualizza i dettagli o lascia qualsiasi gruppo.

- **Come proprietario del gruppo.** Visualizzare i dettagli, creare un nuovo gruppo, aggiungere o rimuovere membri o eliminare il gruppo.

### <a name="to-view-your-groups-information"></a>Per visualizzare le informazioni sui gruppi

1. Accedere all'account aziendale o dell'istituto di istruzione.

2. Aprire il Web browser https://myapps.microsoft.come passare a o utilizzare il collegamento fornito dall'organizzazione. Ad esempio, si potrebbe essere indirizzati a una https://myapps.microsoft.com/contoso.compagina personalizzata per l'organizzazione, ad esempio .

    Viene visualizzata la pagina **App** che mostra tutte le app basate su cloud di proprietà dell'organizzazione e disponibili per l'uso.

    ![Pagina App nel portale App personali](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. Selezionare il riquadro **Gruppi** per visualizzare le informazioni relative al gruppo.

    ![Pagina Gruppi con gruppi di proprietà e membri](media/my-apps-portal/my-apps-portal-groups-page.png)

4. In base alle autorizzazioni, è possibile utilizzare la pagina **Gruppi** per:

    - **Esamina i gruppi di cui sei proprietario.** Visualizzare informazioni sui gruppi di cui si è proprietari all'interno dell'organizzazione dall'area **Gruppi iproprietari.** La selezione di un nome di gruppo specifico fornisce ulteriori dettagli sul gruppo, tra cui il tipo di gruppo, il numero di membri, il criterio di partecipazione e l'elenco dei membri attivi.

    - **Creare un nuovo gruppo.** Creare un nuovo gruppo con l'utente come proprietario dell'area **Gruppi di cui sono proprietario.** Per la procedura specifica, vedere la sezione [Creare un nuovo gruppo](#create-a-new-group) di questo articolo.

    - **Modificare un gruppo esistente.** Modificare i dettagli per uno qualsiasi dei propri gruppi. Per la procedura specifica, vedere la sezione [Modificare un gruppo esistente](#edit-an-existing-group) di questo articolo.

    - **Aggiungere o rimuovere membri.** Aggiungere o rimuovere membri per i gruppi di cui si è proprietari. Per la procedura specifica, vedere la sezione [Aggiungere o rimuovere un membro](#add-or-remove-a-member) di questo articolo.

    - **Rinnovare un gruppo di Office 365.** Se l'organizzazione lo consente, è possibile rinnovare i gruppi di Office 365. Per la procedura specifica, vedere la sezione Rinnovare un gruppo di [Office 365](#renew-an-office-365-group) di questo articolo. 

    - **Eliminare un gruppo.** Eliminare tutti i gruppi di cui si è proprietari. Per la procedura specifica, vedere la sezione [Eliminare un gruppo](#delete-a-group) di questo articolo.

    - **Esamina i gruppi di cui fai parte.** Visualizza i nomi di tutti i gruppi di cui sei membro dell'area **Gruppi in cui mi trovo.** La selezione di un nome di gruppo specifico fornisce ulteriori dettagli sul gruppo, tra cui il tipo di gruppo, il numero di membri, il criterio di partecipazione e l'elenco dei membri attivi.

    - **Partecipa a un gruppo.** Unisciti a un gruppo esistente, di cui non sei già membro, dall'area **Gruppi in cui mi trovo.** Per la procedura specifica, vedere [Join a existing group](#join-an-existing-group).

## <a name="create-a-new-group"></a>Creare un nuovo gruppo

1. Nella pagina **Gruppi** selezionare **Crea un gruppo** nell'area Gruppi di cui sono **proprietario.**

    Viene visualizzata la casella **di gruppo Crea.**

    ![Crea casella di gruppo](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Immettere le informazioni necessarie:

    - **Tipo gruppo:**

        - **Sicurezza.** Usato per gestire l'accesso di membri e computer a risorse condivise per un gruppo di utenti. Ad esempio, è possibile creare un gruppo di sicurezza per criteri di sicurezza specifici. In questo modo, è possibile concedere un set di autorizzazioni a tutti i membri in una sola volta, invece di dover aggiungere autorizzazioni per ogni membro singolarmente.

        - **Office 365.** Offre opportunità di collaborazione consentendo ai membri di accedere a cassette postali, calendari, file, siti di SharePoint e altri elementi condivisi. Questa opzione consente anche di concedere l'accesso al gruppo a utenti esterni all'organizzazione.

    - **Nome del gruppo.** Aggiungere un nome per il gruppo, significativo e facile da ricordare.

    - **Descrizione del gruppo (facoltativo).** Aggiungere una descrizione facoltativa per il gruppo.

    - **Criteri di gruppo.** Scegliere questa opzione per consentire a tutti gli utenti di unirsi al gruppo o consentire solo al proprietario del gruppo di aggiungere membri.

3. Selezionare **Crea**.

    Il nuovo gruppo viene creato con te come proprietario e viene visualizzato nell'elenco **Dei gruppi che possiedo.** Poiché sei il proprietario, questo gruppo viene visualizzato anche nell'elenco Dei gruppi in cui **mi trovo.**

## <a name="edit-an-existing-group"></a>Modificare un gruppo esistente

Dopo aver creato un gruppo, è possibile modificarne i dettagli, incluso l'aggiornamento di qualsiasi informazione esistente.

1. Selezionare il gruppo che si desidera modificare dalla pagina **Gruppi** e quindi selezionare **Modifica dettagli** nella * &lt;&gt; * pagina group_name.

    Viene visualizzata la finestra **Modifica dettagli** ed è possibile aggiornare le informazioni aggiunte al momento della creazione iniziale del gruppo.

2. Apportare tutte le modifiche e quindi selezionare **Aggiorna**.

## <a name="add-or-remove-a-member"></a>Aggiungere o rimuovere un membro

È possibile aggiungere o rimuovere membri per tutti i gruppi di cui si è proprietari.

1. Selezionare il gruppo a cui si **+** desidera aggiungere membri, quindi selezionare nella * &lt;&gt; * pagina group_name.

    ![Aggiungere un membro del gruppo, con il segno più evidenziato](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Cercare il membro che si desidera aggiungere, nella casella **Aggiungi membri** selezionare **Aggiungi**.

    ![Casella Aggiungi membri con nuovo membro da aggiungere](media/my-apps-portal/my-apps-portal-add-member-page.png)

    Viene inviato un invito al nuovo membro per iniziare ad accedere alle app dell'organizzazione.

3. Se è stato aggiunto un membro per errore o se un membro ha lasciato l'organizzazione, è possibile rimuovere il membro selezionando **Rimuovi membro** accanto al nome del membro nella * &lt;&gt; * pagina group_name.

    ![Rimuovere un membro con il collegamento di rimozione evidenziato](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Rinnovare un gruppo di Office 365

Se l'organizzazione lo consente, è possibile rinnovare un gruppo di Office 365, estendendo la data di scadenza.

1. Selezionare il gruppo di Office 365 che si desidera rinnovare e quindi selezionare **Rinnova gruppo**.

    ![Rinnovare un gruppo di Office 365, estendendo la data di scadenza](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. Fare clic **su OK** per chiudere il messaggio di conferma.

    Dopo aver aggiornato la pagina, verranno visualizzate le date di scadenza **dell'ultimo rinnovo** e del **gruppo** aggiornate.

## <a name="delete-a-group"></a>Eliminare un gruppo

È possibile eliminare qualsiasi gruppo in qualsiasi momento. Tuttavia, se elimini un gruppo per errore, dovrai crearlo e aggiungere di nuovo i membri.

1. Selezionare il gruppo che si desidera eliminare definitivamente e quindi selezionare **Elimina gruppo** nella * &lt;&gt; * pagina group_name.

    ![<Group_name pagina> con il collegamento Elimina gruppo evidenziato](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. Selezionare **Sì** nel messaggio di conferma.

    Il gruppo viene eliminato definitivamente.

## <a name="join-an-existing-group"></a>Partecipare a un gruppo esistente

È possibile partecipare o lasciare un gruppo già esistente dalla pagina **Gruppi.**

1. Nella pagina **Gruppi** selezionare **Partecipa al gruppo** nell'area Gruppi in cui si **è.**

    Viene visualizzata la pagina **Gruppi di join.**

    ![Pagina Partecipa a gruppi con il pulsante Partecipa al gruppo evidenziato](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. Nella pagina **Unisci gruppi** selezionare il nome del gruppo a cui si desidera partecipare, visualizzare i dettagli del gruppo associato e quindi, se il gruppo è disponibile, selezionare **Partecipa al gruppo**.

    Se il gruppo richiede che il proprietario del gruppo approvi l'appartenenza, ti verrà chiesto di immettere una motivazione aziendale per il motivo per cui è necessario unirsi al gruppo e quindi selezionare **Richiedi**. Se il gruppo non richiede l'approvazione, l'utente viene immediatamente aggiunto come membro e il gruppo viene visualizzato **nell'elenco Dei gruppi in** cui sono attivo.

3. Se si è uniti a un gruppo per errore o se non è più necessario farne parte, è possibile selezionare il nome del gruppo dalla pagina **Join gruppi** e quindi selezionare **Abbandona gruppo**.

    ![Pagina Partecipa a gruppi con il pulsante Abbandona gruppo evidenziato](media/my-apps-portal/my-apps-portal-leave-group-link.png)

## <a name="next-steps"></a>Passaggi successivi

- [Accedere e utilizzare le app nel portale App personali](my-apps-portal-end-user-access.md).

- [Modificare le informazioni del profilo](my-apps-portal-end-user-update-profile.md).

- [Eseguire le proprie verifiche di accesso](my-apps-portal-end-user-access-reviews.md).
