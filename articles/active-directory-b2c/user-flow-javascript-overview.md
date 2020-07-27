---
title: Versioni del layout di pagina e JavaScript
titleSuffix: Azure AD B2C
description: Informazioni su come abilitare JavaScript e usare le versioni del layout di pagina in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.custom: project-no-code, devx-track-javascript
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d85ffca8c72e7a247d5bbd1e593335fd7995b594
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170138"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Versioni del layout di pagina e JavaScript in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C fornisce un set di contenuto in pacchetto contenente HTML, CSS e JavaScript per gli elementi dell'interfaccia utente nei flussi utente e nei criteri personalizzati.

Per abilitare JavaScript per le applicazioni:

* Abilitarlo nel flusso utente usando il portale di Azure
* Selezionare un [layout di pagina](page-layout.md)
* Usare [b2clogin.com](b2clogin.md) nelle richieste

Se si intende abilitare il codice [JavaScript](javascript-samples.md) lato client, gli elementi su cui si basa il codice JavaScript non devono essere modificabili. Se non sono immutabili, le eventuali modifiche potrebbero provocare comportamenti imprevisti nelle pagine utente. Per evitare questi problemi, imporre l'uso di un layout di pagina e specificare una versione del layout di pagina per assicurarsi che le definizioni di contenuto su cui si basa JavaScript siano non modificabili. Anche se non si intende abilitare JavaScript, è possibile specificare una versione del layout di pagina per le pagine.

## <a name="enable-javascript"></a>Abilitare JavaScript

Nelle **Proprietà**del flusso utente è possibile abilitare JavaScript. L'abilitazione di JavaScript impone anche l'uso di un layout di pagina. È quindi possibile impostare la versione del layout di pagina per il flusso utente, come descritto nella sezione successiva.

![Pagina delle proprietà del flusso utente con l'impostazione Abilita JavaScript evidenziata](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Selezionare una versione del layout di pagina

Se si Abilita JavaScript nelle proprietà del flusso utente, è possibile specificare una versione del layout di pagina per le pagine del flusso utente. Aprire il flusso utente e selezionare **layout di pagina**. In **nome layout**selezionare una pagina flusso utente e scegliere la **versione del layout di pagina**.

Per informazioni sulle diverse versioni del layout di pagina, vedere il [log delle modifiche della versione del layout di pagina](page-layout.md).

![Impostazioni del layout di pagina nel portale che mostra il menu a discesa Versione layout pagina](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare esempi di utilizzo di JavaScript in [esempi di JavaScript da usare in Azure Active Directory B2C](javascript-samples.md).
