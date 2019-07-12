---
title: Delegare le attività di gestione dei diritti di Azure AD (anteprima) - Azure Active Directory
description: Informazioni sui ruoli che è possibile assegnare a delegare le attività di gestione dei diritti di Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/10/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4c4933847a39a56084894e5bbd40e166e6b73b6
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798642"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>Delegare le attività di gestione dei diritti di Azure AD (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Per impostazione predefinita, gli amministratori globali e agli amministratori di utenti possono creare e gestire tutti gli aspetti della gestione dei diritti di Azure AD. Tuttavia, gli utenti di questi ruoli potrebbero non sapere a tutti gli scenari in cui sono necessari i pacchetti di accesso. Si tratta in genere gli utenti all'interno di reparti che sanno che hanno bisogno di collaborare.

Anziché concedere le autorizzazioni senza restrizioni a utenti non amministratori, è possibile concedere agli utenti le autorizzazioni che necessarie per svolgere le proprie e di evitare di creare in conflitto o diritti di accesso non appropriato minimi. Questo articolo descrive i ruoli che è possibile assegnare al delegato diverse attività di gestione dei diritti.

## <a name="delegate-example-for-departmental-adoption"></a>Esempio di delegato per l'adozione del reparto

Per comprendere il modo in cui è possibile delegare l'attività di gestione dei diritti, è utile prendere in considerazione un esempio. Si supponga che l'organizzazione dispone di cinque utenti seguenti:

| Utente | department | Note |
| --- | --- | --- |
| Alice | IT | Amministratore globale |
| Bob | Research | Bob è anche proprietario di un gruppo di ricerca |
| Chiara | Research |  |
| Dave | Marketing |  |
| Elisa | Marketing | ELISA è anche proprietario di un'applicazione di Marketing |

Ricerca e i reparti Marketing vuole usare la gestione dei diritti per i propri utenti. Alice non è ancora pronta per altri reparti usare la gestione dei diritti. Ecco un metodo che Alice è stato possibile delegare le attività per la ricerca e i reparti marketing.

1. Alice crea un nuovo gruppo per i creatori di catalogo, sicurezza di Azure AD e aggiunge Bob, Carol, Dave ed Elisa come membri di tale gruppo.

1. Alice utilizza le impostazioni di gestione dei diritti per aggiungere il gruppo al ruolo autori di catalogo.

1. Carol crea una **Research** del catalogo e aggiunge Bob come Comproprietario di tale catalogo. Bob aggiunge il gruppo di ricerca che è responsabile per il catalogo come una risorsa, in modo che può essere utilizzato in un pacchetto di accesso per la collaborazione research.

1. Dave crea una **Marketing** del catalogo e aggiunge Elisa come Comproprietario di tale catalogo. ELISA aggiunge l'applicazione di marketing che è la proprietaria al catalogo come una risorsa, in modo che può essere utilizzato in un pacchetto di accesso per la collaborazione di marketing.

Ora di ricerca e i reparti marketing possono utilizzare il diritto di gestione. Bob, Carol, Dave ed Elisa può creare e gestire i pacchetti di accesso nei rispettivi cataloghi.

![Esempio di delegato di gestione dei diritti](./media/entitlement-management-delegate/elm-delegate.png)

## <a name="entitlement-management-roles"></a>Ruoli di gestione dei diritti

Gestione Entitlement presenta i seguenti ruoli che sono specifici per la gestione dei diritti.

| Role | Descrizione |
| --- | --- |
| Creatore del catalogo | Creare e gestire cataloghi. In genere un amministratore IT che non è un amministratore globale o un proprietario della risorsa per una raccolta di risorse. La persona che crea automaticamente un catalogo diventa proprietario catalogo prima del catalogo e possa aggiungere proprietari catalogo aggiuntivi. |
| Proprietario catalogo | Modificare e gestire cataloghi esistenti. In genere un amministratore IT o i proprietari delle risorse o un utente che è designato il proprietario del catalogo. |
| Gestione di pacchetti di accesso | Modificare e gestire tutti i pacchetti di accesso esistenti all'interno di un catalogo. |

Inoltre, un richiedente di un pacchetto di accesso e un responsabile approvazione designato inoltre avere diritti, sebbene non costituiscano ruoli.
 
* Responsabile approvazione: Autorizzato da un criterio per approvare o rifiutare richieste di accesso ai pacchetti, tuttavia non possono modificare le definizioni del pacchetto di accesso.
* Richiedente: Autorizzato da un criterio di un pacchetto di accesso a richiedere tale pacchetto di accesso.

La tabella seguente elenca le attività che possono eseguire questi ruoli.

