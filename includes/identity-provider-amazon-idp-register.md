---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 05/04/2020
ms.author: mimart
ms.openlocfilehash: de1b67265c5c3b3f7247b7f21506eed88abfd550
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900343"
---
1. Accedere a [Amazon Developer Console](https://developer.amazon.com/dashboard) con le credenziali dell'account Amazon.
1. Se non è già stato fatto, fare clic su **Sign Up**(Iscrizione), seguire la procedura di registrazione per sviluppatori e accettare le condizioni.
1. Dal Dashboard selezionare **Accedi con Amazon**.
1. Selezionare **Create a New Security Profile** (Crea un nuovo profilo di sicurezza).
1. Immettere un **nome del profilo di sicurezza**, la **Descrizione del profilo di sicurezza**e l'URL dell' `https://www.contoso.com/privacy` **informativa sulla privacy di consenso**. ad esempio, l'URL dell'informativa sulla privacy è una pagina che fornisce informazioni sulla privacy agli utenti. Fare quindi clic su **Salva**.
1. Nella sezione **account di accesso con configurazioni di Amazon** selezionare il **nome del profilo di sicurezza** creato, fare clic sull'icona **Gestisci** e selezionare **Impostazioni Web**.
1. Nella sezione **Impostazioni Web** copiare i valori dell'**ID client**. Selezionare **Mostra il segreto** per ottenere il segreto client e quindi copiarlo. Sono necessari entrambi per configurare un account Amazon come provider di identità nel tenant. **Client Secret** è un'importante credenziale di sicurezza.
1. Nella sezione **Impostazioni Web** selezionare **modifica**. In **origini consentite** e **URL restituiti consentiti**immettere gli URL appropriati (indicati in precedenza). 
1. Fare clic su **Salva**.
