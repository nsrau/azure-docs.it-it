---
title: Configurare Azure AD Privileged Identity Management | Documentazione Microsoft
description: Argomento che descrive Azure AD Privileged Identity Management e illustra come usare PIM per migliorare la sicurezza del cloud.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: c548ed2e-06e3-4eaf-a63d-0f02ee72da25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 827e3521be8918f4de00113fd9eaf4e01679cac5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Che cos'è Azure AD Privileged Identity Management?

Con Azure Active Directory (AD) Privileged Identity Management è possibile gestire, il controllo e monitorare l'accesso all'interno dell'organizzazione. È incluso l'accesso alle risorse in Azure AD, Risorse di Azure (Anteprima) e altri Servizi online Microsoft, ad esempio Office 365 o Microsoft Intune.

> [!NOTE]
> Quando si abilita Privileged Identity Management per il tenant, è necessaria una licenza valida per Azure AD Premium P2 o Enterprise Mobility + Security E5 a pagamento o di valutazione per ogni utente che riceve un vantaggio dal servizio o che interagisce con esso. Gli esempi includono gli utenti o gli utenti in un gruppo che sono:
>
>- Assegnati al ruolo Amministratore dei ruoli con privilegi 
>- Assegnati come idonei per altri ruoli della directory gestibili tramite PIM 
>- In grado di approvare o rifiutare le richieste in PIM 
>- Assegnati a un ruolo delle risorse di Azure con assegnazioni Just-In-Time o dirette (basate su tempo)  
>- Assegnati a una verifica di accesso
>
>Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

Le organizzazioni desiderano ridurre al minimo il numero di utenti che hanno accesso a informazioni o risorse protette per ridurre le probabilità che un utente malintenzionato acceda al sistema, o che un utente autorizzato abbia inavvertitamente effetto su una risorsa sensibile.  Tuttavia, gli utenti devono comunque eseguire operazioni con privilegi, nelle app di Azure AD, Azure, Office 365 o SaaS. Le organizzazioni possono concedere agli utenti l'accesso con privilegi alle risorse di Azure, quali le Sottoscrizioni e Azure AD. È necessario procedere con una supervisione di ciò che gli utenti fanno con i loro privilegi di amministratori. Azure AD Privileged Identity Management (Gestione identità con privilegi di Azure) consente di ridurre il rischio di diritti di accesso eccessivi, non necessari o usati in modo improprio.

Azure AD Privileged Identity Management consente all'azienda di eseguire le operazioni seguenti:

- Vedere a quali utenti vengono assegnati i ruoli con privilegi per gestire le risorse di Azure (Anteprima), nonché a quali utenti vengono assegnati i ruoli amministrativi in Azure AD
- Abilitare l'accesso come amministratore JIT su richiesta ai Microsoft Online Services, ad esempio Office 365 e, e alle risorse di Azure (Anteprima) delle sottoscrizioni, ai gruppi di risorse e alle singole risorse, ad esempio le Macchine Virtuali 
-   Visualizzare una cronologia dell'attivazione dell'amministratore, comprese le modifiche che gli amministratori hanno apportato alle risorse di Azure (Anteprima)
- Ottenere gli avvisi sulle modifiche apportate nelle assegnazioni di amministratore
- Richiedere l'approvazione per attivare i ruoli di amministratore con privilegi di Azure AD (Anteprima) 
- Esaminare l'appartenenza dei ruoli di amministratore e richiedere agli utenti di fornire una giustificazione per l'appartenenza continua

In Azure AD, Azure AD Privileged Identity Management è in grado di gestire gli utenti assegnati ai ruoli aziendali predefiniti di Azure Active Directory, ad esempio Amministratore globale. In Azure, Azure AD Privileged Identity Management è possibile gestire gli utenti e i gruppi assegnati tramite i ruoli di Azure RBAC, compresi il Proprietario o il Collaboratore.

## <a name="just-in-time-administrator-access"></a>Accesso amministratore just-in-time

