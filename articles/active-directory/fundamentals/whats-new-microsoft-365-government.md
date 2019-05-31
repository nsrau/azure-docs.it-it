---
title: Novità di Azure Active Directory in Microsoft 365 Government - Azure Active Directory | Microsoft Docs
description: Conoscere le modifiche apportate ad Azure Active Directory (Azure AD) nell'istanza di Microsoft 365 Government cloud, che potrebbe compromettere è.
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
ms.openlocfilehash: 5b077c7b5efbad2add971d42ff31938b56f6bc33
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258895"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Novità di Azure Active Directory in Microsoft 365 Government

Sono state apportate alcune modifiche ad Azure Active Directory (Azure AD) nell'istanza di Microsoft 365 Government cloud, che è applicabile ai clienti che usano i servizi seguenti:

- Microsoft Azure per enti pubblici

- Microsoft 365 Government-GCC elevata

- Microsoft 365 per enti pubblici, Dipartimento della difesa

Questo articolo non si applica a Microsoft 365 Government – clienti GCC.

## <a name="changes-to-the-initial-domain-name"></a>Modifiche apportate al nome di dominio iniziale

Durante l'iscrizione per un servizio online Microsoft 365 Government iniziale della propria organizzazione, è stato chiesto di scegliere il nome di dominio dell'organizzazione, `<your-domain-name>.onmicrosoft.com`. Se si dispone già di un nome di dominio con il suffisso. com, nulla verrà modificato.

Tuttavia, se si esegue l'iscrizione per un nuovo servizio di Microsoft 365 Government, verrà chiesto di scegliere un nome di dominio usando il `.us` suffisso. Pertanto, sarà `<your-domain-name>.onmicrosoft.us`.

>[!Note]
>Questa modifica non è applicabile a tutti i clienti che sono gestiti dai provider di servizi cloud (CSP).

## <a name="changes-to-portal-access"></a>Modifiche per l'accesso al portale

Abbiamo aggiornato l'endpoint nel portale di Microsoft Azure per enti pubblici, Microsoft 365 Government-GCC High e Microsoft 365 Government – DoD, come illustrato nella [tabella di mapping di Endpoint](#endpoint-mapping).

In precedenza ai clienti è stato possibile accedere tramite i portali di Office 365 (portal.office.com) e di Azure in tutto il mondo (portal.azure.com). Con questo aggiornamento, i clienti devono ora accedere tramite il Microsoft Azure per enti pubblici, Microsoft 365 Government - GCC High e Microsoft 365 Government - portali DoD specifico.

## <a name="endpoint-mapping"></a>Mapping degli endpoint

La tabella seguente illustra gli endpoint per tutti i clienti:

| NOME | dettagli dell'endpoint |
|------|------------------|
| Portali |Microsoft Azure Government: https://portal.azure.us<p>Microsoft 365 Government-GCC elevata: https://portal.office365.us<p>Microsoft 365 per enti pubblici, Dipartimento della difesa: https://portal.apps.mil |
| Endpoint dell'autorità di Azure Active Directory | https://login.microsoftonline.us |
| API Graph di Azure Active Directory | https://graph.windows.net |
| API Graph Microsoft per enti pubblici di Microsoft 365 - GCC elevata | https://graph.microsoft.us |
| API Graph Microsoft per enti pubblici di Microsoft 365 - dipartimento della difesa | https://dod-graph.microsoft.us |
| Gli endpoint di servizi di Azure per enti pubblici | Per informazioni dettagliate, vedere [Guida per gli sviluppatori di Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 Government - endpoint GCC High | Per informazioni dettagliate, vedere [Office 365 US Endpoint GCC High per enti pubblici](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Government - DoD | Per informazioni dettagliate, vedere [Office 365 US Endpoint dipartimento della difesa di enti pubblici](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere questi articoli:

- [Informazioni su Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Aggiornamento dell'Endpoint dell'autorità AAD Azure per enti pubblici](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Endpoint di Microsoft Graph nel cloud US Government](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 US Government GCC High e DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)