| Attività | Creatore del catalogo | Proprietario catalogo | Gestione di pacchetti di accesso | Responsabile approvazione |
| --- | :---: | :---: | :---: | :---: |
| [Crea un nuovo catalogo](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [Aggiungere una risorsa a un catalogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [Modifica di un catalogo](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [Eliminare un catalogo](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [Aggiungere un proprietario del catalogo o un package manager l'accesso a un catalogo](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [Creare un nuovo pacchetto di accesso in un catalogo](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Gestire i ruoli delle risorse in un pacchetto di accesso](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Specificare chi può richiedere un pacchetto di accesso](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Assegnare un utente direttamente a un pacchetto di accesso](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Visualizzazione con un'assegnazione a un pacchetto di accesso](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Visualizzare le richieste di un pacchetto accesso](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Visualizzare gli errori di recapito della richiesta](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Annulla una richiesta in sospeso](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Nascondere un pacchetto di accesso](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Eliminare un pacchetto di accesso](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Approvare una richiesta di accesso](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Ruoli necessari per aggiungere risorse a un catalogo

Un amministratore globale può aggiungere o rimuovere qualsiasi gruppo (gruppi di sicurezza cloud creato o cloud creato gruppi di Office 365), applicazione o sito di SharePoint Online in un catalogo. Un utente amministratore può aggiungere o rimuovere qualsiasi gruppo o applicazione in un catalogo.

Per gli utenti che non sono un amministratore globale o un amministratore degli utenti, per aggiungere gruppi, applicazioni o siti di SharePoint Online in un catalogo, l'utente deve avere *entrambi* i necessari entitlement proprietario catalogo e ruolo di Azure AD directory ruolo gestionale. Nella tabella seguente sono elencate le combinazioni di ruolo necessarie per aggiungere risorse a un catalogo. Per rimuovere le risorse da un catalogo, è necessario disporre degli stessi ruoli.

| Ruolo della directory di Azure AD | Ruolo di gestione dei diritti | Può aggiungere il gruppo di sicurezza | Può aggiungere il gruppo di Office 365 | Possono aggiungere app | Può aggiungere il sito di SharePoint Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Amministratore globale](../users-groups-roles/directory-assign-admin-roles.md) | n/d |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Amministratore utenti](../users-groups-roles/directory-assign-admin-roles.md) | n/d |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Amministratore di Intune](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario catalogo | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Amministratore di Exchange](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario catalogo |  | :heavy_check_mark: |  |  |
| [Amministratore del servizio Team](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario catalogo |  | :heavy_check_mark: |  |  |
| [Amministratore di SharePoint](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario catalogo |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Amministratore dell'applicazione](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario catalogo |  |  | :heavy_check_mark: |  |
| [Amministratore applicazione cloud](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario catalogo |  |  | :heavy_check_mark: |  |
| Utente | Proprietario catalogo | Solo se gruppo proprietario | Solo se gruppo proprietario | Solo se proprietario dell'app |  |

## <a name="add-a-catalog-creator"></a>Aggiungere un creatore di catalogo

Se si desidera delegare la creazione del catalogo, aggiungere utenti al ruolo di creatore del catalogo.  È possibile aggiungere singoli utenti, o per motivi di praticità è possibile aggiungere un gruppo, i cui membri sono in grado di creare cataloghi. Seguire questi passaggi per assegnare un utente al ruolo di creatore del catalogo.

**Ruolo prerequisito:** Amministratore globale o Amministratore utenti

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, nelle **gestione Entitlement** fare clic su **impostazioni**.

1. Fare clic su **Modifica**.

1. Nel **delegare la gestione dei diritti** fare clic su **aggiungere gli autori di catalogo** per selezionare gli utenti o gruppi che saranno i membri per questo ruolo di gestione dei diritti.

1. Fare clic su **Seleziona**.

1. Fare clic su **Save**.

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>Aggiungere un proprietario del catalogo o una gestione di pacchetti di accesso

Se si desidera delegare la gestione di un catalogo o accesso pacchetti nel catalogo, aggiungere gli utenti il proprietario del catalogo o i ruoli manager di accesso pacchetto. Utente che crea un catalogo diventa il proprietario del catalogo prima. Seguire questi passaggi per assegnare un utente al ruolo di manager di accesso pacchetto o il proprietario del catalogo.

**Ruolo prerequisito:** L'utente amministratore o proprietario catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **cataloghi** e quindi aprire il catalogo da aggiungere agli amministratori.

1. Nel menu a sinistra, fare clic su **ruoli e gli amministratori**.

1. Fare clic su **Aggiungi proprietari** oppure **aggiungere i gestori di pacchetti di accesso** per selezionare i membri per questi ruoli.

1. Fare clic su **seleziona** per aggiungere questi membri.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere responsabili approvazione](entitlement-management-access-package-edit.md#policy-request)
- [Aggiungere risorse a un catalogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
