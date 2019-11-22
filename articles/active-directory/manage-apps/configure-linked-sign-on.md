---
title: Accesso collegato per app Azure AD-piattaforma di identità Microsoft
description: Configurare l'Single Sign-On collegato (SSO) per le applicazioni aziendali Azure AD nella piattaforma di identità Microsoft (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c4547bddeea8b67bd3377124b1c299662fea3e6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274179"
---
# <a name="configure-linked-sign-on"></a>Configurare l'accesso collegato

Quando si aggiunge una raccolta o un'applicazione Web non raccolta, una delle opzioni di Single Sign-On disponibili è l' [accesso collegato](what-is-single-sign-on.md). Selezionare questa opzione per aggiungere un collegamento all'applicazione nel pannello di accesso Azure AD o nel portale di Office 365 dell'organizzazione. È possibile utilizzare questo metodo per aggiungere collegamenti alle applicazioni Web personalizzate che attualmente utilizzano Active Directory Federation Services (o un altro servizio federativo) anziché Azure AD per l'autenticazione. In alternativa, è possibile aggiungere collegamenti diretti a pagine specifiche di SharePoint o ad altre pagine Web da visualizzare nei riquadri di accesso dell'utente.

## <a name="before-you-begin"></a>Prima di iniziare

Se l'applicazione non è stata aggiunta al tenant di Azure AD, vedere [Aggiungere un'app della raccolta](add-gallery-app.md) oppure [Aggiungere un'app non della raccolta](add-non-gallery-app.md).

### <a name="open-the-app-and-select-linked-sign-on"></a>Aprire l'app e selezionare accesso collegato

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore dell’applicazione cloud o amministratore dell’applicazione per il proprio tenant di Azure AD.

1. Passare a **Azure Active Directory** > **applicazioni aziendali**. Viene visualizzato un esempio casuale delle applicazioni del tenant di Azure AD. 

1. Nel menu **Tipo di applicazione** selezionare **Tutte le applicazioni** e quindi **Applica**.

1. Immettere il nome dell'applicazione nella casella di ricerca, quindi selezionarla nei risultati.

1. Nella sezione **Gestisci** selezionare **Single Sign-On**. 

1. Selezionare **collegato**.

1. Immettere l'URL dell'applicazione a cui collegarsi. Digitare l'URL e selezionare **Salva**. 
 
1. È possibile assegnare utenti e gruppi all'applicazione, che fa sì che l'applicazione venga visualizzata nell' [utilità di avvio delle app di Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) o nel [pannello di accesso Azure ad](end-user-experiences.md) per tali utenti.

1. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare utenti o gruppi all'applicazione](methods-for-assigning-users-and-groups.md)
- [Configurare il provisioning automatico degli account utente](configure-automatic-user-provisioning-portal.md)
