---
title: Creare un nuovo pacchetto di accesso nella gestione dei diritti - Azure ADCreate a new access package in entitlement management - Azure AD
description: Informazioni su come creare un nuovo pacchetto di accesso di risorse da condividere nella gestione dei diritti di Azure Active Directory.Learn how to create a new access package of resources you want to share in Azure Active Directory entitlement management.
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
ms.openlocfilehash: 7c858a17d4574e6e45283df7c1276cd303f25297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262009"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Creare un nuovo pacchetto di accesso nella gestione dei diritti di Azure ADCreate a new access package in Azure AD entitlement management

Un pacchetto di accesso consente di eseguire un'impostazione una tantera di risorse e criteri che amministra automaticamente l'accesso per tutta la durata del pacchetto di accesso. In questo articolo viene descritto come creare un nuovo pacchetto di accesso.

## <a name="overview"></a>Panoramica

Tutti i pacchetti di accesso devono essere inseriti in un contenitore denominato catalogo. Un catalogo definisce le risorse che è possibile aggiungere al pacchetto di accesso. Se non si specifica un catalogo, il pacchetto di accesso verrà inserito nel catalogo Generale. Attualmente, non è possibile spostare un pacchetto di accesso esistente in un catalogo diverso.

Se si è un gestore di pacchetti di accesso, non è possibile aggiungere risorse di cui si è proprietari a un catalogo. L'utilizzo delle risorse disponibili nel catalogo è limitato all'utilizzo delle risorse disponibili. Se è necessario aggiungere risorse a un catalogo, è possibile rivolgersi al proprietario del catalogo.

Tutti i pacchetti di accesso devono avere almeno un criterio. I criteri specificano chi può richiedere il pacchetto di accesso e anche le impostazioni di approvazione e ciclo di vita. Quando si crea un nuovo pacchetto di accesso, è possibile creare un criterio iniziale per gli utenti nella directory, per gli utenti non nella directory, solo per le assegnazioni dirette dell'amministratore oppure è possibile scegliere di creare il criterio in un secondo momento.

![Creare un pacchetto di accesso](./media/entitlement-management-access-package-create/access-package-create.png)

Ecco i passaggi di alto livello per creare un nuovo pacchetto di accesso.

1. In Identity Governance avviare il processo per creare un nuovo pacchetto di accesso.

1. Selezionare il catalogo in cui si desidera creare il pacchetto di accesso.

1. Aggiungere risorse dal catalogo al pacchetto di accesso.

1. Assegnare ruoli delle risorse per ogni risorsa.

1. Specificare gli utenti che possono richiedere l'accesso.

1. Specificare le impostazioni di approvazione.

1. Specificare le impostazioni del ciclo di vita.

## <a name="start-new-access-package"></a>Avviare un nuovo pacchetto di accesso

**Ruolo prerequisito:** Amministratore globale, Amministratore utente, Proprietario catalogo o Gestore pacchetti di Access