In passato, era possibile assegnare un utente a un ruolo di amministratore tramite il portale di Azure, altri portali di Microsoft Online Services o i cmdlet di Azure AD in Windows PowerShell. Di conseguenza l'utente diventa **amministratore permanente**, sempre attivo nel ruolo assegnato. Azure AD Privileged Identity Management introduce il concetto di **amministratore idoneo**. Gli amministratori idonei devono essere utenti che necessitano dell'accesso con privilegi in modo occasionale, non con cadenza quotidiana, per tutto il giorno. Il ruolo è inattivo fino a quando l'utente che necessita dell'accesso non completa un processo di attivazione e diventa amministratore attivo per un periodo di tempo predeterminato. Sempre più organizzazioni scelgono di usare questo approccio per ridurre o eliminare l'"accesso di amministratore permanente" a ruoli con privilegi.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Abilitare Privileged Identity Management per la directory

È possibile iniziare a usare Azure AD Privileged Identity Management nel [portale di Azure](https://portal.azure.com/).

> [!NOTE]
> Per abilitare Azure AD Privileged Identity Management per una directory, è necessario essere un amministratore globale, ad esempio @yourdomain.com e non un account Microsoft, ad esempio @outlook.com.

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale della directory.
2. Se l'organizzazione ha più directory, selezionare il proprio nome utente nell'angolo superiore destro del portale di Azure. Selezionare la directory in cui si userà Azure AD Privileged Identity Management.
3. Selezionare **Tutti i servizi** e usare la casella di testo Filtro per cercare **Azure AD Privileged Identity Management**.
4. Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**. Verrà aperta l'applicazione Privileged Identity Management.

Se si è la prima persona a usare Azure AD Privileged Identity Management nella directory e si naviga nei ruoli della directory di Azure AD, una [procedura guidata relativa alla sicurezza](active-directory-privileged-identity-management-security-wizard.md) illustra nel dettaglio l'esperienza di assegnazione iniziale. Al termine della procedura guidata si diventa automaticamente primo **amministratore della sicurezza** e **amministratore dei ruoli con privilegi** della directory.

Per i ruoli di Azure AD, solo un utente che appartiene al ruolo di amministratore con privilegi di ruolo può gestire le assegnazioni per altri amministratori di Azure AD PIM. È possibile [consentire ad altri utenti di gestire ruoli della directory in PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md). Gli amministratori globali, gli amministratori della sicurezza e il Ruolo con autorizzazioni di lettura per la sicurezza possono visualizzare le assegnazioni ai ruoli di Azure AD in Azure AD PIM.
Per i ruoli di Azure RBAC, le assegnazioni per altri amministratori di Azure AD PIM possono essere gestite solo da un amministratore della sottoscrizione, da un proprietario della risorsa o da un amministratore dell'accesso dell'utente delle risorse.  Gli utenti che sono amministratori con privilegi di ruolo, gli amministratori della sicurezza o i Ruoli con autorizzazioni di lettura per la sicurezza non dispongono, per impostazione predefinita, dell'accesso per visualizzare le assegnazioni ai ruoli di Azure RBAC in Azure AD PIM.

## <a name="privileged-identity-management-overview-entry-point"></a>Panoramica di Privileged Identity Management (Punto di ingresso)

Azure AD Privileged Identity Management supporta l'amministrazione dei ruoli della directory di Azure AD e le risorse di Azure (Anteprima). La funzione di ruoli per le risorse di Azure differisce dai ruoli amministrativi in Azure AD. I ruoli delle risorse di Azure forniscono le autorizzazioni di granularità per la risorsa in cui sono assegnati e tutte le risorse subordinate nella gerarchia di risorse (nota come ereditarietà). [Altre informazioni su RBAC, gerarchia delle risorse ed ereditarietà](role-based-access-control-configure.md). È possibile amministrare PIM sia per i ruoli della directory di Azure AD che per le Risorse di Azure (Anteprima) accedendo al collegamento appropriato nella sezione Gestisci del menu di spostamento a sinistra del punto di ingresso della Panoramica di PIM.

PIM offre un comodo accesso per l'attivazione di ruoli, la visualizzazione delle attivazioni/richieste in sospeso, delle approvazioni in sospeso (per i ruoli della directory di Azure AD) e delle verifiche in sospeso in attesa della risposta dell'utente dalla sezione Attività nel menu di spostamento a sinistra.

Quando si accede a una delle voci del menu Attività dal punto di ingresso Panoramica, la visualizzazione risultante contiene i risultati per i ruoli della directory di Azure AD e per i ruoli delle risorse di Azure (Anteprima).

![Avvio rapido](./media/active-directory-privileged-identity-management-configure/quick-start.png)

Ruoli personali contiene un elenco delle assegnazioni a ruoli attivi e idonei dell'utente per i ruoli della directory di Azure AD e per i ruoli delle risorse di Azure (Anteprima). [Altre informazioni sull'attivazione di assegnazioni di ruolo idonee](active-directory-privileged-identity-management-how-to-activate-role.md).

