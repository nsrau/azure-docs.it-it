---
title: "Avvio rapido: Eliminare un'applicazione dal tenant di Azure Active Directory (Azure AD)"
description: In questo argomento di avvio rapido si usa il portale di Azure per eliminare un'applicazione nel tenant di Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec0b3ef559abe1c65872d8ecf87f63e6ff3ed4b0
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223908"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Avvio rapido: Eliminare un'applicazione dal tenant di Azure Active Directory (Azure AD)

In questo argomento di avvio rapido si usa il portale di Azure per eliminare un'applicazione aggiunta al tenant di Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Prerequisiti

Per eliminare un'applicazione dal tenant di Azure AD, sono necessari:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno dei ruoli seguenti: amministratore globale, amministratore di applicazioni cloud o amministratore di applicazioni oppure proprietario dell'entità servizio.
- Facoltativo: completamento di [Visualizzare le app](view-applications-portal.md).
- Facoltativo: completamento di [Aggiungere un'app](add-application-portal.md).
- Facoltativo: completamento di [Configurare un'app](add-application-portal-configure.md).
- Facoltativo: completamento di [Configurare Single Sign-On](add-application-portal-setup-sso.md).

>[!IMPORTANT]
>Per testare i passaggi di questo argomento di avvio rapido, usare un ambiente non di produzione.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Eliminare un'applicazione dal tenant di Azure AD

Per eliminare un'applicazione dal tenant di Azure AD:

1. Nel portale di Azure AD selezionare **Applicazioni aziendali**. Quindi trovare e selezionare l'applicazione da eliminare. In questo caso è stata eliminata l'applicazione **GitHub_test** aggiunta nell'argomento di avvio rapido precedente.
1. Nella sezione **Gestisci** nel riquadro sinistro selezionare **Proprietà**.
1. Selezionare **Elimina** e quindi fare clic su **Sì** per confermare che si vuole eliminare l'app dal tenant di Azure AD.


## <a name="next-steps"></a>Passaggi successivi

- [Procedure consigliate per la gestione delle applicazioni](application-management-fundamentals.md)
- [Scenari comuni di gestione delle applicazioni](common-scenarios.md)
- [Visibilità e controllo della gestione delle applicazioni](cloud-app-security.md)