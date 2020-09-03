---
title: Configurare un'istanza e l'autenticazione (portale)
titleSuffix: Azure Digital Twins
description: Vedere come configurare un'istanza del servizio dispositivi digitali gemelli di Azure usando il portale di Azure
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 300a11c36b11230d2bd75534f152a0a5955743ed
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181791"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Configurare un'istanza e l'autenticazione di Azure Digital Twins (portale)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Questo articolo illustra i passaggi per **configurare una nuova istanza di Azure Digital Twins**, inclusa la creazione dell'istanza e la configurazione dell'autenticazione. Al termine dell'articolo, si disporrà di un'istanza di gemelli digitali di Azure pronta per iniziare la programmazione.

Questa versione di questo articolo descrive manualmente questa procedura, una alla volta, usando il portale di Azure. Il portale di Azure è una console unificata basata sul Web che offre un'alternativa agli strumenti da riga di comando.
* Per eseguire questa procedura manualmente usando l'interfaccia della riga di comando, vedere la versione dell'interfaccia della riga di comando di questo articolo: [*procedura: configurare un'istanza e l'autenticazione (CLI)*](how-to-set-up-instance-cli.md).
* Per eseguire un'installazione automatica usando uno script di distribuzione di esempio, vedere la versione con script di questo articolo: [*procedura: configurare un'istanza e l'autenticazione (con script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Creare l'istanza di Azure Digital Twins

