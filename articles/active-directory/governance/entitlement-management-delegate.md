---
title: Delega e ruoli nella gestione dei diritti - Azure ADDelegation and roles in entitlement management - Azure AD
description: Informazioni su come delegare la governance dell'accesso dagli amministratori IT ai responsabili di reparto e ai project manager in modo che possano gestire l'accesso da soli.
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
ms.openlocfilehash: 86d924860e97b15a0a4af46c5bc35b0e0050292b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261840"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Delega e ruoli nella gestione dei diritti di Azure ADDelegation and roles in Azure AD entitlement management

Per impostazione predefinita, gli amministratori globali e gli amministratori utente possono creare e gestire tutti gli aspetti della gestione dei diritti di Azure AD. Tuttavia, gli utenti in questi ruoli potrebbero non conoscere tutte le situazioni in cui sono necessari pacchetti di accesso. In genere sono gli utenti all'interno dei rispettivi reparti, team o progetti che sanno con chi stanno collaborando, utilizzando le risorse e per quanto tempo. Anziché concedere autorizzazioni senza restrizioni a utenti non amministratori, è possibile concedere agli utenti le autorizzazioni meno necessarie per eseguire il proprio lavoro ed evitare di creare diritti di accesso in conflitto o inappropriati.

Questo video offre una panoramica su come delegare la governance dell'accesso dall'amministratore IT agli utenti che non sono amministratori.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Esempio di delegato

Per comprendere come è possibile delegare la governance dell'accesso nella gestione dei diritti, è utile prendere in considerazione un esempio. Si supponga che l'organizzazione disponga dei seguenti responsabili e amministratori.

