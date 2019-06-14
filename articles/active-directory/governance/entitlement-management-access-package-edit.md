---
title: Modificare e gestire un pacchetto di accesso esistente nella gestione dei diritti di Azure AD (anteprima) - Azure Active Directory
description: Informazioni su come modificare e gestire un pacchetto di accesso esistente nella gestione dei diritti di Azure Active Directory (anteprima).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce51f4df50de50cef06bba699ca7c6f76bc5d166
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65833213"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Modificare e gestire un pacchetto di accesso esistente nella gestione dei diritti di Azure AD (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Un pacchetto di accesso consente di eseguire un'attività una tantum dei criteri e le risorse che gestisce automaticamente l'accesso per il ciclo di vita del pacchetto di accesso. Come manager di accesso pacchetto, è possibile modificare le risorse in un pacchetto di accesso in qualsiasi momento senza doversi preoccupare provisioning l'accesso dell'utente per le nuove risorse oppure di rimuovere l'accesso dalle risorse precedenti. I criteri possono anche essere aggiornati in qualsiasi momento, tuttavia, le modifiche influiscono solo sui nuovi accessi.

Questo articolo descrive come modificare e gestire pacchetti di accesso esistenti.

## <a name="add-resource-roles"></a>Aggiungere i ruoli delle risorse

Un ruolo di risorse è una raccolta di autorizzazioni associate a una risorsa. Il modo che rendere disponibili agli utenti di richiedere risorse consiste nell'aggiungere i ruoli delle risorse per il pacchetto di accesso. È possibile aggiungere i ruoli delle risorse per i gruppi, applicazioni e siti di SharePoint.

**Ruolo prerequisito:** L'utente amministratore, proprietario del catalogo o Gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **l'accesso ai pacchetti** e quindi aprire il pacchetto di accesso.

1. Nel menu a sinistra, fare clic su **i ruoli delle risorse**.

