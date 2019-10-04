---
title: Creare una verifica di accesso di gruppi o applicazioni-Azure Active Directory | Microsoft Docs
description: Informazioni su come creare una verifica di accesso dei membri del gruppo o dell'accesso alle applicazioni in Azure Active Directory verifiche di accesso.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 230fb40c8e3a100d2fdfa0af6b40c93c3e5b47d2
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499734"
---
# <a name="create-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Creare una verifica di accesso di gruppi o applicazioni in Azure AD verifiche di accesso

L'accesso a gruppi e applicazioni per dipendenti e utenti guest cambia nel corso del tempo. Per ridurre i rischi associati alle assegnazioni di accesso obsolete, gli amministratori possono usare Azure Active Directory (Azure AD) per creare verifiche di accesso per i membri dei gruppi o l'accesso alle applicazioni. Se è necessario esaminare periodicamente l'accesso, è anche possibile creare verifiche di accesso ricorrenti. Per altre informazioni su questi scenari, vedere [Gestire l'accesso utente](manage-user-access-with-access-reviews.md) e [Gestire l'accesso guest](manage-guest-access-with-access-reviews.md).

Questo articolo descrive come creare una o più verifiche di accesso per i membri del gruppo o l'accesso alle applicazioni.

## <a name="prerequisites"></a>Prerequisiti

- Azure AD P2 Premium
- Amministratore globale o Amministratore utenti

Per altre informazioni, vedere [quali utenti devono avere licenze?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="create-one-or-more-access-reviews"></a>Creare una o più verifiche di accesso

1. Accedere alla portale di Azure e aprire la [pagina governance delle identità](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Nel menu a sinistra fare clic su verifiche di **accesso**.

1. Fare clic su **Nuova verifica di accesso** per creare una nuova verifica di accesso.

    ![Riquadro verifiche di accesso nella governance delle identità](./media/create-access-review/access-reviews.png)

1. Assegnare un nome alla verifica di accesso. Facoltativamente è possibile assegnare una descrizione alla verifica. Il nome e la descrizione vengono visualizzati dai revisori.

    ![Creare una verifica di accesso - Nome della verifica e descrizione](./media/create-access-review/name-description.png)

1. Impostare un valore per **Data di inizio**. Per impostazione predefinita, la verifica di accesso viene eseguita una sola volta, viene avviata lo stesso giorno in cui viene creata e termina entro un mese. È possibile cambiare le date di inizio e di fine per iniziare la verifica di accesso in futuro e impostare la durata sul numero di giorni desiderato.

    ![Creare una verifica di accesso - Date di inizio e di fine](./media/create-access-review/start-end-dates.png)

1. Per fare in modo che la verifica di accesso ricorra, modificare l'impostazione della **frequenza** da **una volta a una** **settimana**, ogni **mese**, ogni **trimestre** o **ogni anno**. Usare il dispositivo di scorrimento **durata** o la casella di testo per definire il numero di giorni per cui ogni revisione della serie ricorrente verrà aperta per l'input dai revisori. La durata massima che è possibile impostare per una verifica mensile, ad esempio, è di 27 giorni, per evitare la sovrapposizione delle verifiche.

1. Usare l'impostazione **Fine** per specificare come terminare la serie di verifiche di accesso ricorrenti. La serie può terminare in tre modi: può essere eseguita in modo continuo per avviare le verifiche per un periodo illimitato o fino a una data specifica oppure terminare dopo che è stato completato un numero definito di occorrenze. Un altro amministratore utente o un altro amministratore globale può arrestare la serie dopo la creazione modificando la data in **Impostazioni**, in modo che termini a tale data.

1. Nella sezione **utenti** specificare gli utenti a cui si applica la verifica di accesso. Le verifiche di accesso possono essere relative ai membri di un gruppo o agli utenti assegnati a un'applicazione. È possibile definire ulteriormente l'ambito della verifica di accesso in modo da controllare solo gli utenti guest che sono membri o che sono stati assegnati all'applicazione invece di controllare tutti gli utenti che sono membri o che hanno accesso all'applicazione.

    ![Creare una verifica di accesso - Utenti](./media/create-access-review/users.png)

1. Nella sezione **gruppo** selezionare uno o più gruppi di cui si vuole verificare l'appartenenza.

    > [!NOTE]
    > Se si seleziona più di un gruppo, vengono create più verifiche di accesso. Se ad esempio si seleziona cinque gruppi, vengono create cinque verifiche di accesso separate.
    
    ![Creare una verifica di accesso-selezionare un gruppo](./media/create-access-review/select-group.png)

1. Nella sezione **applicazioni** (se è stato selezionato **assegnato a un'applicazione** nel passaggio 8), selezionare le applicazioni a cui si desidera esaminare l'accesso.

    > [!NOTE]
    > Se si seleziona più di un'applicazione, vengono create più verifiche di accesso. Se ad esempio si selezionano cinque applicazioni, vengono create cinque verifiche di accesso separate.
    
    ![Creare una verifica di accesso-selezionare l'applicazione](./media/create-access-review/select-application.png)

1. Nella sezione **Revisori** selezionare una o più persone per la verifica di tutti gli utenti nell'ambito. In alternativa è possibile fare in modo che i membri verifichino il proprio accesso. Se la risorsa è un gruppo, è possibile richiedere ai proprietari del gruppo di eseguire la verifica. È possibile anche richiedere che i revisori specifichino un motivo per l'approvazione dell'accesso.

    ![Creare una verifica di accesso - Revisori](./media/create-access-review/reviewers.png)

1. Nella sezione **Programmi** selezionare il programma da usare. **Programma predefinito** è sempre presente.

    ![Creare una verifica di accesso - Programmi](./media/create-access-review/programs.png)

    È possibile semplificare il rilevamento e la raccolta delle verifiche di accesso per scopi diversi organizzandole nei programmi. Ogni verifica di accesso può essere collegata a un programma. Quando si preparano i report per un revisore, è possibile concentrarsi sulle verifiche di accesso nell'ambito per un'iniziativa specifica. I risultati dei programmi e della verifica di accesso sono visibili agli utenti con ruolo amministratore globale, Amministratore utenti, amministratore della sicurezza o lettore sicurezza.

    Per visualizzare un elenco di programmi, passare alla pagina delle verifiche di accesso e selezionare **programmi**. Se si dispone di un ruolo amministratore globale o Amministratore utenti, è possibile creare programmi aggiuntivi. Ad esempio, è possibile scegliere che sia disponibile un programma per ogni obiettivo di business o iniziativa di conformità. Se un programma non è più necessario e non dispone di controlli collegati, è possibile eliminarlo.

### <a name="upon-completion-settings"></a>Impostazioni al completamento

1. Per specificare cosa succede dopo il completamento di una verifica, espandere la sezione **Impostazioni al completamento**.

    ![Creare una verifica di accesso-dopo le impostazioni di completamento](./media/create-access-review/upon-completion-settings.png)

1. Se si vuole rimuovere automaticamente l'accesso per gli utenti rifiutati, impostare l'opzione **Applica automaticamente i risultati alla risorsa** su **Abilita**. Per applicare manualmente i risultati al termine della verifica, impostare l'opzione su **Disabilita**.

1. Usare l'elenco **In caso di mancata risposta del revisore** per specificare cosa accade per gli utenti non sottoposti a verifica dal revisore entro il periodo di verifica. Questa impostazione non ha alcun impatto sugli utenti che sono stati sottoposti a verifica manualmente dai revisori. Se la decisione finale del revisore è il rifiuto, l'accesso dell'utente verrà rimosso.

    - **Nessuna modifica**: non viene apportata alcuna modifica all'accesso dell'utente
    - **Rimuovi accesso**: l'accesso dell'utente viene rimosso
    - **Approva accesso**: l'accesso dell'utente viene approvato
    - **Accetta i consigli**: vengono applicati i consigli del sistema per rifiutare o approvare l'accesso continuo dell'utente

### <a name="advanced-settings"></a>Impostazioni avanzate

1. Per specificare impostazioni aggiuntive, espandere la sezione **Impostazioni avanzate**.

    ![Creare una verifica di accesso-impostazioni avanzate](./media/create-access-review/advanced-settings.png)

1. Impostare **Mostra i consigli** su **Abilita** per mostrare ai revisori i consigli del sistema basati sulle informazioni di accesso dell'utente.

1. Impostare **Richiedi il motivo all'approvazione** su **Abilita** per richiedere al revisore di specificare un motivo per l'approvazione.

1. Impostare **Notifiche tramite posta elettronica** su **Abilita** per fare in modo che Azure AD invii notifiche tramite posta elettronica ai revisori all'avvio di una verifica di accesso e agli amministratori al completamento di una verifica.

1. Impostare **Promemoria** su **Abilita** per fare in modo che Azure AD invii promemoria delle verifiche di accesso in corso ai revisori che non hanno completato la verifica.

    Per impostazione predefinita, Azure AD invia automaticamente un promemoria ai revisori che non hanno ancora risposto a metà periodo rispetto alla data di scadenza.

## <a name="start-the-access-review"></a>Avviare la verifica di accesso

Una volta specificate le impostazioni per una verifica di accesso, fare clic su **Avvia**. La verifica di accesso verrà visualizzata nell'elenco con un indicatore dello stato.

![Elenco delle verifiche di accesso e del relativo stato](./media/create-access-review/access-reviews-list.png)

Per impostazione predefinita, Azure AD invia un messaggio di posta elettronica ai revisori poco tempo dopo l'inizio della verifica. Se si imposta Azure AD in modo che non invii un messaggio di posta elettronica, assicurarsi di informare i revisori che vi è una verifica di accesso in attesa di completamento. È possibile visualizzare le istruzioni su come [verificare l'accesso ai gruppi o alle applicazioni](perform-access-review.md). Se la verifica è destinata ai guest per verificare il proprio accesso, visualizzare le istruzioni su come [verificare l'accesso da soli a gruppi o applicazioni](review-your-access.md).

Se sono stati assegnati Guest come revisori che non hanno accettato l'invito, non riceveranno un messaggio di posta elettronica dalle verifiche di accesso perché devono prima accettare l'invito prima di esaminarlo.

## <a name="create-reviews-via-apis"></a>Creare verifiche tramite API

È anche possibile creare verifiche di accesso usando le API. Le operazioni eseguite per gestire le verifiche di accesso di gruppi e utenti di applicazioni nel portale di Azure possono essere svolte anche tramite le API Microsoft Graph. Per altre informazioni, vedere le informazioni di [riferimento sull'API Azure ad Access](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta)revisioni. Per un esempio di codice, vedere [esempio di recupero delle verifiche di accesso Azure ad tramite Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Passaggi successivi

- [Verificare l'accesso a gruppi o applicazioni](perform-access-review.md)
- [Verificare l'accesso per se stessi a gruppi o applicazioni](review-your-access.md)
- [Completare una verifica di accesso di gruppi o applicazioni](complete-access-review.md)
