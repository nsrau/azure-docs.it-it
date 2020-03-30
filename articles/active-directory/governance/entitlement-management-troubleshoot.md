---
title: Risolvere i problemi relativi alla gestione dei diritti - Azure ADTroubleshoot entitlement management - Azure AD
description: Informazioni su alcuni elementi da controllare per risolvere i problemi relativi alla gestione dei diritti di Azure Active Directory.Learn about some items you should check for you troubleshoot Azure Active Directory entitlement management.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128468"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Risolvere i problemi relativi alla gestione dei diritti di Azure ADTroubleshoot Azure AD entitlement management

Questo articolo descrive alcuni elementi che è necessario controllare per risolvere i problemi di gestione dei diritti di Azure Active Directory (Azure AD).

## <a name="administration"></a>Amministrazione

* Se viene visualizzato un messaggio di accesso negato durante la configurazione della gestione dei diritti e si è un amministratore globale, verificare che la directory disponga di una [licenza di Azure AD Premium P2 (o EMS E5).](entitlement-management-overview.md#license-requirements)

* Se viene visualizzato un messaggio di accesso negato durante la creazione o la visualizzazione di pacchetti di accesso e si è membri di un gruppo di creatori di Catalog, è necessario [creare un catalogo](entitlement-management-catalog-create.md) prima di creare il primo pacchetto di accesso.

## <a name="resources"></a>Risorse

* I ruoli per le applicazioni sono definiti dall'applicazione stessa e vengono gestiti in Azure AD. Se un'applicazione non dispone di ruoli di risorsa, la gestione dei diritti assegna gli utenti a un ruolo **Accesso predefinito.**

    Si noti che il portale di Azure può anche mostrare le entità servizio per i servizi che non possono essere selezionati come applicazioni.  In particolare, **Exchange Online** e **SharePoint Online** sono servizi, non applicazioni con ruoli di risorse nella directory, pertanto non possono essere inclusi in un pacchetto di accesso.  Usare invece le licenze basate su gruppi per stabilire una licenza appropriata per un utente che ha bisogno di accedere a tali servizi.

* Per poter essere una risorsa in un pacchetto di accesso, un gruppo deve essere modificabile in Azure AD.  I gruppi che hanno origine in Active Directory locale non possono essere assegnati come risorse perché i loro attributi di proprietario o membro non possono essere modificati in Azure AD.   Nemmeno i gruppi che hanno origine in Exchange Online come gruppi di distribuzione possono essere modificati in Azure AD. 

* Le raccolte documenti e i singoli documenti di SharePoint Online non possono essere aggiunti come risorse.  Creare invece un gruppo di sicurezza di [Azure AD,](../fundamentals/active-directory-groups-create-azure-portal.md)includere tale gruppo e un ruolo del sito nel pacchetto di accesso e in SharePoint Online utilizzare tale gruppo per controllare l'accesso alla raccolta documenti o al documento.

* Se ci sono utenti già assegnati a una risorsa che si desidera gestire con un pacchetto di accesso, assicurarsi che siano assegnati al pacchetto di accesso con un criterio appropriato. Ad esempio, potrebbe essere necessario includere un gruppo in un pacchetto di accesso che ha già utenti nel gruppo. Se gli utenti nel gruppo richiedono accesso continuo, devono disporre dei un criterio appropriato per i pacchetti di accesso, in modo da non perdere l'accesso al gruppo. È possibile assegnare il pacchetto di accesso chiedendo agli utenti di richiedere il pacchetto di accesso contenente tale risorsa oppure assegnandoli direttamente al pacchetto di accesso. Per ulteriori informazioni, consultate [Modificare le impostazioni di richiesta e approvazione per un pacchetto di accesso.](entitlement-management-access-package-request-policy.md)

* Quando si rimuove un membro di un team, questo membro del team viene rimosso anche dal gruppo di Office 365. La rimozione dalla funzionalità di chat del team potrebbe essere posticipata. Per ulteriori informazioni, vedere [Appartenenza a gruppi](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

* Verificare che la directory non sia configurata per varie aree geografiche. Attualmente la gestione entitlement non supporta varie posizioni geografiche per SharePoint Online. Per poter essere gestiti con la gestione entitlement, i siti di SharePoint Online devono trovarsi nella posizione geografica predefinita. Per ulteriori informazioni, vedere [Funzionalità multi-geografiche in OneDrive e SharePoint Online](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365).

## <a name="access-packages"></a>Pacchetti di accesso

* Se si tenta di eliminare un pacchetto o un criterio di accesso e viene visualizzato un messaggio di errore che indica che sono presenti assegnazioni attive, se non viene visualizzato alcun utente con assegnazioni, verificare se gli utenti eliminati di recente dispongono ancora di assegnazioni. Durante la finestra di 30 giorni dopo l'eliminazione di un utente, l'account utente può essere ripristinato.   

## <a name="external-users"></a>Utenti esterni

* Quando un utente esterno desidera richiedere l'accesso a un pacchetto di accesso, assicurarsi che utilizzi il **collegamento al portale My Access** per il pacchetto di accesso. Per ulteriori informazioni, consultate Collegamento Condividi per richiedere un pacchetto di [accesso.](entitlement-management-access-package-settings.md) Se un utente esterno visita semplicemente **myaccess.microsoft.com** e non utilizza il collegamento completo al portale My Access, visualizzerà i pacchetti di accesso disponibili nella propria organizzazione e non nell'organizzazione.

* Se un utente esterno non è in grado di richiedere l'accesso a un pacchetto di accesso o non è in grado di accedere alle risorse, verificare le impostazioni degli [utenti esterni.](entitlement-management-external-users.md#settings-for-external-users)

* Se un nuovo utente esterno, che non ha precedentemente eseguito l'accesso alla directory, riceve un pacchetto di accesso che include un sito di SharePoint Online, il relativo pacchetto di accesso verrà visualizzato come non completamente recapitato fino a quando non viene eseguito il provisioning dell'account in SharePoint Online. Per ulteriori informazioni sulle impostazioni di condivisione, vedere [Esaminare le impostazioni](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings)di condivisione esterna di SharePoint Online .

## <a name="requests"></a>Requests

* Quando un utente desidera richiedere l'accesso a un pacchetto di accesso, assicurarsi di utilizzare il **collegamento del portale My Access** per il pacchetto di accesso. Per ulteriori informazioni, consultate Collegamento Condividi per richiedere un pacchetto di [accesso.](entitlement-management-access-package-settings.md)

* Se si apre il portale Accesso personale con il browser impostato sulla modalità privata o in incognito, potrebbe verificarsi un conflitto con il comportamento di accesso. È consigliabile non usare la modalità privata o in incognito per il browser quando si visita il portale Accesso personale.

* Quando un utente che non è ancora presente nella directory accede al portale Accesso personale per richiedere un pacchetto di accesso, assicurarsi che esegua l'autenticazione usando il suo account aziendale. L'account aziendale può appartenere alla directory delle risorse o a una directory inclusa in uno dei criteri del pacchetto di accesso. Se l'account dell'utente non è un account aziendale o la directory in cui esegue l'autenticazione non è inclusa nel criterio, l'utente non visualizzerà il pacchetto di accesso. Per ulteriori informazioni, consultate [Richiedere l'accesso a un pacchetto](entitlement-management-request-access.md)di accesso.

* Se a un utente viene impedito di accedere alla directory delle risorse, tale utente non potrà richiedere l'accesso nel portale Accesso personale. Prima che l'utente possa richiedere l'accesso, è necessario rimuovere il blocco dell'accesso dal profilo dell'utente. Per rimuovere il blocco di accesso, nel portale di Azure fare clic su **Azure Active Directory**, su **Utenti**, sull'utente e quindi su **Profilo**. Modificare la sezione **Impostazioni** e impostare **Blocca accesso su** **No**. Per altre informazioni, vedere Aggiungere o aggiornare le informazioni sul profilo di un utente usando Azure Active Directory.For more information, see Add or [update a user's profile information using Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  È inoltre possibile verificare se l'utente è stato bloccato a causa di un criterio di [protezione dell'identità](../identity-protection/howto-unblock-user.md).

* Nel portale Accesso personale, se un utente è sia un richiedente che un approvatore, non visualizzerà la richiesta di un pacchetto di accesso nella pagina **Approvazioni.** Questo comportamento è intenzionale: un utente non può approvare la sua stessa richiesta. Verificare che nei criteri del pacchetto di accesso richiesto siano configurati altri responsabili approvazione. Per ulteriori informazioni, consultate [Modificare le impostazioni di richiesta e approvazione per un pacchetto di accesso.](entitlement-management-access-package-request-policy.md)

### <a name="view-a-requests-delivery-errors"></a>Visualizzare gli errori di recapito di una richiesta

**Ruolo prerequisito:** Amministratore globale, Amministratore utente, Proprietario catalogo o Gestore pacchetti di Access

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Pacchetti di accesso** e quindi apri il pacchetto di accesso.

1. Fare clic su **Richieste**.

1. Selezionare la richiesta che si desidera visualizzare.

    Se la richiesta contiene errori di recapito, lo stato della richiesta sarà **Non recapitato** o **Recapitato parzialmente**.

    Se sono presenti errori di recapito, nel riquadro dei dettagli della richiesta si verifica un conteggio degli errori di recapito.

1. Fare clic sul conteggio per visualizzare tutti gli errori di recapito della richiesta.

### <a name="reprocess-a-request"></a>Rielaborare una richiesta

Se una richiesta rileva un errore, è possibile rielaborarla per riprovare. È possibile rielaborare solo una richiesta con stato **Recapito non riuscito** o **Recapito parziale** e data di completamento inferiore a una settimana.

**Ruolo prerequisito:** Amministratore globale, Amministratore utente, Proprietario catalogo o Gestore pacchetti di Access

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Pacchetti di accesso** e quindi apri il pacchetto di accesso.

1. Fare clic su **Richieste**.

1. Fare clic sulla richiesta che si desidera rielaborare.

1. Nel riquadro dei dettagli della richiesta fare clic su **Rielabora richiesta**.

    ![Rielaborare una richiesta non riuscitaReprocess a failed request](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Annullare una richiesta in sospeso

È possibile annullare solo una richiesta in sospeso che non è ancora stata recapitata o la cui consegna non è riuscita.

**Ruolo prerequisito:** Amministratore globale, Amministratore utente, Proprietario catalogo o Gestore pacchetti di Access

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Pacchetti di accesso** e quindi apri il pacchetto di accesso.

1. Fare clic su **Richieste**.

1. Fare clic sulla richiesta che si desidera annullare.

1. Nel riquadro dei dettagli della richiesta fare clic su **Annulla richiesta**.

## <a name="multiple-policies"></a>Criteri multipli

* La gestione dei diritti segue le procedure consigliate per i privilegi minimi. Quando un utente richiede l'accesso a un pacchetto di accesso con più criteri applicabili, la gestione dei diritti include la logica per garantire la priorità di criteri più restrittivi o più specifici rispetto ai criteri generici. Se un criterio è generico, la gestione dei diritti potrebbe non visualizzare il criterio al richiedente o selezionare automaticamente un criterio più rigoroso.

* Si consideri ad esempio un pacchetto di accesso con due criteri per i dipendenti interni in cui entrambi i criteri si applicano al richiedente. Il primo criterio è per utenti specifici che includono il richiedente. Il secondo criterio è per tutti gli utenti in una directory di cui il richiedente è membro. In questo scenario, il primo criterio viene selezionato automaticamente per il richiedente perché è più rigoroso. Al richiedente non viene data la possibilità di selezionare il secondo criterio.

* Quando si applicano più criteri, i criteri selezionati automaticamente o i criteri visualizzati al richiedente si basano sulla logica di priorità seguente:When multiple policies apply, the policy that is automatically selected or the policies that are displayed to the requestor is based on the following priority logic:

    | Priorità politica | Scope |
    | --- | --- |
    | P1 | Utenti e gruppi specifici nella directory OR Organizzazioni connesse specifiche |
    | P2 | Tutti i membri della directory (esclusi gli ospiti) |
    | P3 | Tutti gli utenti nella directory (inclusi gli ospiti) OPPURE Organizzazioni connesse specifiche |
    | P4 | Tutte le organizzazioni connesse O Tutti gli utenti (tutte le organizzazioni connesse, ovvero eventuali nuovi utenti esterni) |
    
    Se un criterio rientra in una categoria con priorità più alta, le categorie con priorità inferiore vengono ignorate. Per un esempio di visualizzazione di più criteri con la stessa priorità al richiedente, vedere [Selezionare un criterio](entitlement-management-request-access.md#select-a-policy).

## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'accesso per gli utenti esterni](entitlement-management-external-users.md)
- [Visualizzare i report su come gli utenti hanno ottenuto l'accesso nella gestione dei diritti](entitlement-management-reports.md)