In questa sezione si creerà **una nuova istanza di Azure Digital Twins** usando il [portale di Azure](https://ms.portal.azure.com/). Passare al portale e accedere con le proprie credenziali.

Una volta nel portale, iniziare selezionando _Crea una risorsa_ nel menu di Home page dei servizi di Azure.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Selezione di ' crea una risorsa ' dal home page della portale di Azure":::

Cercare i dispositivi *gemelli digitali di Azure* nella casella di ricerca e scegliere il servizio **Azure Digital gemelli (anteprima)** dai risultati. Selezionare il pulsante _Crea_ per creare una nuova istanza del servizio.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Selezione di ' Crea ' dalla pagina del servizio Azure Digital Twins":::

Nella pagina *Crea risorsa* seguente inserire i valori indicati di seguito:
* **Sottoscrizione**: la sottoscrizione di Azure in uso
  - **Gruppo di risorse**: gruppo di risorse in cui distribuire l'istanza. Se non è già presente un gruppo di risorse esistente, è possibile crearne uno selezionando il collegamento *Crea nuovo* e immettendo un nome per un nuovo gruppo di risorse
* **Location**: area abilitata per i dispositivi gemelli di Azure per la distribuzione. Per altri dettagli sul supporto regionale, visita i [*prodotti Azure disponibili in base all'area (gemelli digitali di Azure)*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* **Nome della risorsa**: un nome per l'istanza di Azure Digital gemelli. Il nome della nuova istanza deve essere univoco all'interno dell'area per la sottoscrizione. Ciò significa che se la sottoscrizione ha un'altra istanza di Azure Digital gemelli nell'area che usa già il nome scelto, verrà chiesto di selezionare un nome diverso.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Inserire i valori descritti per creare una risorsa di dispositivi gemelli digitali di Azure":::

Al termine, selezionare _Verifica + crea_. Verrà visualizzata una pagina di riepilogo in cui è possibile esaminare i dettagli dell'istanza immessi e quindi fare clic su _Crea_. 

### <a name="verify-success-and-collect-important-values"></a>Verificare l'esito positivo e raccogliere i valori importanti

Dopo aver premuto *Crea*, è possibile visualizzare lo stato della distribuzione dell'istanza nelle notifiche di Azure lungo la barra delle icone del portale. La notifica indicherà quando la distribuzione ha avuto esito positivo e sarà possibile selezionare il pulsante _Vai alla risorsa_ per visualizzare l'istanza creata.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Visualizzazione delle notifiche di Azure che mostrano una distribuzione riuscita ed evidenziano il pulsante Vai alla risorsa":::

In alternativa, se la distribuzione ha esito negativo, la notifica indicherà il motivo. Osservare il suggerimento del messaggio di errore e riprovare a creare l'istanza.

>[!TIP]
>Una volta creata l'istanza, è possibile tornare alla relativa pagina in qualsiasi momento cercando il nome dell'istanza nella barra di ricerca portale di Azure.

Dalla pagina *Panoramica* dell'istanza, annotare il *nome*, il *gruppo di risorse*e il *nome host*. Questi sono tutti i valori importanti che potrebbero essere necessari quando si continua a usare l'istanza di Azure Digital gemelli. Se altri utenti eseguiranno la programmazione in base all'istanza, è necessario condividerli con questi valori.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Evidenziazione dei valori importanti dalla pagina Panoramica dell'istanza":::

A questo punto si ha un'istanza di Azure Digital Twins pronta per l'uso. Successivamente, si forniranno le autorizzazioni utente di Azure appropriate per gestirlo.

## <a name="set-up-user-access-permissions"></a>Configurare le autorizzazioni di accesso utente

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Prima di tutto, aprire la pagina per l'istanza di Azure Digital Twins nell'portale di Azure. Selezionare *controllo di accesso (IAM)* dal menu dell'istanza. Selezionare il pulsante  *Aggiungi* in *Aggiungi un'assegnazione di ruolo*.

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Selezione per aggiungere un'assegnazione di ruolo dalla pagina controllo di accesso (IAM)":::

Nella pagina *Aggiungi assegnazione ruolo* , inserire i valori (che devono essere completati da un utente con [autorizzazioni sufficienti](#prerequisites-permission-requirements) nella sottoscrizione di Azure):
* **Ruolo**: selezionare il *proprietario di Azure Digital gemelli (anteprima)* dal menu a discesa
* **Assegnare l'accesso a**: selezionare *Azure ad utente, gruppo o entità servizio* dal menu a discesa
* **Select**: consente di cercare il nome o l'indirizzo di posta elettronica dell'utente da assegnare. Quando si seleziona il risultato, l'utente viene visualizzato nella sezione *membri selezionati* .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="Compilazione dei campi elencati nella finestra di dialogo ' Aggiungi assegnazione ruolo '":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Al termine dell'immissione dei dettagli, fare clic sul pulsante *Salva* .

### <a name="verify-success"></a>Verificare l'esito positivo

È possibile visualizzare l'assegnazione di ruolo configurata in *controllo di accesso (IAM) > assegnazioni di ruolo*. L'utente dovrebbe essere visualizzato nell'elenco con un ruolo di proprietario dei dispositivi *gemelli digitali di Azure (anteprima)*. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Visualizzazione delle assegnazioni di ruolo per un'istanza di dispositivi gemelli digitali di Azure in portale di Azure":::

A questo punto si ha un'istanza di Azure Digital Twins pronta per l'uso e sono state assegnate le autorizzazioni per gestirla. Successivamente, si imposteranno le autorizzazioni per l'accesso di un'app client.

## <a name="set-up-access-permissions-for-client-applications"></a>Configurare le autorizzazioni di accesso per le applicazioni client

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Per iniziare, passare a [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) nell'portale di Azure (è possibile usare questo collegamento o trovarlo nella barra di ricerca del portale). Selezionare *registrazioni app* dal menu servizio e quindi *+ nuova registrazione*.

:::image type="content" source="media/how-to-set-up-instance/portal/new-registration.png" alt-text="Visualizzazione della pagina del servizio Azure AD nell'portale di Azure, evidenziando l'opzione di menu ' Registrazioni app ' e il pulsante ' + nuova registrazione '":::

Nella pagina *registra un'applicazione* che segue compilare i valori richiesti:
* **Nome**: un Azure ad nome visualizzato dell'applicazione da associare alla registrazione
* **Tipi di account supportati**: selezionare *solo gli account in questa directory organizzativa (solo directory predefinita-tenant singolo)*
* **URI di reindirizzamento**: *URL di risposta dell'applicazione Azure ad* per l'applicazione Azure ad. Aggiungere un URI *public client/native (mobile & desktop)* per `http://localhost` .

Al termine, fare clic sul pulsante *Register (registra* ).

:::image type="content" source="media/how-to-set-up-instance/portal/register-an-application.png" alt-text="Visualizzazione della pagina ' registra un'applicazione ' con i valori descritti compilati":::

Al termine dell'impostazione della registrazione, il portale reindirizza alla pagina dei dettagli.

### <a name="provide-azure-digital-twins-api-permission"></a>Fornire l'autorizzazione API per i dispositivi digitali gemelli di Azure

Successivamente, configurare la registrazione dell'app creata con le autorizzazioni di base per le API dei dispositivi gemelli digitali di Azure.

Dalla pagina del portale per la registrazione dell'app, selezionare *autorizzazioni API* dal menu. Nella pagina autorizzazioni seguenti, fare clic sul pulsante *+ Aggiungi un'autorizzazione* .

:::image type="content" source="media/how-to-set-up-instance/portal/add-permission.png" alt-text="Visualizzazione della registrazione dell'app nel portale di Azure, evidenziando l'opzione di menu ' autorizzazioni API ' e il pulsante ' + Aggiungi un'autorizzazione '":::

Nella pagina *autorizzazioni API richiesta* seguente passare alle API usate dall' *organizzazione* e cercare i dispositivi *gemelli digitali di Azure*. Selezionare *Azure Digital gemelli* dai risultati della ricerca per procedere con l'assegnazione delle autorizzazioni per le API dei dispositivi gemelli digitali di Azure.

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-1.png" alt-text="Visualizzazione dei risultati della ricerca nella pagina autorizzazioni API richieste con i dispositivi gemelli digitali di Azure":::

>[!NOTE]
> Se si ha un'istanza di Azure Digital Twins esistente dalla precedente versione di anteprima pubblica del servizio (prima del 2020 luglio) ancora nella sottoscrizione, sarà necessario cercare il *servizio Azure Smart Spaces* . Si tratta di un nome precedente per lo stesso set di API e l'esperienza non verrà modificata oltre questo passaggio.

Selezionare quindi le autorizzazioni da concedere per le API. Espandere l'autorizzazione **lettura (1)** e selezionare la casella *Read. Write* per concedere le autorizzazioni di lettura e scrittura per la registrazione dell'app.

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-2.png" alt-text="Visualizzazione della pagina autorizzazioni API richiesta selezionando le autorizzazioni di lettura. scrittura per le API gemelle digitali di Azure":::

Al termine, fare clic su *Aggiungi autorizzazioni* .

### <a name="verify-success"></a>Verificare l'esito positivo

Tornare alla pagina *autorizzazioni API* , verificare che sia ora disponibile una voce per i dispositivi gemelli digitali di Azure che riflettono le autorizzazioni di lettura/scrittura:

:::image type="content" source="media/how-to-set-up-instance/portal/verify-api-permissions.png" alt-text="Visualizzazione del portale delle autorizzazioni API per la registrazione dell'app Azure AD, che mostra l'accesso in lettura/scrittura per i dispositivi gemelli digitali di Azure":::

È anche possibile verificare la connessione ai dispositivi gemelli digitali di Azure entro la *manifest.js*della registrazione dell'app in, che è stata aggiornata automaticamente con le informazioni di Azure Digital gemelli quando sono state aggiunte le autorizzazioni dell'API.

A tale scopo, selezionare *manifesto* dal menu per visualizzare il codice manifesto della registrazione dell'app. Scorrere fino alla fine della finestra del codice e cercare questi campi sotto `requiredResourceAccess` . I valori devono corrispondere a quelli dello screenshot seguente:

:::image type="content" source="media/how-to-set-up-instance/portal/verify-manifest.png" alt-text="Visualizzazione del portale del manifesto per la registrazione dell'app Azure AD. Annidato in ' requiredResourceAccess ', esiste un valore ' resourceAppId ' di 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 e un valore ' resourceAccess > ID ' di 4589bd03-58cb-4e6c-B17F-b580e39652f8":::

### <a name="collect-important-values"></a>Raccogli valori importanti

Selezionare quindi *Panoramica* dalla barra dei menu per visualizzare i dettagli della registrazione dell'app:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Visualizzazione del portale dei valori importanti per la registrazione dell'app":::

Prendere nota dell'ID dell' *applicazione (client)* e della *Directory (tenant)* visualizzati nella **pagina.** Questi valori saranno necessari in un secondo momento per [autenticare un'app client sulle API dei dispositivi gemelli digitali di Azure](how-to-authenticate-client.md). Se non si è la persona che scriverà il codice per tali applicazioni, è necessario condividere questi valori con la persona che lo sarà.

### <a name="other-possible-steps-for-your-organization"></a>Altri passaggi possibili per l'organizzazione

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Passaggi successivi

Vedere come connettere l'applicazione client all'istanza scrivendo il codice di autenticazione dell'app client:
* [*Procedura: scrivere codice di autenticazione dell'app*](how-to-authenticate-client.md)
