---
title: Provare gratuitamente il Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Iniziare a usare il servizio di riconoscimento vocale è facile e conveniente. Ci sono due opzioni disponibili gratuitamente in modo da poter scoprire cosa può fare il servizio e decidere se è giusto per le vostre esigenze.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: f74e3ea3d20ad2666b434e009cf62add6f88d200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219665"
---
# <a name="try-the-speech-service-for-free"></a>Provare gratuitamente il Servizio di riconoscimento vocale

In questo articolo, si sceglie un'opzione per testare facilmente il servizio di riconoscimento vocale gratuitamente in modo da poter individuare le operazioni che il servizio può eseguire e decidere se è adatto alle proprie esigenze. Scegliere una delle due opzioni seguenti a seconda della situazione e del caso d'uso:

- [Opzione 1:](#no-card)ottenere immediatamente le chiavi API di **valutazione gratuita** senza fornire alcuna informazione sulla carta di credito (è necessario disporre di un account Azure esistente). La **prova gratuita** dura 30 giorni e i dati vengono eliminati alla fine. Questa opzione è ideale per una rapida sperimentazione con il servizio.
- [Opzione 2:](#new-resource)creare una nuova risorsa riconoscimento vocale in Azure, senza alcun costo aggiuntivo usando una **sottoscrizione gratuita** (informazioni sulla carta di credito necessarie). Un **abbonamento gratuito** ha principalmente limiti di tariffa più severi rispetto a un abbonamento a pagamento. Questa opzione è ideale se si desidera testare il servizio, ma anche pianificare l'aggiornamento a una sottoscrizione a pagamento in futuro e non si desidera perdere dati.

## <a name="try-the-speech-service-without-credit-card-info"></a><a id="no-card"></a>Prova il servizio di riconoscimento vocale senza informazioni sulla carta di credito

Completa i passaggi seguenti per attivare una prova gratuita di 30 giorni e ottenere chiavi API. Il periodo di prova inizia immediatamente una volta completati i passaggi seguenti.

1. Vai a [Prova servizio cognitivo](https://azure.microsoft.com/try/cognitive-services/).
1. Selezionare la scheda **API di riconoscimento vocale**.
1. Scegliere **Ottieni chiave API**.

Verranno visualizzate le opzioni di fatturazione. Scegliere l'opzione gratuita e quindi leggere e approvare il contratto utente. Ti verranno presentate le chiavi che puoi usare per provare il servizio di riconoscimento vocale gratuitamente per 30 giorni.

## <a name="try-the-speech-service-by-creating-an-azure-resource"></a><a id="new-resource"></a>Provare il servizio di riconoscimento vocale creando una risorsa di AzureTry the Speech service by creating an Azure resource

Per i passaggi seguenti sono necessari sia un account Microsoft che un account Azure.For the following steps, you need both a Microsoft account and an Azure account. Se non si dispone di un account Microsoft, è possibile registrarsi gratuitamente nel [portale dell'account Microsoft](https://account.microsoft.com/account). Selezionare **Accedi con Microsoft** e quindi, quando viene richiesto di accedere, selezionare Crea un account **Microsoft**. Seguire i passaggi per creare e verificare il nuovo account Microsoft.

Dopo aver creato un account Microsoft, passare alla pagina di iscrizione ad [Azure](https://azure.microsoft.com/free/ai/), selezionare **Avvia gratuito**e creare un nuovo account Azure usando un account Microsoft.

> [!NOTE]
> Il servizio di riconoscimento vocale ha due livelli di servizio: gratuito e abbonamento, che hanno limitazioni e vantaggi diversi. Quando ti iscrivi a un account Azure gratuito, viene fornito con 200 dollari di credito di servizio che puoi applicare a una sottoscrizione a pagamento del servizio di riconoscimento vocale, valida per un massimo di 30 giorni.
>
> Se si utilizza il livello di servizio di riconoscimento vocale gratuito a basso volume, è possibile mantenere questo abbonamento gratuito anche dopo la scadenza del credito gratuito di prova o di servizio.
>
> Per ulteriori informazioni, vedere [Servizi cognitivi - Servizio di riconoscimento vocale](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-resource"></a>Creare la risorsa

Per aggiungere una risorsa del servizio vocale (gratuita o a pagamento) al proprio account Azure:

1. Accedere al [portale di Azure](https://portal.azure.com/) mediante il proprio account Microsoft.

1. Selezionare **Crea una risorsa** in alto a sinistra sul portale. Se non viene visualizzata l'opzione **Crea una risorsa**, è sempre possibile trovarla selezionando il menu compresso in alto a sinistra:

   ![pulsante di spostamento compresso](media/index/collapsed-nav.png)

1. Nella finestra **Nuovo,** digitare "speech" nella casella di ricerca e premere INVIO.

1. Nei risultati della ricerca, selezionare **Voce**.

   ![risultati della ricerca vocale](media/index/speech-search.png)

1. Selezionare **Crea**, quindi:

   - Assegnare un nome univoco per la nuova risorsa. Il nome consente di distinguere tra più sottoscrizioni legate allo stesso servizio.
   - Scegliere la sottoscrizione di Azure a cui è associata la nuova risorsa per determinare le modalità di fatturazione.
   - Scegliere [l'area](regions.md) in cui verrà utilizzata la risorsa.
   - Scegli un piano tariffario gratuito (F0) o a pagamento (S0). Per informazioni complete sui prezzi e le quote di utilizzo per ogni livello, selezionare **Visualizza dettagli sui prezzi completi.**
   - Creare un nuovo gruppo di risorse per questa sottoscrizione di riconoscimento vocale o assegnarla a un gruppo di risorse esistente. I gruppi di risorse consentono di mantenere organizzate le diverse sottoscrizioni di Azure.
   - Selezionare **Crea**. Verrà visualizzata la panoramica della distribuzione e verranno visualizzati i messaggi di stato della distribuzione.

> [!NOTE]
> È possibile creare un numero illimitato di sottoscrizioni di livello standard in una o più aree. Tuttavia, è possibile creare solo una sottoscrizione gratuita. Le distribuzioni di modelli nel livello gratuito che rimangono inutilizzate per 7 giorni verranno rimosse automaticamente.

La distribuzione della nuova risorsa Riconoscimento vocale richiede alcuni minuti. Al termine della distribuzione, selezionare **Vai alla risorsa** e nel riquadro di spostamento sinistro selezionare **Chiavi** per visualizzare le chiavi di sottoscrizione del servizio di riconoscimento vocale. Ogni sottoscrizione dispone di due chiavi, entrambi utilizzabili nell'applicazione. Per copiare/incollare rapidamente una chiave nell'editor di codice o in un'altra posizione, selezionare il pulsante Copia accanto a ogni chiave, passare da una finestra all'altra per incollare il contenuto degli Appunti nella posizione desiderata.

> [!IMPORTANT]
> Queste chiavi di sottoscrizione vengono usate per accedere all'API del servizio cognitivo. Non condividere le chiavi. Archiviarli in modo sicuro, ad esempio usando L'insieme di credenziali delle chiavi di Azure.Store them securely- for example, using Azure Key Vault. Si consiglia inoltre di rigenerare questi tasti regolarmente. Per effettuare una chiamata API è necessaria una sola chiave. Quando si rigenera la prima chiave, è possibile utilizzare la seconda chiave per l'accesso continuo al servizio.

## <a name="switch-to-a-new-subscription"></a>Passare a una nuova sottoscrizione

Per passare da una sottoscrizione a un'altra, ad esempio quando la versione di prova gratuita è scaduta o quando si pubblica la propria applicazione, sostituire l'area e la chiave di sottoscrizione nel codice con l'area e la chiave di sottoscrizione della nuova risorsa di Azure.

## <a name="about-regions"></a>Informazioni sulle regioni

- Se l'applicazione usa un [SDK per il riconoscimento vocale](speech-sdk.md), specificare il codice di area, ad esempio `westus`, durante la creazione di una configurazione di riconoscimento vocale.
- Se l'applicazione usa una delle [API REST](rest-apis.md) del Servizio di riconoscimento vocale, l'area è parte dell'URI dell'endpoint usato quando si effettuano richieste.
- Le chiavi create per un'area sono valide solo in quell'area. Se si prova a usarle con altre aree si verificheranno errori di autenticazione.

## <a name="next-steps"></a>Passaggi successivi

Completare una delle guide introduttive di 10 minuti o vedere gli esempi di SDK:

> [!div class="nextstepaction"]
> [Guida introduttiva: Riconoscere il riconoscimento vocale negli](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> esempi di[C](speech-sdk.md#get-the-samples)
