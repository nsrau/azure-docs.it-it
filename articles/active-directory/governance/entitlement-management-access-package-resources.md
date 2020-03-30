---
title: Modificare i ruoli delle risorse per un pacchetto di accesso nella gestione dei diritti di Azure AD - Azure Active DirectoryChange resource roles for an access package in Azure AD entitlement management - Azure Active Directory
description: Informazioni su come modificare i ruoli delle risorse per un pacchetto di accesso esistente nella gestione dei diritti di Azure Active Directory.Learn how to change the resource roles for an existing access package in Azure Active Directory entitlement management.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261892"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Modificare i ruoli delle risorse per un pacchetto di accesso nella gestione dei diritti di Azure ADChange resource roles for an access package in Azure AD entitlement management

In qualità di gestore di pacchetti di accesso, è possibile modificare le risorse in un pacchetto di accesso in qualsiasi momento senza doversi preoccupare di eseguire il provisioning dell'accesso dell'utente alle nuove risorse o di rimuovere l'accesso dalle risorse precedenti. In questo articolo viene descritto come modificare i ruoli delle risorse per un pacchetto di accesso esistente.

Questo video offre una panoramica su come modificare un pacchetto di accesso.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Controllare il catalogo per le risorse

Se è necessario aggiungere risorse a un pacchetto di accesso, è necessario verificare se le risorse necessarie sono disponibili nel catalogo. Se si è un gestore di pacchetti di accesso, non è possibile aggiungere risorse a un catalogo, anche se sono proprietarie. L'utilizzo delle risorse disponibili nel catalogo è limitato all'utilizzo delle risorse disponibili.

**Ruolo prerequisito:** Amministratore globale, Amministratore utente, Proprietario catalogo o Gestore pacchetti di Access

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Catalogo,** quindi apri il catalogo.

