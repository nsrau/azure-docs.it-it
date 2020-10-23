---
title: Creare la registrazione di un'app
titleSuffix: Azure Digital Twins
description: Vedere come creare una registrazione dell'app Azure AD, come opzione di autenticazione per le app client.
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f252471cd3cd7e3a950bf2cfe324e580da129041
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210168"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Creare una registrazione per l'app da usare con i dispositivi gemelli digitali di Azure

Quando si lavora con un'istanza di dispositivi gemelli digitali di Azure, è comune interagire con tale istanza tramite le applicazioni client, ad esempio un'app client personalizzata o un esempio come [ADT Explorer](quickstart-adt-explorer.md). Tali applicazioni devono eseguire l'autenticazione con i dispositivi gemelli digitali di Azure per interagire con esso e alcuni dei [meccanismi di autenticazione](how-to-authenticate-client.md) che possono essere usati dalle app comportano una **registrazione dell'app** [Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md) .

Questa operazione non è necessaria per tutti gli scenari di autenticazione. Tuttavia, se si usa una strategia di autenticazione o un esempio di codice che richiede la registrazione di un'app, inclusi un **ID client** e un **ID tenant**, questo articolo illustra come configurarne uno.

## <a name="using-azure-ad-app-registrations"></a>Uso delle registrazioni dell'app Azure AD

[Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md) è il servizio Microsoft di gestione delle identità e degli accessi basato sul cloud. La configurazione di una **registrazione di app** in Azure ad è un modo per concedere a un'app client l'accesso ai dispositivi gemelli digitali di Azure.

Questa registrazione dell'app è la posizione in cui vengono configurate le autorizzazioni di accesso alle [API di Azure Digital gemelli](how-to-use-apis-sdks.md) Successivamente, le app client possono eseguire l'autenticazione nella registrazione dell'app usando i **valori di ID client e tenant**della registrazione e, di conseguenza, vengono concesse le autorizzazioni di accesso configurate alle API.

>[!TIP]
> Potrebbe essere preferibile configurare una nuova registrazione dell'app ogni volta che ne serve uno *o* eseguire questa operazione una sola volta, stabilendo una singola registrazione dell'app che verrà condivisa tra tutti gli scenari che lo richiedono.

## <a name="create-the-registration"></a>Creare la registrazione

Per iniziare, passare a [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) nell'portale di Azure (è possibile usare questo collegamento o trovarlo nella barra di ricerca del portale). Selezionare *registrazioni app* dal menu servizio e quindi *+ nuova registrazione*.

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Visualizzazione della pagina del servizio Azure AD nell'portale di Azure, evidenziando l'opzione di menu ' Registrazioni app ' e il pulsante ' + nuova registrazione '":::

Nella pagina *registra un'applicazione* che segue compilare i valori richiesti:
* **Nome**: un Azure ad nome visualizzato dell'applicazione da associare alla registrazione
* **Tipi di account supportati**: selezionare *solo gli account in questa directory organizzativa (solo directory predefinita-tenant singolo)*
* **URI di reindirizzamento**: *URL di risposta dell'applicazione Azure ad* per l'applicazione Azure ad. Aggiungere un URI *public client/native (mobile & desktop)* per `http://localhost` .

Al termine, fare clic sul pulsante *Register (registra* ).

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Visualizzazione della pagina del servizio Azure AD nell'portale di Azure, evidenziando l'opzione di menu ' Registrazioni app ' e il pulsante ' + nuova registrazione '":::

Al termine dell'impostazione della registrazione, il portale reindirizza alla pagina dei dettagli.

## <a name="collect-client-id-and-tenant-id"></a>Raccogli ID client e ID tenant

Raccogliere quindi alcuni valori importanti sulla registrazione dell'app dalla pagina dei dettagli:

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Visualizzazione della pagina del servizio Azure AD nell'portale di Azure, evidenziando l'opzione di menu ' Registrazioni app ' e il pulsante ' + nuova registrazione '":::

Prendere nota dell'ID dell' _**applicazione (client)**_ e della _**Directory (tenant)**_ visualizzati nella **pagina.** Questi sono i valori che un'app client dovrà usare per l'autenticazione con i dispositivi gemelli digitali di Azure.

## <a name="provide-azure-digital-twins-api-permission"></a>Fornire l'autorizzazione API per i dispositivi digitali gemelli di Azure

Successivamente, configurare la registrazione dell'app creata con le autorizzazioni di base per le API dei dispositivi gemelli digitali di Azure.

Dalla pagina del portale per la registrazione dell'app, selezionare *autorizzazioni API* dal menu. Nella pagina autorizzazioni seguenti, fare clic sul pulsante *+ Aggiungi un'autorizzazione* .

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Visualizzazione della pagina del servizio Azure AD nell'portale di Azure, evidenziando l'opzione di menu ' Registrazioni app ' e il pulsante ' + nuova registrazione '":::

