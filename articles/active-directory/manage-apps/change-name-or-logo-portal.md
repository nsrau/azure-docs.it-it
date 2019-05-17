---
title: Modificare il nome o il logo di un'applicazione aziendale in Azure Active Directory | Microsoft Docs
description: Come modificare il nome o il logo per un'applicazione aziendale personalizzata in Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30da8d6843c27c42d4d99adef50b9ad98a131c95
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780914"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Modificare il nome o il logo di un'applicazione aziendale in Azure Active Directory

Modificare il nome o il logo per un'applicazione aziendale personalizzata in Azure Active Directory (Azure AD) è un'operazione facile. È necessario disporre delle autorizzazioni appropriate per apportare queste modifiche e deve essere l'autore dell'applicazione personalizzata.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Come modificare nome o il logo di un'applicazione aziendale?

1. Accedi per il [portale di Azure Active Directory](https://aad.portal.azure.com/) con un account che sia un amministratore globale della directory. Il **interfaccia di amministrazione di Azure Active Directory** verrà visualizzata la pagina.
2. Nel riquadro sinistro selezionare **Applicazioni aziendali**. Verrà visualizzato l'elenco delle applicazioni aziendali.
3. Selezionare un'applicazione. Viene visualizzata la pagina di panoramica dell'applicazione.
4. Nel riquadro della panoramica dell'applicazione, sotto il **Manage** titolo, selezionare **proprietà**. Il **proprietà** verrà visualizzata la pagina.
5. Se si desidera modificare il nome, selezionare la **Name** casella, digitare il nuovo nome e quindi premere **invio**.
6. Se si desidera modificare il logo, trovare il **Logo** campo e selezionare l'icona della cartella accanto al **selezionare un file** casella che si trova sotto l'immagine del logo dell'applicazione corrente.

   ![Selezione del comando Proprietà](./media/change-name-or-logo-portal/change-logo.png)

   In caso contrario, se si non modifica il logo, andare al passaggio 8.
7. Nel selettore di file, selezionare il file desiderato come nuovo logo. Il nome del file viene visualizzato nella casella sotto l'immagine del logo corrente.

   > [!NOTE]
   > Azure richiede che l'immagine del logo deve essere un file PNG, e viene applicato i limiti della larghezza, altezza e dimensioni del file.
8. Selezionare **Salva**. Se si sceglie un nuovo logo, il **Logo** modifiche all'immagine del campo in modo da riflettere il nuovo file del logo.

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Visualizzare i gruppi e membri dell'organizzazione in Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Assegnare un utente o gruppo a un'app aziendale](assign-user-or-group-access-portal.md)
* [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](remove-user-or-group-access-portal.md)
* [Disabilitare l'accesso degli utenti per un'app aziendale](disable-user-sign-in-portal.md)
