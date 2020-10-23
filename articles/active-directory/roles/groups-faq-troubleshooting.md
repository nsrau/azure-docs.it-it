---
title: Domande frequenti sulla risoluzione dei problemi assegnati al gruppo di Cloud-Azure Active Directory | Microsoft Docs
description: Informazioni sulle domande comuni e suggerimenti per la risoluzione dei problemi per l'assegnazione di ruoli a gruppi in Azure Active Directory.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 837750d7eeef9bc7a133a54b23a0c52c847364eb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377624"
---
# <a name="troubleshooting-roles-assigned-to-cloud-groups"></a>Risoluzione dei problemi correlati ai ruoli assegnati ai gruppi cloud

Di seguito sono riportate alcune domande comuni e suggerimenti per la risoluzione dei problemi per l'assegnazione di ruoli a gruppi in Azure Active Directory (Azure AD).

**D:** Sono un amministratore di gruppi, ma non è possibile vedere che i **ruoli di Azure ad possono essere assegnati all'opzione di gruppo** .

**R:** Solo gli amministratori dei ruoli con privilegi o gli amministratori globali possono creare un gruppo idoneo per l'assegnazione di ruolo. Il controllo viene visualizzato solo dagli utenti di tali ruoli.

**D:** Chi può modificare l'appartenenza dei gruppi assegnati ai ruoli Azure AD?

**R:** Per impostazione predefinita, solo l'amministratore del ruolo con privilegi e l'amministratore globale gestiscono l'appartenenza di un gruppo assegnabile al ruolo, ma è possibile delegare la gestione dei gruppi assegnabili al ruolo aggiungendo i proprietari del gruppo.

**D**: sono un amministratore dell'helpdesk nell'organizzazione, ma non è possibile aggiornare la password di un utente che è un lettore di directory. Perché si verifica?

**R**: l'utente potrebbe avere ottenuto un lettore di directory tramite un gruppo assegnabile al ruolo. Tutti i membri e i proprietari di un gruppo assegnabile al ruolo sono protetti. Solo gli utenti con privilegi di amministratore di autenticazione o ruoli di amministratore globale possono reimpostare le credenziali per un utente protetto.

**D:** Non è possibile aggiornare la password di un utente. Non dispone di un ruolo con privilegi più elevati assegnato. Perché si verifica?

**R:** L'utente può essere un proprietario di un gruppo assegnabile al ruolo. Proteggiamo i proprietari dei gruppi assegnabili ai ruoli per evitare l'elevazione dei privilegi. Un esempio potrebbe essere se un gruppo Contoso_Security_Admins viene assegnato al ruolo di amministratore della sicurezza, dove Bob è il proprietario del gruppo e Alice è amministratore password nell'organizzazione. Se questa protezione non fosse presente, Alice poteva reimpostare le credenziali di Bob e prendere il sopravvento sulla propria identità. Successivamente, Alice potrebbe aggiungere se stesso o chiunque al gruppo Contoso_Security_Admins gruppo per diventare amministratore della sicurezza nell'organizzazione. Per sapere se un utente è un proprietario del gruppo, ottenere l'elenco di oggetti di proprietà dell'utente e verificare se uno dei gruppi ha isAssignableToRole impostato su true. In caso affermativo, l'utente è protetto e il comportamento è di progettazione. Fare riferimento a queste documentazioni per ottenere oggetti di proprietà:

- [Get-AzureADUserOwnedObject](/powershell/module/azuread/get-azureaduserownedobject?view=azureadps-2.0)  
- [Elencare ownedObjects](/graph/api/user-list-ownedobjects?tabs=http&view=graph-rest-1.0)

**D:** È possibile creare una verifica di accesso nei gruppi che possono essere assegnati ai ruoli Azure AD (in particolare, gruppi con la proprietà isAssignableToRole impostata su true)?  

**R:** Sì, è possibile. Se si utilizza la versione più recente della verifica di accesso, i revisori vengono indirizzati all'accesso per impostazione predefinita e solo gli amministratori globali possono creare verifiche di accesso per i gruppi assegnabili al ruolo. Tuttavia, se si utilizza la versione precedente di verifica di accesso, i revisori vengono indirizzati al pannello di accesso per impostazione predefinita e sia gli amministratori globali che l'amministratore utente possono creare verifiche di accesso per i gruppi assegnabili al ruolo. La nuova esperienza verrà implementata per tutti i clienti il 28 luglio 2020, ma se si vuole eseguire l'aggiornamento prima, effettuare una richiesta per [Azure ad le verifiche di accesso-esperienza di revisore aggiornata nell'iscrizione all'accesso](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

