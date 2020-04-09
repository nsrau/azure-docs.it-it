---
title: Creare una verifica di accesso di gruppi & applicazioni - Azure ADCreate an access review of groups & applications - Azure AD
description: Informazioni su come creare una verifica di accesso dei membri del gruppo o dell'accesso alle applicazioni nelle verifiche di accesso ad Azure Active Directory.Learn how to create an access review of group members or application access in Azure Active Directory access reviews.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/08/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e01951b6147cfc39fe6c46035db822071bda3aa
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984072"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Creare una verifica di accesso di gruppi e applicazioni nelle verifiche di accesso di Azure ADCreate an access review of groups and applications in Azure AD access reviews

L'accesso a gruppi e applicazioni per dipendenti e utenti guest cambia nel corso del tempo. Per ridurre i rischi associati alle assegnazioni di accesso obsolete, gli amministratori possono usare Azure Active Directory (Azure AD) per creare verifiche di accesso per i membri dei gruppi o l'accesso alle applicazioni. Se è necessario esaminare periodicamente l'accesso, è anche possibile creare verifiche di accesso ricorrenti. Per altre informazioni su questi scenari, vedere [Gestire l'accesso utente](manage-user-access-with-access-reviews.md) e [Gestire l'accesso guest](manage-guest-access-with-access-reviews.md).

In questo articolo viene descritto come creare una o più verifiche di accesso per i membri del gruppo o l'accesso alle applicazioni.

## <a name="prerequisites"></a>Prerequisiti

- Azure AD Premium P2
- Amministratore globale o Amministratore utenti

Per altre informazioni, vedere [Requisiti relativi alle licenze](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Creare una o più verifiche di accesso

1. Accedere al portale di Azure e aprire la [pagina Governance identità](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Nel menu a sinistra, fai clic su **Revisioni di accessi.**

1. Fare clic su **Nuova verifica di accesso** per creare una nuova verifica di accesso.

    ![Riquadro Delle verifiche di accesso in Identity Governance](./media/create-access-review/access-reviews.png)

1. Assegnare un nome alla verifica di accesso. Facoltativamente è possibile assegnare una descrizione alla verifica. Il nome e la descrizione vengono visualizzati dai revisori.

    ![Creare una verifica di accesso - Nome della verifica e descrizione](./media/create-access-review/name-description.png)

1. Impostare un valore per **Data di inizio**. Per impostazione predefinita, la verifica di accesso viene eseguita una sola volta, viene avviata lo stesso giorno in cui viene creata e termina entro un mese. È possibile cambiare le date di inizio e di fine per iniziare la verifica di accesso in futuro e impostare la durata sul numero di giorni desiderato.

    ![Creare una verifica di accesso - Date di inizio e di fine](./media/create-access-review/start-end-dates.png)

1. Per rendere ricorrente la verifica dell'accesso, modificare l'impostazione **Frequenza** da **Una volta** a **Settimanale**, **Mensile**, **Trimestrale**, **Semestrale**o **Annuale**. Utilizzare il dispositivo di scorrimento **Durata** o la casella di testo per definire il numero di giorni in cui ogni revisione della serie ricorrente sarà aperta per l'input dei revisori. La durata massima che è possibile impostare per una verifica mensile, ad esempio, è di 27 giorni, per evitare la sovrapposizione delle verifiche.

1. Usare l'impostazione **Fine** per specificare come terminare la serie di verifiche di accesso ricorrenti. La serie può terminare in tre modi: può essere eseguita in modo continuo per avviare le verifiche per un periodo illimitato o fino a una data specifica oppure terminare dopo che è stato completato un numero definito di occorrenze. L'utente, un altro amministratore utente o un altro amministratore globale può interrompere la serie dopo la creazione modificando la data in **Impostazioni**, in modo che termini in tale data.

1. Nella sezione **Utenti** specificare gli utenti a cui si applica la verifica di accesso. Le verifiche di accesso possono essere relative ai membri di un gruppo o agli utenti assegnati a un'applicazione. È possibile definire ulteriormente l'ambito della verifica di accesso in modo da controllare solo gli utenti guest che sono membri o che sono stati assegnati all'applicazione invece di controllare tutti gli utenti che sono membri o che hanno accesso all'applicazione.

    ![Creare una verifica di accesso - Utenti](./media/create-access-review/users.png)

1. Nella sezione **Gruppo** selezionare uno o più gruppi di cui si desidera esaminare l'appartenenza.

    > [!NOTE]
    > Selezionando più di un gruppo verranno create più verifiche di accesso. Ad esempio, selezionando cinque gruppi verranno create cinque verifiche di accesso separate.
    
    ![Creare una verifica di accesso - Seleziona gruppoCreate an access review - Select group](./media/create-access-review/select-group.png)

1. Nella sezione **Applicazioni** (se nel passaggio 8 è stata selezionata l'opzione **Assegnato a un'applicazione),** selezionare le applicazioni a cui si desidera verificare l'accesso.

    > [!NOTE]
    > Selezionando più di un'applicazione verranno create più verifiche di accesso. Ad esempio, selezionando cinque applicazioni verranno create cinque verifiche di accesso separate.
    
    ![Creare una verifica di accesso - Selezionare l'applicazioneCreate an access review - Select application](./media/create-access-review/select-application.png)

1. Nella sezione **Revisori** selezionare una o più persone per la verifica di tutti gli utenti nell'ambito. In alternativa è possibile fare in modo che i membri verifichino il proprio accesso. Se la risorsa è un gruppo, è possibile richiedere ai proprietari del gruppo di eseguire la verifica. È possibile anche richiedere che i revisori specifichino un motivo per l'approvazione dell'accesso.

    ![Creare una verifica di accesso - Revisori](./media/create-access-review/reviewers.png)

1. Nella sezione **Programmi** selezionare il programma da usare. **Programma predefinito** è sempre presente.

    ![Creare una verifica di accesso - Programmi](./media/create-access-review/programs.png)

    È possibile semplificare il rilevamento e la raccolta delle verifiche di accesso per scopi diversi organizzandole nei programmi. Ogni verifica di accesso può essere collegata a un programma. Quando si preparano i report per un revisore, è possibile concentrarsi sulle verifiche di accesso nell'ambito per un'iniziativa specifica. I programmi e i risultati della verifica dell'accesso sono visibili agli utenti del ruolo Amministratore globale, Amministratore utente, Amministratore sicurezza o Lettore sicurezza.

    Per visualizzare un elenco di programmi, accedere alla pagina delle verifiche di accesso e selezionare **Programmi**. Se si ha un ruolo di amministratore globale o amministratore utente, è possibile creare programmi aggiuntivi. Ad esempio, è possibile scegliere che sia disponibile un programma per ogni obiettivo di business o iniziativa di conformità. Se un programma non è più necessario e non dispone di controlli collegati, è possibile eliminarlo.

### <a name="upon-completion-settings"></a>Impostazioni al completamento

1. Per specificare cosa succede dopo il completamento di una verifica, espandere la sezione **Impostazioni al completamento**.

    ![Creare una verifica di accesso - Impostazioni al completamentoCreate an access review - Upon completion settings](./media/create-access-review/upon-completion-settings.png)

1. Se si desidera rimuovere automaticamente, l'accesso per gli utenti negati, impostare **Applica automaticamente i risultati alla risorsa** su **Abilita**. Per applicare manualmente i risultati al termine della verifica, impostare l'opzione su **Disabilita**.

1. Usare l'elenco **In caso di mancata risposta del revisore** per specificare cosa accade per gli utenti non sottoposti a verifica dal revisore entro il periodo di verifica. Questa impostazione non ha alcun impatto sugli utenti che sono stati sottoposti a verifica manualmente dai revisori. Se la decisione finale del revisore è il rifiuto, l'accesso dell'utente verrà rimosso.

    - **Nessuna modifica**: non viene apportata alcuna modifica all'accesso dell'utente
    - **Rimuovi accesso**: l'accesso dell'utente viene rimosso
    - **Approva accesso**: l'accesso dell'utente viene approvato
    - **Accetta i consigli**: vengono applicati i consigli del sistema per rifiutare o approvare l'accesso continuo dell'utente

### <a name="advanced-settings"></a>Impostazioni avanzate

1. Per specificare impostazioni aggiuntive, espandere la sezione **Impostazioni avanzate**.

    ![Creare una verifica di accesso - Impostazioni avanzate](./media/create-access-review/advanced-settings.png)

1. Impostare **Mostra i consigli** su **Abilita** per mostrare ai revisori i consigli del sistema basati sulle informazioni di accesso dell'utente.

1. Impostare **Richiedi il motivo all'approvazione** su **Abilita** per richiedere al revisore di specificare un motivo per l'approvazione.

1. Impostare **Notifiche tramite posta elettronica** su **Abilita** per fare in modo che Azure AD invii notifiche tramite posta elettronica ai revisori all'avvio di una verifica di accesso e agli amministratori al completamento di una verifica.

1. Impostare **Promemoria** su **Abilita** per fare in modo che Azure AD invii promemoria delle verifiche di accesso in corso ai revisori che non hanno completato la verifica.

    Per impostazione predefinita, Azure AD invia automaticamente un promemoria ai revisori che non hanno ancora risposto a metà periodo rispetto alla data di scadenza.

## <a name="start-the-access-review"></a>Avviare la verifica di accesso

Una volta specificate le impostazioni per una verifica di accesso, fare clic su **Avvia**. La verifica di accesso verrà visualizzata nell'elenco con un indicatore del relativo stato.

![Elenco delle verifiche di accesso e relativo stato](./media/create-access-review/access-reviews-list.png)

Per impostazione predefinita, Azure AD invia un messaggio di posta elettronica ai revisori poco tempo dopo l'inizio della verifica. Se si imposta Azure AD in modo che non invii un messaggio di posta elettronica, assicurarsi di informare i revisori che vi è una verifica di accesso in attesa di completamento. È possibile mostrare loro le istruzioni su come [controllare l'accesso a gruppi o applicazioni](perform-access-review.md). Se la tua recensione prevede che gli ospiti rivedano il proprio accesso, mostra loro le istruzioni su come [controllare l'accesso a gruppi o applicazioni.](review-your-access.md)

Se hai assegnato ospiti come revisori e non hanno accettato l'invito, non riceveranno un'e-mail dalle verifiche di accesso perché devono prima accettare l'invito prima di rivederlo.

## <a name="access-review-status-table"></a>Tabella dello stato di verifica di accessi

| Stato | Definizione |
|--------|------------|
|NotStarted | La revisione è stata creata, l'individuazione degli utenti è in attesa di avvio. |
|Inizializzazione in corso   | L'individuazione degli utenti è in corso per identificare tutti gli utenti che fanno parte della revisione. |
|Avvio in corso | La revisione sta iniziando. Se le notifiche e-mail sono abilitate, i messaggi di posta elettronica vengono inviati ai revisori. |
|InProgress | La revisione è iniziata. Se le notifiche e-mail sono abilitate, le e-mail sono state inviate ai revisori. I revisori possono inviare decisioni fino alla data di scadenza. |
|Completamento | È in corso il completamento della revisione e vengono inviati messaggi di posta elettronica al proprietario della revisione. |
|Revisione automatica | La revisione è in una fase di revisione del sistema. Il sistema sta registrando le decisioni per gli utenti che non sono stati esaminati in base a raccomandazioni o decisioni preconfigurate. |
|Revisione automatica | Le decisioni sono state registrate dal sistema per tutti gli utenti che non sono stati rivisti. La revisione è pronta per procedere **all'applicazione** se l'applicazione automatica è abilitata. |
|Applicazione | Non verrà apportata alcuna modifica all'accesso per gli utenti approvati. |
|Applicazione | Gli utenti con errori, se presenti, sono stati rimossi dalla risorsa o dalla directory. |

## <a name="create-reviews-via-apis"></a>Creare verifiche tramite API

È anche possibile creare verifiche di accesso usando le API. Le operazioni eseguite per gestire le verifiche di accesso di gruppi e utenti di applicazioni nel portale di Azure possono essere svolte anche tramite le API Microsoft Graph. Per altre informazioni, vedere le informazioni di riferimento sull'API di controllo delle revisioni di accesso di Azure AD.For more [information,](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta)see the Azure AD access reviews API reference . Per un esempio di codice, vedere Esempio di recupero delle verifiche di accesso di Azure AD tramite Microsoft Graph.For a code sample, see [Example of retrieving Azure AD access reviews via Microsoft Graph.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096)

## <a name="next-steps"></a>Passaggi successivi

- [Verificare l'accesso a gruppi o applicazioni](perform-access-review.md)
- [Esaminare l'accesso a gruppi o applicazioni](review-your-access.md)
- [Completare una verifica di accesso di gruppi o applicazioni](complete-access-review.md)