L'attivazione dei ruoli per Risorse di Azure (Anteprima) introduce una nuova esperienza che consente ai membri di un ruolo idoneo di pianificare l'attivazione per una data/ora futura e selezionare una durata di attivazione specifica entro il valore massimo, consentito dagli amministratori.

![](./media/active-directory-privileged-identity-management-configure/activations.png)

Nel caso in cui un'attivazione pianificata non sia più necessaria, gli utenti possono annullare la richiesta in sospeso passando alle richieste in sospeso dal menu di spostamento a sinistra e scegliendo il pulsante Annulla in linea con la richiesta.

![Richieste in sospeso](./media/active-directory-privileged-identity-management-configure/pending-requests.png)

## <a name="privileged-identity-management-admin-dashboard"></a>Dashboard amministratore di Privileged Identity Management

In Azure AD Privileged Identity Management è disponibile un dashboard dell'amministratore che visualizza informazioni importanti, tra cui:

* Avvisi che segnalano opportunità di miglioramento della sicurezza
* Numero di utenti assegnati a ogni ruolo con privilegi  
* Numero di amministratori permanenti e idonei
* Un grafo di attivazioni del ruolo con privilegi nella directory
*   Il numero di JIT, del limite di tempo e delle assegnazioni permanenti per i ruoli delle risorse di Azure (Anteprima)
*   Utenti e gruppi con nuove assegnazioni di ruolo negli ultimi 30 giorni (Ruoli delle risorse di Azure)


![Dashboard PIM - Schermata][2]

## <a name="privileged-role-management"></a>Gestione dei ruoli con privilegi

Con Azure AD Privileged Identity Management è possibile gestire gli amministratori tramite l'aggiunta o la rimozione di amministratori permanenti o idonei a ogni ruolo per i ruoli della directory di Azure AD. Con PIM per le Risorse di Azure (Anteprima), i proprietari, gli amministratori dell'accesso utente e gli amministratori globali che consentono la gestione delle sottoscrizioni al proprio tenant possono assegnare utenti o gruppi ai ruoli delle risorse di Azure come ad esempio idoneo (accesso JIT), Limite di tempo (attivazione non necessaria) con una data/ora di inizio e di fine o permanente (se abilitato nelle impostazioni del ruolo).

![Aggiunta/Rimozione di amministratori PIM - Schermata][3]

## <a name="configure-the-role-activation-settings"></a>Configurare le impostazioni di attivazione del ruolo

L'uso delle [impostazioni del ruolo](active-directory-privileged-identity-management-how-to-change-default-settings.md) consente di configurare le proprietà di attivazione del ruolo idoneo per le regole della directory di Azure AD, comprese:

* Durata del periodo di attivazione del ruolo
* Notifica di attivazione del ruolo
* Informazioni che l'utente deve specificare durante il processo di attivazione del ruolo
* Ticket di servizio o numero dell'evento imprevisto
* [Requisiti del flusso di lavoro di approvazione - Anteprima](./privileged-identity-management/azure-ad-pim-approval-workflow.md)

