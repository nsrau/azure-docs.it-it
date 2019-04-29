---
title: File di inclusione
description: File di inclusione
services: azure-resource-manager
author: rockboyfor
manager: digimobile
ms.service: azure-resource-manager
ms.topic: include
origin.date: 02/16/2018
ms.date: 04/30/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: f77a5d482c3f8632a3d86bd8e027fbb4418168c3
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122908"
---
Si vuole che gli utenti dell'organizzazione dispongano del giusto livello di accesso a queste risorse. Non si vuole concedere un accesso illimitato agli utenti, ma si vuole comunque avere la certezza che tutti possano svolgere il proprio lavoro. Il controllo degli accessi in base al ruolo consente di stabilire quali utenti sono autorizzati a completare specifiche azioni in un determinato ambito. Un ruolo definisce un set di azioni consentite. Si assegna il ruolo a un ambito e quindi si specifica quali utenti appartengono a tale ruolo per l'ambito.

Quando si pianifica la strategia di controllo degli accessi, concedere agli utenti almeno il privilegio di completare la propria sessione di lavoro. La figura seguente mostra un modello consigliato per l'assegnazione del controllo degli accessi in base al ruolo.

![Scope](./media/resource-manager-governance-rbac/role-examples.png)

Sono disponibili tre ruoli che si applicano a tutte le risorse: Proprietario, Collaboratore e Lettore. Qualsiasi account assegnato al ruolo Proprietario deve essere controllato attentamente e usato di rado. Agli utenti che devono limitarsi a verificare lo stato delle soluzioni deve essere assegnato il ruolo Lettore.

Alla maggior parte degli utenti vengono concessi [ruoli correlati alle risorse](../articles/role-based-access-control/built-in-roles.md) o [ruoli personalizzati](../articles/role-based-access-control/custom-roles.md) a livello di gruppo di risorse o di sottoscrizione. Questi ruoli definiscono in modo preciso le azioni consentite. Assegnando agli utenti a questi ruoli si concede loro l'accesso richiesto senza assegnare un livello eccessivo di controllo. È possibile assegnare un account a più di un ruolo. All'utente corrispondente saranno concesse le autorizzazioni combinate di tali ruoli. La concessione dell'accesso a livello di risorse è spesso troppo restrittiva per gli utenti, ma potrebbe risultare utile per un processo automatizzato progettato per attività specifiche.

### <a name="who-can-assign-roles"></a>Utenti autorizzati all'assegnazione di ruoli

Per creare e rimuovere assegnazioni di ruoli, gli utenti devono disporre di accesso `Microsoft.Authorization/roleAssignments/*`. Questo accesso viene concesso tramite il ruolo Proprietario o Amministratore accessi utente.
<!--ms.date: 04/30/2018-->