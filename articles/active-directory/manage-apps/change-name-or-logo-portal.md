---
title: Modificare il nome o il logo di un'applicazione aziendale in Azure AD
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
ms.openlocfilehash: d3dfe0f8788275dd4403b6c9cad99a8eb09e479b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274726"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Modificare il nome o il logo di un'applicazione aziendale in Azure Active Directory

Modificare il nome o il logo per un'applicazione aziendale personalizzata in Azure Active Directory (Azure AD) è un'operazione facile. È necessario disporre delle autorizzazioni appropriate per apportare queste modifiche ed è necessario essere l'autore dell'applicazione personalizzata.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Ricerca per categorie modificare il nome o il logo di un'applicazione aziendale?

1. Accedere al portale di [Azure Active Directory](https://aad.portal.azure.com/) con un account amministratore globale per la directory. Viene visualizzata la pagina **centro di amministrazione Azure Active Directory** .
2. Nel riquadro sinistro selezionare **Applicazioni aziendali**. Viene visualizzato l'elenco delle applicazioni aziendali.
3. Selezionare un'applicazione. Verrà visualizzata la pagina Panoramica applicazione.
4. Nel riquadro Panoramica applicazione, sotto l'intestazione **Gestisci** , selezionare **Proprietà**. Verrà visualizzata la pagina **Proprietà** .
5. Se si desidera modificare il nome, selezionare la casella **nome** , digitare il nuovo nome e premere **invio**.
6. Se si vuole modificare il logo, trovare il campo **logo** e selezionare l'icona della cartella accanto alla casella **selezionare un file** , che si trova sotto l'immagine del logo corrente dell'applicazione.

   ![Selezione del comando Proprietà](./media/change-name-or-logo-portal/change-logo.png)

   In caso contrario, se non si modifica il logo, andare al passaggio 8.
7. In selezione file selezionare il file desiderato come nuovo logo. Il nome del file viene visualizzato nella casella sotto l'immagine del logo corrente.

   > [!NOTE]
   > Azure richiede che l'immagine del logo sia un file PNG e applica limiti a larghezza, altezza e dimensioni del file.
8. Selezionare **Salva**. Se si sceglie un nuovo logo, l'immagine del campo del **logo** viene modificata per riflettere il nuovo file del logo.

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: visualizzare i gruppi e i membri dell'organizzazione in Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Assegnare un utente o gruppo a un'app aziendale](assign-user-or-group-access-portal.md)
* [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](remove-user-or-group-access-portal.md)
* [Disabilitare l'accesso degli utenti per un'app aziendale](disable-user-sign-in-portal.md)
