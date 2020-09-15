---
title: "Avvio rapido: Registrare ed esporre un'API Web | Azure"
titleSuffix: Microsoft identity platform
description: Questo argomento di avvio rapido illustra come registrare un'API Web con Microsoft Identity Platform e configurarne gli ambiti, esponendola ai client per l'accesso basato su autorizzazioni alle risorse dell'API.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperfq1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 72d66bd4c738ed60bbaefc123daae90ecc0db163
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442147"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Avvio rapido: Configurare un'applicazione per esporre un'API Web

Questo argomento di avvio rapido illustra come registrare un'API Web con Microsoft Identity Platform ed esporla alle app client aggiungendo un ambito di esempio. Registrando l'API Web ed esponendola tramite gli ambiti, è possibile fornire l'accesso basato sulle autorizzazioni alle relative risorse non solo agli utenti autorizzati, ma anche alle app client che accedono all'API.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva ([creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* Completamento di [Avvio rapido: Configurare un tenant](quickstart-create-new-tenant.md)

## <a name="register-the-web-api"></a>Registrare l'API Web

Per fornire l'accesso con ambito alle risorse nell'API Web, è prima di tutto necessario registrare l'API con Microsoft Identity Platform.

1. Seguire la procedura descritta nella sezione **Registrare un'applicazione** di [Avvio rapido: Registrare un'app con Microsoft Identity Platform](quickstart-register-app.md).
1. Ignorare le sezioni **Aggiungere un URI di reindirizzamento** e **Configurare le impostazioni della piattaforma**. Non è necessario configurare un URI di reindirizzamento per un'API Web perché nessun utente è connesso in modo interattivo.
1. Per il momento ignorare la sezione **Aggiungere le credenziali**. L'API richiede le proprie credenziali solo se accede a un'API downstream, uno scenario che non viene trattato in questo articolo.

Dopo aver registrato l'API Web, è possibile aggiungere gli ambiti che il codice API può usare per fornire l'autorizzazione granulare ai consumer dell'API.

## <a name="add-a-scope"></a>Aggiungere un ambito

Per richiedere l'autorizzazione per eseguire le operazioni definite dall'API Web, il codice in un'applicazione client passa un token di accesso unitamente alle relative richieste alla risorsa protetta, ovvero l'API Web. L'API Web esegue quindi l'operazione richiesta solo se il token di accesso ricevuto contiene gli ambiti necessari per l'operazione.

Seguire innanzitutto questa procedura per creare un ambito di esempio denominato `Employees.Read.All`:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/quickstart-configure-app-expose-web-apis/portal-01-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant che contiene la registrazione dell'app client.
1. Selezionare **Azure Active Directory** > **Registrazioni app** e quindi selezionare la registrazione dell'app dell'API.
1. Selezionare **Esporre un'API** > **Aggiungi un ambito**.

    :::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-02-expose-api.png" alt-text="Riquadro Esporre un'API di una registrazione dell'app nel portale di Azure":::

1. Se non è stato ancora configurato, viene richiesto di impostare un valore per **URI ID applicazione**.

   L'URI dell'ID applicazione funge da prefisso per gli ambiti a cui verrà fatto riferimento nel codice dell'API e deve essere univoco a livello globale. È possibile usare il valore predefinito fornito, in formato `api://<application-client-id>`, oppure specificare un URI più leggibile, ad esempio `https://contoso.com/api`.

1. Specificare quindi gli attributi dell'ambito nel riquadro **Aggiungi un ambito**. Per questa procedura dettagliata è possibile usare i valori di esempio o specificare valori personalizzati.

    | Campo | Descrizione | Esempio |
    |-------|-------------|---------|
    | **Nome ambito** | Nome dell'ambito. Una convenzione di denominazione standard è `resource.operation.constraint`. | `Employees.Read.All` |
    | **Utenti che possono fornire il consenso** | Indica se il consenso per l'ambito può essere fornito dagli utenti oppure se è necessario il consenso amministratore. Selezionare **Solo amministratori** per autorizzazioni con privilegi più elevati. | **Amministratori e utenti** |
    | **Nome visualizzato per il consenso amministratore** | Breve descrizione dello scopo dell'ambito che verrà visualizzata solo dagli amministratori. | `Read-only access to Employee records` |
    | **Descrizione del consenso amministratore** | Descrizione più dettagliata dell'autorizzazione concessa dall'ambito che verrà visualizzata solo dagli amministratori. | `Allow the application to have read-only access to all Employee data.` |
    | **Nome visualizzato per il consenso utente** | Breve descrizione dello scopo dell'ambito. Viene visualizzata agli utenti solo se si imposta **Utenti autorizzati al consenso** su **Amministratori e utenti**. | `Read-only access to your Employee records` |
    | **Descrizione del consenso utente** | Descrizione più dettagliata dell'autorizzazione concessa dall'ambito. Viene visualizzata agli utenti solo se si imposta **Utenti autorizzati al consenso** su **Amministratori e utenti**. | `Allow the application to have read-only access to your Employee data.` |

1. Impostare **Stato** su **Abilitato** e quindi selezionare **Aggiungi ambito**.

1. (Facoltativo) Per rimuovere la richiesta del consenso degli utenti dell'app per gli ambiti definiti, è possibile *pre-autorizzare* l'accesso dell'applicazione client all'API Web. Pre-autorizzare *solo* le applicazioni client attendibili, perché gli utenti non hanno la possibilità di rifiutare il consenso.
    1. In **Applicazioni client autorizzate** selezionare **Aggiungi applicazione client**
    1. Immettere il valore di **ID applicazione (client)** dell'applicazione client da pre-autorizzare, ad esempio quello di un'applicazione Web già registrata.
    1. In **Ambiti autorizzati**selezionare gli ambiti per cui rimuovere la richiesta di consenso, quindi selezionare **Aggiungi applicazione**.

    Se è stato eseguito questo passaggio facoltativo, l'app client è ora un'app client pre-autorizzata e agli utenti non verrà richiesto il consenso per accedervi.

## <a name="add-a-scope-requiring-admin-consent"></a>Aggiungere un ambito che richiede il consenso dell'amministratore

Aggiungere quindi un altro ambito di esempio denominato `Employees.Write.All` per il quale solo gli amministratori possono concedere il consenso. Gli ambiti che richiedono il consenso dell'amministratore vengono usati in genere per fornire l'accesso a operazioni con privilegi più elevati e spesso da applicazioni client che vengono eseguite come servizi back-end o daemon che non eseguono l'accesso utente interattivo.

Per aggiungere l'ambito di esempio `Employees.Write.All`, seguire la procedura descritta nella sezione [Aggiungere un ambito](#add-a-scope) e specificare questi valori nel riquadro **Aggiungi un ambito**:

| Campo                          | Valore di esempio                                                      |
|--------------------------------|--------------------------------------------------------------------|
| **Nome ambito**                 | `Employees.Write.All`                                              |
| **Utenti che possono fornire il consenso**            | **Solo amministratori**                                                    |
| **Nome visualizzato per il consenso amministratore** | `Write access to Employee records`                                 |
| **Descrizione del consenso amministratore**  | `Allow the application to have write access to all Employee data.` |
| **Nome visualizzato per il consenso utente**  | *Nessuno (lasciare vuoto)*                                               |
| **Descrizione del consenso utente**   | *Nessuno (lasciare vuoto)*                                               |

## <a name="verify-the-exposed-scopes"></a>Verificare gli ambiti esposti

Se sono stati aggiunti entrambi, gli ambiti di esempio descritti nelle sezioni precedenti verranno visualizzati nel riquadro **Esporre un'API** della registrazione dell'app dell'API Web, come illustrato nell'immagine seguente:

:::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-03-scopes-list.png" alt-text="Screenshot del riquadro Esporre un'API che mostra due ambiti esposti.":::

Come illustrato nell'immagine, la stringa completa di un ambito è costituita dalla concatenazione del valore **URI ID applicazione** dell'API Web e del valore di **Nome ambito** dell'ambito.

Se, ad esempio, l'URI ID applicazione dell'API Web è `https://contoso.com/api` e il nome dell'ambito è `Employees.Read.All`, l'ambito completo è:

`https://contoso.com/api/Employees.Read.All`

## <a name="using-the-exposed-scopes"></a>Uso degli ambiti esposti

Nell'articolo successivo della serie viene configurata la registrazione di un'app client con l'accesso all'API Web e gli ambiti definiti seguendo la procedura descritta in questo articolo.

Dopo che a una registrazione dell'app del client è stata concessa l'autorizzazione per accedere all'API Web, Microsoft Identity Platform può rilasciare al client un token di accesso OAuth 2.0. Quando il client chiama l'API Web, presenta un token di accesso con attestazione di ambito (`scp`) impostata sulle autorizzazioni specificate nella registrazione dell'app del client.

Se necessario, è possibile esporre altri ambiti successivamente. Tenere presente che l'API Web può esporre più ambiti associati a operazioni diverse. La risorsa può controllare l'accesso all'API Web in fase di esecuzione, valutando le attestazioni di ambito (`scp`) nel token di accesso OAuth 2.0 ricevuto.

## <a name="next-steps"></a>Passaggi successivi

Ora che l'API Web è stata esposta configurandone gli ambiti, configurare la registrazione dell'app client con l'autorizzazione per accedere agli ambiti.

> [!div class="nextstepaction"]
> [Configurare la registrazione di un'app per l'accesso all'API Web](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
