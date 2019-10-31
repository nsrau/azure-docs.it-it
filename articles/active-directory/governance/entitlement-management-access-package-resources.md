---
title: Modificare i ruoli delle risorse per un pacchetto di accesso in Azure AD gestione dei diritti-Azure Active Directory
description: Informazioni su come modificare i ruoli delle risorse per un pacchetto di accesso esistente in Azure Active Directory gestione dei diritti.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 102bbfbd1c02c93830f5c7fce89fe95d7fde54c5
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174653"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Modificare i ruoli delle risorse per un pacchetto di accesso in Azure AD gestione dei diritti

Come gestione pacchetti di accesso, è possibile modificare le risorse in un pacchetto di accesso in qualsiasi momento senza preoccuparsi di effettuare il provisioning dell'accesso dell'utente alle nuove risorse o di rimuovere l'accesso dalle risorse precedenti. Questo articolo descrive come modificare i ruoli delle risorse per un pacchetto di accesso esistente.

In questo video viene fornita una panoramica su come modificare un pacchetto di accesso.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Controllare il catalogo per le risorse

Se è necessario aggiungere risorse a un pacchetto di accesso, è necessario verificare se le risorse necessarie sono disponibili nel catalogo. Se si è una gestione pacchetti di Access, non è possibile aggiungere risorse a un catalogo, anche se si è proprietari. Si è limitati all'uso delle risorse disponibili nel catalogo.

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Catalog** , quindi aprire il catalogo.