Nella pagina *autorizzazioni API richiesta* seguente passare alle API usate dall' *organizzazione* e cercare i dispositivi *gemelli digitali di Azure*. Selezionare _**Azure Digital gemelli**_ dai risultati della ricerca per procedere con l'assegnazione delle autorizzazioni per le API dei dispositivi gemelli digitali di Azure.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Visualizzazione della pagina del servizio Azure AD nell'portale di Azure, evidenziando l'opzione di menu ' Registrazioni app ' e il pulsante ' + nuova registrazione '":::

>[!NOTE]
> Se la sottoscrizione ha ancora un'istanza di Azure Digital Twins esistente dalla precedente anteprima pubblica del servizio (prima del 2020 luglio), è necessario cercare e selezionare il _**servizio Azure Smart Spaces**_ . Si tratta di un nome precedente per lo stesso set di API (si noti che l' *ID dell'applicazione (client)* è identico a quello della schermata precedente) e l'esperienza non verrà modificata oltre questo passaggio.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Visualizzazione della pagina del servizio Azure AD nell'portale di Azure, evidenziando l'opzione di menu ' Registrazioni app ' e il pulsante ' + nuova registrazione '":::

Selezionare quindi le autorizzazioni da concedere per le API. Espandere l'autorizzazione **lettura (1)** e selezionare la casella *Read. Write* per concedere le autorizzazioni di lettura e scrittura per la registrazione dell'app.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Visualizzazione della pagina del servizio Azure AD nell'portale di Azure, evidenziando l'opzione di menu ' Registrazioni app ' e il pulsante ' + nuova registrazione '":::

Al termine, fare clic su *Aggiungi autorizzazioni* .

### <a name="verify-success"></a>Verificare l'esito positivo

Nella pagina *autorizzazioni API* verificare che sia presente una voce per i dispositivi gemelli digitali di Azure che riflettono le autorizzazioni di lettura/scrittura:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Visualizzazione della pagina del servizio Azure AD nell'portale di Azure, evidenziando l'opzione di menu ' Registrazioni app ' e il pulsante ' + nuova registrazione '":::

È anche possibile verificare la connessione ai dispositivi gemelli digitali di Azure entro la *manifest.js*della registrazione dell'app in, che è stata aggiornata automaticamente con le informazioni di Azure Digital gemelli quando sono state aggiunte le autorizzazioni dell'API.

A tale scopo, selezionare *manifesto* dal menu per visualizzare il codice manifesto della registrazione dell'app. Scorrere fino alla fine della finestra del codice e cercare questi campi sotto `requiredResourceAccess` . I valori devono corrispondere a quelli dello screenshot seguente:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Visualizzazione della pagina del servizio Azure AD nell'portale di Azure, evidenziando l'opzione di menu ' Registrazioni app ' e il pulsante ' + nuova registrazione '":::

Se questi valori sono mancanti, ripetere i passaggi nella [sezione per aggiungere l'autorizzazione API](#provide-azure-digital-twins-api-permission).

## <a name="other-possible-steps-for-your-organization"></a>Altri passaggi possibili per l'organizzazione

È possibile che l'organizzazione richieda azioni aggiuntive da proprietari o amministratori di sottoscrizioni per configurare correttamente la registrazione di un'app. I passaggi necessari possono variare a seconda delle impostazioni specifiche dell'organizzazione.

Di seguito sono riportate alcune possibili attività comuni che possono essere eseguite da un proprietario/amministratore della sottoscrizione. Queste e altre operazioni possono essere eseguite dalla pagina [*registrazioni app Azure ad*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) nel portale di Azure.
* Concedere il consenso dell'amministratore per la registrazione dell'app. È possibile che l'organizzazione abbia il *consenso dell'amministratore necessario* attivato a livello globale in Azure ad per tutte le registrazioni di app all'interno della sottoscrizione. In tal caso, il proprietario o l'amministratore dovrà selezionare questo pulsante per la società nella pagina *delle autorizzazioni dell'API* di registrazione dell'app affinché la registrazione dell'app sia valida:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Visualizzazione della pagina del servizio Azure AD nell'portale di Azure, evidenziando l'opzione di menu ' Registrazioni app ' e il pulsante ' + nuova registrazione '":::
  - Se il consenso è stato concesso correttamente, la voce per i dispositivi gemelli digitali di Azure dovrebbe visualizzare un valore di *stato* _concesso per **(la società)** _
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Visualizzazione della pagina del servizio Azure AD nell'portale di Azure, evidenziando l'opzione di menu ' Registrazioni app ' e il pulsante ' + nuova registrazione '":::
* Attivare l'accesso client pubblico
* Impostare URL di risposta specifici per l'accesso Web e desktop
* Consenti i flussi di autenticazione OAuth2 impliciti

Per altre informazioni sulla registrazione dell'app e sulle diverse opzioni di installazione, vedere [*registrare un'applicazione con la piattaforma di identità Microsoft*](/graph/auth-register-app-v2).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo viene configurata una registrazione di Azure AD app che può essere usata per autenticare le applicazioni client con le API dei dispositivi gemelli digitali di Azure.

Leggere quindi i meccanismi di autenticazione, inclusi quelli che usano registrazioni per l'app e altri che non:
* [*Procedura: scrivere codice di autenticazione dell'app*](how-to-authenticate-client.md)