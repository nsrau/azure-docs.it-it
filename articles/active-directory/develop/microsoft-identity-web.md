---
title: Panoramica di Microsoft Identity Web Authentication Library
titleSuffix: Microsoft identity platform
description: Informazioni su Microsoft Identity Web, una libreria di autenticazione e autorizzazione per ASP.NET Core applicazioni che si integrano con Azure Active Directory, Azure AD B2C e Microsoft Graph e altre API Web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/09/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4d915cee962546e355ee9d53c683581730bbbf36
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778856"
---
# <a name="microsoft-identity-web-authentication-library"></a>Microsoft Identity Web Authentication Library

Microsoft Identity Web è un set di librerie di ASP.NET Core che semplifica l'aggiunta del supporto per l'autenticazione e l'autorizzazione alle app Web e alle API Web che si integrano con la piattaforma di identità Microsoft. Fornisce un livello di praticità delle API a singola superficie che unisce ASP.NET Core, il middleware di autenticazione e [Microsoft Authentication Library (MSAL) per .NET](https://github.com/azuread/microsoft-authentication-library-for-dotnet).

## <a name="supported-application-scenarios"></a>Scenari di applicazioni supportati

Se si stanno compilando ASP.NET Core app Web o API Web e si vuole usare Azure Active Directory (Azure AD) o Azure AD B2C per la gestione delle identità e degli accessi (IAM), è consigliabile usare Microsoft Identity Web per tutti questi scenari:

