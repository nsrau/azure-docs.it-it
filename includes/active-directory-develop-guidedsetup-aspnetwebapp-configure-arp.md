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
ms.openlocfilehash: f3048daa7d597d92041733cdf5c6f299cebc2f73
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142797"
---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Configurare l'App Web ASP.NET con le informazioni di registrazione dell'applicazione

In questo passaggio si configurerà il progetto in modo da usare SSL e quindi si userà l'URL SSL per configurare le informazioni di registrazione dell'applicazione. Successivamente, si aggiungeranno le informazioni di registrazione dell'applicazione alla soluzione tramite *web.config*.

1. In Esplora soluzioni selezionare il progetto e controllare la finestra `Properties` (se non viene visualizzata una finestra delle proprietà, premere F4)
2. Modificare `SSL Enabled` in `True`
3. Copiare il valore da `SSL URL` e incollarlo nel campo `Redirect URL` disponibile nella parte superiore della pagina e quindi fare clic su *Aggiorna*:<br/><br/>![Proprietà del progetto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
4. Aggiungere il codice seguente nel file `web.config` presente nella cartella radice, nella sezione `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
