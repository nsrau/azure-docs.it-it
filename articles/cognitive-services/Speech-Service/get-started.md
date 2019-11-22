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
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 30bdbf9fa0ea346892622c3e7f24f9f31652a650
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280462"
---
# <a name="try-the-speech-service-for-free"></a>Provare gratuitamente il Servizio di riconoscimento vocale

L'uso del servizio di riconoscimento vocale è facile ed economicamente conveniente. Sono disponibili due opzioni gratuite che consentono di individuare le operazioni che il servizio può eseguire e decidere se è adatto alle proprie esigenze:

- Ottenere una versione di valutazione gratuita senza fornire informazioni sulla carta di credito (è necessario avere un account Azure esistente)
- Crea gratuitamente un nuovo account Azure per un periodo di valutazione (sono necessarie le informazioni sulla carta di credito)

In questo articolo si sceglierà una delle opzioni più adatte alle proprie esigenze.

> [!NOTE]
> Il servizio di riconoscimento vocale ha due livelli di servizio: gratuito e sottoscrizione, con limitazioni e vantaggi diversi. Quando si esegue l'iscrizione per ottenere un account Azure gratuito, questo è disponibile con $200 nel credito di servizio che è possibile applicare a una sottoscrizione a pagamento per un servizio di riconoscimento vocale, valida per un massimo di 30 giorni.
>
> Se si usa il livello di servizio gratuito a volume ridotto, è possibile lasciare questa sottoscrizione gratuita anche dopo la scadenza della versione di valutazione gratuita o del credito di servizio.
>
> Per altre informazioni, vedere [prezzi di servizi cognitivi-servizi vocali](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="try-the-speech-service-without-credit-card-info"></a>Prova il servizio di riconoscimento vocale senza informazioni sulla carta di credito

Sebbene sia consigliabile provare il servizio di riconoscimento vocale usando le istruzioni nella sezione successiva, è possibile che si preferisca le istruzioni della sezione se si verifica quanto segue:

- Si dispone già di un account Azure attivo.
- Si vuole valutare il servizio di riconoscimento vocale senza creare un nuovo account Azure.
- Si preferisce che non sia richiesta una carta di credito e che non vengano salvati dati dopo il periodo di valutazione.

> [!NOTE]
> Il periodo di valutazione inizia immediatamente dopo aver completato i passaggi seguenti.

1. Passare a [try cognitive Service](https://azure.microsoft.com/try/cognitive-services/).
1. Selezionare la scheda **API di riconoscimento vocale**.
1. Scegliere **Ottieni chiave API**.

Verranno visualizzate le opzioni di fatturazione. Scegliere l'opzione gratuito, quindi leggere e approvare il contratto utente. Verranno visualizzate le chiavi che è possibile usare per provare il servizio di riconoscimento vocale per un periodo di tempo limitato.

## <a name="try-the-speech-service-using-a-new-azure-account"></a>Prova il servizio di riconoscimento vocale usando un nuovo account Azure

Per iscriversi per un nuovo account Azure, sarà necessario un account Microsoft. Se non si ha una account Microsoft, è possibile iscriversi gratuitamente al [portale di account Microsoft](https://account.microsoft.com/account). Selezionare **Accedi con Microsoft** e quindi, quando viene richiesto di eseguire l'accesso, selezionare **Crea una account Microsoft**. Seguire i passaggi per creare e verificare il nuovo account Microsoft.

Quando si dispone di un account Microsoft, passare alla [pagina di iscrizione ad Azure](https://azure.microsoft.com/free/ai/), selezionare **Avvia gratis**e creare un nuovo account Azure usando una account Microsoft.

### <a name="create-a-speech-resource-in-azure"></a>Creare una risorsa di riconoscimento vocale in Azure

> [!NOTE]
> È possibile creare un numero illimitato di sottoscrizioni di livello standard in una o più aree. Tuttavia, è possibile creare solo una sottoscrizione gratuita. Le distribuzioni di modelli sul livello gratuito che rimangono inutilizzate per 7 giorni verranno rimosse automaticamente.

Per aggiungere una risorsa del servizio vocale (gratuita o a pagamento) al proprio account Azure:

1. Accedere al [portale di Azure](https://portal.azure.com/) mediante il proprio account Microsoft.

1. Selezionare **Crea una risorsa** in alto a sinistra sul portale. Se non viene visualizzato **Crea una risorsa**, è sempre possibile individuarla selezionando il menu compresso in alto a sinistra:

   ![pulsante di spostamento compresso](media/index/collapsed-nav.png)

1. Nella **nuova** finestra digitare "speech" nella casella di ricerca e premere INVIO.

1. Nei risultati della ricerca, selezionare **Voce**.

   ![Risultati della ricerca vocale](media/index/speech-search.png)

1. Selezionare **Crea**, quindi:

   - Assegnare un nome univoco per la nuova risorsa. Il nome consente di distinguere tra più sottoscrizioni per il servizio stesso.
   - Scegliere la sottoscrizione di Azure a cui è associata la nuova risorsa per determinare le modalità di fatturazione.
   - Scegliere l' [area](regions.md) in cui verrà utilizzata la risorsa.
   - Scegliere un piano tariffario gratuito (F0) o a pagamento (S0). Per informazioni complete su prezzi e quote di utilizzo per ogni livello, selezionare **Visualizza dettagli prezzi completi**.
   - Creare un nuovo gruppo di risorse per questa sottoscrizione di riconoscimento vocale o assegnarla a un gruppo di risorse esistente. I gruppi di risorse consentono di mantenere organizzate le diverse sottoscrizioni di Azure.
   - Selezionare **Create**. Verrà visualizzata la panoramica della distribuzione e i messaggi sullo stato di avanzamento della distribuzione.

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
