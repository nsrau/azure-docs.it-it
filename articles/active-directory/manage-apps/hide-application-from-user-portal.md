---
title: Nascondi un'applicazione aziendale dall'esperienza dell'utente in Azure AD
description: Come nascondere un'applicazione aziendali dall'esperienza dell'utente in Azure Active Directory i pannelli di accesso o i Lancier di Office 365.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: d21ba14fba24c9b8e0b460e56b93d0e5212bfb27
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367700"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Nascondi le applicazioni aziendali dagli utenti finali nel Azure Active Directory

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
6.  Scegliere **Proprietà**. 
7.  Nel campo **Visibile agli utenti?** fare clic su **No**.
8.  Fare clic su **Salva**.

> [!NOTE]
> Queste istruzioni sono valide solo per le applicazioni aziendali.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Usare Azure AD PowerShell per nascondere un'applicazione

Per nascondere un'applicazione dal pannello app, è possibile aggiungere manualmente il tag HideApp all'entità servizio per l'applicazione. Eseguire i seguenti comandi di [PowerShell per AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) per impostare la proprietà Visible dell'applicazione **per gli utenti?** su **No**. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Nascondere le applicazioni di Office 365 nel pannello App personali

Seguire questa procedura per nascondere tutte le applicazioni di Office 365 dal pannello App personali. Le applicazioni continueranno a essere visibili nel portale di Office 365.

1.  Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale della directory.
2.  Selezionare **Azure Active Directory**.
3.  Selezionare **utenti**.
4.  Selezionare **impostazioni utente**.
5.  In **Applicazioni aziendali** fare clic su **Gestire il modo in cui gli utenti finali avviano e visualizzano le applicazioni**.
6.  In **Gli utenti possono visualizzare le app di Office 365 solo nel portale di Office 365** fare clic su **Sì**.
7.  Fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi
* [Visualizzare tutti i gruppi personali](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Assegnare un utente o gruppo a un'app aziendale](assign-user-or-group-access-portal.md)
* [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](remove-user-or-group-access-portal.md)
* [Modificare il nome o il logo di un'app aziendale](change-name-or-logo-portal.md)

