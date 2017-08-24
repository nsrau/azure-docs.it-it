---
title: Introduzione ad Azure AD Privileged Identity Management | Microsoft Azure
description: "Informazioni su come gestire le identità con privilegi con l'applicazione Azure Active Directory Privileged Identity Management nel portale di Azure."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 2299db7d-bee7-40d0-b3c6-8d628ac61071
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: billmath
ms.custom: pim ; H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 17cdff033cc3dbb199d11c3b8ac1acbc92499877
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="start-using-azure-ad-privileged-identity-management"></a>Iniziare ad usare Azure AD Privileged Identity Management
Con Azure Active Directory (AD) Privileged Identity Management è possibile gestire, il controllo e monitorare l'accesso all'interno dell'organizzazione. Questo ambito include l'accesso alle risorse in Azure AD e altri Microsoft Online Services, ad esempio Office 365 o Microsoft Intune.

Questo articolo descrive come aggiungere l'app Azure AD PIM al dashboard del portale di Azure.

## <a name="add-the-privileged-identity-management-application"></a>Aggiungere l'applicazione Privileged Identity Management
Prima di usare Azure AD Privileged Identity Management è necessario aggiungere l'applicazione al dashboard del portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale della directory.
2. Se l'organizzazione ha più directory, selezionare il proprio nome utente nell'angolo superiore destro del portale di Azure. Selezionare la directory in cui si vuole usare PIM.
3. Selezionare **Altri servizi** e usare la casella di testo Filtro per cercare **Azure AD Privileged Identity Management**.
4. Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**. Verrà aperta l'applicazione Privileged Identity Management.

I ruoli **Amministratore della sicurezza** e **Amministratore del ruolo con privilegi** della directory vengono assegnati automaticamente alla prima persona che usa Azure AD Privileged Identity Management nella directory. Solo gli amministratori del ruolo con privilegi possono gestire le assegnazioni di ruoli degli utenti. È anche possibile scegliere di eseguire la [procedura guidata per la sicurezza](active-directory-privileged-identity-management-security-wizard.md), che illustra in modo dettagliato l'esperienza di individuazione e assegnazione iniziale.

## <a name="navigate-to-your-tasks"></a>Accedere alle attività
Dopo la configurazione di Azure AD Privileged Identity Management, a ogni apertura dell'applicazione viene visualizzato il pannello di spostamento. Usare questo pannello per eseguire le attività di gestione delle identità.

![Attività principali per PIM - Screenshot](./media/active-directory-privileged-identity-management-getting-started/PIM_Tasks_New.png)

* **Ruoli personali** visualizza un elenco di ruoli assegnati all'utente. In questa sezione è possibile attivare i ruoli per cui si è idonei.
* **Approva richieste (anteprima)** visualizza un elenco di richieste di attivazione in sospeso dagli utenti della directory. [Altre informazioni.](./privileged-identity-management/azure-ad-pim-approval-workflow.md)
* **Richieste in sospeso (anteprima)** visualizza eventuali richieste correnti da attivare.
* **Verifica l'accesso** consente di accedere alle eventuali verifiche di accesso in sospeso, sia per se stessi che per altri utenti, che devono essere completate.
* **Ruoli della directory di Azure AD**, disponibile nella sezione "Gestisci", è il dashboard in cui gli amministratori dei ruoli con privilegi possono gestire le assegnazioni di ruoli, modificare le impostazioni di attivazione dei ruoli, avviare le verifiche di accesso e altro ancora. Per gli utenti che non sono amministratori dei ruoli con privilegi, le opzioni nel dashboard sono disabilitate.

## <a name="next-steps"></a>Passaggi successivi
La [panoramica di Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) include altri dettagli su come gestire l'accesso amministrativo nell'organizzazione.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