1. Fare clic su **aggiungere i ruoli delle risorse** per aprire i componente i ruoli delle risorse per accedere alla pagina del pacchetto.

    ![Accedere ai pacchetti: aggiungere i ruoli delle risorse](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. A seconda che si desideri aggiungere un gruppo, dell'applicazione o sito di SharePoint, eseguire i passaggi in una delle seguenti risorse ruolo sezioni.

### <a name="add-a-group-resource-role"></a>Aggiungere un ruolo del gruppo di risorse

È possibile avere management entitlement Aggiungi automaticamente gli utenti a un gruppo quando vengono assegnati un pacchetto di accesso. 

- Quando un gruppo fa parte di un pacchetto di accesso e un utente viene assegnato a tale pacchetto di accesso, l'utente viene aggiunto a tale gruppo, se non è già presente.
- Alla scadenza di assegnazione di pacchetti di accesso dell'utente, vengono rimossi dal gruppo, a meno che non dispongono attualmente di un'assegnazione a un altro pacchetto di accesso che include allo stesso gruppo.

È possibile selezionare qualsiasi gruppo di Office 365 o il gruppo di sicurezza di Azure AD.  Gli amministratori possono aggiungere un gruppo a un catalogo. i proprietari del catalogo è possono aggiungere qualsiasi gruppo nel catalogo se fossero proprietario del gruppo. Quando si seleziona un gruppo, tenere presente i seguenti vincoli di Azure AD:

- Quando un utente, compresi guest, viene aggiunto come membro a un gruppo, è possibile visualizzare tutti gli altri membri del gruppo.
- Azure AD non è possibile modificare l'appartenenza di un gruppo che è stato sincronizzato da Windows Server Active Directory con Azure AD Connect.  
- Impossibile aggiornare l'appartenenza a gruppi dinamici tramite l'aggiunta o rimozione di un membro, in modo che le appartenenze ai gruppi dinamiche non sono adatti per l'uso con gestione dei diritti.

1. Nel **aggiungere i ruoli delle risorse per accedere a package** pagina, fare clic su **gruppi** per aprire il riquadro selezionare i gruppi.

1. Selezionare i gruppi da includere nel pacchetto di accesso.

    ![Accedere ai pacchetti: aggiungere i ruoli delle risorse - selezionare i gruppi](./media/entitlement-management-access-package-edit/group-select.png)

1. Fare clic su **Seleziona**.

1. Nel **ruolo** elenco, selezionare **proprietario** oppure **membro**.

    In genere si seleziona il ruolo di membro. Se si seleziona il ruolo di proprietario, che consentirà agli utenti di aggiungere o rimuovere altri membri o proprietari.

    ![Accedere ai pacchetti: aggiungere il ruolo delle risorse per un gruppo](./media/entitlement-management-access-package-edit/group-role.png)

1. Fare clic su **Aggiungi**.

    Qualsiasi utente con le assegnazioni esistenti per il pacchetto di accesso verranno portato automaticamente i membri di questo gruppo quando viene aggiunto.

### <a name="add-an-application-resource-role"></a>Aggiungere un ruolo per le risorse dell'applicazione

È possibile avere Azure AD assegnare automaticamente gli utenti l'accesso a un'applicazione aziendale di Azure AD, tra cui le applicazioni SaaS e le applicazioni dell'organizzazione federate ad Azure AD, quando un utente viene assegnato un pacchetto di accesso. Per le applicazioni che si integrano con Azure AD tramite single sign-on federato, Azure AD rilascerà token federativo per gli utenti assegnati all'applicazione.

Le applicazioni possono avere più ruoli. Quando si aggiunge un'applicazione a un pacchetto di accesso, se tale applicazione dispone di più di un ruolo, è necessario specificare il ruolo appropriato per gli utenti.  Se si sviluppano applicazioni, è possibile leggere più sul modo in cui tali ruoli vengono forniti per le applicazioni nell'articolo sulla [configurare l'attestazione basata su ruolo rilasciata nel token SAML](../develop/active-directory-enterprise-app-role-management.md).

Una volta che un ruolo applicazione è parte di un pacchetto di accesso:

- Quando un utente è assegnato tale pacchetto di accesso, l'utente viene aggiunto a tale ruolo applicazione, se non già presente.
- Quando l'assegnazione di pacchetti di accesso dell'utente scade, l'accesso verrà rimosso dall'applicazione, a meno che non dispongono di un'assegnazione a un altro pacchetto di accesso che include tale ruolo applicazione.

Di seguito sono riportate alcune considerazioni quando si seleziona un'applicazione:

- Le applicazioni possono essere anche i gruppi assegnati oltre ai ruoli.  È possibile scegliere di aggiungere un gruppo al posto di un ruolo applicazione in un pacchetto di accesso, tuttavia, quindi l'applicazione non saranno visibile all'utente come parte del pacchetto di accesso nel portale di accesso personale.

1. Nel **aggiungere i ruoli delle risorse per accedere a package** pagina, fare clic su **applicazioni** per aprire il riquadro di selezionare le applicazioni.

1. Selezionare le applicazioni da includere nel pacchetto di accesso.

    ![Accedere ai pacchetti: aggiungere i ruoli delle risorse - selezionare le applicazioni](./media/entitlement-management-access-package-edit/application-select.png)

1. Fare clic su **Seleziona**.

1. Nel **ruolo** selezionare un ruolo applicazione.

    ![Accedere ai pacchetti: aggiungere il ruolo delle risorse per un'applicazione](./media/entitlement-management-access-package-edit/application-role.png)

1. Fare clic su **Aggiungi**.

    Qualsiasi utente con le assegnazioni esistenti per il pacchetto di accesso verranno automaticamente essere concesso l'accesso a questa applicazione quando viene aggiunto.

### <a name="add-a-sharepoint-site-resource-role"></a>Aggiungere un ruolo di risorse del sito di SharePoint

Azure AD può assegnare automaticamente gli utenti l'accesso a un sito di SharePoint Online o di una raccolta siti di SharePoint Online quando vengono assegnati un pacchetto di accesso.

1. Nel **aggiungere i ruoli delle risorse per accedere a package** pagina, fare clic su **siti di SharePoint** per aprire il riquadro siti selezionare SharePoint Online.

1. Selezionare i siti di SharePoint Online da includere nel pacchetto di accesso.

    ![Accedere ai pacchetti: aggiungere i ruoli delle risorse - siti selezionare SharePoint Online](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. Fare clic su **Seleziona**.

1. Nel **ruolo** selezionare un ruolo del sito SharePoint Online.

    ![Accedere ai pacchetti: aggiungere il ruolo delle risorse per un sito di SharePoint Online](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. Fare clic su **Aggiungi**.

    Qualsiasi utente con le assegnazioni esistenti per il pacchetto di accesso verranno automaticamente essere concesso l'accesso a questo sito di SharePoint Online quando viene aggiunto.

## <a name="remove-resource-roles"></a>Rimuovere i ruoli delle risorse

**Ruolo prerequisito:** L'utente amministratore, proprietario del catalogo o Gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **l'accesso ai pacchetti** e quindi aprire il pacchetto di accesso.

1. Nel menu a sinistra, fare clic su **i ruoli delle risorse**.

1. Nell'elenco dei ruoli delle risorse, individuare il ruolo di risorse che si desidera rimuovere.

1. Fare clic sui puntini di sospensione ( **...** ) e quindi fare clic su **Rimuovi ruolo per le risorse**.

    Qualsiasi utente con le assegnazioni esistenti per il pacchetto di accesso avranno automaticamente l'accesso revocato a questo ruolo di risorsa quando viene rimosso.

## <a name="add-a-new-policy"></a>Aggiungere un nuovo criterio

La modalità di specifica chi può richiedere un pacchetto di accesso consiste nel creare un criterio. Se si vuole consentire set diversi di utenti a cui concedere le assegnazioni con approvazione diversi e impostazioni di scadenza, è possibile creare più criteri per un pacchetto di accesso singolo. Impossibile utilizzare un singolo criterio per assegnare gli utenti interni ed esterni per lo stesso pacchetto di accesso. Tuttavia, è possibile creare due criteri nello stesso pacchetto di accesso: uno per gli utenti interni e uno per gli utenti esterni. Se sono presenti più criteri che si applicano a un utente, cui viene chiesto al momento della loro richiesta per selezionare i criteri che desiderano essere assegnato a.

Il diagramma seguente illustra il processo generale per creare un criterio per un pacchetto di accesso esistente.

![Creare un processo dei criteri](./media/entitlement-management-access-package-edit/policy-process.png)

**Ruolo prerequisito:** L'utente amministratore, proprietario del catalogo o Gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **l'accesso ai pacchetti** e quindi aprire il pacchetto di accesso.

1. Fare clic su **politiche** e quindi **Aggiungi criterio**.

1. Digitare un nome e una descrizione per il criterio.

    ![Creare criteri con nome e descrizione](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. In base alla selezione per **gli utenti che possono richiedere l'accesso**, eseguire i passaggi in una delle seguenti sezioni dei criteri.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>Modificare un criterio esistente

È possibile modificare un criterio in qualsiasi momento. Se si modifica la data di scadenza per un criterio, non si modificherà la data di scadenza per le richieste che sono già in un'approvazione in sospeso o approvato dello stato.

**Ruolo prerequisito:** L'utente amministratore, proprietario del catalogo o Gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **l'accesso ai pacchetti** e quindi aprire il pacchetto di accesso.

1. Fare clic su **criteri** e quindi fare clic sui criteri da modificare.

    Il **dettagli criteri** viene visualizzato il riquadro nella parte inferiore della pagina.

    ![Pacchetto di accesso - riquadro dei dettagli dei criteri](./media/entitlement-management-access-package-edit/policy-details.png)

1. Fare clic su **modifica** per modificare i criteri.

    ![Pacchetto di accesso - Modifica criteri](./media/entitlement-management-access-package-edit/policy-edit.png)

1. Al termine, fare clic su **Update**.

## <a name="directly-assign-a-user"></a>Assegnare direttamente un utente

In alcuni casi, è possibile assegnare direttamente utenti specifici a un pacchetto di accesso in modo che gli utenti non devono passare attraverso il processo di richiesta del pacchetto di accesso. Per assegnare direttamente gli utenti, il pacchetto di accesso deve avere un criterio che consente all'amministratore le assegnazioni dirette.

**Ruolo prerequisito:** L'utente amministratore, proprietario del catalogo o Gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **l'accesso ai pacchetti** e quindi aprire il pacchetto di accesso.

1. Nel menu a sinistra, fare clic su **assegnazioni**.

1. Fare clic su **nuova assegnazione** aprire Aggiungi utente al pacchetto di accesso.

    ![Assegnazioni - aggiunta utente al pacchetto di accesso](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. Fare clic su **aggiungere utenti** selezionare gli utenti che si desidera assegnare il pacchetto di accesso a.

1. Nel **selezionare i criteri** , selezionare un criterio che ha il [None (amministratore assegnazioni dirette solo)](#policy-none-administrator-direct-assignments-only) impostazione.

    Se questo pacchetto di accesso non dispone di questo tipo di criteri, è possibile fare clic su **Crea nuovo criterio** per aggiungerne uno.

1. Impostare la data e ora si vuole che l'assegnazione degli utenti selezionati per iniziare e terminare. Se non viene specificata una data di fine, impostazioni di scadenza del criterio verranno utilizzate.

1. Facoltativamente, fornire una giustificazione per l'assegnazione diretta per la registrazione.

1. Fare clic su **Add** direttamente assegnare gli utenti selezionati al pacchetto di accesso.

    Dopo qualche istante, fare clic su **Aggiorna** per visualizzare gli utenti nell'elenco delle assegnazioni.

## <a name="view-who-has-an-assignment"></a>Visualizzazione con un'assegnazione

**Ruolo prerequisito:** L'utente amministratore, proprietario del catalogo o Gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **l'accesso ai pacchetti** e quindi aprire il pacchetto di accesso.

1. Fare clic su **assegnazioni** per visualizzare un elenco delle assegnazioni attive.

1. Fare clic su un'assegnazione di specifica per vedere dettagli aggiuntivi.

1. Per visualizzare un elenco di assegnazioni che non disponevano di tutti i ruoli delle risorse correttamente il provisioning, fare clic sullo stato del filtro e selezionare **Delivering**.

    È possibile visualizzare dettagli aggiuntivi in caso di errori di recapito individuando la richiesta dell'utente corrispondenti nel **richieste** pagina.

1. Per visualizzare le assegnazioni scadute, scegliere lo stato del filtro e selezionare **scaduta**.

1. Per scaricare un file CSV con l'elenco filtrato, fare clic su **scaricare**.

## <a name="view-requests"></a>Visualizza richieste

**Ruolo prerequisito:** L'utente amministratore, proprietario del catalogo o Gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **l'accesso ai pacchetti** e quindi aprire il pacchetto di accesso.

1. Fare clic su **richieste**.

1. Fare clic su una richiesta specifica per visualizzare dettagli aggiuntivi.

## <a name="view-a-requests-delivery-errors"></a>Visualizzare gli errori di recapito della richiesta

**Ruolo prerequisito:** L'utente amministratore, proprietario del catalogo o Gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **l'accesso ai pacchetti** e quindi aprire il pacchetto di accesso.

1. Fare clic su **richieste**.

1. Selezionare la richiesta di cui che si desidera visualizzare.

    Se la richiesta include gli eventuali errori di recapito, lo stato della richiesta saranno **non consegnato** e sarà lo stato secondario **recapitato parzialmente**.

    Se sono presenti errori di distribuzione, nel riquadro dei dettagli della richiesta, esisterà un conteggio degli errori di recapito.

1. Fare clic su tale valore per visualizzare tutti gli errori di recapito della richiesta.

## <a name="cancel-a-pending-request"></a>Annullare una richiesta in sospeso

È possibile solo annullare una richiesta in sospeso che non è ancora stata recapitata.

**Ruolo prerequisito:** L'utente amministratore, proprietario del catalogo o Gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **l'accesso ai pacchetti** e quindi aprire il pacchetto di accesso.

1. Fare clic su **richieste**.

1. Fare clic su richiesta che si desidera annullare

1. Nel riquadro dei dettagli richiesta, fare clic su **richiesta di annullamento**.

## <a name="copy-my-access-portal-link"></a>Copia il collegamento del portale di accesso personali

La maggior parte degli utenti nella directory possono accedere al portale di accesso personale e automaticamente visualizzato un elenco di pacchetti di accesso che possono richiedere. Tuttavia, per gli utenti di partner commerciali esterni che non sono ancora nella directory, è necessario inviare loro un collegamento che possono utilizzare per richiedere un pacchetto di accesso. Fino a quando il pacchetto di accesso è abilitato per gli utenti esterni e si dispone di un criterio per la directory dell'utente esterno, l'utente esterno può utilizzare il collegamento del portale di accesso personale per richiedere il pacchetto di accesso.

**Ruolo prerequisito:** L'utente amministratore, proprietario del catalogo o Gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **l'accesso ai pacchetti** e quindi aprire il pacchetto di accesso.

1. Nella pagina Panoramica, copiare il **collegamento del portale di accesso personale**.

    ![Panoramica del pacchetto di accesso - Collegamento del portale di Accesso personale](./media/entitlement-management-shared/my-access-portal-link.png)

1. Messaggio di posta elettronica o inviare il collegamento al partner commerciali esterni. Possono condividere il collegamento con agli utenti di richiedere il pacchetto di accesso.

## <a name="change-the-hidden-setting"></a>Modificare l'impostazione Hidden

I pacchetti di accesso sono individuabili per impostazione predefinita. Ciò significa che se un criterio consente agli utenti di richiedere il pacchetto di accesso, visualizzerà automaticamente il pacchetto di accesso elencato nel portale di accesso personale.

**Ruolo prerequisito:** L'utente amministratore, proprietario del catalogo o Gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **l'accesso ai pacchetti** e quindi aprire il pacchetto di accesso.

1. Nella pagina Panoramica fare clic su **modifica**.

1. Impostare il **Hidden** impostazione.

    Se impostato su **No**, il pacchetto di accesso verrà elencato nel portale di accesso personale dell'utente.

    Se impostato su **Sì**, il pacchetto di accesso non verrà elencato nel portale di accesso personale dell'utente. L'unico modo un utente può visualizzare il pacchetto di accesso è se dispongono di metodo diretto **collegamento del portale di accesso personale** al pacchetto di accesso.

## <a name="delete"></a>Delete

Un pacchetto di accesso può essere eliminato solo se ha alcuna assegnazione utente attivo.

**Ruolo prerequisito:** L'utente amministratore, proprietario del catalogo o Gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **l'accesso ai pacchetti** e quindi aprire il pacchetto di accesso.

1. Nel menu a sinistra, fare clic su **assegnazioni** e rimuovere l'accesso per tutti gli utenti.

1. Nel menu a sinistra, fare clic su **Overview** e quindi fare clic su **eliminare**.

1. Nel messaggio di eliminazione visualizzato, fare clic su **Sì**.

## <a name="when-are-changes-applied"></a>Quando vengono applicate le modifiche

In Gestione dei diritti, Azure AD elaborerà le modifiche in blocco per l'assegnazione e le risorse nei pacchetti di accesso più volte al giorno. In modo che, se si effettuano un'assegnazione o modificano i ruoli delle risorse del pacchetto di accesso, può richiedere fino a 24 ore affinché la modifica da eseguire in Azure AD, più la quantità di tempo impiegato per propagare tali modifiche in altri Microsoft Online Services o connesso applicazione SaaS s. Se la modifica interessa solo alcuni oggetti, la modifica probabilmente richiederà solo alcuni minuti per applicare in Azure AD, dopo il quale gli altri componenti di Azure AD quindi rileverà che modificare e aggiornare le applicazioni SaaS. Se la modifica influisce su migliaia di oggetti, la modifica richiederà più tempo. Ad esempio, se si dispone di un pacchetto di accesso con 2 applicazioni e le assegnazioni utente 100 e si decide di aggiungere un ruolo del sito di SharePoint per il pacchetto di accesso, si potrebbe verificare un ritardo fino a quando tutti gli utenti fanno parte di questo ruolo del sito di SharePoint. È possibile monitorare lo stato di avanzamento tramite il log di controllo di Azure AD, il log di provisioning di Azure AD e i log di controllo del sito di SharePoint.

## <a name="next-steps"></a>Passaggi successivi

- [Richiedere le notifiche di posta elettronica e di processo](entitlement-management-process.md)