![Impostazioni di PIM - Attivazione dell'amministratore - Schermata][4]

Nell'immagine notare che i pulsanti per **Multi-Factor Authentication** sono disabilitati. Per determinati ruoli con privilegi elevati, è necessaria l'autenticazione MFA per una protezione avanzata.

Le impostazioni di ruolo per i ruoli di risorse di Azure (Anteprima) consentono agli amministratori di configurare delle impostazioni JIT e dirette, tra cui:

- La possibilità di assegnare l'utente o i gruppi ai ruoli senza una data/ora di fine (assegnazione permanente)
- La durata predefinita di un'assegnazione (quando non permanente)
- La durata massima di attivazione (quando si attiva un membro del ruolo idoneo)
- Le informazioni che un utente ha bisogno di fornire durante l'attivazione del ruolo (assegnazioni JIT) o il processo di assegnazione (assegnazioni dirette)

![](./media/active-directory-privileged-identity-management-configure/role-settings-details.png)

## <a name="role-activation"></a>Attivazione del ruolo

Per [attivare un ruolo](active-directory-privileged-identity-management-how-to-activate-role.md), un amministratore idoneo richiede una "attivazione" con vincoli di tempo per il ruolo. È possibile richiedere l'attivazione usando l'opzione **Attiva il mio ruolo** in Gestione identità con privilegi di Azure AD.

Un amministratore che vuole attivare un ruolo deve inizializzare Azure AD Privileged Identity Management nel portale di Azure.

L'attivazione del ruolo è personalizzabile. Nelle impostazioni di PIM è possibile impostare la durata dell'attivazione e le informazioni che l'amministratore deve specificare per attivare il ruolo.

![Richiesta di attivazione del ruolo dell'amministratore PIM - Schermata][5]

## <a name="review-role-activity"></a>Verificare l'attività del ruolo

È possibile verificare l'uso dei ruoli con privilegi da parte di dipendenti e amministratori in due modi. La prima opzione consiste nell'uso della [Cronologia di controllo dei ruoli della directory](active-directory-privileged-identity-management-how-to-use-audit-log.md). La cronologia di controllo registra il rilevamento delle modifiche nelle assegnazioni di ruolo con privilegi, nella cronologia di attivazione del ruolo, e nelle modifiche alle impostazioni per i ruoli di risorse di Azure (Anteprima). 

![Cronologia di attivazione PIM - Schermata][6]

La seconda opzione consiste nel configurare [verifiche di accesso](active-directory-privileged-identity-management-how-to-start-security-review.md)regolari. Queste verifiche di accesso possono essere eseguite e assegnate da un revisore, ad esempio il responsabile del team, o dai dipendenti stessi. Questo è il modo migliore per monitorare gli utenti che necessitano ancora dell'accesso e gli utenti che non devono più accedere.

## <a name="azure-ad-pim-at-subscription-expiration"></a>Azure AD PIM alla scadenza della sottoscrizione

Un tenant deve avere una sottoscrizione di valutazione o a pagamento Azure AD Premium P2 (o EMS E5) nel proprio tenant prima di usare Azure AD PIM.  È anche necessario assegnare le licenze per gli amministratori del tenant.  In particolare, le licenze devono essere assegnate agli amministratori nei ruoli di Azure AD gestiti tramite Azure AD PIM, gli amministratori nei ruoli di Azure RBAC gestiti tramite Azure AD PIM e gli utenti senza privilegi di amministratore che eseguono verifiche di accesso.
Se l'azienda non rinnova Azure AD Premium P2 o la versione di valutazione scade, le funzionalità di Azure AD PIM non saranno più disponibili nel tenant, le assegnazioni di ruolo idonee verranno rimosse e gli utenti non saranno più in grado di attivare i ruoli. Per altre informazioni, vedere [Requisiti di sottoscrizione di Azure Active Directory Privileged Identity Management](./privileged-identity-management/subscription-requirements.md)

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Admin_Overview.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_Settings_w_Approval_Disabled.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png
