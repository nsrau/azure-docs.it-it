---
title: Nascondere o eliminare un pacchetto di accesso in Azure AD gestione dei diritti (anteprima)-Azure Active Directory
description: Informazioni su come nascondere o eliminare un pacchetto di accesso in Azure Active Directory gestione dei diritti (anteprima).
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
ms.openlocfilehash: ab6f3a86d8e88ee2d7465fd32bde7b7555f1f326
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389174"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management-preview"></a>Nascondere o eliminare un pacchetto di accesso in Azure AD gestione dei diritti (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Per impostazione predefinita, i pacchetti di Access sono individuabili. Ciò significa che se un criterio consente a un utente di richiedere il pacchetto di accesso, visualizzerà automaticamente il pacchetto di accesso elencato nel portale di accesso personale. Tuttavia, è possibile modificare l'impostazione **nascosta** in modo che il pacchetto di accesso non sia elencato nel portale di accesso personale dell'utente.

Questo articolo descrive come nascondere o eliminare un pacchetto di accesso.

## <a name="change-the-hidden-setting"></a>Modificare l'impostazione nascosta

Per modificare l'impostazione **nascosta** per un pacchetto di Access, attenersi alla seguente procedura.

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Nella pagina Overview fare clic su **Edit**.

1. Impostare l'impostazione **nascosta** .

    Se è impostato su **No**, il pacchetto di accesso verrà elencato nel portale di accesso personale dell'utente.

    Se è impostato su **Sì**, il pacchetto di accesso non verrà elencato nel portale di accesso personale dell'utente. L'unico modo in cui un utente può visualizzare il pacchetto di accesso è il **collegamento al portale** di accesso diretto al pacchetto di accesso. Per ulteriori informazioni, vedere [la pagina relativa alla condivisione del collegamento per richiedere un pacchetto di accesso](entitlement-management-access-package-settings.md).

## <a name="delete-an-access-package"></a>Eliminare un pacchetto di accesso

Un pacchetto di accesso può essere eliminato solo se non sono presenti assegnazioni utente attive. Per eliminare un pacchetto di accesso, seguire questa procedura.

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Nel menu a sinistra fare clic su **assegnazioni** e rimuovere l'accesso per tutti gli utenti.

1. Nel menu a sinistra fare clic su **Panoramica** e quindi su **Elimina**.

1. Nel messaggio di eliminazione visualizzato, fare clic su **Sì**.

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare, aggiungere e rimuovere assegnazioni per un pacchetto di accesso](entitlement-management-access-package-assignments.md)
- [Visualizzare report e log](entitlement-management-reports.md)
