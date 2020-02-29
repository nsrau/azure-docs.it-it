---
title: Provare gratuitamente il Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Iniziare a usare il servizio riconoscimento vocale è facile ed economicamente conveniente. Sono disponibili due opzioni gratuite che consentono di individuare le operazioni che il servizio può eseguire e decidere se è adatto alle proprie esigenze.
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913668"
---
# <a name="try-the-speech-service-for-free"></a>Provare gratuitamente il Servizio di riconoscimento vocale

In questo articolo si sceglie un'opzione che consente di testare facilmente il servizio di riconoscimento vocale gratuitamente, in modo da poter individuare le operazioni che il servizio può eseguire e decidere se è adatto alle proprie esigenze. Scegliere una delle due opzioni seguenti a seconda della situazione e del caso d'uso:

- [Opzione 1](#no-card): ottenere immediatamente le chiavi API della **versione di valutazione gratuita** senza fornire informazioni sulla carta di credito (è necessario avere un account Azure esistente). La **versione di valutazione gratuita** dura 30 giorni e i dati vengono eliminati alla fine. Questa opzione è ideale per sperimentare rapidamente il servizio.
- [Opzione 2](#new-resource): creare una nuova risorsa di riconoscimento vocale in Azure senza alcun addebito usando una **sottoscrizione gratuita** (le informazioni sulla carta di credito sono necessarie). Una **sottoscrizione gratuita** ha principalmente limiti di velocità più restrittivi rispetto a una sottoscrizione a pagamento. Questa opzione è consigliata se si desidera testare il servizio, ma anche pianificare l'aggiornamento a una sottoscrizione a pagamento in futuro e non si desidera perdere i dati.

## <a id="no-card"></a>Prova il servizio di riconoscimento vocale senza informazioni sulla carta di credito

Completare i passaggi seguenti per attivare una versione di valutazione gratuita di 30 giorni e ottenere le chiavi API. Il periodo di valutazione inizia immediatamente dopo aver completato i passaggi seguenti.

1. Passare a [try cognitive Service](https://azure.microsoft.com/try/cognitive-services/).
1. Selezionare la scheda **API di riconoscimento vocale**.
1. Scegliere **Ottieni chiave API**.

Verranno visualizzate le opzioni di fatturazione. Scegliere l'opzione gratuito, quindi leggere e approvare il contratto utente. Verranno visualizzate le chiavi che è possibile usare per provare il servizio di riconoscimento vocale gratuitamente per 30 giorni.

## <a id="new-resource"></a>Provare il servizio di riconoscimento vocale creando una risorsa di Azure

Per i passaggi seguenti sono necessari sia un account Microsoft che un account Azure. Se non si ha una account Microsoft, è possibile iscriversi gratuitamente al [portale di account Microsoft](https://account.microsoft.com/account). Selezionare **Accedi con Microsoft** e quindi, quando viene richiesto di eseguire l'accesso, selezionare **Crea una account Microsoft**. Seguire i passaggi per creare e verificare il nuovo account Microsoft.

Quando si dispone di un account Microsoft, passare alla [pagina di iscrizione ad Azure](https://azure.microsoft.com/free/ai/), selezionare **Avvia gratis**e creare un nuovo account Azure usando una account Microsoft.

> [!NOTE]
> Il servizio di riconoscimento vocale ha due livelli di servizio: gratuito e sottoscrizione, con limitazioni e vantaggi diversi. Quando si esegue l'iscrizione per ottenere un account Azure gratuito, questo è disponibile con $200 nel credito di servizio che è possibile applicare a una sottoscrizione a pagamento per un servizio di riconoscimento vocale, valida per un massimo di 30 giorni.
>
> Se si usa il livello di servizio gratuito a volume ridotto, è possibile lasciare questa sottoscrizione gratuita anche dopo la scadenza della versione di valutazione gratuita o del credito di servizio.
>
> Per altre informazioni, vedere [prezzi di servizi cognitivi-servizio di riconoscimento vocale](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-resource"></a>Creare la risorsa

Per aggiungere una risorsa del servizio vocale (gratuita o a pagamento) al proprio account Azure:

1. Accedere al [portale di Azure](https://portal.azure.com/) mediante il proprio account Microsoft.

1. Selezionare **Crea una risorsa** in alto a sinistra sul portale. Se non viene visualizzato **Crea una risorsa**, è sempre possibile individuarla selezionando il menu compresso in alto a sinistra:

   ![pulsante di spostamento compresso](media/index/collapsed-nav.png)

1. Nella **nuova** finestra digitare "speech" nella casella di ricerca e premere INVIO.

1. Nei risultati della ricerca, selezionare **Voce**.

   ![Risultati della ricerca vocale](media/index/speech-search.png)

1. Selezionare **Crea**, quindi:

   - Assegnare un nome univoco per la nuova risorsa. Il nome consente di distinguere tra più sottoscrizioni legate allo stesso servizio.
   - Scegliere la sottoscrizione di Azure a cui è associata la nuova risorsa per determinare le modalità di fatturazione.
   - Scegliere l' [area](regions.md) in cui verrà utilizzata la risorsa.
   - Scegliere un piano tariffario gratuito (F0) o a pagamento (S0). Per informazioni complete su prezzi e quote di utilizzo per ogni livello, selezionare **Visualizza dettagli prezzi completi**.
   - Creare un nuovo gruppo di risorse per questa sottoscrizione di riconoscimento vocale o assegnarla a un gruppo di risorse esistente. I gruppi di risorse consentono di mantenere organizzate le diverse sottoscrizioni di Azure.
   - Selezionare **Create** (Crea). Verrà visualizzata la panoramica della distribuzione e i messaggi sullo stato di avanzamento della distribuzione.

> [!NOTE]
> È possibile creare un numero illimitato di sottoscrizioni di livello standard in una o più aree. Tuttavia, è possibile creare solo una sottoscrizione gratuita. Le distribuzioni di modelli sul livello gratuito che rimangono inutilizzate per 7 giorni verranno rimosse automaticamente.

Sono necessari alcuni minuti per distribuire la nuova risorsa di riconoscimento vocale. Al termine della distribuzione, selezionare **Vai alla risorsa** e nel riquadro di spostamento a sinistra selezionare **chiavi** per visualizzare le chiavi di sottoscrizione al servizio di riconoscimento vocale. Ogni sottoscrizione dispone di due chiavi, entrambi utilizzabili nell'applicazione. Per copiare e incollare rapidamente una chiave nell'editor di codice o in un altro percorso, selezionare il pulsante copia accanto a ogni chiave, passare a Windows per incollare il contenuto degli Appunti nella posizione desiderata.

> [!IMPORTANT]
> Queste chiavi di sottoscrizione vengono usate per accedere all'API di servizi cognitivi. Non condividere le chiavi. Archiviarli in modo sicuro, ad esempio usando Azure Key Vault. Si consiglia inoltre di rigenerare regolarmente queste chiavi. È necessaria una sola chiave per effettuare una chiamata API. Quando si rigenera la prima chiave, è possibile usare la seconda chiave per l'accesso continuo al servizio.

## <a name="switch-to-a-new-subscription"></a>Passare a una nuova sottoscrizione

Per passare da una sottoscrizione a un'altra, ad esempio quando la versione di prova gratuita è scaduta o quando si pubblica la propria applicazione, sostituire l'area e la chiave di sottoscrizione nel codice con l'area e la chiave di sottoscrizione della nuova risorsa di Azure.

## <a name="about-regions"></a>Informazioni sulle aree

- Se l'applicazione usa un [SDK per il riconoscimento vocale](speech-sdk.md), specificare il codice di area, ad esempio `westus`, durante la creazione di una configurazione di riconoscimento vocale.
- Se l'applicazione usa una delle [API REST](rest-apis.md) del Servizio di riconoscimento vocale, l'area è parte dell'URI dell'endpoint usato quando si effettuano richieste.
- Le chiavi create per un'area sono valide solo in quell'area. Se si prova a usarle con altre aree si verificheranno errori di autenticazione.

## <a name="next-steps"></a>Passaggi successivi

Completare una delle guide introduttive di 10 minuti o vedere gli esempi di SDK:

> [!div class="nextstepaction"]
> [Guida introduttiva: Riconoscimento vocale in C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [Esempi di Speech SDK](speech-sdk.md#get-the-samples)