- [App Web che offre l'accesso agli utenti](scenario-web-app-sign-user-overview.md)
- [App Web che esegue l'accesso degli utenti e chiama un'API Web per loro conto](scenario-web-app-call-api-overview.md)
- [API Web protetta a cui possono accedere solo gli utenti autenticati](scenario-protected-web-api-overview.md)
- [API Web protetta che chiama un'altra API Web (downstream) per conto dell'utente che ha eseguito l'accesso](scenario-web-api-call-api-overview.md)

## <a name="get-the-library"></a>Ottenere la libreria

È possibile ottenere Microsoft Identity Web da [NuGet](#nuget), [modelli di progetto .NET Core](#project-templates)e [GitHub](#github).

#### <a name="nuget"></a>NuGet

Microsoft Identity Web è disponibile su NuGet come set di pacchetti che forniscono funzionalità modulari in base alle esigenze dell'app. Usare il comando dell'interfaccia della riga di comando di .NET `dotnet add` o **Gestione pacchetti NuGet** di Visual Studio per installare i pacchetti appropriati per il progetto:

- [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) : pacchetto principale. Richiesto da tutte le app che usano Microsoft Identity Web.
- [Microsoft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) -facoltativo. Aggiunge l'interfaccia utente per l'accesso e la disconnessione utente e un controller associato per le app Web.
- [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) -facoltativo. Fornisce un'interazione semplificata con l'API Microsoft Graph.

#### <a name="project-templates"></a>Modelli di progetto

I modelli di progetto Web Identity Microsoft sono inclusi in .NET 5,0 e sono disponibili per il download per i progetti ASP.NET Core 3,1.

Se si usa ASP.NET Core 3,1, installare i modelli con l'interfaccia della riga di comando di .NET:

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::1.0.0
```

Il diagramma seguente mostra una visualizzazione di alto livello dei tipi di app supportati e dei relativi argomenti rilevanti:

:::image type="content" source="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" lightbox="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" alt-text="Diagramma dei modelli di progetto dell'interfaccia della riga di comando per i punti di rete disponibili per Microsoft Identity Web&quot;:::
<br /><sup><b>*</b></sup>`MultiOrg`non è supportato con `webapi2` , ma può essere abilitato in *appsettings.json* impostando tenant su `common` o`organizations`
<br /><sup><b>**</b></sup>`--calls-graph`non è supportato per Azure ad B2C

Questo comando dell'interfaccia della riga di comando di .NET di esempio, tratto dall' [esercitazione sul server Blazer](tutorial-blazor-server.md), genera un nuovo progetto server blazer che include i pacchetti corretti e il codice di avvio (valori segnaposto visualizzati):

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph --client-id &quot;00000000-0000-0000-0000-000000000000&quot; --tenant-id &quot;11111111-1111-1111-1111-111111111111" --output my-blazor-app
```

#### <a name="github"></a>GitHub

Microsoft Identity Web è un progetto open source ospitato in GitHub: [AzureAD/Microsoft-Identity-Web](https://github.com/AzureAD/microsoft-identity-web?azure-portal=true)

Il [wiki del repository](https://github.com/AzureAD/microsoft-identity-web/wiki) contiene documentazione aggiuntiva e, se è necessaria assistenza o individuare un bug, è possibile segnalare un [problema](https://github.com/AzureAD/microsoft-identity-web/issues).

## <a name="features"></a>Funzionalità

Microsoft Identity Web include diverse funzionalità non disponibili se si usano i modelli di progetto ASP.NET 3,1 predefiniti.

| Funzionalità                                                                                  | ASP.NET Core 3,1                                                     | Microsoft Identity Web                                                                                  |
|------------------------------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| [Utenti di accesso](scenario-web-app-sign-user-app-configuration.md) in app Web             | <li>Account aziendali o dell'istituto di istruzione<li>Identità social (con Azure AD B2C) | <li>Account aziendali o dell'istituto di istruzione<li>Account Microsoft personali<li>Identità social (con Azure AD B2C)     |
| [Proteggi le API Web](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) | <li>Account aziendali o dell'istituto di istruzione<li>Identità social (con Azure AD B2C) | <li>Account aziendali o dell'istituto di istruzione<li>Account Microsoft personali<li>Identità social (con Azure AD B2C)     |
| Convalida dell'autorità di certificazione nelle app multi-tenant                                                   | No                                                                   | Sì, per [tutti i cloud](authentication-national-cloud.md) e [Azure ad B2C](/azure/active-directory-b2c) |
| App Web/API [chiama Microsoft Graph] [scenario-API-call-graph]                             | No                                                                   | Sì                                                                                                     |
| App Web/API [chiama API Web] [scenario-API-call-API]                                       | No                                                                   | Sì                                                                                                     |
| Supporta le credenziali del certificato                                                         | No                                                                   | Sì, inclusi Azure Key Vault                                                                          |
| Consenso incrementale e supporto dell'accesso condizionale nelle app Web                           | No                                                                   | Sì, in MVC, Razor Pages e Blazer                                                                    |
| Certificati di crittografia dei token nelle API Web                                                | No                                                                   | Sì                                                                                                     |
| [Ambito/convalida del ruolo app] [scenario-API-Validation] in API Web                        | No                                                                   | Sì                                                                                                     |
| `WWW-Authenticate` generazione di intestazioni in API Web                                         | No                                                                   | Sì                                                                                                     |

## <a name="next-steps"></a>Passaggi successivi

Per visualizzare Microsoft Identity Web in azione, provare l'esercitazione sul server Blazer:

[Esercitazione: creare un'app del server blazer che usa la piattaforma di identità Microsoft per l'autenticazione](tutorial-blazor-server.md)

Il wiki di Microsoft Identity Web in GitHub contiene la documentazione di riferimento completa per diversi aspetti della libreria. Ad esempio, l'utilizzo del certificato, il consenso incrementale e il riferimento all'accesso condizionale sono disponibili qui:

- [Uso dei certificati con Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates?azure-portal=true) (GitHub)
- [Consenso incrementale e accesso condizionale](https://github.com/AzureAD/microsoft-identity-web/wiki/Managing-incremental-consent-and-conditional-access?azure-portal=true) (GitHub)

<!-- LINKS -->
<!--  [miw-certs]: microsoft-identity-web-certificates.md  -->
<!--  [miw-certs-decrypt]: microsoft-identity-web-certificates.md#decryption-certificates  -->
<!--  [miw-inc-consent-ca-header]: microsoft-identity-web-consent-conditional-access.md#handling-incremental-consent-or-conditional-access-in-web-apis  -->
<!--  [miw-inc-consent-ca]: microsoft-identity-web-consent-conditional-access.md  -->
[scenario-API-call-API]: scenario-Web-API-call-API-call-API. MD # Option-1-Call-Microsoft-Graph-con-The-SDK [scenario-API-call-graph]: scenario-Web-API-call-API-call-API. MD # Option-1-Call-Microsoft-Graph-con-The-SDK [scenario-API-Validation]: scenario-protected-web-api-verification-scope-app-roles.md
