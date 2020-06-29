---
title: Esempi di codice del connettore API per flussi utente - Azure AD
description: Esempi di codice per i connettori API nei flussi di iscrizione self-service per identità esterne di Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0814312bb12582dd9e9ebfafc60fba470f6a9a9
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905158"
---
# <a name="samples-for-external-identities-self-service-sign-up"></a>Esempi di iscrizione self-service di identità esterne

Le tabelle seguenti forniscono collegamenti a esempi di codice per sfruttare le API Web nei flussi utente di iscrizione self-service usando [connettori API](api-connectors-overview.md).

## <a name="api-connector-azure-function-quickstarts"></a>Argomenti di avvio rapido su Funzioni di Azure per connettori API

| Esempio                                                                                                                          | Descrizione                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Questo esempio di Funzioni di Azure per .NET Core illustra come limitare le iscrizioni a specifici domini tenant e convalidare le informazioni fornite dall'utente. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Questo esempio di Funzioni di Azure per Node.js illustra come limitare le iscrizioni a specifici domini tenant e convalidare le informazioni fornite dall'utente.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Questo esempio di Funzioni di Azure per Python illustra come limitare le iscrizioni a specifici domini tenant e convalidare le informazioni fornite dall'utente.    |

<!-- \| [Java](../../azure-docs-pr/articles/active-directory/b2b/invite-internal-users.md#use-the-invitation-api-to-send-a-b2b-invitation) |  The sample below illustrates how to call the invitation API to invite an internal user as a B2B user. | -->

## <a name="custom-approval-workflows"></a>Flussi di lavoro di approvazione personalizzati

| Esempio | Descrizione |
|--------| ----------- |
| [Flusso di lavoro di approvazione manuale](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connectors-approvals) | Questo esempio illustra un flusso di lavoro di approvazione end-to-end per gestire la creazione di account utente guest nell'iscrizione self-service |

## <a name="identity-verification"></a>Verifica dell'identità

| Esempio                                                                                                            | Descrizione                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | Questo esempio illustra come verificare l'identità di un utente come parte dell'iscrizione self-service usando un connettore API per l'integrazione con IDology. |

<!-- | [Experian](https://github.com/Azure-Samples/) | This sample shows how add identity verification to your self-service sign-up user flow by using an API connector to integrate with Experian. | -->
