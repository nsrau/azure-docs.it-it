---
title: Aggiornare le informazioni sui gruppi nel portale app personali-Azure AD
description: Informazioni su come visualizzare e aggiornare le informazioni relative ai gruppi, tra cui la visualizzazione dei gruppi di cui si è proprietari, la creazione di nuovi gruppi, la visualizzazione dei gruppi a cui si è già membri e l'Unione di tutti i gruppi di cui non si fa già parte.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa251ee361af12ecf3fef3c7cd437fa06c1cea52
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73803928"
---
# <a name="update-your-groups-info-on-the-my-apps-portal"></a>Aggiornare le informazioni sui gruppi nel portale app personali

È possibile usare l'account aziendale o dell'istituto di istruzione con il portale **App personali** basato sul Web per visualizzare e avviare molte delle app dell'organizzazione basate sul cloud, per aggiornare alcune informazioni dell'account e del profilo, per visualizzare le informazioni sui **gruppi** e per eseguire **verifiche di accesso** per le app e i gruppi. Se non si ha accesso al portale **App personali**, è necessario contattare il supporto tecnico per l'autorizzazione.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Questo contenuto è destinato agli utenti. Gli amministratori possono trovare altre informazioni su come configurare e gestire le app basate sul cloud nella [documentazione di Gestione applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="view-your-groups-information"></a>Visualizzare le informazioni sui gruppi

Se l'amministratore ha concesso l'autorizzazione per visualizzare il riquadro **gruppi** , è possibile:

- **Come membro del gruppo.** Visualizza i dettagli o lascia un gruppo.

- **Come proprietario del gruppo.** Visualizzare i dettagli, creare un nuovo gruppo, aggiungere o rimuovere membri oppure eliminare il gruppo.

### <a name="to-view-your-groups-information"></a>Per visualizzare le informazioni sui gruppi

1. Accedere all'account aziendale o dell'istituto di istruzione.

