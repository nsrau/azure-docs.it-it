---
title: Banner della chat di gruppo di esempio
titleSuffix: An Azure Communication Services sample overview
description: Panoramica del banner della chat di esempio sui Servizi di comunicazione di Azure per consentire agli sviluppatori di scoprire di più sul meccanismo dell'esempio e apprendere come modificarlo.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 51b985cd67e1b540e2f24f2f37b19f9bff4223db
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962990"
---
# <a name="get-started-with-the-group-chat-hero-sample"></a>Introduzione al banner della chat di gruppo di esempio

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> links to our Hero Sample repo need to be updated when the sample is publicly available.
---->

Il **banner della chat di gruppo di esempio** dei Servizi di comunicazione di Azure illustra come usare la libreria client Web della chat dei Servizi di comunicazione per creare un'esperienza di chiamata di gruppo.

In questo avvio rapido viene illustrato il funzionamento dell'esempio prima dell'esecuzione sul computer locale. Si passerà quindi alla distribuzione dell'esempio in Azure tramite le risorse dei Servizi di comunicazione di Azure.

> [!IMPORTANT]
> [È possibile scaricare l'esempio da GitHub](https://github.com/Azure/Communication/tree/master/samples/Group%20Chat%20Hero%20Sample/Web/Chat)

## <a name="overview"></a>Panoramica

Nell'esempio sono presenti sia un'applicazione lato client che un'applicazione lato server. L'**applicazione lato client** è un'applicazione Web React/Redux che usa il framework dell'interfaccia utente Fluent di Microsoft. Questa applicazione invia richieste a un'**applicazione lato server** ASP.NET Core che consente all'applicazione lato client di connettersi ad Azure. 

L'esempio ha l'aspetto seguente:

:::image type="content" source="./media/chat/landing-page.png" alt-text="Screenshot che mostra la pagina di destinazione dell'applicazione di esempio.":::

Quando si preme il pulsante "Avvia una chat", l'applicazione Web recupera un token di accesso dell'utente dall'applicazione lato server. Questo token viene quindi usato per connettere l'applicazione client ai Servizi di comunicazione di Azure. Dopo aver recuperato il token, verrà chiesto all'utente di specificare il proprio nome e un'emoji che lo rappresenterà nella chat. 

:::image type="content" source="./media/chat/pre-chat.png" alt-text="Screenshot che mostra la pagina di destinazione dell'applicazione di esempio.":::

Dopo aver configurato il nome visualizzato e l'emoji, è possibile partecipare alla sessione di chat. A questo punto, verrà visualizzato il canvas principale della chat in cui risiede l'esperienza principale di chat.

:::image type="content" source="./media/chat/main-app.png" alt-text="Screenshot che mostra la pagina di destinazione dell'applicazione di esempio.":::

Componenti della schermata principale della chat:

- **Area di chat principale**: si tratta dell'esperienza principale di chat, dove gli utenti possono inviare e ricevere messaggi. Per inviare messaggi, è possibile usare l'area di input e premere Invio (oppure il pulsante Invia). I messaggi di chat ricevuti vengono suddivisi in categorie in base al mittente con il nome e l'emoji corretti. Nell'area della chat vengono visualizzati due tipi di notifiche: 1) notifiche di digitazione quando un utente sta digitando un messaggio e 2) notifiche di invio e lettura dei messaggi.
- **Intestazione**: qui l'utente visualizza il titolo del thread di chat, i controlli per l'attivazione/disattivazione delle barre laterali dei partecipanti e delle impostazioni e il pulsante per uscire dalla sessione di chat.
- **Barra laterale**: qui sono visualizzate le informazioni sui partecipanti e le impostazioni quando vengono attivate tramite i controlli nell'intestazione. Nella barra laterale dei partecipanti è possibile visualizzare un elenco dei partecipanti alla chat e un collegamento per invitare i partecipanti alla sessione di chat. Tramite la barra laterale delle impostazioni è possibile configurare il titolo del thread della chat. 

Di seguito sono disponibili ulteriori informazioni sui prerequisiti e i passaggi da seguire per configurare l'esempio.

## <a name="prerequisites"></a>Prerequisiti

