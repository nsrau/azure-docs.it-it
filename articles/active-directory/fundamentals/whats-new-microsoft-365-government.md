---
title: Novità di Azure AD in Microsoft 365 Government | Microsoft Docs
description: Informazioni su alcune modifiche apportate ad Azure Active Directory (Azure AD) nell'istanza cloud di Microsoft 365 Government, che potrebbero avere un impatto sull'utente.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48a7e5e810e509a97b6010218532f6e7dff0b73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425545"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Novità di Azure Active Directory in Microsoft 365 Government

Sono state apportate alcune modifiche ad Azure Active Directory (Azure AD) nell'istanza cloud di Microsoft 365 Government, applicabile ai clienti che usano i servizi seguenti:We've made some changes to Azure Active Directory (Azure AD) in the Microsoft 365 Government cloud instance, which is applicable to customers using the following services:

- Microsoft Azure per enti pubblici

- Microsoft 365 Government – GCC High

- Microsoft 365 Government – DoD

Questo articolo non si applica ai clienti Microsoft 365 Government – GCC.

## <a name="changes-to-the-initial-domain-name"></a>Modifiche al nome di dominio iniziale

Durante l'iscrizione iniziale dell'organizzazione a un servizio online di Microsoft 365 Government, `<your-domain-name>.onmicrosoft.com`è stato chiesto di scegliere il nome di dominio dell'organizzazione, . Se si dispone già di un nome di dominio con il suffisso .com, non cambierà nulla.

Tuttavia, se ti stai iscrivendo a un nuovo servizio Microsoft 365 Government, `.us` ti verrà chiesto di scegliere un nome di dominio usando il suffisso. Così, sarà `<your-domain-name>.onmicrosoft.us`.

>[!Note]
>Questa modifica non si applica ai clienti gestiti dai provider di servizi cloud (CSP).

## <a name="changes-to-portal-access"></a>Modifiche all'accesso al portaleChanges to portal access

Sono stati aggiornati gli endpoint del portale per Microsoft Azure per enti pubblici, Microsoft 365 Government - GCC High e Microsoft 365 Government – DoD, come illustrato nella [tabella Mapping degli endpoint](#endpoint-mapping).

In precedenza i clienti potevano accedere usando i portali di Azure (portal.azure.com) e Office 365 (portal.office.com) in tutto il mondo. Con questo aggiornamento, i clienti devono ora accedere utilizzando i portali specifici di Microsoft Azure per enti pubblici, Microsoft 365 Government - GCC High e Microsoft 365 Government - DoD.

## <a name="endpoint-mapping"></a>Mapping degli endpoint

Nella tabella seguente vengono illustrati gli endpoint per tutti i clienti:

| Nome | Dettagli endpoint |
|------|------------------|
| Portali |Microsoft Azure per enti pubblici:https://portal.azure.us<p>Microsoft 365 Government – GCC Alto:https://portal.office365.us<p>Microsoft 365 Government – DoD:https://portal.apps.mil |
| Azure Active Directory Authority Endpoint | https://login.microsoftonline.us |
| API Microsoft Graph per Microsoft 365 Government - GCC High | https://graph.microsoft.us |
| API Microsoft Graph per Microsoft 365 Government - DoD | https://dod-graph.microsoft.us |
| Endpoint dei servizi di Azure per enti pubbliciAzure Government services endpoints | Per informazioni dettagliate, vedere Guida per gli sviluppatori di [Azure per enti pubbliciFor details,](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) see Azure Government developer guide |
| Microsoft 365 Government - Endpoint elevati GCC | Per informazioni dettagliate, vedere [Endpoint GCC di Office 365 U.S. Government](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Government - DoD | Per informazioni dettagliate, vedere Endpoint DoD di [Office 365 Stati Uniti](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere questi articoli:

- [Informazioni su Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Azure Government AAD Authority Endpoint Update](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Endpoint di Microsoft Graph nel cloud del governo degli Stati Uniti](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 US Government GCC Alta e DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)
