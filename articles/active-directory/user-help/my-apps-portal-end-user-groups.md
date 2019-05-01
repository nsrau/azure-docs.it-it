---
title: Aggiornare le tue info di gruppi da Azure Active Directory - portale App personali | Microsoft Docs
description: Informazioni su come visualizzare e aggiornare le informazioni correlate a gruppi, inclusa la visualizzazione di gruppi di cui si è proprietari, creando nuovi gruppi, visualizzazione dei gruppi a cui si ha già un membro e aggiunta di uno qualsiasi dei gruppi è non lo fanno già parte di.
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
ms.openlocfilehash: 578ca5fa1de338a92280e2dc7cc252fb616e2111
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64706688"
---
# <a name="update-your-groups-info-from-the-my-apps-portal"></a>Aggiornare le tue info di gruppi dal portale App personali
È possibile usare l'account aziendale o dell'istituto di istruzione con il portale **App personali** basato sul Web per visualizzare e avviare molte delle app dell'organizzazione basate sul cloud, per aggiornare alcune informazioni dell'account e del profilo, per visualizzare le informazioni sui **gruppi** e per eseguire **verifiche di accesso** per le app e i gruppi. Se non si ha accesso al portale **App personali**, è necessario contattare il supporto tecnico per l'autorizzazione.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Questo contenuto è destinato agli utenti. Gli amministratori possono trovare altre informazioni su come configurare e gestire le app basate sul cloud nella [documentazione di Gestione applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="view-your-groups-information"></a>Visualizzare le informazioni di gruppi
Se l'amministratore ha concesso l'autorizzazione per visualizzare il **gruppi** riquadro, è possibile:

- **Come membro del gruppo.** Visualizzare i dettagli o abbandonare qualsiasi gruppo.

- **Come un proprietario del gruppo.** Visualizzare i dettagli, creare un nuovo gruppo, aggiungere o rimuovere membri o eliminare il gruppo.

### <a name="to-view-your-groups-information"></a>Per visualizzare le informazioni sui gruppi

1.  Accedere all'account aziendale o dell'istituto di istruzione.