1. Nel menu a sinistra fare clic su **risorse** per visualizzare l'elenco delle risorse in questo catalogo.

    ![Elenco di risorse in un catalogo](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Se si è una gestione pacchetti di Access ed è necessario aggiungere risorse al catalogo, è possibile chiedere al proprietario del catalogo di aggiungerle.

## <a name="add-resource-roles"></a>Aggiungere i ruoli delle risorse

Un ruolo di risorsa è una raccolta di autorizzazioni associate a una risorsa. Per rendere le risorse disponibili per gli utenti da richiedere è possibile aggiungere i ruoli delle risorse al pacchetto di accesso. È possibile aggiungere i ruoli delle risorse per i gruppi, i team, le applicazioni e i siti di SharePoint.

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Nel menu a sinistra fare clic su **ruoli risorsa**.

1. Fare clic su **Aggiungi ruoli risorsa** per aprire la pagina Aggiungi ruoli risorse per accedere al pacchetto.

    ![Accedere al pacchetto: aggiungere i ruoli delle risorse](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. A seconda che si desideri aggiungere un gruppo, un team, un'applicazione o un sito di SharePoint, eseguire i passaggi in una delle sezioni seguenti del ruolo risorsa.

## <a name="add-a-group-or-team-resource-role"></a>Aggiungere un gruppo o un ruolo di risorsa team

È possibile fare in modo che la gestione dei diritti aggiunga automaticamente gli utenti a un gruppo o a un team in Microsoft teams quando viene assegnato un pacchetto di accesso. 

- Quando un gruppo o un team fa parte di un pacchetto di accesso e un utente viene assegnato a tale pacchetto di accesso, l'utente viene aggiunto al gruppo o al team, se non è già presente.
- Quando l'assegnazione di un pacchetto di accesso di un utente scade, vengono rimossi dal gruppo o dal team, a meno che non dispongano attualmente di un'assegnazione a un altro pacchetto di accesso che include lo stesso gruppo o team.

È possibile selezionare qualsiasi [gruppo di sicurezza Azure ad o gruppo di Office 365](../fundamentals/active-directory-groups-create-azure-portal.md). Gli amministratori possono aggiungere qualsiasi gruppo a un catalogo. i proprietari del catalogo possono aggiungere qualsiasi gruppo al catalogo se sono proprietari del gruppo. Quando si seleziona un gruppo, tenere presenti i seguenti vincoli di Azure AD:

- Quando un utente, incluso un Guest, viene aggiunto come membro di un gruppo o di un team, può visualizzare tutti gli altri membri del gruppo o del team.
- Azure AD non è possibile modificare l'appartenenza di un gruppo sincronizzato da Windows Server Active Directory utilizzando Azure AD Connect o creato in Exchange Online come gruppo di distribuzione.  
- Non è possibile aggiornare l'appartenenza dei gruppi dinamici tramite l'aggiunta o la rimozione di un membro, quindi l'appartenenza dinamica ai gruppi non è adatta per l'uso con la gestione dei diritti.

Per altre informazioni, vedere [confrontare gruppi](https://docs.microsoft.com/office365/admin/create-groups/compare-groups) e [gruppi di Office 365 e Microsoft teams](https://docs.microsoft.com/microsoftteams/office-365-groups).

1. Nella pagina **Aggiungi ruoli risorse per accedere al pacchetto** fare clic su **gruppi e team** per aprire il riquadro Seleziona gruppi.

1. Selezionare i gruppi e i team che si desidera includere nel pacchetto di accesso.

    ![Pacchetto di accesso-Aggiungi ruoli risorse-Seleziona gruppi](./media/entitlement-management-access-package-resources/group-select.png)

1. Fare clic su **Seleziona**.

    Dopo aver selezionato il gruppo o il team, nella colonna **sottotipo** sarà presente uno dei sottotipi seguenti:

    |  |  |
    | --- | --- |
    | Sicurezza | Utilizzato per concedere l'accesso alle risorse. |
    | Distribuzione | Utilizzato per l'invio di notifiche a un gruppo di utenti. |
    | O365 | Gruppo Office 365 non abilitato per i team. Usato per la collaborazione tra utenti, all'interno e all'esterno dell'azienda. |
    | Team | Gruppo Office 365 abilitato per i team. Usato per la collaborazione tra utenti, all'interno e all'esterno dell'azienda. |

1. Nell'elenco **ruolo** selezionare **proprietario** o **membro**.

    Si seleziona in genere il ruolo di membro. Se si seleziona il ruolo proprietario, ciò consentirà agli utenti di aggiungere o rimuovere altri membri o proprietari.

    ![Pacchetto di accesso-Aggiungi ruolo risorsa per un gruppo o un team](./media/entitlement-management-access-package-resources/group-role.png)

1. Fare clic su **Aggiungi**.

    Tutti gli utenti con assegnazioni esistenti al pacchetto di accesso diventeranno automaticamente membri di questo gruppo o team quando verranno aggiunti.

## <a name="add-an-application-resource-role"></a>Aggiungere un ruolo di risorsa dell'applicazione

È possibile avere Azure AD assegnare automaticamente agli utenti l'accesso a un'applicazione Azure AD aziendale, incluse le applicazioni SaaS e le applicazioni dell'organizzazione federate per Azure AD, quando a un utente viene assegnato un pacchetto di accesso. Per le applicazioni che si integrano con Azure AD tramite Single Sign-On federato, Azure AD rilascia i token federativi per gli utenti assegnati all'applicazione.

Le applicazioni possono avere più ruoli. Quando si aggiunge un'applicazione a un pacchetto di accesso, se l'applicazione ha più di un ruolo, sarà necessario specificare il ruolo appropriato per tali utenti. Se si sviluppano applicazioni, è possibile leggere altre informazioni sull'aggiunta di questi ruoli alle applicazioni in [procedura: configurare l'attestazione del ruolo rilasciata nel token SAML per le applicazioni aziendali](../develop/active-directory-enterprise-app-role-management.md).

Quando un ruolo applicazione fa parte di un pacchetto di accesso:

- Quando un utente viene assegnato al pacchetto di accesso, l'utente viene aggiunto al ruolo applicazione, se non è già presente.
- Quando l'assegnazione del pacchetto di accesso di un utente scade, l'accesso verrà rimosso dall'applicazione, a meno che non disponga di un'assegnazione a un altro pacchetto di accesso che includa tale ruolo applicazione.

Di seguito sono riportate alcune considerazioni per la selezione di un'applicazione:

- Anche le applicazioni possono avere gruppi assegnati ai rispettivi ruoli.  È possibile scegliere di aggiungere un gruppo al posto di un ruolo applicazione in un pacchetto di accesso, ma l'applicazione non sarà visibile all'utente come parte del pacchetto di accesso nel portale di accesso personale.

1. Nella pagina **Aggiungi ruoli risorse per accedere al pacchetto** fare clic su **applicazioni** per aprire il riquadro Seleziona applicazioni.

1. Selezionare le applicazioni che si desidera includere nel pacchetto di accesso.

    ![Pacchetto di accesso-aggiungere ruoli delle risorse-selezionare le applicazioni](./media/entitlement-management-access-package-resources/application-select.png)

1. Fare clic su **Seleziona**.

1. Nell'elenco **ruolo** selezionare un ruolo applicazione.

    ![Pacchetto di accesso-Aggiungi ruolo risorsa per un'applicazione](./media/entitlement-management-access-package-resources/application-role.png)

1. Fare clic su **Aggiungi**.

    A tutti gli utenti con assegnazioni esistenti al pacchetto di accesso verrà automaticamente concesso l'accesso a questa applicazione al momento dell'aggiunta.

## <a name="add-a-sharepoint-site-resource-role"></a>Aggiungere un ruolo di risorsa del sito di SharePoint

Azure AD possibile assegnare automaticamente agli utenti l'accesso a un sito di SharePoint Online o a una raccolta di siti di SharePoint Online quando viene assegnato un pacchetto di accesso.

1. Nella pagina **Aggiungi ruoli risorse all'accesso al pacchetto** fare clic su **siti SharePoint** per aprire il riquadro selezionare i siti di SharePoint Online.

1. Selezionare i siti di SharePoint Online che si desidera includere nel pacchetto di accesso.

    ![Pacchetto di accesso-Aggiungi ruoli delle risorse-selezionare i siti di SharePoint Online](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. Fare clic su **Seleziona**.

1. Nell'elenco **ruolo** selezionare un ruolo del sito di SharePoint Online.

    ![Pacchetto di accesso-Aggiungi ruolo risorsa per un sito di SharePoint Online](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. Fare clic su **Aggiungi**.

    A tutti gli utenti con assegnazioni esistenti al pacchetto di accesso verrà automaticamente concesso l'accesso al sito di SharePoint Online quando viene aggiunto.

## <a name="remove-resource-roles"></a>Rimuovere i ruoli delle risorse

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Nel menu a sinistra fare clic su **ruoli risorsa**.

1. Nell'elenco dei ruoli delle risorse individuare il ruolo della risorsa che si desidera rimuovere.

1. Fare clic sui puntini di sospensione ( **...** ) e quindi fare clic su **Rimuovi ruolo risorsa**.

    A tutti gli utenti con assegnazioni esistenti al pacchetto di accesso verrà revocato automaticamente l'accesso a questo ruolo della risorsa quando viene rimosso.

## <a name="when-changes-are-applied"></a>Quando vengono applicate le modifiche

Per la gestione dei diritti, Azure AD elaborerà le modifiche bulk per l'assegnazione e le risorse nei pacchetti di accesso più volte al giorno. Se quindi si effettua un'assegnazione o si modificano i ruoli delle risorse del pacchetto di accesso, possono essere necessarie fino a 24 ore prima che la modifica venga effettuata in Azure AD, oltre alla quantità di tempo necessaria per propagare tali modifiche ad altri Microsoft Online Services o a un'applicazione SaaS connessa s. Se la modifica interessa solo pochi oggetti, la modifica richiederà solo alcuni minuti per l'applicazione Azure AD, dopo la quale altri componenti Azure AD rileveranno la modifica e aggiorneranno le applicazioni SaaS. Se la modifica interessa migliaia di oggetti, la modifica avrà più tempo. Se, ad esempio, si dispone di un pacchetto di accesso con due applicazioni e 100 assegnazioni utente e si decide di aggiungere un ruolo del sito di SharePoint al pacchetto di accesso, è possibile che si verifichi un ritardo fino a quando tutti gli utenti non fanno parte del ruolo del sito di SharePoint. È possibile monitorare lo stato di avanzamento tramite il registro di controllo Azure AD, il log di provisioning Azure AD e i log di controllo del sito di SharePoint.

Quando si rimuove un membro di un team, questi vengono rimossi anche dal gruppo di Office 365. La rimozione dalla funzionalità di chat del team potrebbe essere posticipata. Per ulteriori informazioni, vedere [appartenenza](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership)a un gruppo.

## <a name="next-steps"></a>Passaggi successivi

- [Creazione di un gruppo di base e aggiunta di membri con Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Procedura: configurare l'attestazione del ruolo rilasciata nel token SAML per le applicazioni aziendali](../develop/active-directory-enterprise-app-role-management.md)
- [Introduzione a SharePoint Online](/sharepoint/introduction)