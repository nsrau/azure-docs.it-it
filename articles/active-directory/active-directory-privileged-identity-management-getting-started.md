---
title: Introduzione ad Azure AD Privileged Identity Management | Microsoft Azure
description: "Informazioni su come gestire le identità con privilegi con l'applicazione Azure Active Directory Privileged Identity Management nel portale di Azure."
services: active-directory
documentationcenter: 
author: barclayn
manager: mtillman
editor: 
ms.assetid: 2299db7d-bee7-40d0-b3c6-8d628ac61071
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: 9f51daabef7d1e02917869e4e6943b8ea28b56f5
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="start-using-azure-ad-privileged-identity-management"></a>Iniziare ad usare Azure AD Privileged Identity Management

Con Azure Active Directory (AD) Privileged Identity Management è possibile gestire, il controllo e monitorare l'accesso all'interno dell'organizzazione. Questo ambito include l'accesso alle risorse Azure, Azure AD e ad altri servizi Microsoft online, ad esempio Office 365 o Microsoft Intune.

Questo articolo descrive come aggiungere l'app Azure AD PIM al dashboard del portale di Azure.

## <a name="add-the-privileged-identity-management-application"></a>Aggiungere l'applicazione Privileged Identity Management

Prima di usare Azure AD Privileged Identity Management è necessario aggiungere l'applicazione al dashboard del portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale della directory.
2. Se l'organizzazione ha più directory, selezionare il proprio nome utente nell'angolo superiore destro del portale di Azure. Selezionare la directory in cui si vuole usare PIM.
3. Selezionare **Altri servizi** e usare la casella di testo Filtro per cercare **Azure AD Privileged Identity Management**.
4. Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**. Verrà aperta l'applicazione Privileged Identity Management.

I ruoli **Amministratore della sicurezza** e **Amministratore del ruolo con privilegi** della directory vengono assegnati automaticamente alla prima persona che usa Azure AD Privileged Identity Management nella directory. Solo gli amministratori del ruolo con privilegi possono gestire le assegnazioni di ruoli della directory Azure AD degli utenti. È anche possibile scegliere di eseguire la [procedura guidata per la sicurezza](active-directory-privileged-identity-management-security-wizard.md), che illustra in modo dettagliato l'esperienza di individuazione e assegnazione iniziale.

## <a name="navigate-to-your-tasks"></a>Accedere alle attività

Dopo la configurazione di Azure AD Privileged Identity Management, a ogni apertura dell'applicazione viene visualizzato il pannello di spostamento. Usare questo pannello per eseguire le attività di gestione delle identità.

![Attività principali per PIM - Screenshot](./media/active-directory-privileged-identity-management-getting-started/PIM_Tasks_New.png)

- **My roles** (Ruoli personali) visualizza un elenco di ruoli idonei e attivi assegnati all'utente. Qui è possibile attivare tutti i ruoli idonei assegnati.
- **Approva richieste (anteprima)** visualizza un elenco di richieste per l'attivazione di ruoli idonei della directory Azure AD da parte degli utenti della directory, che l'utente ha designato all'approvazione. [Altre informazioni.](./privileged-identity-management/azure-ad-pim-approval-workflow.md)
- **Richieste in sospeso (anteprima)** visualizza le richieste in sospeso per attivare le assegnazioni dei ruoli idonei.
- **Verifica l'accesso** elenca le verifiche di accesso attive assegnate all'utente per essere completate, sia per se stesso che per altri utenti.
- **Ruoli della directory di Azure AD**, disponibile nella sezione della gestione del menu di spostamento sinistro, è il dashboard in cui gli amministratori dei ruoli con privilegi possono gestire le assegnazioni di ruoli, modificare le impostazioni di attivazione dei ruoli, avviare le verifiche di accesso e altro ancora. Per gli utenti che non sono amministratori dei ruoli con privilegi, questo dashboard è disabilitato. Questi utenti possono accedere a un dashboard speciale denominato My view (Visualizzazione personalizzata). Il dashboard My view (Visualizzazione personalizzata) visualizza solo informazioni relative all'accesso dell'utente al dashboard, non l'intero tenant.
- **Ruoli delle risorse di Azure (anteprima)** nella sezione di gestione del menu di spostamento sinistro è visualizzato un elenco di risorse di sottoscrizione da cui scegliere per l'assegnazione dei ruoli 

## <a name="next-steps"></a>Passaggi successivi
La [panoramica di Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) include altri dettagli su come gestire l'accesso amministrativo nell'organizzazione.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
