---
title: Condividi il collegamento per richiedere un pacchetto di accesso in Azure AD gestione diritti (anteprima)-Azure Active Directory
description: Informazioni su come condividere il collegamento per richiedere un pacchetto di accesso in Azure Active Directory gestione dei diritti (anteprima).
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
ms.openlocfilehash: 91b98df1f93991d4781283bc38b02dc20d11268d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392332"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management-preview"></a>Condividi il collegamento per richiedere un pacchetto di accesso in Azure AD gestione dei diritti (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La maggior parte degli utenti nella directory può accedere al portale di accesso personale e visualizzare automaticamente un elenco di pacchetti di accesso che possono richiedere. Tuttavia, per gli utenti di partner commerciali esterni che non si trovano ancora nella directory, è necessario inviare loro un collegamento che possono usare per richiedere un pacchetto di accesso. 

Fino a quando il catalogo per il pacchetto di accesso è [abilitato per gli utenti esterni](entitlement-management-catalog-create.md) e si dispone di un [criterio per la directory dell'utente](entitlement-management-access-package-request-policy.md)esterno, l'utente esterno può usare il collegamento portale di accesso personale per richiedere il pacchetto di accesso.

## <a name="share-link-to-request-an-access-package"></a>Condividi il collegamento per richiedere un pacchetto di accesso

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Nella pagina Overview copiare il **collegamento My Access Portal**.

    ![Panoramica del pacchetto di accesso - Collegamento del portale di Accesso personale](./media/entitlement-management-shared/my-access-portal-link.png)

    È importante copiare l'intero collegamento del portale di accesso personale quando lo si invia a un partner aziendale interno. In questo modo si garantisce che il partner ottenga l'accesso al portale della directory per effettuare la richiesta. Il collegamento inizia con `myaccess`, include un hint di directory e termina con un ID pacchetto di accesso.

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Inviare un messaggio di posta elettronica o inviare il collegamento al partner aziendale esterno. Possono condividere il collegamento con i loro utenti per richiedere il pacchetto di accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Richiedere l'accesso a un pacchetto di accesso](entitlement-management-request-access.md)
- [Approva o rifiuta le richieste di accesso](entitlement-management-request-approve.md)