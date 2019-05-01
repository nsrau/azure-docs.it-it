---
title: JavaScript e pagina del contratto versioni - Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come abilitare JavaScript e usare le versioni del contratto delle pagine in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 91b4b621fc3dcedb52f88372fbfac222a744dbd1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570621"
---
# <a name="javascript-and-page-contract-versions-in-azure-active-directory-b2c"></a>JavaScript e pagina Contratto di versioni in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C offre un set di contenuto nel pacchetto contenente codice HTML, CSS e JavaScript per gli elementi dell'interfaccia utente nei flussi degli utenti e i criteri personalizzati. Per abilitare JavaScript per le applicazioni, è necessario aggiungere un elemento per il [criteri personalizzati](active-directory-b2c-overview-custom.md) o abilitarla nel portale per i flussi utente, selezionare un contratto di pagina e usare [b2clogin.com](b2clogin.md) nelle richieste.

Se si intende abilitare [JavaScript](javascript-samples.md) codice lato client, è opportuno per assicurarsi che gli elementi si basa su JavaScript non sono modificabili. In caso contrario, tutte le modifiche potrebbe causare comportamenti imprevisti nelle pagine utente. Per evitare questi problemi, è possibile imporre l'uso di un contratto di pagina e specificare una versione del contratto di pagina. Questa operazione assicura che tutte le definizioni del contenuto che è stata base di JavaScript non sono modificabili. Anche se non si prevede di abilitare JavaScript, è possibile specificare una versione del contratto pagina per le pagine.

## <a name="user-flows"></a>Flussi degli utenti

Nelle proprietà del flusso utente, è possibile abilitare JavaScript, operazione che impone anche l'uso di un contratto di pagina. È quindi possibile impostare la versione del contratto di pagina come descritto nella sezione seguente.

![Abilitare le impostazioni di JavaScript](media/user-flow-javascript-overview/javascript-settings.png)

Sia che si preveda o meno di abilitare JavaScript nelle proprietà del flusso utente, è possibile specificare una versione del contratto di pagina per le pagine del flusso utente. Aprire il flusso utente e selezionare **Layout di pagina**. In **Nome layout**, selezionare una pagina di flusso utente e scegliere la **Versione del contratto di pagina**.

![Abilitare le impostazioni di JavaScript](media/user-flow-javascript-overview/page-contract-version.png)

## <a name="custom-policies"></a>Criteri personalizzati

Per abilitare JavaScript nei criteri personalizzati, aggiungere il **ScriptExecution** elemento per il **RelyingParty** elemento nel file di criteri personalizzata. Per altre informazioni, vedere [esempi di JavaScript per l'uso in Azure Active Directory B2C](javascript-samples.md).

Se si abilita JavaScript nei criteri personalizzati, è possibile specificare una versione del contratto pagina per le pagine. Per altre informazioni su come specificare un contratto di pagina, vedere [selezionare un contratto di pagina in Azure Active Directory B2C usando criteri personalizzati](page-contract.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere gli [Esempi JavaScript da usare in Azure Active Directory B2C](javascript-samples.md).