2.  Aprire il web browser e passare a https://myapps.microsoft.com, oppure usare il collegamento fornito dall'organizzazione. Ad esempio, si potrebbe essere indirizzati a una pagina personalizzata per l'organizzazione, ad esempio https://myapps.microsoft.com/contoso.com.

    Il **app** viene visualizzata la pagina che mostra tutte le app basate su cloud proprietà da parte dell'organizzazione e disponibili per l'utilizzo.

    ![Pagina delle app nel portale App personali](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. Selezionare il **gruppi** riquadro per visualizzare le informazioni correlate al gruppo.

    ![Pagina gruppi con sia di proprietà e i gruppi di membri](media/my-apps-portal/my-apps-portal-groups-page.png)

4. In base alle autorizzazioni, è possibile usare la **gruppi** pagina per:

    - **Controllare i gruppi di cui che si è proprietari.** Visualizzare informazioni sui gruppi si proprietari all'interno dell'organizzazione dal **gruppi di cui proprietari** area. Se si seleziona un nome di gruppo specifico fornisce altri dettagli relativi al gruppo, inclusi il tipo di gruppo, il numero di membri, i criteri di join e l'elenco di membri attivi.

    - **Creare un nuovo gruppo.** Creare un nuovo gruppo con l'utente come proprietario dal **gruppi di cui proprietari** area. Per passaggi specifici, vedere la [creare un nuovo gruppo](#create-a-new-group) sezione di questo articolo.

    - **Modificare un gruppo esistente.** Modificare i dettagli per uno qualsiasi dei gruppi personalizzati. Per passaggi specifici, vedere la [modifica un gruppo esistente](#edit-an-existing-group) sezione di questo articolo.

    - **Aggiungere o rimuovere membri.** Aggiungere o rimuovere membri per i gruppi di cui si è proprietari. Per passaggi specifici, vedere la [aggiungere o rimuovere un membro](#add-or-remove-a-member) sezione di questo articolo.

    - **Rinnovare un gruppo di Office 365.** Se l'organizzazione consente, è possibile rinnovare i gruppi di Office 365. Per passaggi specifici, vedere la [rinnovare un gruppo di Office 365](#renew-an-office-365-group) sezione di questo articolo. 

    - **Eliminare un gruppo.** Eliminare tutti i gruppi a cui si è proprietari. Per passaggi specifici, vedere la [Elimina un gruppo](#delete-a-group) sezione di questo articolo.

    - **Controllare i gruppi di cui di che si fa una parte.** Visualizzare i nomi di tutti i gruppi a cui si è un membro di **gruppi a cui appartiene** area. Se si seleziona un nome di gruppo specifico fornisce altri dettagli relativi al gruppo, inclusi il tipo di gruppo, il numero di membri, i criteri di join e l'elenco di membri attivi.

    - **Partecipare a un gruppo.** Partecipa a un gruppo esistente, per cui non si ha già un membro dal **gruppi a cui appartiene** area. Per passaggi specifici, vedere la [partecipa a un gruppo esistente](#join-an-existing-group).

## <a name="create-a-new-group"></a>Creare un nuovo gruppo
1. Nel **gruppi** pagina, selezionare **creare un gruppo** dal **gruppi di proprietà dell'utente** area.

    Il **Crea gruppo** verrà visualizzata la finestra.

    ![Creare una casella di gruppo](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Immettere le informazioni necessarie:

    - **Tipo gruppo:**
        
        - **Sicurezza.** Usato per gestire l'accesso di membri e computer a risorse condivise per un gruppo di utenti. Ad esempio, è possibile creare un gruppo di sicurezza per criteri di sicurezza specifici. In questo modo, è possibile concedere un set di autorizzazioni a tutti i membri in una sola volta, invece di dover aggiungere autorizzazioni per ogni membro singolarmente.

        - **Office 365.** Offre opportunità di collaborazione consentendo ai membri di accedere a cassette postali, calendari, file, siti di SharePoint e altri elementi condivisi. Questa opzione consente anche di concedere l'accesso al gruppo a utenti esterni all'organizzazione.

    - **Nome gruppo.** Aggiungere un nome per il gruppo, significativo e facile da ricordare.

    - **Descrizione del gruppo (facoltativo).** Aggiungere una descrizione facoltativa per il gruppo.

    - **Criteri di gruppo.** Scegliere di consentire a chiunque di accedere al gruppo o per consentire solo il proprietario del gruppo aggiungere membri.

3. Selezionare **Create**.

    Viene creato il nuovo gruppo con l'utente come proprietario e viene visualizzato nei **gruppi di cui proprietari** elenco. Poiché si è proprietari, questo gruppo viene visualizzato anche nella **gruppi a cui appartiene** elenco.

## <a name="edit-an-existing-group"></a>Modificare un gruppo esistente
Dopo aver creato un gruppo, è possibile modificare i dettagli, incluso l'aggiornamento di qualsiasi informazione esistente.

### <a name="to-edit-your-details"></a>Per modificare i dettagli
1. Selezionare il gruppo che si desidera modificare dal **gruppi** pagina e quindi selezionare **modificare i dettagli** sul *&lt;nome_gruppo&gt;* pagina.

    Il **modificare i dettagli** verrà visualizzata la finestra ed è possibile aggiornare le informazioni aggiunte al momento della creazione iniziale del gruppo.

2. Apportare tutte le modifiche e quindi scegliere **Update**.

## <a name="add-or-remove-a-member"></a>Aggiungere o rimuovere un membro
È possibile aggiungere o rimuovere membri per uno qualsiasi dei gruppi personalizzati.

### <a name="to-add-or-remove-a-member"></a>Per aggiungere o rimuovere un membro
1. Selezionare il gruppo di cui si desidera aggiungere membri a e quindi selezionare **+** sul *&lt;nome_gruppo&gt;* pagina.

    ![Aggiungere un membro del gruppo, con + sign evidenziato](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Ricerca per il membro che si desidera aggiungere, dal **aggiungere i membri** e quindi selezionare **Add**.

    ![Finestra di membri, Aggiungi con nuovo membro da aggiungere](media/my-apps-portal/my-apps-portal-add-member-page.png)

    Viene inviato un invito per il nuovo membro, per iniziare a usare l'accesso alle App dell'organizzazione.

3. Se è stato aggiunto un membro per errore, o se un membro ha lasciato l'organizzazione, è possibile rimuovere il membro selezionando **Rimuovi membro** accanto al nome del membro nel *&lt;nome_gruppo&gt;* pagina.

    ![Rimuovere un membro, con evidenziato il collegamento di rimozione](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Rinnovare un gruppo di Office 365
Se l'organizzazione consente, è possibile rinnovare un gruppo di Office 365, estendere la data di scadenza.

### <a name="to-renew-a-group"></a>Per rinnovare un gruppo
1. Selezionare il gruppo di Office 365 desiderato per il rinnovo e quindi selezionare **rinnova il gruppo**.

    ![Rinnovare un gruppo di Office 365, estendere la data di scadenza](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. Fare clic su **OK** per chiudere il messaggio di conferma.

    Dopo aver aggiornato la pagina, si noterà l'aggiornata **rinnovato ultima** e **raggruppare scadenza** date.

## <a name="delete-a-group"></a>Eliminare un gruppo
È possibile eliminare uno dei gruppi personalizzati in qualsiasi momento. Tuttavia, se si elimina accidentalmente un gruppo è possibile crearlo e aggiungere di nuovo i membri.

### <a name="to-delete-the-group"></a>Per eliminare il gruppo
1. Selezionare il gruppo di cui si vuole eliminare in modo permanente e quindi selezionare **Elimina gruppo** nel *&lt;nome_gruppo&gt;* pagina.

    ![pagina < nome_gruppo > con il collegamento gruppo Elimina evidenziato](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. Selezionare **Sì** nel messaggio di conferma.

    Il gruppo viene eliminato definitivamente. 
    
## <a name="join-an-existing-group"></a>Partecipa a un gruppo esistente
È possibile aggiungere un gruppo già esistente dal **gruppi** pagina.

### <a name="to-join-or-leave-a-group"></a>Per partecipare o abbandonare un gruppo

1. Nel **gruppi** pagina, selezionare **partecipa a gruppo** dal **gruppi a cui appartiene** area.

    Il **partecipare a gruppi** verrà visualizzata la pagina.

    ![Aggiungere pagina gruppi con il pulsante gruppo Join evidenziato](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. Nel **partecipare a gruppi** pagina, selezionare il nome del gruppo di cui si desidera aggiungere e visualizzare i dettagli del gruppo associato, se il gruppo è disponibile, selezionare **partecipa a gruppo**.

    Se il gruppo richiede il proprietario del gruppo approvare l'appartenenza, verrà richiesto di immettere una motivazione aziendale per il motivo per cui è necessario aggiungere il gruppo e quindi selezionare **richiedere**. Se il gruppo non richiede l'approvazione, esegue immediatamente aggiunto come membro e il gruppo viene visualizzato nei **gruppi a cui appartiene** elenco.

3. Se hai aderito per errore un gruppo o se è non è più necessario far parte di esso, è possibile selezionare il nome del gruppo dal **partecipare a gruppi** pagina e quindi selezionare **lascia gruppo**.

    ![Aggiungere pagina gruppi con il pulsante di uscita gruppo evidenziato](media/my-apps-portal/my-apps-portal-leave-group-link.png)    

## <a name="next-steps"></a>Passaggi successivi

- [Accedere e usare le app nel portale App personali](my-apps-portal-end-user-access.md).

- [Modificare le informazioni sul profilo](my-apps-portal-end-user-update-profile.md).

- [Eseguire le verifiche di accesso personalizzati](my-apps-portal-end-user-access-reviews.md).