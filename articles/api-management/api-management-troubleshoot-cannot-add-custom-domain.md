---
title: Impossibile aggiungere un dominio personalizzato utilizzando il certificato dell'insieme di credenziali delle chiavi
titleSuffix: Azure API Management
description: Informazioni su come risolvere il problema in cui non è possibile aggiungere un dominio personalizzato in Gestione API di Azure usando un certificato dell'insieme di credenziali delle chiavi.
services: api-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: a09c15466a4a9f62b2696b087cb7ab23cc767379
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430584"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>Impossibile aggiornare i nomi host del servizio Gestione API

In questo articolo viene descritto l'errore "Impossibile aggiornare i nomi host del servizio Gestione API" che può verificarsi quando si aggiunge un dominio personalizzato per il servizio Gestione API di Azure.This article describes the "Failed to update API Management service hostnames" that you may experience when you add a custom domain for the Azure API Management service. In questo articolo viene illustrata la procedura di risoluzione dei problemi per risolvere il problema.

## <a name="symptoms"></a>Sintomi

Quando si tenta di aggiungere un dominio personalizzato per il servizio Gestione API utilizzando un certificato da Azure Key Vault, viene visualizzato il seguente messaggio di errore:

- Impossibile aggiornare i nomi host del servizio Gestione API. Richiesta allahttps://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0risorsa ' ' non riuscita con StatusCode: non consentito per RequestId: . Messaggio di eccezione: Operazione ha restituito un codice di stato non valido 'Accesso negato'.

## <a name="cause"></a>Causa

Il servizio Gestione API non dispone dell'autorizzazione per accedere all'insieme di credenziali delle chiavi che si sta tentando di utilizzare per il dominio personalizzato.

## <a name="solution"></a>Soluzione

Per risolvere il problema, seguire questa procedura:

1. Passare al [portale di Azure](Https://portal.azure.com), selezionare l'istanza di Gestione API e quindi selezionare **Identità gestite**. Assicurarsi che l'opzione **Registra con Azure Active Directory** sia impostata su **Sì**. 
    ![Registrazione con Azure Active Director](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. Nel portale di Azure aprire il servizio **insiemi** di chiavi e selezionare l'insieme di credenziali delle chiavi che si sta tentando di usare per il dominio personalizzato.
1. Selezionare Criteri di **accesso**e verificare se è presente un'entità servizio corrispondente al nome dell'istanza del servizio Gestione API. In caso affermativo, selezionare l'entità servizio e assicurarsi che disponga dell'autorizzazione **Get** elencata in **Autorizzazioni segrete**.  
    ![Aggiunta di criteri di accesso per l'entità servizioAdding access policy for service principal](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. Se il servizio Gestione API non è presente nell'elenco, selezionare **Aggiungi criteri**di accesso e quindi creare i criteri di accesso seguenti:
    - **Configura da modello**: Nessuno
    - **Seleziona entità**: Cercare il nome del servizio Gestione API, quindi selezionarlo dall'elenco
    - **Autorizzazioni chiave**: Nessuna
    - **Permessi segreti**: Ottenere
    - **Autorizzazioni certificato**: Nessuna
1. Selezionare **OK** per creare il criterio di accesso.
1. Selezionare **Salva** per salvare le modifiche.

Controllare se il problema è stato risolto. A tale scopo, provare a creare il dominio personalizzato nel servizio Gestione API utilizzando il certificato dell'insieme di credenziali delle chiavi.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sul servizio Gestione API:Learn more about API Management service:

- Altre informazioni sui [video](https://azure.microsoft.com/documentation/videos/index/?services=api-management) relativi a Gestione API.
* Per altri metodi di protezione del servizio back-end, vedere [Autenticazione reciproca dei certificati](api-management-howto-mutual-certificates.md).

* [Creare un'istanza](get-started-create-service-instance.md)del servizio Gestione API.
* [Gestire la prima API](import-and-publish.md).