![Delegare dall'amministratore IT ai responsabili](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

In qualità di amministratore IT, Hana ha contatti in ogni reparto, ovvero Mamta in Marketing, Mark in Finance e Joe in Legal, responsabili delle risorse del proprio reparto e dei contenuti aziendali critici.

Con la gestione dei diritti, è possibile delegare la governance dell'accesso a questi non amministratori perché sono quelli che sanno quali utenti hanno bisogno di accedere, per quanto tempo e a quali risorse. Ciò garantisce che le persone giuste gestiscano l'accesso per i propri reparti.

Ecco un modo in cui Hana potrebbe delegare la governance di accesso ai dipartimenti di marketing, finanza e legale.

1. Hana crea un nuovo gruppo di sicurezza di Azure AD e aggiunge Mamta, Mark e Joe come membri del gruppo.

1. Hana aggiunge tale gruppo al ruolo di creatori del catalogo.

    Mamta, Mark e Joe possono ora creare cataloghi per i propri reparti, aggiungere risorse necessarie ai reparti ed eseguire ulteriori delega all'interno del catalogo.

    Si noti che Mamta, Mark e Joe non possono vedere i cataloghi dell'altro.

1. Mamta crea un catalogo **Marketing,** che è un contenitore di risorse.

1. Mamta aggiunge le risorse di proprietà del reparto marketing a questo catalogo.

1. Mamta può aggiungere altre persone del suo reparto come proprietari del catalogo per questo catalogo. Ciò consente di condividere le responsabilità di gestione del catalogo.

1. Mamta può inoltre delegare la creazione e la gestione dei pacchetti di accesso nel catalogo Marketing ai project manager del reparto Marketing. Può farlo assegnandoli al ruolo di gestore di pacchetti di accesso. Un gestore di pacchetti di accesso può creare e gestire i pacchetti di accesso. 

Nel diagramma seguente vengono illustrati i cataloghi con risorse per i reparti marketing, Finanza e Legale. Utilizzando questi cataloghi, i project manager possono creare pacchetti di accesso per i team o i progetti.

![Esempio di delegato per la gestione dei diritti](./media/entitlement-management-delegate/elm-delegate.png)

Dopo la delega, il reparto marketing potrebbe avere ruoli simili alla tabella seguente.

| Utente | Ruolo professionale | Ruolo di Azure AD | Ruolo di gestione dei diritti |
| --- | --- | --- | --- |
| Hana | Amministratore IT | Amministratore globale o Amministratore utenti |  |
| Mamta | Responsabile marketing | Utente | Creatore del catalogo e proprietario del catalogo |
| Bob | Responsabile del marketing | Utente | Proprietario del catalogo |
| Jessica | Responsabile del progetto di marketing | Utente | Accedere al gestore dei pacchettiAccess package manager |

## <a name="entitlement-management-roles"></a>Ruoli di gestione dei diritti

La gestione dei diritti ha i ruoli seguenti specifici per la gestione dei diritti.

| Ruolo di gestione dei diritti | Descrizione |
| --- | --- |
| Creatore del catalogo | Creare e gestire cataloghi. In genere un amministratore IT che non è un amministratore globale o un proprietario della risorsa per una raccolta di risorse. La persona che crea un catalogo diventa automaticamente il primo proprietario del catalogo e può aggiungere altri proprietari del catalogo. Un creatore di cataloghi non può gestire o visualizzare i cataloghi di cui non è proprietario e che non può aggiungere risorse di cui non è proprietario a un catalogo. Se l'autore del catalogo deve gestire un altro catalogo o aggiungere risorse di cui non è proprietario, può richiedere di essere un comproprietario di tale catalogo o risorsa. |
| Proprietario del catalogo | Modificare e gestire i cataloghi esistenti. In genere un amministratore IT o proprietari di risorse o un utente designato dal proprietario del catalogo. |
| Accedere al gestore dei pacchettiAccess package manager | Modificare e gestire tutti i pacchetti di accesso esistenti all'interno di un catalogo. |

Inoltre, un approvatore designato e un richiedente di un pacchetto di accesso dispongono anche di diritti, anche se non sono ruoli.

| Right | Descrizione |
| --- | --- |
| Responsabile approvazione | Autorizzato da un criterio per approvare o rifiutare le richieste di accesso ai pacchetti, anche se non possono modificare le definizioni dei pacchetti di accesso. |
| Richiedente | Autorizzato da un criterio di un pacchetto di accesso per richiedere il pacchetto di accesso. |

Nella tabella seguente sono elencate le attività che possono essere eseguite dai ruoli di gestione dei diritti.

| Attività | Amministrativi | Creatore del catalogo | Proprietario del catalogo | Accedere al gestore dei pacchettiAccess package manager |
| --- | :---: | :---: | :---: | :---: |
| [Delegare a un creatore del catalogo](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [Aggiungere un'organizzazione connessa](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |
| [Crea un nuovo catalogo](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Aggiungere una risorsa a un catalogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Aggiungere un proprietario del catalogo](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Modificare un catalogo](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Eliminare un catalogo](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Delegare a un gestore di pacchetti di accessoDelegate to an access package manager](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Rimuovere un gestore di pacchetti di accessoRemove an access package manager](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Creare un nuovo pacchetto di accesso in un catalogoCreate a new access package in a catalog](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [Modificare i ruoli delle risorse in un pacchetto di accessoChange resource roles in an access package](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Creare e modificare i criteri](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Assegnare direttamente un utente a un pacchetto di accesso](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Visualizzare chi ha un'assegnazione a un pacchetto di accesso](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Visualizzare le richieste di un pacchetto di accessoView an access package's requests](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Visualizzare gli errori di recapito di una richiesta](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Rielaborare una richiesta](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Annullare una richiesta in sospeso](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Nascondere un pacchetto di accesso](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Eliminare un pacchetto di accessoDelete an access package](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Ruoli necessari per aggiungere risorse a un catalogoRequired roles to add resources to a catalog

Un amministratore globale può aggiungere o rimuovere qualsiasi gruppo (gruppi di sicurezza creati nel cloud o gruppi di Office 365 creati nel cloud), un'applicazione o un sito di SharePoint Online in un catalogo. Un amministratore utente può aggiungere o rimuovere qualsiasi gruppo o applicazione in un catalogo.

Per un utente che non è un amministratore globale o un amministratore utente, per aggiungere gruppi, applicazioni o siti di SharePoint Online a un catalogo, tale utente deve disporre *sia* del ruolo di gestione dei diritti della directory di Azure AD richiesto che del ruolo di gestione dei diritti del proprietario del catalogo. Nella tabella seguente sono elencate le combinazioni di ruoli necessarie per aggiungere risorse a un catalogo. Per rimuovere risorse da un catalogo, è necessario disporre degli stessi ruoli.

| Ruolo della directory di Azure AD | Ruolo di gestione dei diritti | Può aggiungere un gruppo di sicurezza | È possibile aggiungere un gruppo di Office 365 | Può aggiungere app | Può aggiungere un sito di SharePoint Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Amministratore globale](../users-groups-roles/directory-assign-admin-roles.md) | n/d |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Amministratore utenti](../users-groups-roles/directory-assign-admin-roles.md) | n/d |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Amministratore di Intune](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario del catalogo | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Amministratore Exchange](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario del catalogo |  | :heavy_check_mark: |  |  |
| [Amministratore del servizio Team](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario del catalogo |  | :heavy_check_mark: |  |  |
| [Amministratore SharePoint](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario del catalogo |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Amministratore dell'applicazione](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario del catalogo |  |  | :heavy_check_mark: |  |
| [Amministratore delle applicazioni cloud](../users-groups-roles/directory-assign-admin-roles.md) | Proprietario del catalogo |  |  | :heavy_check_mark: |  |
| Utente | Proprietario del catalogo | Solo se il proprietario del gruppo | Solo se il proprietario del gruppo | Solo se il proprietario dell'app |  |

Per determinare il ruolo con privilegi minimi per un'attività, è anche possibile fare riferimento [ai ruoli di amministratore in base all'attività di amministrazione in Azure Active Directory.](../users-groups-roles/roles-delegate-by-task.md#entitlement-management)

## <a name="next-steps"></a>Passaggi successivi

- [Delegare la governance dell'accesso ai creatori del catalogo](entitlement-management-delegate-catalog.md)
- [Creare e gestire un catalogo di risorse](entitlement-management-catalog-create.md)