1. Nel menu a sinistra fare clic su **Risorse** per visualizzare l'elenco delle risorse in questo catalogo.

    ![Elenco delle risorse in un catalogo](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Se si è un gestore di pacchetti di accesso ed è necessario aggiungere risorse al catalogo, è possibile chiedere al proprietario del catalogo di aggiungerle.

## <a name="add-resource-roles"></a>Aggiungere ruoli delle risorseAdd resource roles

Un ruolo di risorsa è una raccolta di autorizzazioni associate a una risorsa. Il modo in cui si rendono disponibili gli utenti per la richiesta consiste nell'aggiungere ruoli di risorsa al pacchetto di accesso. È possibile aggiungere ruoli delle risorse per gruppi, team, applicazioni e siti di SharePoint.You can add resource roles for groups, teams, applications, and SharePoint sites.

**Ruolo prerequisito:** Amministratore globale, Amministratore utente, Proprietario catalogo o Gestore pacchetti di Access

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Pacchetti di accesso** e quindi apri il pacchetto di accesso.

1. Nel menu a sinistra fare clic su **Ruoli risorsa**.

1. Fare clic su **Aggiungi ruoli risorsa** per aprire la pagina Aggiungi ruoli del risorsa per accedere al pacchetto.

    ![Pacchetto di accesso - Aggiungere ruoli delle risorseAccess package - Add resource roles](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. A seconda che si desideri aggiungere un gruppo, un team, un'applicazione o un sito di SharePoint, eseguire i passaggi descritti in una delle sezioni del ruolo delle risorse seguenti.

## <a name="add-a-group-or-team-resource-role"></a>Aggiungere un ruolo di gruppo o di risorsa teamAdd a group or team resource role

È possibile fare in modo che la gestione dei diritti aggiuva automaticamente utenti a un gruppo o a un team in Microsoft Teams quando gli viene assegnato un pacchetto di accesso. 

- Quando un gruppo o un team fa parte di un pacchetto di accesso e un utente viene assegnato a tale pacchetto di accesso, l'utente viene aggiunto a tale gruppo o team, se non è già presente.
- Quando l'assegnazione del pacchetto di accesso di un utente scade, viene rimossa dal gruppo o dal team, a meno che non disponga attualmente di un'assegnazione a un altro pacchetto di accesso che include lo stesso gruppo o team.

È possibile selezionare qualsiasi gruppo di sicurezza di Azure AD o Gruppo di [Office 365](../fundamentals/active-directory-groups-create-azure-portal.md). Gli amministratori possono aggiungere qualsiasi gruppo a un catalogo; i proprietari del catalogo possono aggiungere qualsiasi gruppo al catalogo se sono proprietari del gruppo. Tenere presenti i vincoli di Azure AD seguenti quando si seleziona un gruppo:Keep the following Azure AD constraints in mind when selecting a group:

- Quando un utente, incluso un ospite, viene aggiunto come membro a un gruppo o team, può visualizzare tutti gli altri membri di tale gruppo o team.
- Azure AD non può modificare l'appartenenza di un gruppo sincronizzato da Windows Server Active Directory tramite Azure AD Connect o creato in Exchange Online come gruppo di distribuzione.  
- L'appartenenza ai gruppi dinamici non può essere aggiornata aggiungendo o rimuovendo un membro, pertanto le appartenenze ai gruppi dinamici non sono adatte per l'utilizzo con la gestione dei diritti.

Per ulteriori informazioni, vedere [Confrontare gruppi](https://docs.microsoft.com/office365/admin/create-groups/compare-groups) e gruppi di [Office 365 e Microsoft Teams.](https://docs.microsoft.com/microsoftteams/office-365-groups)

1. Nella pagina **Aggiungi ruoli risorsa per accedere al pacchetto** fare clic su Gruppi e **team** per aprire il riquadro Seleziona gruppi.

1. Selezionare i gruppi e i team che si desidera includere nel pacchetto di accesso.

    ![Pacchetto di Accesso - Aggiungere ruoli delle risorse - Selezionare gruppi](./media/entitlement-management-access-package-resources/group-select.png)

1. Fare clic su **Seleziona**.

    Dopo aver selezionato il gruppo o il team, la colonna **Sottotipo** elencherà uno dei seguenti sottotipi:

    |  |  |
    | --- | --- |
    | Security | Utilizzato per concedere l'accesso alle risorse. |
    | Distribuzione | Utilizzato per l'invio di notifiche a un gruppo di persone. |
    | O365 | Gruppo di Office 365 non abilitato per Teams. Utilizzato per la collaborazione tra utenti, sia all'interno che all'esterno dell'azienda. |
    | Team | Gruppo di Office 365 abilitato per Teams. Utilizzato per la collaborazione tra utenti, sia all'interno che all'esterno dell'azienda. |

1. Nell'elenco **Ruolo** selezionare **Proprietario** o **Membro**.

    In genere si seleziona il ruolo membro. Se si seleziona il ruolo Proprietario, gli utenti potranno aggiungere o rimuovere altri membri o proprietari.

    ![Pacchetto di accesso - Aggiungere il ruolo di risorsa per un gruppo o un teamAccess package - Add resource role for a group or team](./media/entitlement-management-access-package-resources/group-role.png)

1. Fare clic su **Aggiungi**.

    Tutti gli utenti con assegnazioni esistenti al pacchetto di accesso diventeranno automaticamente membri di questo gruppo o team quando viene aggiunto.

## <a name="add-an-application-resource-role"></a>Aggiungere un ruolo di risorsa applicazioneAdd an application resource role

È possibile fare in modo che Azure AD assegni automaticamente agli utenti l'accesso a un'applicazione aziendale di Azure AD, incluse le applicazioni SaaS e le applicazioni dell'organizzazione federate in Azure AD, quando a un utente viene assegnato un pacchetto di accesso. Per le applicazioni che si integrano con Azure AD tramite Single Sign-On federato, Azure AD emetterà token di federazione per gli utenti assegnati all'applicazione.

Le applicazioni possono avere più ruoli. Quando si aggiunge un'applicazione a un pacchetto di accesso, se tale applicazione ha più di un ruolo, è necessario specificare il ruolo appropriato per tali utenti. Se si sviluppano applicazioni, è possibile leggere ulteriori informazioni su come tali ruoli vengono aggiunti alle applicazioni in [Procedura: Configurare l'attestazione di ruolo rilasciata nel token SAML per](../develop/active-directory-enterprise-app-role-management.md)le applicazioni aziendali .

Una volta che un ruolo applicazione fa parte di un pacchetto di accesso:Once an application role is part of an access package:

- Quando a un utente viene assegnato il pacchetto di accesso, l'utente viene aggiunto al ruolo applicazione, se non è già presente.
- Quando l'assegnazione del pacchetto di accesso di un utente scade, l'accesso verrà rimosso dall'applicazione, a meno che non disponga di un'assegnazione a un altro pacchetto di accesso che include tale ruolo dell'applicazione.

Di seguito sono riportate alcune considerazioni relative alla selezione di un'applicazione:Here are some considerations when selecting an application:

- Le applicazioni possono anche avere gruppi assegnati ai loro ruoli.  È possibile scegliere di aggiungere un gruppo al posto di un ruolo applicazione in un pacchetto di accesso, tuttavia l'applicazione non sarà visibile all'utente come parte del pacchetto di accesso nel portale di accesso personale.

1. Nella pagina **Aggiungi ruoli risorsa per accedere al pacchetto** fare clic su **Applicazioni** per aprire il riquadro Seleziona applicazioni.

1. Selezionare le applicazioni che si desidera includere nel pacchetto di accesso.

    ![Pacchetto di accesso - Aggiungere ruoli delle risorse - Selezionare le applicazioniAccess package - Add resource roles - Select applications](./media/entitlement-management-access-package-resources/application-select.png)

1. Fare clic su **Seleziona**.

1. Nell'elenco **Ruolo** selezionare un ruolo applicazione.

    ![Pacchetto di Accesso- Aggiungere il ruolo di risorsa per un'applicazioneAccess package - Add resource role for an application](./media/entitlement-management-access-package-resources/application-role.png)

1. Fare clic su **Aggiungi**.

    Tutti gli utenti con assegnazioni esistenti al pacchetto di accesso avranno automaticamente accesso a questa applicazione quando viene aggiunto.

## <a name="add-a-sharepoint-site-resource-role"></a>Aggiungere un ruolo di risorsa sito di SharePointAdd a SharePoint site resource role

Azure AD può assegnare automaticamente agli utenti l'accesso a un sito di SharePoint Online o a una raccolta siti di SharePoint Online quando viene assegnato un pacchetto di accesso.

1. Nella pagina **Aggiungi ruoli di risorsa al pacchetto** di accesso fare clic su Siti di **SharePoint** per aprire il riquadro Seleziona siti di SharePoint Online.

1. Selezionare i siti di SharePoint Online che si desidera includere nel pacchetto di accesso.

    ![Pacchetto di Access - Aggiungi ruoli delle risorse - Selezionare i siti di SharePoint OnlineAccess package - Add resource roles - Select SharePoint Online sites](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. Fare clic su **Seleziona**.

1. Nell'elenco Ruolo selezionare un ruolo del sito di SharePoint Online.In the **Role** list, select a SharePoint Online site role.

    ![Pacchetto di Access: aggiungere il ruolo di risorsa per un sito di SharePoint OnlineAccess package - Add resource role for a SharePoint Online site](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. Fare clic su **Aggiungi**.

    Tutti gli utenti con assegnazioni esistenti al pacchetto di accesso avranno automaticamente accesso a questo sito di SharePoint Online quando viene aggiunto.

## <a name="remove-resource-roles"></a>Rimuovere i ruoli delle risorseRemove resource roles

**Ruolo prerequisito:** Amministratore globale, Amministratore utente, Proprietario catalogo o Gestore pacchetti di Access

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Pacchetti di accesso** e quindi apri il pacchetto di accesso.

1. Nel menu a sinistra fare clic su **Ruoli risorsa**.

1. Nell'elenco dei ruoli delle risorse individuare il ruolo della risorsa che si desidera rimuovere.

1. Fare clic sui conividi (**...**) e quindi fare clic su **Rimuovi ruolo risorsa**.

    Tutti gli utenti con assegnazioni esistenti al pacchetto di accesso avranno automaticamente il loro accesso revocato a questo ruolo di risorsa quando viene rimosso.

## <a name="when-changes-are-applied"></a>Quando vengono applicate le modifiche

Nella gestione dei diritti, Azure AD elaborerà le modifiche collettive per l'assegnazione e le risorse nei pacchetti di accesso più volte al giorno. Pertanto, se si effettua un'assegnazione o si modificano i ruoli delle risorse del pacchetto di accesso, potrebbero essere necessarie fino a 24 ore per apportare le modifiche in Azure AD, oltre alla quantità di tempo necessaria per propagare tali modifiche ad altri Microsoft Online Services o a SaaS connessi. Applicazioni. Se la modifica interessa solo alcuni oggetti, è probabile che l'applicazione della modifica richiederà solo alcuni minuti in Azure AD, dopo di che altri componenti di Azure AD rileveranno tale modifica e aggiorneranno le applicazioni SaaS. Se la modifica interessa migliaia di oggetti, la modifica richiederà più tempo. Ad esempio, se si dispone di un pacchetto di accesso con 2 applicazioni e 100 assegnazioni utente e si decide di aggiungere un ruolo del sito di SharePoint al pacchetto di accesso, potrebbe verificarsi un ritardo fino a quando tutti gli utenti fanno parte di tale ruolo del sito di SharePoint. È possibile monitorare lo stato di avanzamento tramite il log di controllo di Azure AD, il log di provisioning di Azure AD e i log di controllo del sito di SharePoint.You can monitor the progress through the Azure AD audit log, the Azure AD provisioning log, and the SharePoint site audit logs.

Quando si rimuove un membro di un team, questo membro del team viene rimosso anche dal gruppo di Office 365. La rimozione dalla funzionalità di chat del team potrebbe essere posticipata. Per ulteriori informazioni, vedere [Appartenenza a gruppi](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

## <a name="next-steps"></a>Passaggi successivi

- [Creare un gruppo di base e aggiungere membri con Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Procedura: Configurare l'attestazione basata su ruolo rilasciata nel token SAML per applicazioni aziendali](../develop/active-directory-enterprise-app-role-management.md)
- [Introduzione a SharePoint Online](/sharepoint/introduction)