**D:** È possibile creare un pacchetto di accesso e inserire gruppi che possono essere assegnati a Azure AD ruoli?

**R:** Sì, è possibile. L'amministratore globale e l'amministratore utente hanno la possibilità di inserire qualsiasi gruppo in un pacchetto di accesso. Non sono state apportate modifiche per l'amministratore globale, ma è presente una lieve modifica nelle autorizzazioni del ruolo Amministratore utenti. Per inserire un gruppo assegnabile da ruoli in un pacchetto di accesso, è necessario essere un amministratore utente e anche il proprietario del gruppo assegnabile al ruolo. Ecco la tabella completa che mostra chi può creare un pacchetto di accesso in Enterprise License Management:

Ruolo della directory di Azure AD | Ruolo di gestione dei diritti | È possibile aggiungere un gruppo di sicurezza\* | È possibile aggiungere Microsoft 365 gruppo\* | È possibile aggiungere l'app | È possibile aggiungere il sito di SharePoint Online
----------------------- | --------------------------- | ----------------------- | ------------------------- | ----------- |  -----------------------------
Amministratore globale | n/d | ✔️ | ✔️ | ✔️  | ✔️
Amministratore utenti  | n/d  | ✔️  | ✔️  | ✔️
Amministratore di Intune | Proprietario Catalogo | ✔️  | ✔️  | &nbsp;  | &nbsp;
Amministratori di Exchange  | Proprietario Catalogo  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
Amministratore del servizio Teams | Proprietario Catalogo  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
Amministratore di SharePoint | Proprietario Catalogo | &nbsp; | ✔️  | &nbsp;  | ✔️ 
Amministratore di applicazioni | Proprietario Catalogo  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Amministratore di applicazioni cloud | Proprietario Catalogo  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Utente | Proprietario Catalogo | Solo se il proprietario del gruppo | Solo se il proprietario del gruppo | Solo se il proprietario dell'app  | &nbsp;

\*Il gruppo non è assegnabile al ruolo; ovvero isAssignableToRole = false. Se un gruppo è assegnabile al ruolo, l'utente che crea il pacchetto di accesso deve essere anche proprietario del gruppo assegnabile al ruolo.

**D:** Non è possibile trovare l'opzione "Rimuovi assegnazione" in "ruoli assegnati". Ricerca per categorie eliminare un'assegnazione di ruolo a un utente?

**R:** Questa risposta è applicabile solo alle organizzazioni Azure AD Premium P1.

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire **Azure Active Directory**.
1. Selezionare utenti e aprire un profilo utente.
1. Selezionare **ruoli assegnati**.
1. Selezionare l'icona dell'ingranaggio. Viene visualizzato un riquadro che può fornire queste informazioni. Accanto alle assegnazioni dirette è presente un pulsante "Rimuovi". Per rimuovere l'assegnazione di ruolo indiretta, rimuovere l'utente dal gruppo a cui è stato assegnato il ruolo.

**D:** Ricerca per categorie visualizzare tutti i gruppi assegnabili al ruolo?

**R:** Attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire **Azure Active Directory**.
1. Selezionare **gruppi**  >  **tutti i gruppi**.
1. Selezionare **Aggiungi filtri**.
1. Filtrare per **assegnare un ruolo assegnabile**.

**D:** Ricerca per categorie sa quale ruolo viene assegnato a un'entità direttamente e indirettamente?

**R:** Attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire **Azure Active Directory**.
1. Selezionare utenti e aprire un profilo utente.
1. Selezionare **ruoli assegnati**, quindi:

    - In Azure AD Premium le organizzazioni con licenza P1: selezionare l'icona dell'ingranaggio. Viene visualizzato un riquadro che può fornire queste informazioni.
    - Nelle organizzazioni Azure AD Premium P2 con licenza: sono disponibili informazioni sulle licenze dirette e ereditate nella colonna **Membership** .

**D:** Perché viene applicata la creazione di un nuovo gruppo di cloud per assegnarlo a Role?  

**R:** Se si assegna un gruppo esistente a un ruolo, il proprietario del gruppo esistente potrebbe aggiungere altri membri al gruppo senza che i nuovi membri rendano conto che avranno il ruolo. Poiché i gruppi assegnabili al ruolo sono potenti, sono previste numerose restrizioni per la protezione. Non si vuole che le modifiche al gruppo siano sorprendenti per la persona che gestisce il gruppo.

## <a name="next-steps"></a>Passaggi successivi

- [Usare i gruppi di cloud per gestire le assegnazioni di ruolo](groups-concept.md)
- [Creare un gruppo assegnabile a un ruolo](groups-create-eligible.md)