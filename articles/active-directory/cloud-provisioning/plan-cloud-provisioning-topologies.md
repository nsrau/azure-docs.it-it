---
title: Topologie e scenari supportati per il provisioning del Cloud Azure AD Connect
description: Questo argomento descrive i prerequisiti e i requisiti hardware per il provisioning del cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 142974423816b07d754a5425017aedc3195e2f4e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793997"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Topologie e scenari supportati per il provisioning del Cloud Azure AD Connect
Questo articolo descrive diverse topologie locali e Azure Active Directory (Azure AD) che usano Azure AD Connect il provisioning cloud. Questo articolo include solo le configurazioni e gli scenari supportati.

> [!IMPORTANT]
> Microsoft non supporta la modifica o il funzionamento Azure AD Connect il provisioning del cloud al di fuori delle configurazioni o delle azioni che sono formalmente documentate. Una di queste configurazioni o azioni può causare uno stato incoerente o non supportato del provisioning del Azure AD Connect Cloud. Microsoft pertanto non offre il supporto tecnico per distribuzioni di questo tipo.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Aspetti da ricordare per tutti gli scenari e le topologie
Di seguito è riportato un elenco di informazioni da tenere presenti quando si seleziona una soluzione.

- Utenti e gruppi devono essere identificati in modo univoco in tutte le foreste
- La corrispondenza tra foreste non si verifica con il provisioning cloud
- Un utente o un gruppo deve essere rappresentato solo una volta in tutte le foreste
- L'ancoraggio di origine per gli oggetti viene scelto automaticamente.  USA ms-DS-ConsistencyGuid se presente; in caso contrario, viene usato ObjectGUID.
- Non è possibile modificare l'attributo usato per l'ancoraggio di origine.



## <a name="multi-forest-single-azure-ad-tenant"></a>Tenant a più foreste, Single Azure AD
![Topologia per un multi-foresta e un tenant singolo](media/plan-cloud-provisioning-topologies/multi-forest.png)

La topologia più comune è rappresentata da più insiemi di strutture di Active Directory, con uno o più domini, e un singolo tenant Azure AD.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Foresta esistente con Azure AD Connect, nuova foresta con provisioning nel cloud
![Topologia per una foresta singola e un tenant singolo](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

Questo scenario è simile allo scenario a più foreste, tuttavia questo implica un ambiente di Azure AD Connect esistente e quindi l'introduzione di una nuova foresta con Azure AD Connect il provisioning cloud.  Per un esempio di questo scenario [, vedere Esercitazione: una foresta esistente con un singolo tenant di Azure ad](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>Distribuzione pilota Azure AD Connect il provisioning cloud in una foresta di Active Directory ibrida esistente
![topologia per una foresta singola e un singolo tenant](media/plan-cloud-provisioning-topologies/migrate.png) lo scenario pilota prevede l'esistenza di Azure AD Connect e Azure AD Connect il provisioning cloud nella stessa foresta e l'ambito degli utenti e dei gruppi di conseguenza. Nota: un oggetto deve essere nell'ambito solo in uno degli strumenti. 

Per un esempio di questo scenario [, vedere Esercitazione: provisioning pilota Azure ad Connect Cloud in una foresta di Active Directory sincronizzata esistente](tutorial-pilot-aadc-aadccp.md)

## <a name="single-forest-single-azure-ad-tenant"></a>Foresta singola, tenant singolo di Azure AD
![Topologia per una foresta singola e un tenant singolo](media/plan-cloud-provisioning-topologies/single-forest.png)

La topologia più semplice è una singola foresta locale, con uno o più domini, e un singolo tenant Azure AD.  Per un esempio di questo scenario [, vedere Esercitazione: una singola foresta con un singolo tenant di Azure ad](tutorial-single-forest.md)

## <a name="next-steps"></a>Passaggi successivi 

- [Che cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è Azure AD Connect provisioning cloud?](what-is-cloud-provisioning.md)

