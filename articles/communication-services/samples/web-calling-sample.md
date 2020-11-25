---
title: Servizi di comunicazione di Azure - Esempio di chiamata Web
titleSuffix: An Azure Communication Services sample
description: Informazioni sull'esempio di chiamata Web di Servizi di comunicazione
author: chriswhilar
manager: mariusu-msft
services: azure-communication-services
ms.author: mariusu
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8faa6533ba4dc7fad61c045278c5504c5f78d900
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888964"
---
# <a name="get-started-with-the-web-calling-sample"></a>Introduzione all'esempio di chiamata Web

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

> [!IMPORTANT]
> [Questo esempio è disponibile su GitHub](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/).

L'**esempio di chiamata Web** di Servizi di comunicazione di Azure illustra come usare la libreria client per le chiamate di Servizi di comunicazione per creare un'esperienza di chiamata con JavaScript.

In questo avvio rapido viene illustrato il funzionamento dell'esempio prima dell'esecuzione sul computer locale. Si passerà quindi alla distribuzione dell'esempio in Azure tramite le risorse dei Servizi di comunicazione di Azure.

## <a name="overview"></a>Panoramica

L'esempio di chiamata Web è un'applicazione Web che funge da procedura dettagliata per le varie funzionalità fornite dalla libreria client per le chiamate Web di Servizi di comunicazione. 

Questo esempio è stato creato per gli sviluppatori e offre un modo molto semplice per iniziare a familiarizzare con Servizi di comunicazione. La sua interfaccia utente è suddivisa in più sezioni, ognuna con un pulsante "Show code" (Mostra codice) che consente di copiare il codice direttamente dal browser nell'applicazione di Servizi di comunicazione.

Quando si esegue l'[esempio di chiamata Web](https://github.com/Azure-Samples/communication-services-web-calling-tutorial) nel computer, viene visualizzata la pagina di destinazione seguente:

:::image type="content" source="./media/web-calling-tutorial-page-1.png" alt-text="Esercitazione chiamata Web 1" lightbox="./media/web-calling-tutorial-page-1.png":::

:::image type="content" source="./media/web-calling-tutorial-page-2.png" alt-text="Esercitazione chiamata Web 2" lightbox="./media/web-calling-tutorial-page-2.png":::


## <a name="user-provisioning-and-sdk-initialization"></a>Provisioning degli utenti e inizializzazione dell'SDK 

Per iniziare a usare la demo, immettere la stringa di connessione dalla [risorsa di Servizi di comunicazione](../quickstarts/create-communication-resource.md) in `config.json`. Questa stringa verrà usata per effettuare il provisioning di un [token di accesso utente](../concepts/authentication.md) in modo da inizializzare l'SDK per le chiamate.

Immettere il proprio identificatore personale nella casella di input dell'identità utente. Se non viene specificato alcun ID, verrà generata un'identità utente casuale. 

Fare clic su "Provision user and initialize SDK" (Effettua il provisioning dell'utente e inizializza l'SDK) usando un token fornito dal servizio back-end di provisioning dei token. Questo servizio back-end è in `/project/webpack.config.js`.

Fare clic sul pulsante "Show code" (Mostra codice) per visualizzare il codice di esempio che può essere usato nella propria soluzione.

Dopo l'inizializzazione dell'SDK dovrebbe essere visualizzata la schermata seguente:

:::image type="content" source="./media/user-provisioning.png" alt-text="Provisioning utenti" lightbox="./media/user-provisioning.png":::

A questo punto è possibile iniziare a effettuare chiamate usando la risorsa di Servizi di comunicazione.

## <a name="placing-and-receiving-calls"></a>Esecuzione e ricezione di chiamate

L'SDK per le chiamate Web di Servizi di comunicazione consente di effettuare chiamate **1:1**, **1: N** e **di gruppo**.

Per le chiamate in uscita 1:1 o 1: N è possibile specificare più identità utente di Servizi di comunicazione da chiamare usando valori separati da virgole. È anche possibile specificare numeri di telefono tradizionali (PSTN) da chiamare usando valori separati da virgole. 

Quando si chiamano numeri di telefono PSTN, specificare l'ID chiamante alternativo. Fare clic sul pulsante "Place call" (Chiama) per effettuare una chiamata in uscita:

:::image type="content" source="./media/place-a-call.png" alt-text="Effettuare una chiamata" lightbox="./media/place-a-call.png":::

Per partecipare a una chiamata di gruppo, immettere il GUID che identifica la chiamata e fare clic sul pulsante "Join group" (Entra nel gruppo):

:::image type="content" source="./media/join-a-group-call.png" alt-text="Partecipare a una chiamata di gruppo" lightbox="./media/join-a-group-call.png":::

Fare clic sul pulsante "Show code" (Mostra codice) per visualizzare il codice di esempio per effettuare chiamate, ricevere chiamate e partecipare a chiamate di gruppo.

Una chiamata attiva ha un aspetto simile al seguente:

:::image type="content" source="./media/group-call.png" alt-text="Chiamata di gruppo" lightbox="./media/group-call.png":::

Questo esempio fornisce anche frammenti di codice per le funzionalità seguenti:

  - Fare clic sull'icona del video per accendere/spegnere la videocamera
  - Fare clic sull'icona del microfono per accendere/spegnere il microfono
  - Fare clic sull'icona Riproduci per mettere in attesa/riprendere la chiamata
  - Fare clic sull'icona dello schermo per avviare/interrompere la condivisione dello schermo
  - Fare clic sull'icona della persona per aggiungere un partecipante alla chiamata
  - Fare clic su "Rimuovi partecipante" nell'elenco partecipanti per rimuovere un partecipante specifico dalla chiamata


## <a name="next-steps"></a>Passaggi successivi

>[!div class="nextstepaction"] 
>[È possibile scaricare l'esempio da GitHub](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

Per altre informazioni, vedere gli articoli seguenti:

- Acquisire familiarità con l'[uso della libreria client Chiamate](../quickstarts/voice-video-calling/calling-client-samples.md)
- Leggere altre informazioni sul [funzionamento delle chiamate](../concepts/voice-video-calling/about-call-types.md)
- Vedere la [documentazione di riferimento sulle API](/javascript/api/azure-communication-services/@azure/communication-calling/?view=azure-communication-services-js)

## <a name="additional-reading"></a>Altre letture

- [Pagina GitHub dei servizi di comunicazione di Azure](https://github.com/Azure/communication) - Nella pagina ufficiale di GitHub sono disponibili altri esempi e informazioni
- [Redux](https://redux.js.org/) - Gestione dello stato lato client
- [FluentUI](https://aka.ms/fluent-ui) - Libreria dell'interfaccia utente di Microsoft
- [React](https://reactjs.org/) - Libreria per la compilazione di interfacce utente
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) - Framework per la compilazione di applicazioni Web