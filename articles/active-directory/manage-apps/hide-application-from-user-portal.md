---
title: Nascondere un'applicazione dall'esperienza dell'utente in Azure AD
description: Nascondere un'applicazione dall'esperienza utente nei pannelli di accesso di Azure Active Directory o nelle schermate di avvio di Office 365.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: be29f51771e24c67a8cd99a81e6a69be830dacb8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970636"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Nascondere le applicazioni per gli utenti finali in Azure Active Directory

Istruzioni su come nascondere le applicazioni nel pannello App personali degli utenti finali o nella utilità di avvio di Office 365. Quando un'applicazione è nascosta, gli utenti continuano a disporre delle autorizzazioni relative all'applicazione. 

## <a name="prerequisites"></a>Prerequisiti

Per nascondere un'applicazione nel pannello App personali e nella utilità di avvio di Office 365, è necessario disporre dei privilegi Amministratore di applicazioni.

Per nascondere tutte le applicazioni di Office 365, è necessario disporre dei privilegi Amministratore globale.


## <a name="hide-an-application-from-the-end-user"></a>Nascondere un'applicazione per gli utenti finali
Seguire questa procedura per nascondere un'applicazione nel pannello App personali e nella utilità di avvio di Office 365.

1.  Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale della directory.
2.  Selezionare **Azure Active Directory**.
3.  Selezionare **Applicazioni aziendali**. Verrà visualizzato il pannello **Applicazioni aziendali - Tutte le applicazioni**.
4.  In **Tipo di applicazione** selezionare **Applicazioni aziendali** se l'opzione non è già selezionata.
5.  Cercare l'applicazione che si desidera nascondere e fare clic su di essa.  Viene visualizzata la panoramica dell'applicazione.
6.  Fare clic su **Proprietà**. 
7.  Nel campo **Visibile agli utenti?** fare clic su **No**.
8.  Fare clic su **Salva**

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Usare Azure AD PowerShell per nascondere un'applicazione

Per nascondere un'applicazione dal pannello app, è possibile aggiungere manualmente il tag HideApp all'entità servizio per l'applicazione. Eseguire i seguenti comandi di [PowerShell per AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) per impostare la proprietà Visible dell'applicazione **per gli utenti?** su **No**. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags.Add("HideApp")
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Nascondere le applicazioni di Office 365 nel pannello App personali

Seguire questa procedura per nascondere tutte le applicazioni di Office 365 dal pannello App personali. Le applicazioni continueranno a essere visibili nel portale di Office 365.

1.  Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale della directory.
2.  Selezionare **Azure Active Directory**.
3.  Selezionare **Impostazioni utente**.
4.  In **Applicazioni aziendali** fare clic su **Gestire il modo in cui gli utenti finali avviano e visualizzano le applicazioni**.
5.  In **Gli utenti possono visualizzare le app di Office 365 solo nel portale di Office 365** fare clic su **Sì**.
6.  Fare clic su **Salva**

## <a name="next-steps"></a>Passaggi successivi
* [Visualizzare tutti i gruppi personali](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Assegnare un utente o gruppo a un'app aziendale](assign-user-or-group-access-portal.md)
* [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](remove-user-or-group-access-portal.md)
* [Modificare il nome o il logo di un'app aziendale](change-name-or-logo-portal.md)

