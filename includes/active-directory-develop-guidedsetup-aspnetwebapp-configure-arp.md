---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: b18b6e2fd5bb2071f57033dbe74ef87da028720b
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843372"
---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Configurare l'App Web ASP.NET con le informazioni di registrazione dell'applicazione

In questo passaggio si configurerà il progetto in modo da usare SSL e quindi si userà l'URL SSL per configurare le informazioni di registrazione dell'applicazione. Successivamente, si aggiungeranno le informazioni di registrazione dell'applicazione alla soluzione tramite *web.config*.

1.  In Esplora soluzioni selezionare il progetto e controllare la finestra `Properties` (se non viene visualizzata una finestra delle proprietà, premere F4)
2.  Modificare `SSL Enabled` in `True`
3.  Copiare il valore da `SSL URL` e incollarlo nel campo `Redirect URL` disponibile nella parte superiore della pagina e quindi fare clic su *Aggiorna*:<br/><br/>![Proprietà del progetto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
4.  Aggiungere il codice seguente nel file `web.config` presente nella cartella radice, nella sezione `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