2. Aprire il Web browser e passare a https://myapps.microsoft.como usare il collegamento fornito dall'organizzazione. Ad esempio, si potrebbe essere indirizzati a una pagina personalizzata per l'organizzazione, ad esempio https://myapps.microsoft.com/contoso.com.

    Viene visualizzata la pagina **app** , che Mostra tutte le app basate sul cloud di proprietà dell'organizzazione e disponibili per l'uso.

    ![Pagina app nel portale app personali](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. Selezionare il riquadro **gruppi** per visualizzare le informazioni correlate al gruppo.

    ![Pagina dei gruppi con gruppi di proprietà e membri](media/my-apps-portal/my-apps-portal-groups-page.png)

4. A seconda delle autorizzazioni, è possibile utilizzare la pagina **gruppi** per:

    - **Esaminare i gruppi di cui si è proprietari.** Visualizzare le informazioni su tutti i gruppi di cui si è proprietari all'interno dell'organizzazione dall'area **gruppi di cui** si è proprietari. Se si seleziona un nome di gruppo specifico, vengono forniti altri dettagli sul gruppo, inclusi il tipo di gruppo, il numero di membri, i criteri di join e l'elenco dei membri attivi.

    - **Creare un nuovo gruppo.** Creare un nuovo gruppo con l'utente come proprietario dall'area **gruppi di cui** si è proprietari. Per i passaggi specifici, vedere la sezione [creare un nuovo gruppo](#create-a-new-group) di questo articolo.

    - **Modificare un gruppo esistente.** Modificare i dettagli per uno dei gruppi personali. Per i passaggi specifici, vedere la sezione [modificare un gruppo esistente](#edit-an-existing-group) di questo articolo.

    - **Aggiungere o rimuovere membri.** Aggiungere o rimuovere membri per i gruppi di cui si è proprietari. Per i passaggi specifici, vedere la sezione [aggiungere o rimuovere un membro](#add-or-remove-a-member) di questo articolo.

    - **Rinnovare un gruppo di Office 365.** Se l'organizzazione lo consente, è possibile rinnovare i gruppi di Office 365. Per i passaggi specifici, vedere la sezione [rinnovare un gruppo di Office 365](#renew-an-office-365-group) di questo articolo. 

    - **Eliminare un gruppo.** Eliminare tutti i gruppi di cui si è proprietari. Per i passaggi specifici, vedere la sezione [eliminare un gruppo](#delete-a-group) di questo articolo.

    - **Esaminare i gruppi di cui si fa parte.** Visualizzare i nomi di tutti i gruppi a cui si è membri dai **gruppi che sono in** area. Se si seleziona un nome di gruppo specifico, vengono forniti altri dettagli sul gruppo, inclusi il tipo di gruppo, il numero di membri, i criteri di join e l'elenco dei membri attivi.

    - **Aggiungere un gruppo.** Aggiungere un gruppo esistente, per cui non si è già membri, dai **gruppi che sono in** area. Per i passaggi specifici, vedere il [join di un gruppo esistente](#join-an-existing-group).

## <a name="create-a-new-group"></a>Creare un nuovo gruppo

1. Nella pagina **gruppi** selezionare **Crea un gruppo** dall'area gruppi di **cui** si è proprietari.

    Verrà visualizzata la casella **Crea gruppo** .

    ![Casella Crea gruppo](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Immettere le informazioni necessarie:

    - **Tipo gruppo:**

        - **Sicurezza.** Usato per gestire l'accesso di membri e computer a risorse condivise per un gruppo di utenti. Ad esempio, è possibile creare un gruppo di sicurezza per criteri di sicurezza specifici. In questo modo, è possibile concedere un set di autorizzazioni a tutti i membri in una sola volta, invece di dover aggiungere autorizzazioni per ogni membro singolarmente.

        - **Office 365.** Offre opportunità di collaborazione consentendo ai membri di accedere a cassette postali, calendari, file, siti di SharePoint e altri elementi condivisi. Questa opzione consente anche di concedere l'accesso al gruppo a utenti esterni all'organizzazione.

    - **Nome gruppo.** Aggiungere un nome per il gruppo, significativo e facile da ricordare.

    - **Descrizione del gruppo (facoltativo).** Aggiungere una descrizione facoltativa per il gruppo.

    - **Criteri di gruppo.** Scegliere questa opzione per consentire a tutti di partecipare al gruppo o consentire solo al proprietario del gruppo di aggiungere membri.

3. Selezionare **Crea**.

    Il nuovo gruppo verrà creato con il proprietario e verrà visualizzato nell'elenco dei **gruppi personali** . Poiché si è il proprietario, questo gruppo viene visualizzato anche nell'elenco **gruppi i** .

## <a name="edit-an-existing-group"></a>Modificare un gruppo esistente

Dopo aver creato un gruppo, è possibile modificarne i dettagli, incluso l'aggiornamento di qualsiasi informazione esistente.

### <a name="to-edit-your-details"></a>Per modificare i dettagli

1. Selezionare il gruppo che si desidera modificare dalla pagina **gruppi** , quindi selezionare **Modifica dettagli** nella pagina *&lt;group_name&gt;* .

    Viene visualizzata la finestra di dialogo **Modifica dettagli** ed è possibile aggiornare le informazioni aggiunte al momento della creazione iniziale del gruppo.

2. Apportare tutte le modifiche e quindi selezionare **Aggiorna**.

## <a name="add-or-remove-a-member"></a>Aggiungere o rimuovere un membro

È possibile aggiungere o rimuovere membri per qualsiasi gruppo.

### <a name="to-add-or-remove-a-member"></a>Per aggiungere o rimuovere un membro

1. Selezionare il gruppo a cui si desidera aggiungere membri, quindi selezionare **+** nella pagina *&lt;group_name&gt;* .

    ![Aggiungere un membro del gruppo con segno + evidenziato](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Cercare il membro che si vuole aggiungere, nella casella **Aggiungi membri** , quindi selezionare **Aggiungi**.

    ![Casella Aggiungi membri, con nuovo membro da aggiungere](media/my-apps-portal/my-apps-portal-add-member-page.png)

    Viene inviato un invito al nuovo membro per iniziare ad accedere alle app dell'organizzazione.

3. Se è stato aggiunto un membro per errore o se un membro ha lasciato l'organizzazione, è possibile rimuovere il membro selezionando **Rimuovi membro** accanto al nome del membro nella pagina *&lt;group_name&gt;* .

    ![Rimuovere un membro con il collegamento di rimozione evidenziato](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Rinnovare un gruppo Office 365

Se l'organizzazione lo consente, è possibile rinnovare un gruppo di Office 365, estendendo la data di scadenza.

### <a name="to-renew-a-group"></a>Per rinnovare un gruppo

1. Selezionare il gruppo di Office 365 che si vuole rinnovare, quindi selezionare **rinnova il gruppo**.

    ![Rinnovare un gruppo di Office 365, estendendo la data di scadenza](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. Fare clic su **OK** per chiudere il messaggio di conferma.

    Dopo l'aggiornamento della pagina, verranno visualizzate le date di scadenza dell' **ultimo rinnovo** e del **gruppo** aggiornate.

## <a name="delete-a-group"></a>Eliminare un gruppo

È possibile eliminare qualsiasi gruppo in qualsiasi momento. Tuttavia, se si elimina un gruppo per errore, sarà necessario crearlo e aggiungere nuovamente i membri.

### <a name="to-delete-the-group"></a>Per eliminare il gruppo

1. Selezionare il gruppo che si desidera eliminare definitivamente, quindi selezionare **Elimina gruppo** nella pagina *&lt;group_name&gt;* .

    ![< Group_name pagina > con il collegamento Elimina gruppo evidenziato](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. Selezionare **Sì** nel messaggio di conferma.

    Il gruppo viene eliminato definitivamente.

## <a name="join-an-existing-group"></a>Unisci a un gruppo esistente

È possibile aggiungere un gruppo già esistente dalla pagina **gruppi** .

### <a name="to-join-or-leave-a-group"></a>Per partecipare o uscire da un gruppo

1. Nella pagina **gruppi** selezionare **Unisci gruppo** tra i **gruppi in** area.

    Verrà visualizzata la pagina **join groups** .

    ![Pagina Unisci gruppi, con il pulsante Unisci gruppo evidenziato](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. Nella pagina **Unisci gruppi** selezionare il nome del gruppo che si desidera unire, visualizzare i dettagli del gruppo associato, quindi, se il gruppo è disponibile, selezionare **join gruppo**.

    Se il gruppo richiede che il proprietario del gruppo approvi l'appartenenza, verrà chiesto di immettere una giustificazione aziendale per i motivi per cui è necessario partecipare al gruppo e quindi selezionare **Request**. Se il gruppo non richiede l'approvazione, l'utente viene immediatamente aggiunto come membro e il gruppo viene visualizzato nei **gruppi che sono in** elenco.

3. Se è stato aggiunto un gruppo per errore o se non è più necessario farne parte, è possibile selezionare il nome del gruppo nella pagina **Unisci gruppi** e quindi selezionare **Lascia gruppo**.

    ![Pagina Unisci gruppi, con pulsante Lascia gruppo evidenziato](media/my-apps-portal/my-apps-portal-leave-group-link.png)    

## <a name="next-steps"></a>Passaggi successivi

- [Accedere e usare le app nel portale app personali](my-apps-portal-end-user-access.md).

- [Modificare le informazioni del profilo](my-apps-portal-end-user-update-profile.md).

- [Eseguire le proprie](my-apps-portal-end-user-access-reviews.md)verifiche di accesso.
