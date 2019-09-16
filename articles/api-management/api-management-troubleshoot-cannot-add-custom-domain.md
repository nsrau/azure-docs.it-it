---
title: Non è possibile aggiungere un dominio personalizzato usando Key Vault certificato in gestione API di Azure | Microsoft Docs
description: Informazioni su come risolvere il problema per cui non è possibile aggiungere un dominio personalizzato in gestione API di Azure usando un certificato di Key Vault.
services: api-management
documentationcenter: ''
author: genlin
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: 30dfd864c6c237024c72325da813691ac5043bd0
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072082"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>Non è stato possibile aggiornare i nomi host del servizio gestione API

Questo articolo descrive l'errore "non è stato possibile aggiornare i nomi host del servizio gestione API" che potrebbe verificarsi quando si aggiunge un dominio personalizzato per il servizio gestione API di Azure. Questo articolo illustra la procedura di risoluzione dei problemi per risolvere il problema.

## <a name="symptoms"></a>Sintomi

Quando si tenta di aggiungere un dominio personalizzato per il servizio gestione API utilizzando un certificato di Azure Key Vault, viene visualizzato il messaggio di errore seguente:

- Non è stato possibile aggiornare i nomi host del servizio gestione API. La richiesta alla risorsa 'https://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0 ' non è riuscita con StatusCode: Non consentito per RequestId:. Messaggio eccezione: L'operazione ha restituito un codice di stato non valido ' Forbidden '.

## <a name="cause"></a>Causa

Il servizio gestione API non è autorizzato ad accedere all'insieme di credenziali delle chiavi che si sta tentando di usare per il dominio personalizzato.

## <a name="solution"></a>Soluzione

Per risolvere il problema, seguire questa procedura:

1. Passare alla [portale di Azure](Https://portal.azure.com), selezionare l'istanza di gestione API e quindi selezionare **identità gestite**. Verificare che l'opzione **registra con Azure Active Directory** sia impostata su **Sì**. 
    ![Registrazione con Azure Active Directory](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. Nel portale di Azure aprire il servizio insiemi di credenziali delle **chiavi** e selezionare l'insieme di credenziali delle chiavi che si sta provando a usare per il dominio personalizzato.
1. Selezionare **criteri di accesso**e verificare se esiste un'entità servizio che corrisponde al nome dell'istanza del servizio gestione API. In caso contrario, selezionare l'entità servizio e assicurarsi che disponga dell'autorizzazione **Get** elencata in **autorizzazioni segrete**.  
    ![Aggiunta dei criteri di accesso per l'entità servizio](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. Se il servizio gestione API non è presente nell'elenco, selezionare **Aggiungi criteri di accesso**e quindi creare i criteri di accesso seguenti:
    - **Configura da modello**: Nessuna
    - **Selezionare l'entità**di protezione: Cercare il nome del servizio gestione API e quindi selezionarlo dall'elenco
    - **Autorizzazioni chiave**: Nessuna
    - **Autorizzazioni segrete**: Recupera
    - **Autorizzazioni**per i certificati: Nessuna
1. Selezionare **OK** per creare i criteri di accesso.
1. Selezionare **Salva** per salvare le modifiche.

Controllare se il problema è stato risolto. A tale scopo, provare a creare il dominio personalizzato nel servizio gestione API utilizzando il certificato Key Vault.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sul servizio gestione API:

- Altre informazioni sui [video](https://azure.microsoft.com/documentation/videos/index/?services=api-management) relativi a Gestione API.
* Per altri metodi di protezione del servizio back-end, vedere [Autenticazione reciproca dei certificati](api-management-howto-mutual-certificates.md).

* [Creare una nuova istanza del servizio Gestione API di Azure](get-started-create-service-instance.md).
* [Gestire la prima API](import-and-publish.md).