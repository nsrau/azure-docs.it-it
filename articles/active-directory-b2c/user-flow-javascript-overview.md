---
title: Versioni del layout di pagina e JavaScript
titleSuffix: Azure AD B2C
description: Informazioni su come abilitare JavaScript e usare le versioni del layout di pagina in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 92925dc32d7f26b224811aa852112a275f112b35
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950834"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Versioni del layout di pagina e JavaScript in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C fornisce un set di contenuto in pacchetto contenente HTML, CSS e JavaScript per gli elementi dell'interfaccia utente nei flussi utente e nei criteri personalizzati. Per abilitare JavaScript per le applicazioni, è necessario aggiungere un elemento al [criterio personalizzato](active-directory-b2c-overview-custom.md) o abilitarlo nel portale per i flussi utente, selezionare un layout di pagina e usare [b2clogin.com](b2clogin.md) nelle richieste.

Se si intende abilitare il codice [JavaScript](javascript-samples.md) lato client, è necessario assicurarsi che gli elementi su cui si basa il codice JavaScript non siano modificabili. In caso contrario, le modifiche potrebbero provocare comportamenti imprevisti nelle pagine utente. Per evitare questi problemi, è possibile imporre l'utilizzo di un layout di pagina e specificare una versione del layout di pagina. In questo modo si garantisce che tutte le definizioni di contenuto su cui è stato basato JavaScript non siano modificabili. Anche se non si intende abilitare JavaScript, è possibile specificare una versione del layout di pagina per le pagine.

## <a name="user-flows"></a>Flussi degli utenti

Nelle **Proprietà**del flusso utente è possibile abilitare JavaScript, che impone anche l'uso di un layout di pagina. È quindi possibile impostare la versione del layout di pagina per il flusso utente, come descritto nella sezione successiva.

![Pagina delle proprietà del flusso utente con l'impostazione Abilita JavaScript evidenziata](media/user-flow-javascript-overview/javascript-settings.png)

### <a name="select-a-page-layout-version"></a>Selezionare una versione del layout di pagina

Se si Abilita JavaScript nelle proprietà del flusso utente, è possibile specificare una versione del layout di pagina per le pagine del flusso utente. Aprire il flusso utente e selezionare **layout di pagina**. In **nome layout**selezionare una pagina flusso utente e scegliere la **versione del layout di pagina**.

Per informazioni sulle diverse versioni del layout di pagina, vedere il [log delle modifiche della versione](page-layout.md#version-change-log).

![Impostazioni del layout di pagina nel portale che mostra il menu a discesa Versione layout pagina](media/user-flow-javascript-overview/page-layout-version.png)

## <a name="custom-policies"></a>Criteri personalizzati

Per abilitare JavaScript nei criteri personalizzati, aggiungere l'elemento **ScriptExecution** all'elemento **RelyingParty** nel file dei criteri personalizzato. Per ulteriori informazioni, vedere [esempi di JavaScript da utilizzare in Azure Active Directory B2C](javascript-samples.md).

Se si Abilita JavaScript nei criteri personalizzati, è possibile specificare una versione del layout di pagina per le pagine. Per altre informazioni su come specificare un layout di pagina, vedere [selezionare un layout di pagina in Azure Active Directory B2C usando criteri personalizzati](page-layout.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle diverse versioni del layout di pagina, vedere la sezione relativa al **log delle modifiche della versione** di [selezionare un layout di pagina in Azure Active Directory B2C uso di criteri personalizzati](page-layout.md#version-change-log).

È possibile trovare esempi di utilizzo di JavaScript in [esempi di JavaScript da usare in Azure Active Directory B2C](javascript-samples.md).
