---
title: Modificare il nome o il logo di un'applicazione aziendale in Azure ADChange the name or logo of an enterprise application in Azure AD
description: Come modificare il nome o il logo di un'applicazione aziendale personalizzata in Azure Active Directory
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
ms.openlocfilehash: d23849df75d1ab239eb269b462abb21df196e7e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138502"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Modificare il nome o il logo di un'applicazione aziendale in Azure Active Directory

Modificare il nome o il logo per un'applicazione aziendale personalizzata in Azure Active Directory (Azure AD) è un'operazione facile. È necessario disporre delle autorizzazioni appropriate per apportare queste modifiche e deve essere il creatore dell'applicazione personalizzata.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Come si modifica il nome o il logo di un'applicazione aziendale?

1. Accedere al [portale](https://aad.portal.azure.com/) di Azure Active Directory con un account amministratore globale per la directory. Viene visualizzata la pagina **dell'interfaccia** di amministrazione di Azure Active Directory.The Azure Active Directory admin center page appears.
2. Nel riquadro sinistro selezionare **Applicazioni aziendali**. Viene visualizzato l'elenco delle applicazioni aziendali.
3. Selezionare un'applicazione. Viene visualizzata la pagina di panoramica dell'applicazione.
4. Nel riquadro di panoramica dell'applicazione, sotto l'intestazione **Gestisci,** selezionare **Proprietà**. Viene visualizzata la pagina **Proprietà.**
5. Se si desidera modificare il nome, selezionare la casella **Nome** , digitare il nuovo nome e premere **INVIO**.
6. Se si desidera modificare il logo, trovare il campo **Logo** e selezionare l'icona della cartella accanto alla casella **Seleziona un file,** che si trova sotto l'immagine del logo corrente dell'applicazione.

   ![Selezione del comando Proprietà](./media/change-name-or-logo-portal/change-logo.png)

   In caso contrario, se non si modifica il logo, andare al passaggio 8.
7. Nella selezione file, selezionare il file che si desidera impostare come nuovo logo. Il nome del file viene visualizzato nella casella sotto l'immagine del logo corrente.

   > [!NOTE]
   > Azure richiede che l'immagine del logo sia un file PNG e applica limiti di larghezza, altezza e dimensioni del file. I loghi personalizzati devono &times; avere una dimensione standard di 215 215 pixel ed essere in formato PNG. Si consiglia di utilizzare uno sfondo a tinta unita senza trasparenza nel logo dell'applicazione per apparire meglio agli utenti.
8. Selezionare **Salva**. Se si sceglie un nuovo logo, l'immagine del campo **Logo** cambia per riflettere il nuovo file del logo.

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: Visualizzare i gruppi e i membri dell'organizzazione in Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Assegnare un utente o gruppo a un'app aziendale](assign-user-or-group-access-portal.md)
* [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](remove-user-or-group-access-portal.md)
* [Disabilitare l'accesso degli utenti per un'app aziendale](disable-user-sign-in-portal.md)
