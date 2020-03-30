---
title: Versioni JavaScript e layout di pagina
titleSuffix: Azure AD B2C
description: Informazioni su come abilitare JavaScript e usare le versioni del layout di pagina in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 23d345ea9f22be5c4dac20e6e8784a8de079bccb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185838"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Versioni JavaScript e layout di pagina in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C offre un set di contenuto in pacchetto contenente HTML, CSS e JavaScript per gli elementi dell'interfaccia utente nei flussi utente e nei criteri personalizzati.

Per abilitare JavaScript per le applicazioni:

* Abilitarlo nel flusso utente tramite il portale di AzureEnable it on the user flow by using the Azure portal
* Selezionare un [layout di pagina](page-layout.md)
* Utilizzare [b2clogin.com](b2clogin.md) nelle richieste

Se si intende abilitare il codice lato client [JavaScript,](javascript-samples.md) gli elementi su cui si basa JavaScript devono essere non modificabili. Se non sono immutabili, eventuali modifiche potrebbero causare un comportamento imprevisto nelle pagine utente. Per evitare questi problemi, impone l'uso di un layout di pagina e specifica una versione del layout di pagina per assicurarti che le definizioni di contenuto su cui hai basato il tuo JavaScript non siano modificabili. Anche se non intendi abilitare JavaScript, puoi specificare una versione del layout di pagina per le tue pagine.

## <a name="enable-javascript"></a>Abilitare JavaScript

Nelle **proprietà**del flusso utente è possibile abilitare JavaScript. L'abilitazione di JavaScript impone anche l'uso di un layout di pagina. È quindi possibile impostare la versione del layout di pagina per il flusso utente come descritto nella sezione successiva.

![Pagina delle proprietà del flusso utente con l'impostazione Abilita JavaScript evidenziata](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Selezionare una versione del layout di pagina

Indipendentemente dal fatto che si abiliti o meno JavaScript nelle proprietà del flusso utente, è possibile specificare una versione del layout di pagina per le pagine del flusso utente. Aprire il flusso utente e selezionare **Layout di pagina**. In **NOME LAYOUT**, selezionare una pagina del flusso utente e scegliere Versione layout di **pagina**.

Per informazioni sulle diverse versioni del layout di pagina, vedere il log delle modifiche del layout di [pagina](page-layout.md).

![Impostazioni del layout di pagina nel portale con l'elenco a discesa della versione del layout di pagina](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare esempi di utilizzo di JavaScript in [esempi JavaScript da usare in Azure Active Directory B2C.](javascript-samples.md)