1. Accedere al [portale](https://portal.azure.com)di Azure .

1. Fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Pacchetti di accesso**.

1. Fare clic su **Nuovo pacchetto di accesso**.
   
    ![Gestione entitlement nel portale di Azure](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Nozioni di base

Nella scheda **Nozioni di base assegnare** un nome al pacchetto di accesso e specificare in quale catalogo creare il pacchetto di accesso.

1. Immettere un nome visualizzato e una descrizione per il pacchetto di accesso. Gli utenti vedranno queste informazioni quando inviano una richiesta per il pacchetto di accesso.

1. Nell'elenco a discesa **Catalogo** selezionare il catalogo in cui si desidera creare il pacchetto di accesso. Ad esempio, potresti avere un proprietario del catalogo che gestisce tutte le risorse di marketing che possono essere richieste. In questo caso, è possibile selezionare il catalogo marketing.

    Verranno visualizzati solo i cataloghi in cui si dispone dell'autorizzazione per creare pacchetti di accesso. Per creare un pacchetto di accesso in un catalogo esistente, è necessario essere un amministratore globale o un amministratore utente oppure essere un proprietario del catalogo o un gestore di pacchetti di accesso in tale catalogo.

    ![Pacchetto di accesso - Nozioni di base](./media/entitlement-management-access-package-create/basics.png)

    Se si è un amministratore globale, un amministratore utente o un creatore del catalogo e si desidera creare il pacchetto di accesso in un nuovo catalogo non elencato, fare clic su **Crea nuovo catalogo**. Immettere il nome e la descrizione del catalogo, quindi fare clic su **Crea**.

    Il pacchetto di accesso che si sta creando e tutte le risorse incluse in esso verranno aggiunte al nuovo catalogo. È inoltre possibile aggiungere altri proprietari di cataloghi in un secondo momento.

1. Fare clic su **Avanti**.

## <a name="resource-roles"></a>Ruoli delle risorse

Nella scheda **Ruoli risorsa** selezionare le risorse da includere nel pacchetto di accesso. Gli utenti che richiedono e ricevono il pacchetto di accesso riceveranno tutti i ruoli delle risorse nel pacchetto di accesso.

1. Fare clic sul tipo di risorsa che si desidera aggiungere (**Gruppi e team**, **Applicazioni**o Siti **di SharePoint**).

1. Nel riquadro Seleziona visualizzato selezionare una o più risorse dall'elenco.

    ![Pacchetto di accesso - Ruoli delle risorseAccess package - Resource roles](./media/entitlement-management-access-package-create/resource-roles.png)

    Se si crea il pacchetto di accesso nel catalogo Generale o in un nuovo catalogo, sarà possibile selezionare qualsiasi risorsa dalla directory di cui si è proprietari. È necessario essere almeno un amministratore globale, un amministratore utente o un autore del catalogo.

    Se si crea il pacchetto di accesso in un catalogo esistente, è possibile selezionare qualsiasi risorsa già presente nel catalogo senza possederla.

    Se si è un amministratore globale, un amministratore utente o il proprietario del catalogo, è possibile selezionare le risorse di cui si è proprietari che non sono ancora presenti nel catalogo. Se si selezionano risorse non attualmente presenti nel catalogo selezionato, queste risorse verranno aggiunte al catalogo anche per gli altri amministratori del catalogo con cui creare pacchetti di accesso. Se si desidera selezionare solo le risorse attualmente presenti nel catalogo selezionato, selezionare la casella di controllo **Visualizza solo** nella parte superiore del riquadro Seleziona.

1. Dopo aver selezionato le risorse, nell'elenco **Ruolo** selezionare il ruolo che si desidera assegnare agli utenti per la risorsa.

    ![Pacchetto di accesso - Selezione ruolo risorsaAccess package - Resource role selection](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Fare clic su **Avanti**.

## <a name="requests"></a>Requests

Nella scheda **Richieste** è possibile creare il primo criterio per specificare chi può richiedere il pacchetto di accesso e anche le impostazioni di approvazione. Successivamente, è possibile creare più criteri di richiesta per consentire ad altri gruppi di utenti di richiedere il pacchetto di accesso con le proprie impostazioni di approvazione.

![Pacchetto di accesso - scheda Richieste](./media/entitlement-management-access-package-create/requests.png)

A seconda di chi si desidera poter richiedere questo pacchetto di accesso, eseguire i passaggi in una delle sezioni seguenti.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Rivedi e crea

Nella scheda **Revisione e creazione** è possibile esaminare le impostazioni e verificare la presenza di eventuali errori di convalida.

1. Rivedere le impostazioni del pacchetto di accesso

    ![Pacchetto di accesso - Criterio- Abilita impostazione dei criteri](./media/entitlement-management-access-package-create/review-create.png)

1. Fare clic su **Crea** per creare il pacchetto di accesso.

    Il nuovo pacchetto di accesso viene visualizzato nell'elenco dei pacchetti di accesso.

## <a name="creating-an-access-package-programmatically"></a>Creazione di un pacchetto di accesso a livello di codiceCreating an access package programmatically

È anche possibile creare un pacchetto di accesso usando Microsoft Graph.You can also create an access package using Microsoft Graph.  Un utente con un ruolo appropriato con `EntitlementManagement.ReadWrite.All` un'applicazione con l'autorizzazione delegata può chiamare l'API per

1. [Elencare accessPackageResources nel catalogo](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresources?view=graph-rest-beta) e [creare un accessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta) per tutte le risorse non ancora presenti nel catalogo.
1. [Elencare accessPackageResourceRoles](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresourceroles?view=graph-rest-beta) di ogni accessPackageResource in un accessPackageCatalog.List the accessPackageResourceRoles of each accessPackageResource in an accessPackageCatalog. Questo elenco di ruoli verrà quindi utilizzato per selezionare un ruolo, quando successivamente si crea un accessPackageResourceRoleScope.This list of roles will then be used to select a role, when subsequently creating an accessPackageResourceRoleScope.
1. [Creare un oggetto accessPackage](https://docs.microsoft.com/graph/api/accesspackage-post?view=graph-rest-beta).
1. [Creare un oggetto accessPackageAssignmentPolicy](https://docs.microsoft.com/graph/api/accesspackageassignmentpolicy-post?view=graph-rest-beta).
1. [Creare un accessPackageResourceRoleScope](https://docs.microsoft.com/graph/api/accesspackage-post-accesspackageresourcerolescopes?view=graph-rest-beta) per ogni ruolo di risorsa necessario nel pacchetto di accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Condividi link per richiedere un pacchetto di accesso](entitlement-management-access-package-settings.md)
- [Modificare i ruoli delle risorse per un pacchetto di accessoChange resource roles for an access package](entitlement-management-access-package-resources.md)
- [Assegnare direttamente un utente al pacchetto di accesso](entitlement-management-access-package-assignments.md)
