---
author: mattchenderson
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: mahender
ms.openlocfilehash: 3b282a99bb7f6f107717d9c265a46d285d03b849
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649097"
---
Un'identità gestita di Azure Active Directory (Azure AD) consente all'app di accedere facilmente ad altre risorse protette da Azure AD, come Azure Key Vault. L'identità viene gestita dalla piattaforma Azure e non è necessario eseguire il provisioning o ruotare alcun segreto. Per altre informazioni sulle identità gestite in Azure AD, vedere [Identità gestite per le risorse di Azure](../articles/active-directory/managed-identities-azure-resources/overview.md).

All'applicazione possono essere concessi due tipi di identità:

- Un'**identità assegnata dal sistema** viene associata all'applicazione e viene eliminata in caso di eliminazione dell'app. A un'app può essere associata una sola identità assegnata dal sistema.
- Un'**identità assegnata dall'utente** è una risorsa di Azure autonoma che può essere assegnata all'app. Un'app può avere più identità assegnate dall'utente.