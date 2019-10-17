---
title: Delega e ruoli in Azure AD gestione dei diritti (anteprima)-Azure Active Directory
description: Informazioni su come delegare la governance degli accessi dagli amministratori IT ai responsabili del reparto e ai responsabili di progetto in modo che possano gestire l'accesso.
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
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d44a4265c3729bff3d983395a37a6cb64a463d4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389125"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management-preview"></a>Delega e ruoli in Azure AD gestione dei diritti (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Per impostazione predefinita, gli amministratori globali e gli amministratori utenti possono creare e gestire tutti gli aspetti della gestione dei diritti Azure AD. Tuttavia, gli utenti di questi ruoli potrebbero non essere a conoscenza di tutte le situazioni in cui sono necessari i pacchetti di accesso. Si tratta in genere di utenti all'interno dei rispettivi reparti, team o progetti che conoscono la collaborazione con, usando le risorse e per quanto tempo. Anziché concedere autorizzazioni senza restrizioni a utenti non amministratori, è possibile concedere agli utenti le autorizzazioni minime necessarie per eseguire il proprio lavoro ed evitare la creazione di diritti di accesso in conflitto o non appropriati.

In questo video viene fornita una panoramica su come delegare la governance di accesso dall'amministratore IT agli utenti che non sono amministratori.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Esempio di delegato

Per comprendere come è possibile delegare la governance di accesso alla gestione dei diritti, è utile prendere in considerazione un esempio. Si supponga che l'organizzazione disponga dei seguenti amministratori e responsabili.

![Delega da amministratore IT ai responsabili](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

In qualità di amministratore IT, Hana ha contattato in ogni reparto, ovvero la muratura in marketing, Mark in Finance e Joe in Legal, responsabile delle risorse del reparto e dei contenuti aziendali critici.

Con la gestione dei diritti, è possibile delegare la governance di accesso a questi non amministratori perché sono quelli che conoscono quali utenti devono accedere, per quanto tempo e per quali risorse. In questo modo si garantisce che gli utenti giusti gestiscano l'accesso ai propri reparti.

Ecco un modo in cui Hana può delegare la governance degli accessi ai reparti marketing, finanza e legale.

1. Hana consente di creare un nuovo gruppo di sicurezza Azure AD e di aggiungere l'oggetto, Mark e Joe come membri del gruppo.

1. Hana aggiunge tale gruppo al ruolo creatori del catalogo.

    È ora possibile creare cataloghi per i reparti, aggiungere le risorse necessarie ai propri reparti ed eseguire ulteriori deleghe all'interno del catalogo.

    Si noti che non è possibile visualizzare i cataloghi di tutti gli altri.

1. Per creare un catalogo **Marketing** , che è un contenitore di risorse,

1. Con la proprietà del reparto marketing è possibile aggiungere le risorse a questo catalogo.

1. È possibile aggiungere altri utenti del reparto come proprietari del catalogo per questo catalogo. Ciò consente di condividere le responsabilità di gestione del catalogo.

1. È possibile delegare ulteriormente la creazione e la gestione dei pacchetti di accesso nel catalogo marketing ai Project Manager del reparto marketing. A tale scopo, è possibile assegnarli al ruolo Gestione pacchetti di accesso. Una gestione pacchetti di accesso può creare e gestire i pacchetti di accesso. 

Il diagramma seguente mostra i cataloghi con risorse per gli uffici marketing, Finance e Legal. Usando questi cataloghi, i Project Manager possono creare pacchetti di accesso per i team o i progetti.

![Esempio di delegati di gestione dei diritti](./media/entitlement-management-delegate/elm-delegate.png)

Dopo la delega, il reparto marketing potrebbe avere ruoli simili alla tabella seguente.

| Utente | Posizione lavorativa | Ruolo Azure AD | Ruolo di gestione dei diritti |
| --- | --- | --- | --- |
| Hana | Amministratore IT | Amministratore globale o Amministratore utenti |  |
| Muja | Responsabile marketing | Utente | Autore del catalogo e proprietario del catalogo |
| Bob | Responsabile marketing | Utente | Proprietario Catalogo |
| Jessica | project manager marketing | Utente | Gestione pacchetti di Access |

## <a name="entitlement-management-roles"></a>Ruoli di gestione dei diritti

La gestione dei diritti ha i seguenti ruoli specifici per la gestione dei diritti.

| Ruolo di gestione dei diritti | Description |
| --- | --- |
| Creatore del catalogo | Creare e gestire cataloghi. In genere un amministratore IT che non è un amministratore globale o un proprietario di risorse per una raccolta di risorse. La persona che crea un catalogo diventa automaticamente il primo proprietario del catalogo e può aggiungere altri proprietari del catalogo. Un autore del catalogo non può gestire o vedere cataloghi di cui non è proprietario e non può aggiungere risorse di cui non è proprietario per un catalogo. Se l'autore del catalogo deve gestire un altro catalogo o aggiungere risorse di cui non è proprietario, può richiedere di essere un comproprietario di tale catalogo o risorsa. |
| Proprietario Catalogo | Modificare e gestire cataloghi esistenti. In genere un amministratore IT o proprietari di risorse oppure un utente designato dal proprietario del catalogo. |
| Gestione pacchetti di Access | Modificare e gestire tutti i pacchetti di accesso esistenti all'interno di un catalogo. |

Inoltre, un responsabile approvazione designato e un richiedente di un pacchetto di accesso dispongono anche di diritti, anche se non sono ruoli.

| Right | Description |
| --- | --- |
| Responsabile approvazione | Autorizzazione eseguita da un criterio per approvare o negare le richieste di accesso ai pacchetti, sebbene non possano modificare le definizioni dei pacchetti di accesso. |
| Richiedente | Autorizzazione eseguita da un criterio di un pacchetto di accesso per richiedere il pacchetto di accesso. |

Nella tabella seguente sono elencate le attività che i ruoli di gestione dei diritti possono eseguire.

| Attività | Admin | Creatore del catalogo | Proprietario Catalogo | Gestione pacchetti di Access |
| --- | :---: | :---: | :---: | :---: |
| [Delega a un creatore del catalogo](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [Crea un nuovo catalogo](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Aggiungere una risorsa a un catalogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Aggiungere un proprietario del catalogo](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Modificare un catalogo](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Eliminare un catalogo](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Delega a una gestione pacchetti di Access](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Rimuovere una gestione pacchetti di accesso](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Creare un nuovo pacchetto di accesso in un catalogo](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [Modificare i ruoli delle risorse in un pacchetto di accesso](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Creazione e modifica di criteri](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Assegnare direttamente un utente a un pacchetto di accesso](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Visualizza chi ha un'assegnazione a un pacchetto di accesso](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Visualizzare le richieste di un pacchetto di accesso](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Visualizzare gli errori di recapito di una richiesta](entitlement-management-access-package-requests.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Annulla una richiesta in sospeso](entitlement-management-access-package-requests.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Nascondi un pacchetto di accesso](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Eliminare un pacchetto di accesso](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Ruoli necessari per aggiungere risorse a un catalogo

Un amministratore globale può aggiungere o rimuovere qualsiasi gruppo (gruppi di sicurezza creati dal cloud o gruppi di Office 365 creati dal cloud), un'applicazione o un sito di SharePoint online in un catalogo. Un amministratore utente può aggiungere o rimuovere qualsiasi gruppo o applicazione in un catalogo.

Per un utente che non è un amministratore globale o un amministratore di utenti, per aggiungere gruppi, applicazioni o siti di SharePoint Online a un catalogo, tale utente deve avere *sia* il ruolo della directory Azure ad necessario che il ruolo di gestione dei diritti del proprietario del catalogo. Nella tabella seguente sono elencate le combinazioni di ruoli necessarie per aggiungere risorse a un catalogo. Per rimuovere le risorse da un catalogo, è necessario avere gli stessi ruoli.

| Ruolo della directory di Azure AD | Ruolo di gestione dei diritti | È possibile aggiungere un gruppo di sicurezza | È possibile aggiungere il gruppo Office 365 | È possibile aggiungere l'app | È possibile aggiungere il sito di SharePoint Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Amministratore globale](../users-groups-roles/directory-assign-admin-roles.md) | N/D |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Amministratore utenti](../users-groups-roles/directory-assign-admin-roles.md) | N/D |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Amministratore di Intune](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario Catalogo | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Amministratore di Exchange](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario Catalogo |  | :heavy_check_mark: |  |  |
| [Amministratore del servizio Teams](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario Catalogo |  | :heavy_check_mark: |  |  |
| [Amministratore di SharePoint](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario Catalogo |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Amministratore dell'applicazione](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario Catalogo |  |  | :heavy_check_mark: |  |
| [Amministratore applicazione cloud](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario Catalogo |  |  | :heavy_check_mark: |  |
| Utente | Proprietario Catalogo | Solo se il proprietario del gruppo | Solo se il proprietario del gruppo | Solo se il proprietario dell'app |  |

Per determinare il ruolo con privilegi minimi per un'attività, è anche possibile fare riferimento ai [ruoli di amministratore per attività di amministrazione in Azure Active Directory](../users-groups-roles/roles-delegate-by-task.md#entitlement-management).

## <a name="next-steps"></a>Passaggi successivi

- [Delega della governance di accesso ai creatori di cataloghi](entitlement-management-delegate-catalog.md)
- [Creare e gestire un catalogo di risorse](entitlement-management-catalog-create.md)
