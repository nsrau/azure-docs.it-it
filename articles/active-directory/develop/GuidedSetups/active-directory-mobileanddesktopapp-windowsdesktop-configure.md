---
title: Introduzione a Windows Desktop per Azure AD v2 - Configurazione | Microsoft Docs
description: "Come un'applicazione .NET per Windows Desktop (XAML) può ottenere un token di accesso e chiamare un'API protetta dall'endpoint di Azure Active Directory v2. | Microsoft Azure | Microsoft Azure"
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
ms.openlocfilehash: 1dfaa7ade664e43dcb9aa788b0197ca17e6ec4cc
ms.contentlocale: it-it


---

## <a name="create-an-application-express"></a>Creare un'applicazione (Rapida)
È ora necessario registrare l'applicazione nel *portale di registrazione delle applicazioni Microsoft*:
1. Registrare l'applicazione tramite il [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure)
2.  Immettere un nome per l'applicazione e l'indirizzo di posta elettronica
3.  Assicurarsi che l'opzione per l'installazione guidata sia selezionata
4.  Seguire le istruzioni per ottenere l'ID dell'applicazione e incollarlo nel codice

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Aggiungere le informazioni di registrazione dell'applicazione alla soluzione (Avanzata)
È ora necessario registrare l'applicazione nel *portale di registrazione delle applicazioni Microsoft*:
1. Passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app) per registrare un'applicazione
2. Immettere un nome per l'applicazione e l'indirizzo di posta elettronica 
3. Assicurarsi che l'opzione per l'installazione guidata sia deselezionata
4. Fare clic su `Add Platform`, selezionare `Native Application` e quindi fare clic su Salva
5. Copiare il GUID nell'ID applicazione, tornare a Visual Studio, aprire `App.xaml.cs` e sostituire `your_client_id_here` con l'ID applicazione appena registrato:

```csharp
private static string ClientId = "your_application_id_here";
```

