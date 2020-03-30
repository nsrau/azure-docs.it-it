---
title: Topologie e scenari supportati per il provisioning cloud di Azure AD ConnectAzure AD Connect cloud provisioning supported topologies and scenarios
description: In questo argomento vengono descritti i prerequisiti e il provisioning cloud dei requisiti hardware.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 386af46bbee623d37bc914d2ee9130c914c6c885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620880"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Topologie e scenari supportati per il provisioning cloud di Azure AD ConnectAzure AD Connect cloud provisioning supported topologies and scenarios
Questo articolo descrive varie topologie locali e di Azure Active Directory (Azure AD) che usano il provisioning cloud di Azure AD Connect.This article describes various on-premises and Azure Active Directory (Azure AD) topologies that use Azure AD Connect cloud provisioning. Questo articolo include solo configurazioni e scenari supportati.

> [!IMPORTANT]
> Microsoft non supporta la modifica o il funzionamento del provisioning cloud di Azure AD Connect all'esterno delle configurazioni o delle azioni formalmente documentate. Ognuna di queste configurazioni o azioni potrebbe causare uno stato incoerente o non supportato del provisioning del cloud di Azure AD Connect.Any of these configurations or actions might result in an inconsistent or unsupported state of Azure AD Connect cloud provisioning. Microsoft pertanto non offre il supporto tecnico per distribuzioni di questo tipo.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Cose da ricordare su tutti gli scenari e le topologie
Di seguito è riportato un elenco di informazioni da tenere presente quando si seleziona una soluzione.

- Gli utenti e i gruppi devono essere identificati in modo univoco in tutte le foreste
- La corrispondenza tra foreste non viene eseguita con il provisioning cloudMatching across forests does not occur with cloud provisioning
- Un utente o un gruppo deve essere rappresentato una sola volta in tutte le foreste
- L'ancoraggio di origine per gli oggetti viene scelto automaticamente.  Utilizza ms-DS-ConsistencyGuid se presente, in caso contrario viene utilizzato ObjectGUID.
- Non è possibile modificare l'attributo utilizzato per l'ancoraggio di origine.

## <a name="single-forest-single-azure-ad-tenant"></a>Foresta singola, tenant singolo di Azure AD
![Topologia per una foresta singola e un tenant singolo](media/plan-cloud-provisioning-topologies/single-forest.png)

La topologia più semplice è una singola foresta locale, con uno o più domini e un singolo tenant di Azure AD.  Per un esempio di questo scenario vedere [Esercitazione: una singola foresta con un singolo tenant](tutorial-single-forest.md) di Azure ADFor an example of this scenario see Tutorial: A single forest with a single Azure AD tenant


## <a name="multi-forest-single-azure-ad-tenant"></a>Tenant di Azure AD a più foreste e singoloMulti-forest, single Azure AD tenant
![Topologia per più foreste e un singolo tenant](media/plan-cloud-provisioning-topologies/multi-forest.png)

Una topologia comune è costituita da più foreste di Active Directory, con uno o più domini e un singolo tenant di Azure AD.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Foresta esistente con Azure AD Connect, nuova foresta con provisioning cloud
![Topologia per una foresta singola e un tenant singolo](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

Questo scenario è simile allo scenario a più foreste, tuttavia questo coinvolge un ambiente Azure AD Connect esistente e quindi porta su una nuova foresta usando il provisioning cloud di Azure AD Connect.This scenario is topology is similar to the multi-forest scenario, however this involves an existing Azure AD Connect environment and then bringing on a new forest using Azure AD Connect cloud provisioning.  Per un esempio di questo scenario vedere [Esercitazione: una foresta esistente con un singolo tenant](tutorial-existing-forest.md) di Azure ADFor an example of this scenario see Tutorial: An existing forest with a single Azure AD tenant

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>Pilotare il provisioning cloud di Azure AD Connect in una foresta ibrida di Active Directory esistente
![Topologia per una singola foresta](media/plan-cloud-provisioning-topologies/migrate.png) e un singolo tenant Lo scenario di pilotaggio prevede l'esistenza di azure AD Connect e Azure AD Connect cloud provisioning nella stessa foresta e l'ambito di utenti e gruppi di conseguenza. NOTA: un oggetto deve essere nell'ambito solo in uno degli strumenti. 

Per un esempio di questo scenario vedere Esercitazione: Eseguire il provisioning del cloud pilota di [Azure AD Connect in una foresta di Active Directory sincronizzata esistenteFor](tutorial-pilot-aadc-aadccp.md) an example of this scenario see Tutorial: Pilot Azure AD Connect cloud provisioning in an existing synced AD forest



## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)

