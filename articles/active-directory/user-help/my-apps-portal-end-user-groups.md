---
title: Aggiornare le informazioni sui gruppi nel portale App personali - Azure AD
description: Di seguito viene descritto come visualizzare e aggiornare le informazioni relative ai gruppi, tra cui la visualizzazione dei gruppi di cui si è proprietari, la creazione di nuovi gruppi, la visualizzazione dei gruppi di cui si fa parte e la partecipazione a quelli di cui non si fa parte.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 10/19/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: cff1298f12d26b1260213d75701c282e0a82511d
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124153"
---
# <a name="update-your-groups-info-on-the-my-apps-portal"></a>Aggiornare le informazioni sui gruppi nel portale App personali

È possibile usare l'account aziendale o dell'Istituto di istruzione con il portale delle **app personali** basato sul Web per visualizzare e avviare molte delle app basate sul cloud dell'organizzazione, per aggiornare alcune delle informazioni sul profilo e sull'account, per visualizzare le informazioni sui **gruppi** e per eseguire verifiche di **accesso** per le app e i gruppi. Se non si ha accesso al portale **App personali** , è necessario contattare il supporto tecnico per l'autorizzazione.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Questo contenuto è destinato agli utenti. Gli amministratori possono trovare altre informazioni su come configurare e gestire le app basate sul cloud nella [documentazione di Gestione applicazioni](../manage-apps/index.yml).

## <a name="view-your-groups-information"></a>Visualizzare le informazioni sui gruppi

Se l'amministratore ha concesso l'autorizzazione a visualizzare il riquadro **Gruppi** , è possibile:

- **In qualità di membro di un gruppo:** visualizzare i dettagli o lasciare un gruppo.

- **In qualità di proprietario del gruppo:** visualizzare i dettagli, creare un nuovo gruppo, aggiungere o rimuovere membri oppure eliminare il gruppo.

### <a name="to-view-your-groups-information"></a>Per visualizzare le informazioni sui gruppi

1. Accedere all'account aziendale o dell'istituto di istruzione.

2. Aprire il Web browser e passare a https://myapps.microsoft.com o usare il collegamento fornito dall'organizzazione. Ad esempio, è possibile un indirizzamento a una pagina personalizzata per l'organizzazione come https://myapps.microsoft.com/contoso.com.

    Viene visualizzata la pagina **App** , che mostra tutte le app basate sul cloud di proprietà dell'organizzazione e disponibili per l'uso.

    ![Pagina App nel portale App personali](media/my-apps-portal-end-user-groups/my-apps-home-large.png)

3. Trascinare il menu **app personali** e selezionare **gruppi personali** per visualizzare le informazioni relative al gruppo.