- Creare un account Azure con una sottoscrizione attiva. Per informazioni dettagliate vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js (8.11.2 e versioni successive)](https://nodejs.org/en/download/)
- [Visual Studio (2017 e versioni successive)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (assicurarsi di installare la versione corrispondente all'istanza di Visual Studio in uso, a 32 o a 64 bit)
- Creare una risorsa di Servizi di comunicazione di Azure. Per informazioni dettagliate vedere [Creare una risorsa di comunicazione di Azure](../quickstarts/create-communication-resource.md). Per questo avvio rapido è necessario registrare la **stringa di connessione** della risorsa.

## <a name="locally-deploying-the-service--client-app"></a>Distribuzione in locale dell'applicazione client e del servizio

L'esempio di chat a thread singolo è composto fondamentalmente da due "applicazioni": un'applicazione client e un'applicazione server.

Aprire Visual Studio in chat.csproj ed eseguirlo in modalità di debug per avviare il servizio front-end della chat. Quando l'applicazione server viene visitata dal browser, reindirizzerà il traffico verso il servizio front-end della chat distribuito localmente.

È possibile testare l'esempio in locale aprendo più sessioni del browser con l'URL della chat per simulare una chat multiutente.

## <a name="before-running-the-sample-for-the-first-time"></a>Prima di eseguire l'esempio per la prima volta

1. Aprire un'istanza di PowerShell, un Terminale Windows, un prompt dei comandi o un equivalente e passare alla directory in cui si desidera clonare l'esempio.
2. `git clone https://github.com/Azure/Communication.git`
3. Ottenere la `Connection String` dal portale di Azure. Per altre informazioni sulle stringhe di connessione, vedere [Creare risorse di comunicazione di Azure](../quickstarts/create-communication-resource.md)
4. Dopo aver ottenuto la `Connection String`, aggiungere la stringa di connessione al file **Chat/appsettings.json** nella cartella Chat. Immettere la stringa di connessione nella variabile: `ResourceConnectionString`.

### <a name="local-run"></a>Esecuzione locale

1. Passare alla cartella Chat e aprire la soluzione `Chat.csproj` in Visual Studio
2. Eseguire il progetto. Il browser verrà aperto in localhost:5000.

#### <a name="troubleshooting"></a>Risoluzione dei problemi

- La soluzione non viene compilata e genera errori durante l'installazione o la compilazione di Monitoraggio prestazioni rete

   Pulire/ricompilare la soluzione C#

## <a name="publish-the-sample-to-azure"></a>Pubblicare l'esempio in Azure

1. Fare clic con il pulsante destro del mouse sul progetto `Chat` e selezionare Pubblica.
2. Creare un nuovo profilo di pubblicazione e selezionare la sottoscrizione di Azure.
3. Prima della pubblicazione, aggiungere la stringa di connessione con `Edit App Service Settings`, immettere `ResourceConnectionString` come chiave e specificare la stringa di connessione (copiata da appSettings.json) come valore.

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire e rimuovere una sottoscrizione di Servizi di comunicazione, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate. Vedere altre informazioni sulla [pulizia delle risorse](../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Passaggi successivi

>[!div class="nextstepaction"] 
>[È possibile scaricare l'esempio da GitHub](https://github.com/Azure/Communication/tree/master/samples/Group%20Chat%20Hero%20Sample/Web/Chat)

Per altre informazioni, vedere gli articoli seguenti:

- Informazioni sui [concetti relativi alla chat](../concepts/chat/concepts.md)
- Acquisire familiarità con la [libreria client della chat](../concepts/chat/sdk-features.md)

## <a name="additional-reading"></a>Altre letture

- [Pagina GitHub dei servizi di comunicazione di Azure](https://github.com/Azure/communication) - Nella pagina ufficiale di GitHub sono disponibili altri esempi e informazioni
- [Redux](https://redux.js.org/) - Gestione dello stato lato client
- [FluentUI](https://aka.ms/fluent-ui) - Libreria dell'interfaccia utente di Microsoft
- [React](https://reactjs.org/) - Libreria per la compilazione di interfacce utente
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) - Framework per la compilazione di applicazioni Web
