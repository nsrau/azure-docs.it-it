---
title: Risolvere i problemi di gestione dei diritti Azure AD-Azure Active Directory
description: Informazioni su alcuni elementi da controllare per la risoluzione dei problemi Azure Active Directory la gestione dei diritti.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f7e5e6168e8b365a04040f595f30e68e4e82f0a
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173324"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Risolvere i problemi di Azure AD gestione dei diritti

Questo articolo descrive alcuni elementi da controllare per semplificare la risoluzione dei problemi di Azure Active Directory (Azure AD) la gestione dei diritti.

## <a name="administration"></a>Administration

* Se si riceve un messaggio di accesso negato quando si configura la gestione dei diritti e si è un amministratore globale, assicurarsi che la directory disponga di una [licenza Azure ad Premium P2 (o EMS E5)](entitlement-management-overview.md#license-requirements).

* Se si riceve un messaggio di accesso negato durante la creazione o la visualizzazione di pacchetti di accesso e si è un membro di un gruppo di autori del catalogo, è necessario [creare un catalogo](entitlement-management-catalog-create.md) prima di creare il primo pacchetto di accesso.

## <a name="resources"></a>resources

* I ruoli per le applicazioni sono definiti dall'applicazione stessa e vengono gestiti in Azure AD. Se un'applicazione non dispone di alcun ruolo delle risorse, la gestione dei diritti assegna gli utenti a un ruolo di **accesso predefinito** .

    Si noti che i portale di Azure possono anche visualizzare entità servizio per i servizi che non possono essere selezionati come applicazioni.  In particolare, **Exchange Online** e **SharePoint Online** sono servizi, non applicazioni con ruoli delle risorse nella directory, quindi non possono essere inclusi in un pacchetto di Access.  Usare invece le licenze basate sui gruppi per stabilire una licenza appropriata per un utente che deve accedere a tali servizi.

* Affinché un gruppo sia una risorsa in un pacchetto di accesso, deve essere in grado di essere modificabile in Azure AD.  I gruppi che hanno origine in un Active Directory locale non possono essere assegnati come risorse perché i relativi attributi proprietario o membro non possono essere modificati nel Azure AD.   I gruppi che provengono da Exchange Online come gruppi di distribuzione non possono essere modificati in Azure AD. 

* Non è possibile aggiungere le raccolte documenti di SharePoint Online e i singoli documenti come risorse.  Al contrario, creare un [gruppo di sicurezza Azure ad](../fundamentals/active-directory-groups-create-azure-portal.md), includere il gruppo e un ruolo del sito nel pacchetto di accesso e in SharePoint Online utilizzare tale gruppo per controllare l'accesso alla raccolta documenti o al documento.

* Se sono presenti utenti che sono già stati assegnati a una risorsa che si vuole gestire con un pacchetto di accesso, assicurarsi che gli utenti siano assegnati al pacchetto di accesso con un criterio appropriato. Ad esempio, potrebbe essere necessario includere un gruppo in un pacchetto di accesso che dispone già di utenti nel gruppo. Se gli utenti del gruppo necessitano dell'accesso continuo, devono avere un criterio appropriato per i pacchetti di accesso in modo che non perdano l'accesso al gruppo. È possibile assegnare il pacchetto di accesso chiedendo agli utenti di richiedere il pacchetto di accesso contenente tale risorsa o di assegnarli direttamente al pacchetto di accesso. Per ulteriori informazioni, vedere [modifica delle impostazioni di richiesta e approvazione per un pacchetto di accesso](entitlement-management-access-package-request-policy.md).

* Quando si rimuove un membro di un team, questi vengono rimossi anche dal gruppo di Office 365. La rimozione dalla funzionalità di chat del team potrebbe essere posticipata. Per ulteriori informazioni, vedere [appartenenza](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership)a un gruppo.

* Verificare che la directory non sia configurata per l'area geografica. Attualmente la gestione dei diritti non supporta le località geografiche per SharePoint Online. I siti di SharePoint Online devono trovarsi nella posizione geografica predefinita per poter essere gestiti con diritti di gestione. Per altre informazioni, vedere funzionalità per più aree [geografiche in OneDrive e SharePoint Online](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365).

## <a name="external-users"></a>Utenti esterni

* Quando un utente esterno desidera richiedere l'accesso a un pacchetto di accesso, assicurarsi che usi il **collegamento portale di accesso personale** per il pacchetto di accesso. Per ulteriori informazioni, vedere [la pagina relativa alla condivisione del collegamento per richiedere un pacchetto di accesso](entitlement-management-access-package-settings.md). Se un utente esterno visita solo **MyAccess.Microsoft.com** e non usa il collegamento del portale di accesso completo, visualizzerà i pacchetti di accesso disponibili nella propria organizzazione e non nell'organizzazione.

* Se un utente esterno non è in grado di richiedere l'accesso a un pacchetto di accesso o non è in grado di accedere alle risorse, assicurarsi di controllare le [impostazioni degli utenti esterni](entitlement-management-external-users.md#settings-for-external-users).

* Se un nuovo utente esterno, che non ha precedentemente eseguito l'accesso alla directory, riceve un pacchetto di accesso che include un sito di SharePoint Online, il relativo pacchetto di accesso verrà visualizzato come non completamente recapitato fino a quando non viene eseguito il provisioning dell'account in SharePoint Online. Per altre informazioni sulle impostazioni di condivisione, vedere [esaminare le impostazioni di condivisione esterna di SharePoint Online](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings).

## <a name="requests"></a>Richieste

* Quando un utente desidera richiedere l'accesso a un pacchetto di accesso, assicurarsi che usi il **collegamento portale di accesso personale** per il pacchetto di accesso. Per ulteriori informazioni, vedere [la pagina relativa alla condivisione del collegamento per richiedere un pacchetto di accesso](entitlement-management-access-package-settings.md).

* Se si apre il portale di accesso personale con il browser impostato sulla modalità in modalità privata o in incognito, è possibile che si verifichi un conflitto con il comportamento di accesso. Si consiglia di non usare la modalità in modalità privata o in incognito per il browser quando si visita il portale di accesso personale.

* Quando un utente che non è ancora presente nella directory accede al portale di accesso personale per richiedere un pacchetto di accesso, assicurarsi di eseguire l'autenticazione usando il proprio account aziendale. L'account aziendale può essere un account nella directory delle risorse o in una directory inclusa in uno dei criteri del pacchetto di accesso. Se l'account dell'utente non è un account aziendale o la directory in cui viene eseguita l'autenticazione non è inclusa nel criterio, l'utente non visualizzerà il pacchetto di accesso. Per altre informazioni, vedere [richiedere l'accesso a un pacchetto di accesso](entitlement-management-request-access.md).

* Se un utente non accede alla directory delle risorse, non sarà in grado di richiedere l'accesso nel portale di accesso personale. Prima che l'utente possa richiedere l'accesso, è necessario rimuovere il blocco di accesso dal profilo dell'utente. Per rimuovere il blocco di accesso, nella portale di Azure fare clic su **Azure Active Directory**, fare clic su **utenti**, fare clic sull'utente e quindi su **profilo**. Modificare la sezione **Impostazioni** e modificare **Accedi** a **No**. Per altre informazioni, vedere [aggiungere o aggiornare le informazioni sul profilo di un utente usando Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  È anche possibile controllare se l'utente è stato bloccato a causa di un [criterio di Identity Protection](../identity-protection/howto-unblock-user.md).

* Se un utente è un richiedente e un responsabile approvazione, nel portale di accesso personale non visualizzerà la richiesta di un pacchetto di accesso nella pagina **approvazioni** . Questo comportamento è intenzionale, un utente non può approvare la propria richiesta. Verificare che il pacchetto di accesso richiesto disponga di responsabili approvazione aggiuntivi configurati nei criteri. Per ulteriori informazioni, vedere [modifica delle impostazioni di richiesta e approvazione per un pacchetto di accesso](entitlement-management-access-package-request-policy.md).

### <a name="view-a-requests-delivery-errors"></a>Visualizzare gli errori di recapito di una richiesta

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **richieste**.

1. Selezionare la richiesta che si desidera visualizzare.

    Se la richiesta presenta errori di recapito, lo stato della richiesta sarà non **recapitato** o **parzialmente recapitato**.

    Se si verificano errori di recapito, nel riquadro dei dettagli della richiesta sarà presente un conteggio degli errori di recapito.

1. Fare clic sul conteggio per visualizzare tutti gli errori di recapito della richiesta.

### <a name="reprocess-a-request"></a>Rielaborare una richiesta

Se una richiesta rileva un errore, è possibile rielaborare la richiesta per riprovare. È possibile rielaborare solo una richiesta con uno stato di **recapito non riuscito** o **parzialmente recapitato** e una data completata inferiore a una settimana.

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **richieste**.

1. Fare clic sulla richiesta che si desidera rielaborare.

1. Nel riquadro Dettagli richiesta fare clic su **rielaborare la richiesta**.

    ![Rielaborare una richiesta non riuscita](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Annullare una richiesta in sospeso

È possibile annullare una richiesta in sospeso che non è ancora stata recapitata o il cui recapito non è riuscito.

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **richieste**.

1. Fare clic sulla richiesta che si desidera annullare.

1. Nel riquadro Dettagli richiesta fare clic su **Annulla richiesta**.

## <a name="multiple-policies"></a>Più criteri

* La gestione dei diritti segue le procedure consigliate per i privilegi minimi. Quando un utente richiede l'accesso a un pacchetto di accesso con più criteri che si applicano, la gestione dei diritti include la logica per garantire che i criteri più severi o più specifici siano classificati in base a criteri generici. Se un criterio è generico, la gestione dei diritti potrebbe non visualizzare i criteri per il richiedente o potrebbe selezionare automaticamente un criterio più restrittivo.

* Si consideri, ad esempio, un pacchetto di accesso con due criteri per i dipendenti interni in cui entrambi i criteri si applicano al richiedente. Il primo criterio è per utenti specifici che includono il richiedente. Il secondo criterio è per tutti gli utenti di una directory di cui il richiedente è membro. In questo scenario, il primo criterio viene selezionato automaticamente per il richiedente perché è più restrittivo. Al richiedente non è stata configurata l'opzione per selezionare il secondo criterio.

* Quando si applicano più criteri, i criteri selezionati automaticamente o i criteri visualizzati per il richiedente sono basati sulla logica di priorità seguente:

    | Priorità dei criteri | Scope |
    | --- | --- |
    | P1 | Utenti e gruppi specifici nella directory o in organizzazioni connesse specifiche |
    | P2 | Tutti i membri della directory (esclusi i guest) |
    | P3 | Tutti gli utenti della directory (inclusi i guest) o organizzazioni connesse specifiche |
    | P4 | Tutte le organizzazioni connesse o tutti gli utenti (tutte le organizzazioni connesse e tutti i nuovi utenti esterni) |
    
    Se un criterio è in una categoria con priorità più elevata, le categorie con priorità più bassa verranno ignorate. Per un esempio di come vengono visualizzati più criteri con la stessa priorità per il richiedente, vedere [selezionare un criterio](entitlement-management-request-access.md#select-a-policy).

## <a name="next-steps"></a>Passaggi successivi

- [Governare l'accesso per gli utenti esterni](entitlement-management-external-users.md)
- [Visualizzare i report relativi al modo in cui gli utenti hanno accesso alla gestione dei diritti](entitlement-management-reports.md)
