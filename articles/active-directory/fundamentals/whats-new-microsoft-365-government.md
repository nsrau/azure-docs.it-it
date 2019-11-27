---
title: Quali sono le novità di Azure AD in Microsoft 365 Government? | Documentazione di Microsoft
description: Informazioni sulle modifiche apportate all'Azure Active Directory (Azure AD) nell'istanza del cloud Microsoft 365 Government, che può influisca sull'utente.
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
ms.openlocfilehash: 75e7c6809810a78c81348bd4bbeb808e103f453f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805290"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Novità relative a Azure Active Directory in Microsoft 365 Government

Sono state apportate alcune modifiche all'Azure Active Directory (Azure AD) nell'istanza del cloud Microsoft 365 Government, applicabile ai clienti che usano i servizi seguenti:

- Microsoft Azure per enti pubblici

- Microsoft 365 Government-GCC High

- Microsoft 365 Government-DoD

Questo articolo non si applica ai clienti di Microsoft 365 Government-GCC.

## <a name="changes-to-the-initial-domain-name"></a>Modifiche al nome di dominio iniziale

Durante l'iscrizione iniziale dell'organizzazione per un servizio online Microsoft 365 Government, è stato richiesto di scegliere il nome di dominio dell'organizzazione `<your-domain-name>.onmicrosoft.com`. Se si dispone già di un nome di dominio con il suffisso. com, nulla cambierà.

Tuttavia, se si esegue l'iscrizione per un nuovo servizio Microsoft 365 Government, verrà chiesto di scegliere un nome di dominio usando il suffisso `.us`. Quindi, sarà `<your-domain-name>.onmicrosoft.us`.

>[!Note]
>Questa modifica non si applica a tutti i clienti gestiti da provider di servizi cloud (CSP).

## <a name="changes-to-portal-access"></a>Modifiche all'accesso al portale

Sono stati aggiornati gli endpoint del portale per Microsoft Azure per enti pubblici, Microsoft 365 Government-GCC High e Microsoft 365 Government-DoD, come illustrato nella tabella di [mapping degli endpoint](#endpoint-mapping).

I clienti in precedenza potevano accedere usando i portali di Azure (portal.azure.com) e Office 365 (portal.office.com) in tutto il mondo. Con questo aggiornamento, i clienti devono ora accedere usando i portali Microsoft Azure per enti pubblici specifici, Microsoft 365 Government-GCC High e Microsoft 365 Government-DoD.

## <a name="endpoint-mapping"></a>Mapping degli endpoint

La tabella seguente Mostra gli endpoint per tutti i clienti:

| Nome | Dettagli endpoint |
|------|------------------|
| Portali |Microsoft Azure per enti pubblici: https://portal.azure.us<p>Microsoft 365 Government-GCC High: https://portal.office365.us<p>Microsoft 365 Government-DoD: https://portal.apps.mil |
| Endpoint autorità Azure Active Directory | https://login.microsoftonline.us |
| API Graph di Azure Active Directory | https://graph.windows.net |
| API Microsoft Graph per Microsoft 365 Government-GCC High | https://graph.microsoft.us |
| API Microsoft Graph per Microsoft 365 Government-DoD | https://dod-graph.microsoft.us |
| Endpoint dei servizi di Azure per enti pubblici | Per informazioni dettagliate, vedere Guida per gli [sviluppatori di Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 Government-endpoint GCC High | Per informazioni dettagliate, vedere [Office 365 US Government GCC High Endpoints](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Government - DoD | Per informazioni dettagliate, vedere gli [endpoint DOD di Office 365 per enti pubblici statunitensi](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere questi articoli:

- [Informazioni su Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Aggiornamento endpoint autorità AAD di Azure per enti pubblici](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Microsoft Graph endpoint nel cloud per enti pubblici degli Stati Uniti](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 US Government GCC High e DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)