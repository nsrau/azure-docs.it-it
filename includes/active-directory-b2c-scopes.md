---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 59cf0b81363c724d709d85105525bece13743f26
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474827"
---
#### <a name="applicationstabapplications"></a>[Applicazioni](#tab/applications/)

1. Selezionare **Applicazioni**.
1. Selezionare l'applicazione *webapi1* per aprire la pagina **Proprietà**.
1. Selezionare **Ambiti pubblicati**. Gli ambiti pubblicati possono essere usati per concedere a un'applicazione client determinate autorizzazioni per l'API Web.
1. Immettere `demo.read` come **AMBITO** e `Read access to the web API` come **DESCRIZIONE**.
1. Immettere `demo.write` come **AMBITO** e `Write access to the web API` come **DESCRIZIONE**.
1. Selezionare **Salva**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Selezionare **Registrazioni app (anteprima)** .
1. Selezionare l'applicazione *webapi1* per aprire la pagina **Panoramica**.
1. In **Gestisci** selezionare **Esponi un'API**.
1. Accanto a **URI ID applicazione** selezionare il collegamento **Imposta**.
1. Sostituire il valore predefinito (un GUID) con `api`, quindi selezionare **Salva**. Viene visualizzato l'URI completo, che deve essere nel formato `https://your-tenant-name.onmicrosoft.com/api`. Quando l'applicazione Web richiede un token di accesso per l'API, deve aggiungere questo URI come prefisso per ogni ambito definito per l'API.
1. In **Ambiti definiti da questa API** selezionare **Aggiungi un ambito**.
1. Immettere i valori seguenti per creare un ambito che definisce l'accesso in lettura all'API, quindi selezionare **Aggiungi ambito**:
    1. **Nome ambito**: `demo.read`
    1. **Nome visualizzato per il consenso amministratore**: `Read access to demo API`
    1. **Descrizione del consenso amministratore**: `Allows read access to the demo API`
1. Selezionare **Aggiungi un ambito** , immettere i valori seguenti per aggiungere un ambito che definisce l'accesso in lettura all'API, quindi selezionare **Aggiungi ambito**:
    1. **Nome ambito**: `demo.write`
    1. **Nome visualizzato per il consenso amministratore**: `Write access to demo API`
    1. **Descrizione del consenso amministratore**: `Allows write access to the demo API`