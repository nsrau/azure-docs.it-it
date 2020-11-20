---
title: Cenni preliminari sulle chiavi dei criteri-Azure Active Directory B2C
description: Informazioni sui tipi di chiavi dei criteri di crittografia che è possibile usare in Azure Active Directory B2C per la firma e la convalida di token, segreti client, certificati e password.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30348d7ca12ded2d1f4b0522a7cabeadf0553a07
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953356"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Panoramica delle chiavi dei criteri in Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) archivia i segreti e i certificati sotto forma di chiavi dei criteri per stabilire una relazione di trust con i servizi con cui si integra. Queste attendibilità sono costituite da:

- Provider di identità esterni
- Connessione con i [Servizi API REST](restful-technical-profile.md)
- Firma e crittografia di token

 Questo articolo descrive le informazioni necessarie sulle chiavi dei criteri usate da Azure AD B2C.

> [!NOTE]
> Attualmente, la configurazione delle chiavi dei criteri è limitata solo ai [criteri personalizzati](./custom-policy-get-started.md) .

È possibile configurare segreti e certificati per stabilire una relazione di trust tra i servizi nel portale di Azure nel menu **chiavi dei criteri** . Le chiavi possono essere simmetriche o asimmetriche. La crittografia *simmetrica* o la crittografia a chiave privata è il punto in cui viene usato un segreto condiviso per crittografare e decrittografare i dati. La crittografia *asimmetrica* o la crittografia a chiave pubblica è un sistema crittografico che usa coppie di chiavi, costituite da chiavi pubbliche condivise con l'applicazione relying party e chiavi private note solo Azure ad B2C.

## <a name="policy-keyset-and-keys"></a>Chiavi e keyset dei criteri

La risorsa di primo livello per le chiavi dei criteri in Azure AD B2C è il contenitore **Keyset** . Ogni keyset contiene almeno una **chiave**. Una chiave ha gli attributi seguenti:

| Attributo |  Obbligatoria | Osservazioni |
| --- | --- |--- |
| `use` | Sì | Usage: identifica l'uso previsto della chiave pubblica. Crittografia dei dati `enc` o verifica della firma sui dati `sig` .|
| `nbf`| No | Data e ora di attivazione. |
| `exp`| No | Data e ora di scadenza. |

È consigliabile impostare i valori di attivazione e scadenza della chiave in base agli standard PKI. Potrebbe essere necessario ruotare periodicamente questi certificati per motivi di sicurezza o dei criteri. Ad esempio, è possibile che si disponga di un criterio per ruotare tutti i certificati ogni anno.

Per creare una chiave, è possibile scegliere uno dei metodi seguenti:

- **Manual** : creare un segreto con una stringa definita dall'utente. Il segreto è una chiave simmetrica. È possibile impostare le date di attivazione e scadenza.
- **Generato** : genera automaticamente una chiave. È possibile impostare le date di attivazione e scadenza. Sono disponibili due opzioni:
  - **Secret** : genera una chiave simmetrica.
  - **RSA** -genera una coppia di chiavi (chiavi asimmetriche).
- **Caricare** : caricare un certificato o una chiave PKCS12. Il certificato deve contenere le chiavi pubbliche e private (chiavi asimmetriche).

## <a name="key-rollover"></a>Rollover della chiave

Per motivi di sicurezza, Azure AD B2C possibile eseguire il rollover delle chiavi periodicamente o immediatamente in caso di emergenza. Qualsiasi applicazione, provider di identità o API REST che si integra con Azure AD B2C deve essere preparata a gestire un evento di rollover della chiave, indipendentemente dalla frequenza con cui può verificarsi. In caso contrario, se l'applicazione o Azure AD B2C tenta di utilizzare una chiave scaduta per eseguire un'operazione di crittografia, la richiesta di accesso avrà esito negativo.

Se un keyset Azure AD B2C dispone di più chiavi, solo una delle chiavi è attiva in qualsiasi momento, in base ai criteri seguenti:

- L'attivazione della chiave è basata sulla **Data di attivazione**.
  - Le chiavi vengono ordinate in base alla data di attivazione in ordine crescente. Le chiavi con le date di attivazione successive verranno visualizzate più in basso nell'elenco. Le chiavi senza una data di attivazione si trovano nella parte inferiore dell'elenco.
  - Quando la data e l'ora correnti sono maggiori della data di attivazione di una chiave, Azure AD B2C attiverà la chiave e smetterà di usare la chiave attiva precedente.
- Quando è trascorso il tempo di scadenza della chiave corrente e il contenitore di chiavi contiene una nuova chiave con tempi *non precedenti* e di *scadenza* validi, la nuova chiave diventa attiva automaticamente.
- Quando è trascorso il tempo di scadenza della chiave corrente e il contenitore di chiavi non contiene una nuova chiave con tempi *non precedenti* e di *scadenza* validi, *Azure ad B2C non sarà* in grado di utilizzare la chiave scaduta. Azure AD B2C genererà un messaggio di errore all'interno di un componente dipendente del criterio personalizzato. Per evitare questo problema, è possibile creare una chiave predefinita senza attivazione e data di scadenza come rete di sicurezza.
- L'endpoint della chiave (URI JWKS) dell'endpoint di configurazione noto di OpenId Connect riflette le chiavi configurate nel contenitore di chiavi, quando si fa riferimento alla chiave nel [profilo tecnico JwtIssuer](./jwt-issuer-technical-profile.md). Un'applicazione che usa una libreria OIDC recupererà automaticamente questi metadati per assicurarsi che usi le chiavi corrette per convalidare i token. Per ulteriori informazioni, vedere l'articolo relativo all'utilizzo di [Microsoft Authentication Library](../active-directory/develop/msal-b2c-overview.md), che consente di recuperare automaticamente le chiavi di firma del token più recenti.

## <a name="policy-key-management"></a>Gestione delle chiavi dei criteri

Per ottenere la chiave attiva corrente all'interno di un contenitore di chiavi, usare l'endpoint [getActiveKey](/graph/api/trustframeworkkeyset-getactivekey) dell'API Microsoft Graph.

Per aggiungere o eliminare chiavi di firma e crittografia:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica, nella sezione **Criteri**, selezionare **Framework dell'esperienza di gestione delle identità**.
1. Selezionare le **chiavi dei criteri** 
    1. Per aggiungere una nuova chiave, selezionare **Aggiungi**.
    1. Per rimuovere una nuova chiave, selezionare la chiave e quindi selezionare **Elimina**. Per eliminare la chiave, digitare il nome del contenitore di chiavi da eliminare. Azure AD B2C eliminerà la chiave e creerà una copia della chiave con il suffisso. bak.

### <a name="replace-a-key"></a>Sostituire una chiave

Le chiavi in un keyset non sono sostituibili o rimovibili. Se è necessario modificare una chiave esistente:

- Si consiglia di aggiungere una nuova chiave con la **Data di attivazione** impostata sulla data e sull'ora correnti. Azure AD B2C attiverà la nuova chiave e smetterà di usare la chiave attiva precedente.
- In alternativa, è possibile creare un nuovo keyset con le chiavi corrette. Aggiornare i criteri per l'uso del nuovo keyset, quindi rimuovere il keyset precedente. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare Microsoft Graph per automatizzare la distribuzione di [chiavi di criteri](microsoft-graph-operations.md#trust-framework-policy-key) e [Keyset](microsoft-graph-operations.md#trust-framework-policy-keyset) .