---
title: Esempio hero di chiamata di gruppo
titleSuffix: An Azure Communication Services sample overview
description: Panoramica dell'esempio hero di chiamata con Servizi di comunicazione di Azure, che offre agli sviluppatori altre informazioni sui meccanismi interni dell'esempio.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e6cbf840fa27755fa43eb2a81e752415562ce41f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461142"
---
# <a name="get-started-with-the-group-calling-hero-sample"></a>Introduzione all'esempio hero di chiamata di gruppo

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> Add links to our Hero Sample repo when the sample is publicly available.
---->

> [!IMPORTANT]
> [Questo esempio è disponibile su GitHub.](https://github.com/Azure-Samples/communication-services-web-calling-hero)

L' **esempio hero di chiamata di gruppo** di Servizi di comunicazione di Azure illustra come usare la libreria client Web Chiamate di Servizi di comunicazione per creare un'esperienza di chiamata di gruppo.

Questa guida di avvio rapido illustra il funzionamento dell'esempio prima che venga eseguito nel computer locale. Si passerà quindi alla distribuzione dell'esempio in Azure tramite le risorse dei Servizi di comunicazione di Azure.

## <a name="overview"></a>Panoramica

L'esempio include sia un'applicazione lato client che un'applicazione lato server. L' **applicazione lato client** è un'applicazione Web React/Redux che usa il framework Fluent UI di Microsoft. Questa applicazione invia richieste a un' **applicazione lato server** ASP.NET Core tramite la quale l'applicazione lato client si connette ad Azure. 

L'esempio ha l'aspetto seguente:

:::image type="content" source="./media/calling/landing-page.png" alt-text="Screenshot che mostra la pagina di destinazione dell'applicazione di esempio.":::

Quando si preme il pulsante "Start a call" (Avvia una chiamata), l'applicazione Web recupera un token di accesso utente dall'applicazione lato server. Questo token viene quindi usato per connettere l'app client a Servizi di comunicazione di Azure. Una volta recuperato il token, verrà chiesto di specificare la fotocamera e il microfono da usare. Sarà possibile disabilitare o abilitare i dispositivi con gli appositi controlli:

:::image type="content" source="./media/calling/pre-call.png" alt-text="Screenshot che mostra la schermata di pre-chiamata dell'applicazione di esempio.":::

Dopo aver configurato il nome visualizzato e i dispositivi, è possibile partecipare alla sessione di chiamata. A questo punto, verrà visualizzato il canvas principale in cui risiede l'esperienza principale della chiamata.

:::image type="content" source="./media/calling/main-app.png" alt-text="Screenshot che mostra la schermata principale dell'applicazione di esempio.":::

Componenti della schermata principale della chiamata:

1. **Raccolta multimediale** : l'area principale in cui vengono visualizzati i partecipanti. Se un partecipante ha la fotocamera abilitata, il suo feed video viene visualizzato qui. A ogni partecipante è associato un singolo riquadro che mostra il nome visualizzato e il flusso video (se presente)
2. **Intestazione** : l'area in cui si trovano i controlli principali per la chiamata, che consentono di attivare e disattivare le impostazioni e la barra laterale dei partecipanti, attivare e disattivare il video e il microfono, condividere lo schermo e abbandonare la chiamata.
3. **Barra laterale** : questa barra mostra i partecipanti e le informazioni sulle impostazioni attivate con i controlli dell'intestazione. Il componente può essere rimosso usando la 'X' nell'angolo in alto a destra. La barra laterale dei partecipanti mostrerà l'elenco dei partecipanti e un collegamento per invitare altri utenti alla chat. La barra laterale delle impostazioni consente di configurare il microfono e la fotocamera.

Di seguito sono disponibili ulteriori informazioni sui prerequisiti e i passaggi da seguire per configurare l'esempio.

## <a name="prerequisites"></a>Prerequisiti

- Creare un account Azure con una sottoscrizione attiva. Per informazioni dettagliate, vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js (12.18.4 e versioni successive)](https://nodejs.org/en/download/)
- [Visual Studio (2019 e versioni successive)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (assicurarsi di installare la versione corrispondente all'istanza di Visual Studio in uso, a 32 o a 64 bit)
- Creare una risorsa di Servizi di comunicazione di Azure. Per informazioni dettagliate, vedere [Creare una risorsa di Servizi di comunicazione di Azure](../quickstarts/create-communication-resource.md). Per questa guida di avvio rapido è necessario registrare la **stringa di connessione** della risorsa.

## <a name="locally-deploy-the-service--client-applications"></a>Distribuire in locale il servizio e le applicazioni client

L'esempio di chiamata di gruppo è costituito essenzialmente da due applicazioni: ClientApp e Service.NET.

Per la distribuzione in locale, è necessario avviare entrambe le applicazioni. Quando l'app server viene visitata nel browser, userà l'app ClientApp distribuita in locale per l'esperienza utente.

È possibile testare l'esempio in locale aprendo più sessioni del browser con l'URL della chiamata per simulare una chiamata multiutente.

## <a name="before-running-the-sample-for-the-first-time"></a>Prima di eseguire l'esempio per la prima volta

1. Aprire un'istanza di PowerShell, Terminale Windows, prompt dei comandi o equivalente e passare alla directory in cui clonare l'esempio.
2. `git clone https://github.com/Azure/Communication.git`
3. Ottenere la `Connection String` dal portale di Azure. Per altre informazioni sulle stringhe di connessione, vedere [Creare risorse di comunicazione di Azure](../quickstarts/create-communication-resource.md)
4. Una volta ottenuto il valore di `Connection String`, aggiungere la stringa di connessione al file **Calling/appsetting.json** nella cartella Service .NET. Immettere la stringa di connessione nella variabile `ResourceConnectionString`.

### <a name="local-run"></a>Esecuzione locale

1. Passare alla cartella Calling e aprire la soluzione `Calling.csproj` in Visual Studio
2. Eseguire il progetto `Calling`. Il browser verrà aperto in localhost:5001

#### <a name="troubleshooting"></a>Risoluzione dei problemi

- La soluzione non viene compilata e genera errori durante l'installazione o la compilazione di NPM.

   Provare a pulire/ricompilare i progetti.

## <a name="publish-the-sample-to-azure"></a>Pubblicare l'esempio in Azure

1. Fare clic con il pulsante destro del mouse sul progetto `Calling` e selezionare Pubblica.
2. Creare un nuovo profilo di pubblicazione e selezionare la sottoscrizione di Azure.
3. Prima della pubblicazione, aggiungere la stringa di connessione con `Edit App Service Settings`, immettere `ResourceConnectionString` come chiave e specificare la stringa di connessione (copiata da appSettings.json) come valore.

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire e rimuovere una sottoscrizione di Servizi di comunicazione, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate. Vedere altre informazioni sulla [pulizia delle risorse](../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Passaggi successivi

>[!div class="nextstepaction"] 
>[È possibile scaricare l'esempio da GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero)

Per altre informazioni, vedere gli articoli seguenti:

- Acquisire familiarità con l'[uso della libreria client Chiamate](../quickstarts/voice-video-calling/calling-client-samples.md)
- Leggere altre informazioni sul [funzionamento delle chiamate](../concepts/voice-video-calling/about-call-types.md)

## <a name="additional-reading"></a>Altre letture

- [Pagina GitHub dei servizi di comunicazione di Azure](https://github.com/Azure/communication) - Nella pagina ufficiale di GitHub sono disponibili altri esempi e informazioni
- [Redux](https://redux.js.org/) - Gestione dello stato lato client
- [FluentUI](https://aka.ms/fluent-ui) - Libreria dell'interfaccia utente di Microsoft
- [React](https://reactjs.org/) - Libreria per la compilazione di interfacce utente
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) - Framework per la compilazione di applicazioni Web
