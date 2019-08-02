---
title: Modificare e gestire un pacchetto di accesso esistente in Azure AD gestione dei diritti (anteprima)-Azure Active Directory
description: Informazioni su come modificare e gestire un pacchetto di accesso esistente in Azure Active Directory gestione dei diritti (anteprima).
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
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17c85d53d05193313f9e166b88beb2a0f82eb197
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618365"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Modificare e gestire un pacchetto di accesso esistente in Azure AD gestione dei diritti (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Un pacchetto di accesso consente di eseguire una singola installazione di risorse e criteri che amministra automaticamente l'accesso per la durata del pacchetto di accesso. Come gestione pacchetti di accesso, è possibile modificare le risorse in un pacchetto di accesso in qualsiasi momento senza preoccuparsi di effettuare il provisioning dell'accesso dell'utente alle nuove risorse o di rimuovere l'accesso dalle risorse precedenti. I criteri possono anche essere aggiornati in qualsiasi momento, ma le modifiche ai criteri influiscono solo sui nuovi accessi.

Questo articolo descrive come modificare e gestire i pacchetti di accesso esistenti.

## <a name="add-resource-roles"></a>Aggiungere i ruoli delle risorse

Un ruolo di risorsa è una raccolta di autorizzazioni associate a una risorsa. Per rendere le risorse disponibili per gli utenti da richiedere è possibile aggiungere i ruoli delle risorse al pacchetto di accesso. È possibile aggiungere ruoli delle risorse per gruppi, applicazioni e siti di SharePoint.

**Ruolo prerequisito:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Nel menu a sinistra fare clic su **ruoli risorsa**.

1. Fare clic su **Aggiungi ruoli risorsa** per aprire la pagina Aggiungi ruoli risorse per accedere al pacchetto.

    ![Accedere al pacchetto: aggiungere i ruoli delle risorse](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. A seconda che si desideri aggiungere un gruppo, un'applicazione o un sito di SharePoint, eseguire i passaggi in una delle sezioni seguenti del ruolo risorsa.

### <a name="add-a-group-resource-role"></a>Aggiungere un ruolo di risorsa di gruppo

È possibile fare in modo che la gestione dei diritti aggiunga automaticamente gli utenti a un gruppo quando viene assegnato un pacchetto di accesso. 

- Quando un gruppo fa parte di un pacchetto di accesso e un utente viene assegnato a tale pacchetto di accesso, l'utente viene aggiunto al gruppo, se non è già presente.
- Quando l'assegnazione del pacchetto di accesso di un utente scade, questi vengono rimossi dal gruppo, a meno che non dispongano attualmente di un'assegnazione a un altro pacchetto di accesso che include lo stesso gruppo.

È possibile selezionare qualsiasi gruppo di Office 365 o Azure AD gruppo di sicurezza.  Gli amministratori possono aggiungere qualsiasi gruppo a un catalogo. i proprietari del catalogo possono aggiungere qualsiasi gruppo al catalogo se sono proprietari del gruppo. Quando si seleziona un gruppo, tenere presenti i seguenti vincoli di Azure AD:

- Quando un utente, incluso un Guest, viene aggiunto come membro di un gruppo, può visualizzare tutti gli altri membri del gruppo.
- Azure AD non è possibile modificare l'appartenenza di un gruppo sincronizzato da Windows Server Active Directory utilizzando Azure AD Connect o creato in Exchange Online come gruppo di distribuzione.  
- Non è possibile aggiornare l'appartenenza dei gruppi dinamici tramite l'aggiunta o la rimozione di un membro, quindi l'appartenenza dinamica ai gruppi non è adatta per l'uso con la gestione dei diritti.

1. Nella pagina **Aggiungi ruoli risorse per accedere al pacchetto** fare clic su **gruppi** per aprire il riquadro Seleziona gruppi.

1. Selezionare i gruppi che si desidera includere nel pacchetto di accesso.

    ![Pacchetto di accesso-Aggiungi ruoli risorse-Seleziona gruppi](./media/entitlement-management-access-package-edit/group-select.png)

1. Fare clic su **Seleziona**.

1. Nell'elenco **ruolo** selezionare **proprietario** o **membro**.

    Si seleziona in genere il ruolo di membro. Se si seleziona il ruolo proprietario, ciò consentirà agli utenti di aggiungere o rimuovere altri membri o proprietari.

    ![Pacchetto di accesso-Aggiungi ruolo risorsa per un gruppo](./media/entitlement-management-access-package-edit/group-role.png)

1. Fare clic su **Aggiungi**.

    Tutti gli utenti con assegnazioni esistenti al pacchetto di accesso diventeranno automaticamente membri di questo gruppo quando verranno aggiunti.

### <a name="add-an-application-resource-role"></a>Aggiungere un ruolo di risorsa dell'applicazione

È possibile avere Azure AD assegnare automaticamente agli utenti l'accesso a un'applicazione Azure AD aziendale, incluse le applicazioni SaaS e le applicazioni dell'organizzazione federate per Azure AD, quando a un utente viene assegnato un pacchetto di accesso. Per le applicazioni che si integrano con Azure AD tramite Single Sign-on federato, Azure AD rilascerà i token federativi per gli utenti assegnati all'applicazione.

Le applicazioni possono avere più ruoli. Quando si aggiunge un'applicazione a un pacchetto di accesso, se l'applicazione ha più di un ruolo, sarà necessario specificare il ruolo appropriato per tali utenti.  Se si sviluppano applicazioni, è possibile leggere altre informazioni sul modo in cui tali ruoli vengono forniti alle applicazioni nell'articolo relativo alla [configurazione dell'attestazione del ruolo rilasciata nel token SAML](../develop/active-directory-enterprise-app-role-management.md).