4. A seconda delle autorizzazioni, è possibile utilizzare la pagina **gruppi personali** per:

    - **Esaminare i gruppi di cui si è proprietari.** Visualizzare informazioni sui gruppi di cui si è proprietari nell'organizzazione all'interno dell'area **Gruppi di cui si è proprietari** . Se si seleziona il nome di un gruppo specifico, vengono forniti altri dettagli sul gruppo, tra cui il tipo di gruppo, il numero di membri, i criteri di partecipazione e l'elenco dei membri attivi.

    - **Creare un nuovo gruppo.** Creare un nuovo gruppo con l'utente come proprietario dall'area **Gruppi di cui si è proprietari** . Per i passaggi specifici, vedere la sezione [Creare un nuovo gruppo](#create-a-new-group) di questo articolo.

    - **Modificare un gruppo esistente.** Modificare i dettagli per uno dei gruppi personali. Per i passaggi specifici, vedere la sezione [Modificare un gruppo esistente](#edit-an-existing-group) di questo articolo.

    - **Aggiungere o rimuovere membri.** Aggiungere o rimuovere membri per i gruppi di cui si è proprietari. Per i passaggi specifici, vedere la sezione [Aggiungere o rimuovere un membro](#add-or-remove-a-member) di questo articolo.

    - **Rinnovare un gruppo di Office 365.** Se l'organizzazione lo consente, è possibile rinnovare i gruppi di Office 365. Per i passaggi specifici, vedere la sezione [Rinnovare un gruppo di Office 365](#renew-an-office-365-group) di questo articolo. 

    - **Eliminare un gruppo.** Eliminare gruppi di cui si è proprietari. Per i passaggi specifici, vedere la sezione [Eliminare un gruppo](#delete-a-group) di questo articolo.

    - **Esaminare i gruppi di cui si fa parte.** Visualizzare i nomi di tutti i gruppi di cui si è membri nell'area **Gruppi a cui si appartiene** . Se si seleziona il nome di un gruppo specifico, vengono forniti altri dettagli sul gruppo, tra cui il tipo di gruppo, il numero di membri, i criteri di partecipazione e l'elenco dei membri attivi.

    - **Partecipare a un gruppo.** Partecipare a un gruppo esistente di cui non si fa ancora parte dall'area **Gruppi a cui si appartiene** . Per i passaggi specifici, vedere la sezione [Partecipare a un gruppo esistente](#join-an-existing-group).

## <a name="create-a-new-group"></a>Creare un nuovo gruppo

1. Nella pagina **Gruppi** selezionare **Crea un gruppo** nell'area **Gruppi a cui si appartiene** .

    Viene visualizzata la finestra **Crea gruppo** .

    ![Finestra Crea gruppo](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Immettere le informazioni necessarie:

    - **Tipo gruppo:**

        - **Sicurezza.** Usato per gestire l'accesso di membri e computer a risorse condivise per un gruppo di utenti. Ad esempio, è possibile creare un gruppo di sicurezza per criteri di sicurezza specifici. In questo modo, è possibile concedere un set di autorizzazioni a tutti i membri in una sola volta, invece di dover aggiungere autorizzazioni per ogni membro singolarmente.

        - **Office 365.** Offre opportunità di collaborazione consentendo ai membri di accedere a cassette postali, calendari, file, siti di SharePoint e altri elementi condivisi. Questa opzione consente anche di concedere l'accesso al gruppo a utenti esterni all'organizzazione.

    - **Nome gruppo.** Aggiungere un nome per il gruppo, significativo e facile da ricordare.

    - **Descrizione del gruppo (facoltativa).** Aggiungere una descrizione facoltativa per il gruppo.

    - **Criteri di gruppo.** Scegliere questa opzione per consentire la partecipazione al gruppo a tutti o l'aggiunta di nuovi membri solo al proprietario del gruppo.

3. Selezionare **Create** (Crea).

    Il nuovo gruppo viene creato con l'utente come proprietario e viene visualizzato nell'elenco **Gruppi di cui si è proprietari** . Poiché l'utente è il proprietario, questo gruppo viene visualizzato anche nell'elenco **Gruppi a cui si appartiene** .

## <a name="edit-an-existing-group"></a>Modificare un gruppo esistente

Dopo aver creato un gruppo, è possibile modificarne i dettagli e aggiornare eventuali informazioni esistenti.

1. Selezionare il gruppo che si desidera modificare nella pagina **Gruppi** , quindi selezionare **Modifica dettagli** nella pagina *&lt;group_name&gt;* .

    Viene visualizzata la finestra **Modifica dettagli** , dove è possibile aggiornare le informazioni aggiunte alla creazione del gruppo.

2. Apportare tutte le modifiche necessarie e selezionare **Aggiorna** .

## <a name="add-or-remove-a-member"></a>Aggiungere o rimuovere un membro

È possibile aggiungere o rimuovere membri nei gruppi di cui si è proprietari.

1. Selezionare il gruppo a cui si desidera aggiungere membri, quindi selezionare **+** nella pagina *&lt;group_name&gt;* .

    ![Aggiunta di un membro del gruppo con il segno + evidenziato](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Cercare il membro che si vuole aggiungere nella casella **Aggiungi membri** , quindi selezionare **Aggiungi** .

    ![Finestra Aggiungi membri con un nuovo membro da aggiungere](media/my-apps-portal/my-apps-portal-add-member-page.png)

    Il nuovo membro riceve un invito a iniziare ad accedere alle app dell'organizzazione.

3. Se un membro è stato aggiunto per errore o ha lasciato l'organizzazione, è possibile rimuoverlo selezionando **Rimuovi membro** accanto al suo nome nella pagina *&lt;group_name&gt;* .

    ![Rimozione di un membro con collegamento di rimozione evidenziato](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Rinnovare un gruppo di Office 365

Se l'organizzazione lo consente, è possibile rinnovare un gruppo di Office 365 estendendo la data di scadenza.

1. Selezionare il gruppo di Office 365 che si vuole rinnovare, quindi selezionare **Rinnova gruppo** .

    ![Rinnovare un gruppo di Office 365 estendendo la data di scadenza](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. Fare clic su **OK** per chiudere il messaggio di conferma.

    Dopo l'aggiornamento della pagina si visualizzeranno la **Data dell'ultimo rinnovo** e la data di **Scadenza del gruppo** .

## <a name="delete-a-group"></a>Eliminare un gruppo

È possibile eliminare qualsiasi gruppo in qualsiasi momento. Tuttavia, se si elimina un gruppo per errore, sarà necessario ricrearlo e aggiungere nuovamente i membri.

1. Selezionare il gruppo che si desidera eliminare definitivamente, quindi selezionare **Elimina gruppo** nella pagina *&lt;group_name&gt;* .

    ![Pagina < Group_name> con il collegamento Elimina gruppo evidenziato](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. Selezionare **Sì** nel messaggio di conferma.

    Il gruppo viene eliminato definitivamente.

## <a name="join-an-existing-group"></a>Partecipare a un gruppo esistente

È possibile lasciare o entrare in un gruppo già esistente dalla pagina **Gruppi** .

1. Nella pagina **Gruppi** selezionare **Crea un gruppo** nell'area **Gruppi a cui si appartiene** .

    Viene visualizzata la pagina **Partecipa a gruppi** .

    ![Pagina Partecipa a gruppi, con il pulsante Partecipa al gruppo evidenziato](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. Nella pagina **Partecipa a gruppi** selezionare il nome del gruppo del quale si desidera fare parte, visualizzarne i dettagli e, se il gruppo è disponibile, selezionare **Partecipa al gruppo** .

    Se il gruppo richiede l'approvazione dell'appartenenza da parte del proprietario, verrà richiesto di immettere una motivazione aziendale con i motivi per cui è necessario partecipare al gruppo, quindi selezionare **Richiesta** . Se il gruppo non richiede l'approvazione, l'utente viene immediatamente aggiunto come membro e il gruppo viene visualizzato nell'elenco **Gruppi a cui si appartiene** .

3. Se si è stati aggiunti a un gruppo per errore o se non è più necessario farne parte, è possibile selezionare il nome del gruppo dalla pagina **Partecipa a gruppi** , quindi selezionare **Lascia gruppo** .

    ![Pagina Partecipa a gruppi, con il pulsante Lascia gruppo evidenziato](media/my-apps-portal/my-apps-portal-leave-group-link.png)

## <a name="next-steps"></a>Passaggi successivi

- [Accedere e usare le app nel portale App personali](my-apps-portal-end-user-access.md).

- [Cambiare le informazioni del profilo](./my-account-portal-settings.md).

- [Eseguire verifiche di accesso personali](my-apps-portal-end-user-access-reviews.md).