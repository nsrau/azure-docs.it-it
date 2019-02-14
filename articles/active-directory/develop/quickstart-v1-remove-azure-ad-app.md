---
title: Rimuovere un'applicazione da Azure Active Directory
description: Informazioni su come rimuovere un'applicazione da Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0038dcc10aafa191121b2797f68d66a3e32b3fa7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207450"
---
# <a name="quickstart-remove-an-application-from-azure-active-directory"></a>Avvio rapido: Rimuovere un'applicazione da Azure Active Directory

Gli sviluppatori aziendali e i fornitori di software come un servizio (SaaS) che hanno registrato delle applicazioni con Azure Active Directory (Azure AD) potrebbero dover rimuovere la registrazione di un'applicazione dal tenant di Azure AD.

In questa guida introduttiva si apprende come:

* [Rimuovere un'applicazione creata dall'organizzazione](#removing-an-application-authored-by-your-organization)
* [Rimuovere un'applicazione multi-tenant creata da un'altra organizzazione](#removing-a-multi-tenant-application-authorized-by-another-organization)

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario un tenant di Azure AD con le applicazioni registrate.

* Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](quickstart-create-new-tenant.md).
* Per informazioni su come aggiungere e registrare le app nel tenant, vedere [Aggiungere un'applicazione Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="removing-an-application-authored-by-your-organization"></a>Rimozione di un'applicazione autorizzata dall'organizzazione

Le applicazioni che l'organizzazione ha registrato appaiono sotto il filtro **Le mie app** nella pagina principale **Registrazioni app** del tenant. Queste sono applicazioni registrate manualmente attraverso il portale di Azure o a livello di codice con PowerShell o l'API Graph di Microsoft. Più precisamente, queste applicazioni sono rappresentate sia da un oggetto applicazione che da un oggetto entità servizio nel tenant. Per altre informazioni su questi oggetti, vedere [Oggetti applicazione e oggetti entità servizio](app-objects-and-service-principals.md).

### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Per rimuovere un'applicazione a tenant singolo dalla directory

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Se l'account consente di accedere a più tenant, selezionare l'account in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
1. Nel riquadro di spostamento a sinistra fare clic sul servizio **Azure Active Directory**, selezionare **Registrazioni app** quindi trovare e selezionare l'applicazione che si vuole configurare.
    Viene visualizzata la pagina di registrazione principale dell'applicazione che apre la pagina **Impostazioni** per l'applicazione.
1. Nella pagina principale della registrazione dell'applicazione selezionare **Elimina**.
1. Scegliere **Sì** per confermare l'eliminazione dell'applicazione.

### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Per rimuovere un'applicazione multi-tenant dalla sua home directory

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Se l'account consente di accedere a più tenant, selezionare l'account in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
1. Nel riquadro di spostamento a sinistra fare clic sul servizio **Azure Active Directory**, selezionare **Registrazioni app** quindi trovare e selezionare l'applicazione che si vuole configurare.
    Viene visualizzata la pagina di registrazione principale dell'applicazione che apre la pagina **Impostazioni** per l'applicazione.
1. Nella pagina **Impostazioni** scegliere **Proprietà** e cambiare il parametro **Multi-tenant** in **No**, per convertire prima di tutto l'applicazione alla modalità a tenant singolo e selezionare **Salva**.
    Gli oggetti entità di servizio dell'applicazione rimangono nei tenant di tutte le organizzazioni che hanno già concesso il consenso.
1. Nella pagina principale della registrazione dell'applicazione selezionare **Elimina**.
1. Scegliere **Sì** per confermare l'eliminazione dell'applicazione.

## <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Rimozione di un'applicazione multi-tenant autorizzata da un'altra organizzazione

Un sottoinsieme delle applicazioni visualizzate con il filtro **Tutte le app** (escluse le registrazioni **Le mie app**) nella pagina **Registrazioni app** principale del tenant, sono applicazioni multi-tenant.

In termini tecnici, queste applicazioni multi-tenant provengono da un altro tenant e sono state registrate nel tenant durante il processo di consenso. Più precisamente, sono rappresentate solo da un oggetto entità servizio nel tenant, senza oggetto applicazione corrispondente. Per altre informazioni sulle differenze tra oggetti applicazione e oggetti entità servizio, vedere [Oggetti applicazione e oggetti entità servizio in Azure AD](app-objects-and-service-principals.md).

Per rimuovere l'accesso di un'applicazione multi-tenant alla directory (dopo avere concesso il consenso), l'amministratore della società deve rimuovere l'entità servizio dell'applicazione. L'amministratore deve avere l'accesso di amministratore globale e può rimuoverlo tramite il portale di Azure o usando i [cmdlet di Azure AD PowerShell](https://go.microsoft.com/fwlink/?LinkId=294151).

## <a name="next-steps"></a>Passaggi successivi

Altre guide introduttive relative alla gestione delle app applicabili alle app che usano la versione 1.0 dell'endpoint di Azure AD:

- [Aggiungere un'applicazione ad Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md)
- [Aggiornare un'applicazione in Azure AD](quickstart-v1-update-azure-ad-app.md)
