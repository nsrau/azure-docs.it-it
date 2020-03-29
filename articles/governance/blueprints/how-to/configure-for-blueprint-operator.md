---
title: Configurazione dell'ambiente per l'operatore blueprint
description: Informazioni su come configurare l'ambiente Azure per l'uso con il ruolo di controllo degli accessi in base al ruolo incorporato dell'operatore Blueprint.
ms.date: 08/26/2019
ms.topic: how-to
ms.openlocfilehash: fba0dd3f2eeb69f768800d1d04640510462d3c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873217"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>Configurare l'ambiente per un operatore di Blueprint

La gestione delle definizioni dei blueprint e delle assegnazioni dei blueprint può essere assegnata a team diversi. È comune che un architetto o un team di governance sia responsabile della gestione del ciclo di vita delle definizioni del blueprint, mentre un team operativo è responsabile della gestione delle assegnazioni di tali definizioni di blueprint controllate centralmente.

Il controllo degli accessi in base al ruolo incorporato **dell'operatore Blueprint** è progettato specificamente per l'utilizzo in questo tipo di scenario. Il ruolo consente ai team di tipo operations di gestire l'assegnazione delle definizioni dei blueprint delle organizzazioni, ma non la possibilità di modificarle. Questa operazione richiede una configurazione nell'ambiente Azure e in questo articolo vengono illustrati i passaggi necessari.

## <a name="grant-permission-to-the-blueprint-operator"></a>Concedere l'autorizzazione all'operatore Blueprint

Il primo passaggio consiste nel concedere il ruolo **Diciatrice** blueprint all'account o al gruppo di sicurezza (scelta consigliata) che assegnerà i blueprint. Questa azione deve essere eseguita al livello più alto nella gerarchia dei gruppi di gestione che include tutti i gruppi di gestione e le sottoscrizioni a cui il team operativo deve disporre dell'accesso di assegnazione del blueprint. Si consiglia di seguire il principio dei privilegi minimi quando si concedono queste autorizzazioni.

1. (scelta consigliata) [Creare un gruppo di sicurezza e aggiungere membriCreate a security group and add members](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. [Aggiungere un'assegnazione](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) di ruolo di **Blueprint Operator** all'account o al gruppo di sicurezza

## <a name="user-assign-managed-identity"></a>Assegnare all'utente l'identità gestitaUser-assign managed identity

Una definizione di blueprint può utilizzare identità gestite assegnate dal sistema o dall'utente. Tuttavia, quando si utilizza il ruolo **Operatore blueprint,** la definizione del blueprint deve essere configurata per l'utilizzo di un'identità gestita assegnata dall'utente. Inoltre, all'account o al gruppo di sicurezza a cui viene concesso il ruolo **Blueprint Operator** deve essere concesso il ruolo Managed **Identity Operator** nell'identità gestita assegnata dall'utente. Senza questa autorizzazione, le assegnazioni dei blueprint non riescono a causa della mancanza di autorizzazioni.

1. [Creare un'identità gestita assegnata dall'utente](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) per l'utilizzo da parte di un blueprint assegnato

1. [Aggiungere un'assegnazione](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) di ruolo **Operatore identità gestita** all'account o al gruppo di sicurezza. Definire l'ambito dell'assegnazione di ruolo alla nuova identità gestita assegnata dall'utente.

1. In qualità di **Operatore blueprint**, [assegnare un blueprint](../create-blueprint-portal.md#assign-a-blueprint) che utilizza la nuova identità gestita assegnata dall'utente.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul ciclo di vita del [blueprint.](../concepts/lifecycle.md)
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).