Quando un ruolo applicazione fa parte di un pacchetto di accesso:

- Quando un utente viene assegnato al pacchetto di accesso, l'utente viene aggiunto al ruolo applicazione, se non è già presente.
- Quando l'assegnazione del pacchetto di accesso di un utente scade, l'accesso verrà rimosso dall'applicazione, a meno che non disponga di un'assegnazione a un altro pacchetto di accesso che includa tale ruolo applicazione.

Di seguito sono riportate alcune considerazioni per la selezione di un'applicazione:

- Anche le applicazioni possono avere gruppi assegnati ai rispettivi ruoli.  È possibile scegliere di aggiungere un gruppo al posto di un ruolo applicazione in un pacchetto di accesso, ma l'applicazione non sarà visibile all'utente come parte del pacchetto di accesso nel portale di accesso personale.

1. Nella pagina **Aggiungi ruoli risorse per accedere al pacchetto** fare clic su **applicazioni** per aprire il riquadro Seleziona applicazioni.

1. Selezionare le applicazioni che si desidera includere nel pacchetto di accesso.

    ![Pacchetto di accesso-aggiungere ruoli delle risorse-selezionare le applicazioni](./media/entitlement-management-access-package-edit/application-select.png)

1. Fare clic su **Seleziona**.

1. Nell'elenco **ruolo** selezionare un ruolo applicazione.

    ![Pacchetto di accesso-Aggiungi ruolo risorsa per un'applicazione](./media/entitlement-management-access-package-edit/application-role.png)

1. Fare clic su **Aggiungi**.

    A tutti gli utenti con assegnazioni esistenti al pacchetto di accesso verrà automaticamente concesso l'accesso a questa applicazione al momento dell'aggiunta.

### <a name="add-a-sharepoint-site-resource-role"></a>Aggiungere un ruolo di risorsa del sito di SharePoint

Azure AD possibile assegnare automaticamente agli utenti l'accesso a un sito di SharePoint Online o a una raccolta di siti di SharePoint Online quando viene assegnato un pacchetto di accesso.

1. Nella pagina **Aggiungi ruoli risorse all'accesso al pacchetto** fare clic su **siti SharePoint** per aprire il riquadro selezionare i siti di SharePoint Online.

1. Selezionare i siti di SharePoint Online che si desidera includere nel pacchetto di accesso.

    ![Pacchetto di accesso-Aggiungi ruoli delle risorse-selezionare i siti di SharePoint Online](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. Fare clic su **Seleziona**.

1. Nell'elenco **ruolo** selezionare un ruolo del sito di SharePoint Online.

    ![Pacchetto di accesso-Aggiungi ruolo risorsa per un sito di SharePoint Online](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. Fare clic su **Aggiungi**.

    A tutti gli utenti con assegnazioni esistenti al pacchetto di accesso verrà automaticamente concesso l'accesso al sito di SharePoint Online quando viene aggiunto.

## <a name="remove-resource-roles"></a>Rimuovere i ruoli delle risorse

**Ruolo prerequisito:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Nel menu a sinistra fare clic su **ruoli risorsa**.

1. Nell'elenco dei ruoli delle risorse individuare il ruolo della risorsa che si desidera rimuovere.

1. Fare clic sui puntini di sospensione ( **...** ) e quindi fare clic su **Rimuovi ruolo risorsa**.

    A tutti gli utenti con assegnazioni esistenti al pacchetto di accesso verrà revocato automaticamente l'accesso a questo ruolo della risorsa quando viene rimosso.

## <a name="add-a-new-policy"></a>Aggiungi un nuovo criterio

Il modo in cui si specificano gli utenti che possono richiedere un pacchetto di accesso consiste nel creare un criterio. È possibile creare più criteri per un singolo pacchetto di accesso se si desidera consentire a diversi set di utenti di concedere le assegnazioni con impostazioni di approvazione e scadenza diverse. Non è possibile usare un singolo criterio per assegnare utenti interni ed esterni allo stesso pacchetto di accesso. Tuttavia, è possibile creare due criteri nello stesso pacchetto di accesso, uno per gli utenti interni e uno per gli utenti esterni. Se sono presenti più criteri che si applicano a un utente, verrà richiesto al momento della richiesta di selezionare i criteri a cui si desidera assegnare.

Il diagramma seguente illustra il processo di alto livello per la creazione di un criterio per un pacchetto di accesso esistente.

![Creazione di un processo di criteri](./media/entitlement-management-access-package-edit/policy-process.png)

**Ruolo prerequisito:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **criteri** , quindi su **Aggiungi criterio**.

1. Digitare un nome e una descrizione per il criterio.

    ![Crea criterio con nome e descrizione](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. In base alla selezione effettuata per **gli utenti che possono richiedere l'accesso**, eseguire i passaggi in una delle sezioni seguenti dei criteri.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>Modificare un criterio esistente

È possibile modificare i criteri in qualsiasi momento. Se si modifica la data di scadenza di un criterio, la data di scadenza per le richieste che si trovano già in uno stato di approvazione in sospeso o approvato non verrà modificata.

**Ruolo prerequisito:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **criteri** , quindi selezionare il criterio che si desidera modificare.

    Verrà visualizzato il riquadro **Dettagli criteri** nella parte inferiore della pagina.

    ![Pacchetto di accesso-riquadro dei dettagli dei criteri](./media/entitlement-management-access-package-edit/policy-details.png)

1. Fare clic su **modifica** per modificare il criterio.

    ![Accedi ai criteri di modifica del pacchetto](./media/entitlement-management-access-package-edit/policy-edit.png)

1. Al termine, fare clic su **Aggiorna**.

## <a name="directly-assign-a-user"></a>Assegnare direttamente un utente

In alcuni casi, potrebbe essere necessario assegnare direttamente utenti specifici a un pacchetto di accesso in modo che gli utenti non debbano eseguire il processo di richiesta del pacchetto di accesso. Per assegnare direttamente gli utenti, il pacchetto di accesso deve disporre di un criterio che consenta l'assegnazione diretta dell'amministratore.

**Ruolo prerequisito:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Nel menu a sinistra fare clicsu assegnazioni.

1. Fare clic su **nuova assegnazione** per aprire Aggiungi utente per accedere al pacchetto.

    ![Assegnazioni-Aggiungi utente al pacchetto di accesso](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. Fare clic su **Aggiungi utenti** per selezionare gli utenti a cui si desidera assegnare il pacchetto di accesso.

1. Nell'elenco **Seleziona criteri** selezionare un criterio con l'impostazione [Nessuna (solo assegnazione diretta amministratore)](#policy-none-administrator-direct-assignments-only) .

    Se il pacchetto di accesso non dispone di questo tipo di criteri, è possibile fare clic su **Crea nuovo criterio** per aggiungerne uno.

1. Consente di impostare la data e l'ora di inizio e fine dell'assegnazione degli utenti selezionati. Se non viene specificata una data di fine, verranno usate le impostazioni di scadenza dei criteri.

1. Facoltativamente, fornire una giustificazione per l'assegnazione diretta per la conservazione dei record.

1. Fare clic su **Aggiungi** per assegnare direttamente gli utenti selezionati al pacchetto di accesso.

    Dopo alcuni istanti, fare clic su **Aggiorna** per visualizzare gli utenti nell'elenco assegnazioni.

## <a name="view-who-has-an-assignment"></a>Visualizza chi ha un'assegnazione

**Ruolo prerequisito:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su assegnazioni per visualizzare un elenco di assegnazioni attive.

1. Fare clic su un'assegnazione specifica per visualizzare altri dettagli.

1. Per visualizzare un elenco di assegnazioni in cui non è stato eseguito correttamente il provisioning di tutti i ruoli delle risorse, fare clic sullo stato del filtro e selezionare **distribuzione**.

    È possibile visualizzare ulteriori dettagli sugli errori di recapito individuando la richiesta corrispondente dell'utente nella pagina **richieste** .

1. Per visualizzare le assegnazioni scadute, fare clic sullo stato del filtro e selezionare **scaduto**.

1. Per scaricare un file CSV dell'elenco filtrato, fare clic su **download**.

## <a name="view-requests"></a>Visualizza richieste

**Ruolo prerequisito:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **richieste**.

1. Fare clic su una richiesta specifica per visualizzare altri dettagli.

## <a name="view-a-requests-delivery-errors"></a>Visualizzare gli errori di recapito di una richiesta

**Ruolo prerequisito:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **richieste**.

1. Selezionare la richiesta che si desidera visualizzare.

    Se la richiesta presenta errori di recapito, lo stato della richiesta verrà annullato **e lo** stato secondario sarà recapitato **parzialmente**.

    Se si verificano errori di recapito, nel riquadro dei dettagli della richiesta sarà presente un conteggio degli errori di recapito.

1. Fare clic sul conteggio per visualizzare tutti gli errori di recapito della richiesta.

## <a name="cancel-a-pending-request"></a>Annullare una richiesta in sospeso

È possibile annullare una richiesta in sospeso che non è ancora stata recapitata.

**Ruolo prerequisito:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **richieste**.

1. Fare clic sulla richiesta che si desidera annullare.

1. Nel riquadro Dettagli richiesta fare clic su **Annulla richiesta**.

## <a name="copy-my-access-portal-link"></a>Copiare il collegamento al portale di accesso

La maggior parte degli utenti nella directory può accedere al portale di accesso personale e visualizzare automaticamente un elenco di pacchetti di accesso che possono richiedere. Tuttavia, per gli utenti di partner commerciali esterni che non si trovano ancora nella directory, è necessario inviare loro un collegamento che possono usare per richiedere un pacchetto di accesso. Finché il pacchetto di accesso è abilitato per gli utenti esterni e si dispone di un criterio per la directory dell'utente esterno, l'utente esterno può usare il collegamento portale di accesso personale per richiedere il pacchetto di accesso.

**Ruolo prerequisito:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Nella pagina Overview copiare il **collegamento My Access Portal**.

    ![Panoramica del pacchetto di accesso - Collegamento del portale di Accesso personale](./media/entitlement-management-shared/my-access-portal-link.png)

1. Inviare un messaggio di posta elettronica o inviare il collegamento al partner aziendale esterno. Possono condividere il collegamento con i loro utenti per richiedere il pacchetto di accesso.

## <a name="change-the-hidden-setting"></a>Modificare l'impostazione nascosta

Per impostazione predefinita, i pacchetti di Access sono individuabili. Ciò significa che se un criterio consente a un utente di richiedere il pacchetto di accesso, visualizzerà automaticamente il pacchetto di accesso elencato nel portale di accesso personale.

**Ruolo prerequisito:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Nella pagina Overview fare clic su **Edit**.

1. Impostare l'impostazione **nascosta** .

    Se è impostato su **No**, il pacchetto di accesso verrà elencato nel portale di accesso personale dell'utente.

    Se è impostato su **Sì**, il pacchetto di accesso non verrà elencato nel portale di accesso personale dell'utente. L'unico modo in cui un utente può visualizzare il pacchetto di accesso è il **collegamento al portale** di accesso diretto al pacchetto di accesso.

## <a name="delete"></a>Eliminare

Un pacchetto di accesso può essere eliminato solo se non sono presenti assegnazioni utente attive.

**Ruolo prerequisito:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Nel menu a sinistra fare clic su assegnazioni e rimuovere l'accesso per tutti gli utenti.

1. Nel menu a sinistra fare clic su **Panoramica** e quindi su **Elimina**.

1. Nel messaggio di eliminazione visualizzato, fare clic su **Sì**.

## <a name="when-are-changes-applied"></a>Quando vengono applicate le modifiche

Per la gestione dei diritti, Azure AD elaborerà le modifiche bulk per l'assegnazione e le risorse nei pacchetti di accesso più volte al giorno. Se quindi si effettua un'assegnazione o si modificano i ruoli delle risorse del pacchetto di accesso, possono essere necessarie fino a 24 ore prima che la modifica venga effettuata in Azure AD, oltre alla quantità di tempo necessaria per propagare tali modifiche ad altri Microsoft Online Services o a un'applicazione SaaS connessa s. Se la modifica interessa solo pochi oggetti, la modifica richiederà solo alcuni minuti per l'applicazione Azure AD, dopo la quale altri componenti Azure AD rileveranno la modifica e aggiorneranno le applicazioni SaaS. Se la modifica interessa migliaia di oggetti, la modifica avrà più tempo. Se, ad esempio, si dispone di un pacchetto di accesso con due applicazioni e 100 assegnazioni utente e si decide di aggiungere un ruolo del sito di SharePoint al pacchetto di accesso, è possibile che si verifichi un ritardo fino a quando tutti gli utenti non fanno parte del ruolo del sito di SharePoint. È possibile monitorare lo stato di avanzamento tramite il registro di controllo Azure AD, il log di provisioning Azure AD e i log di controllo del sito di SharePoint.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere un proprietario del catalogo o una gestione pacchetti di accesso](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [Richieste di processo e notifiche tramite posta elettronica](entitlement-management-process.md)
