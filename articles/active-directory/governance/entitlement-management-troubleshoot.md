---
title: Risolvere i problemi di gestione dei diritti-Azure AD
description: Informazioni su alcuni elementi da controllare per la risoluzione dei problemi Azure Active Directory la gestione dei diritti.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c38e1a61827da547bb39a699a0e92043e63466c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80128468"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Risolvere i problemi di Azure AD gestione dei diritti

Questo articolo descrive alcuni elementi da controllare per semplificare la risoluzione dei problemi di Azure Active Directory (Azure AD) la gestione dei diritti.

## <a name="administration"></a>Amministrazione

* Se si riceve un messaggio di accesso negato quando si configura la gestione dei diritti e si è un amministratore globale, assicurarsi che la directory disponga di una [licenza Azure ad Premium P2 (o EMS E5)](entitlement-management-overview.md#license-requirements).

* Se si riceve un messaggio di accesso negato durante la creazione o la visualizzazione di pacchetti di accesso e si è un membro di un gruppo di autori del catalogo, è necessario [creare un catalogo](entitlement-management-catalog-create.md) prima di creare il primo pacchetto di accesso.

## <a name="resources"></a>Risorse

* I ruoli per le applicazioni sono definiti dall'applicazione stessa e vengono gestiti in Azure AD. Se un'applicazione non dispone di alcun ruolo delle risorse, la gestione dei diritti assegna gli utenti a un ruolo di **accesso predefinito** .

    Si noti che i portale di Azure possono anche visualizzare entità servizio per i servizi che non possono essere selezionati come applicazioni.  In particolare, **Exchange Online** e **SharePoint Online** sono servizi, non applicazioni con ruoli delle risorse nella directory, quindi non possono essere inclusi in un pacchetto di Access.  Usare invece le licenze basate su gruppi per stabilire una licenza appropriata per un utente che ha bisogno di accedere a tali servizi.

* Per poter essere una risorsa in un pacchetto di accesso, un gruppo deve essere modificabile in Azure AD.  I gruppi che hanno origine in Active Directory locale non possono essere assegnati come risorse perché i loro attributi di proprietario o membro non possono essere modificati in Azure AD.   Nemmeno i gruppi che hanno origine in Exchange Online come gruppi di distribuzione possono essere modificati in Azure AD. 

* Le raccolte documenti e i singoli documenti di SharePoint Online non possono essere aggiunti come risorse.  Al contrario, creare un [gruppo di sicurezza Azure ad](../fundamentals/active-directory-groups-create-azure-portal.md), includere il gruppo e un ruolo del sito nel pacchetto di accesso e in SharePoint Online utilizzare tale gruppo per controllare l'accesso alla raccolta documenti o al documento.

* Se ci sono utenti già assegnati a una risorsa che si desidera gestire con un pacchetto di accesso, assicurarsi che siano assegnati al pacchetto di accesso con un criterio appropriato. Ad esempio, potrebbe essere necessario includere un gruppo in un pacchetto di accesso che ha già utenti nel gruppo. Se gli utenti nel gruppo richiedono accesso continuo, devono disporre dei un criterio appropriato per i pacchetti di accesso, in modo da non perdere l'accesso al gruppo. È possibile assegnare il pacchetto di accesso chiedendo agli utenti di richiedere il pacchetto di accesso contenente tale risorsa oppure assegnandoli direttamente al pacchetto di accesso. Per ulteriori informazioni, vedere [modifica delle impostazioni di richiesta e approvazione per un pacchetto di accesso](entitlement-management-access-package-request-policy.md).

* Quando si rimuove un membro di un team, questo membro del team viene rimosso anche dal gruppo di Office 365. La rimozione dalla funzionalità di chat del team potrebbe essere posticipata. Per ulteriori informazioni, vedere [appartenenza](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership)a un gruppo.

* Verificare che la directory non sia configurata per varie aree geografiche. Attualmente la gestione entitlement non supporta varie posizioni geografiche per SharePoint Online. Per poter essere gestiti con la gestione entitlement, i siti di SharePoint Online devono trovarsi nella posizione geografica predefinita. Per altre informazioni, vedere funzionalità per più aree [geografiche in OneDrive e SharePoint Online](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365).

## <a name="access-packages"></a>Pacchetti di accesso

* Se si tenta di eliminare un pacchetto o un criterio di accesso e viene visualizzato un messaggio di errore che indica che sono presenti assegnazioni attive, se non vengono visualizzati utenti con assegnazioni, controllare se gli utenti eliminati di recente hanno ancora assegnazioni. Durante la finestra di 30 giorni dopo l'eliminazione di un utente, è possibile ripristinare l'account utente.   

## <a name="external-users"></a>Utenti esterni

* Quando un utente esterno desidera richiedere l'accesso a un pacchetto di accesso, assicurarsi che usi il **collegamento portale di accesso personale** per il pacchetto di accesso. Per ulteriori informazioni, vedere [la pagina relativa alla condivisione del collegamento per richiedere un pacchetto di accesso](entitlement-management-access-package-settings.md). Se un utente esterno visita solo **MyAccess.Microsoft.com** e non usa il collegamento del portale di accesso completo, visualizzerà i pacchetti di accesso disponibili nella propria organizzazione e non nell'organizzazione.

* Se un utente esterno non è in grado di richiedere l'accesso a un pacchetto di accesso o non è in grado di accedere alle risorse, assicurarsi di controllare le [impostazioni degli utenti esterni](entitlement-management-external-users.md#settings-for-external-users).

* Se un nuovo utente esterno, che non ha precedentemente eseguito l'accesso alla directory, riceve un pacchetto di accesso che include un sito di SharePoint Online, il relativo pacchetto di accesso verrà visualizzato come non completamente recapitato fino a quando non viene eseguito il provisioning dell'account in SharePoint Online. Per altre informazioni sulle impostazioni di condivisione, vedere [esaminare le impostazioni di condivisione esterna di SharePoint Online](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings).

## <a name="requests"></a>Requests

* Quando un utente desidera richiedere l'accesso a un pacchetto di accesso, assicurarsi che usi il **collegamento portale di accesso personale** per il pacchetto di accesso. Per ulteriori informazioni, vedere [la pagina relativa alla condivisione del collegamento per richiedere un pacchetto di accesso](entitlement-management-access-package-settings.md).

* Se si apre il portale Accesso personale con il browser impostato sulla modalità privata o in incognito, potrebbe verificarsi un conflitto con il comportamento di accesso. È consigliabile non usare la modalità privata o in incognito per il browser quando si visita il portale Accesso personale.

* Quando un utente che non è ancora presente nella directory accede al portale Accesso personale per richiedere un pacchetto di accesso, assicurarsi che esegua l'autenticazione usando il suo account aziendale. L'account aziendale può appartenere alla directory delle risorse o a una directory inclusa in uno dei criteri del pacchetto di accesso. Se l'account dell'utente non è un account aziendale o la directory in cui esegue l'autenticazione non è inclusa nel criterio, l'utente non visualizzerà il pacchetto di accesso. Per altre informazioni, vedere [richiedere l'accesso a un pacchetto di accesso](entitlement-management-request-access.md).

* Se a un utente viene impedito di accedere alla directory delle risorse, tale utente non potrà richiedere l'accesso nel portale Accesso personale. Prima che l'utente possa richiedere l'accesso, è necessario rimuovere il blocco dell'accesso dal profilo dell'utente. Per rimuovere il blocco di accesso, nella portale di Azure fare clic su **Azure Active Directory**, fare clic su **utenti**, fare clic sull'utente e quindi su **profilo**. Modificare la sezione **Impostazioni** e modificare **Accedi** a **No**. Per altre informazioni, vedere [aggiungere o aggiornare le informazioni sul profilo di un utente usando Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  È anche possibile controllare se l'utente è stato bloccato a causa di un [criterio di Identity Protection](../identity-protection/howto-unblock-user.md).

* Se un utente è un richiedente e un responsabile approvazione, nel portale di accesso personale non visualizzerà la richiesta di un pacchetto di accesso nella pagina **approvazioni** . Questo comportamento è intenzionale: un utente non può approvare la sua stessa richiesta. Verificare che nei criteri del pacchetto di accesso richiesto siano configurati altri responsabili approvazione. Per ulteriori informazioni, vedere [modifica delle impostazioni di richiesta e approvazione per un pacchetto di accesso](entitlement-management-access-package-request-policy.md).

### <a name="view-a-requests-delivery-errors"></a>Visualizzare gli errori di recapito di una richiesta

**Ruolo prerequisito:** amministratore globale, amministratore utenti, proprietario del catalogo o responsabile dei pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **richieste**.

1. Selezionare la richiesta che si desidera visualizzare.

    Se la richiesta presenta errori di recapito, lo stato della richiesta sarà non **recapitato** o **parzialmente recapitato**.

    Se si verificano errori di recapito, nel riquadro dei dettagli della richiesta sarà presente un conteggio degli errori di recapito.

1. Fare clic sul conteggio per visualizzare tutti gli errori di recapito della richiesta.

### <a name="reprocess-a-request"></a>Rielaborare una richiesta

Se una richiesta rileva un errore, è possibile rielaborare la richiesta per riprovare. È possibile rielaborare solo una richiesta con uno stato di **recapito non riuscito** o **parzialmente recapitato** e una data completata inferiore a una settimana.

**Ruolo prerequisito:** amministratore globale, amministratore utenti, proprietario del catalogo o responsabile dei pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **richieste**.

1. Fare clic sulla richiesta che si desidera rielaborare.

1. Nel riquadro Dettagli richiesta fare clic su **rielaborare la richiesta**.

    ![Rielaborare una richiesta non riuscita](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Annullare una richiesta in sospeso

È possibile annullare una richiesta in sospeso che non è ancora stata recapitata o il cui recapito non è riuscito.

**Ruolo prerequisito:** amministratore globale, amministratore utenti, proprietario del catalogo o responsabile dei pacchetti di accesso

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

- [Gestire l'accesso per gli utenti esterni](entitlement-management-external-users.md)
- [Visualizzare i report relativi al modo in cui gli utenti hanno accesso alla gestione dei diritti](entitlement-management-reports.md)
