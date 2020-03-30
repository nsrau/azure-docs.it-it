---
title: Nascondere o eliminare il pacchetto di accesso nella gestione dei diritti - Azure ADHide or delete access package in entitlement management - Azure AD
description: Informazioni su come nascondere o eliminare un pacchetto di accesso nella gestione dei diritti di Azure Active Directory.Learn how to hide or delete an access package in Azure Active Directory entitlement management.
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
ms.openlocfilehash: cf410db5a483fc4f5d9efc1a6bd5c6202694ef4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261996"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Nascondere o eliminare un pacchetto di accesso nella gestione dei diritti di Azure ADHide or delete an access package in Azure AD entitlement management

I pacchetti di Access sono individuabili per impostazione predefinita. Ciò significa che se un criterio consente a un utente di richiedere il pacchetto di accesso, visualizzerà automaticamente il pacchetto di accesso elencato nel portale My Access. Tuttavia, è possibile modificare l'impostazione **Nascosto** in modo che il pacchetto di accesso non sia elencato nel portale My Access dell'utente.

In questo articolo viene descritto come nascondere o eliminare un pacchetto di accesso.

## <a name="change-the-hidden-setting"></a>Modificare l'impostazione Nascosto

Seguire questi passaggi per modificare l'impostazione **Nascosto** per un pacchetto di accesso.

**Ruolo prerequisito:** Amministratore globale, Amministratore utente, Proprietario catalogo o Gestore pacchetti di Access

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Pacchetti di accesso** e quindi apri il pacchetto di accesso.

1. Nella pagina Panoramica fare clic su **Modifica**.

1. Impostare l'impostazione **Nascosto.**

    Se impostato su **No**, il pacchetto di accesso verrà elencato nel portale My Access dell'utente.

    Se impostato su **Sì**, il pacchetto di accesso non verrà elencato nel portale My Access dell'utente. L'unico modo in cui un utente può visualizzare il pacchetto di accesso è se dispone del collegamento diretto al **portale My Access** al pacchetto di accesso. Per ulteriori informazioni, consultate Collegamento Condividi per richiedere un pacchetto di [accesso.](entitlement-management-access-package-settings.md)

## <a name="delete-an-access-package"></a>Eliminare un pacchetto di accessoDelete an access package

Un pacchetto di accesso può essere eliminato solo se non dispone di assegnazioni utente attive. Seguire questi passaggi per eliminare un pacchetto di accesso.

**Ruolo prerequisito:** Amministratore globale, Amministratore utente, Proprietario catalogo o Gestore pacchetti di Access

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Pacchetti di accesso** e quindi apri il pacchetto di accesso.

1. Nel menu a sinistra, fai clic su **Assegnazioni** e rimuovi l'accesso per tutti gli utenti.

1. Nel menu a sinistra fare clic su **Panoramica** e quindi su **Elimina**.

1. Nel messaggio di eliminazione visualizzato fare clic su **Sì**.

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare, aggiungere e rimuovere assegnazioni per un pacchetto di accessoView, add, and remove assignments for an access package](entitlement-management-access-package-assignments.md)
- [Visualizzare report e log](entitlement-management-reports.md)
