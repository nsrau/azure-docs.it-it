---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: fb521fc9d4927a953cdd66948101969dfc102de4
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843288"
---
## <a name="register-your-application"></a>Registrare l'applicazione
È possibile registrare l'applicazione in uno dei due modi descritti di seguito.

### <a name="option-1-express-mode"></a>Opzione 1: Modalità rapida
È possibile registrare rapidamente l'applicazione seguendo questa procedura:
1. Passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure).

2. Selezionare **Aggiungi un'app**.

3. Nella casella **Application Name** (Nome applicazione) immettere un nome per l'applicazione.

4. Verificare che la casella di controllo **Guided Setup** (Configurazione guidata) sia selezionata e quindi selezionare **Create** (Crea).

5. Seguire le istruzioni per ottenere l'ID applicazione e incollarlo nel codice.

### <a name="option-2-advanced-mode"></a>Opzione 2: Modalità avanzata
Per registrare l'applicazione e aggiungere le relative informazioni di registrazione alla soluzione, seguire questa procedura:
1. Se l'applicazione non è ancora stata registrata, passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app).

2. Selezionare **Aggiungi un'app**.

3. Nella casella **Application Name** (Nome applicazione) immettere un nome per l'applicazione. 

4. Verificare che la casella di controllo **Guided Setup** (Configurazione guidata) sia deselezionata e quindi selezionare **Create** (Crea).

5. Selezionare **Aggiungi piattaforma**, **Applicazione nativa** e quindi **Salva**.

6. Nella casella **Application ID** (ID applicazione) copiare il GUID.

7. Passare a Visual Studio, aprire il file *App.xaml.cs* e quindi sostituire `your_client_id_here` con l'ID applicazione appena registrato e copiato.

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```
