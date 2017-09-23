---
title: Introduzione al server Web ASP.NET per Azure AD v2 - Configurazione | Microsoft Docs
description: Implementazione di accessi Microsoft in una soluzione ASP.NET con un'applicazione tradizionale basata su Web browser tramite lo standard OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 8a1650a65e7980f4a13fa4edc7918b0099bb5464
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017

---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Configurare l'App Web ASP.NET con le informazioni di registrazione dell'applicazione

In questo passaggio si configurerà il progetto in modo da usare SSL e quindi si userà l'URL SSL per configurare le informazioni di registrazione dell'applicazione. Successivamente, si aggiungeranno le informazioni di registrazione dell'applicazione alla soluzione tramite *web.config*.

1.  In Esplora soluzioni selezionare il progetto e controllare la finestra `Properties` (se non viene visualizzata una finestra delle proprietà, premere F4)
2.  Modificare `SSL Enabled` in `True`
3.  Copiare il valore da `SSL URL` e incollarlo nel campo `Redirect URL` disponibile nella parte superiore della pagina e quindi fare clic su *Aggiorna*:<br/><br/>![Proprietà del progetto](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
4.  Aggiungere il codice seguente nel file `web.config` presente nella cartella radice, nella sezione `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```

