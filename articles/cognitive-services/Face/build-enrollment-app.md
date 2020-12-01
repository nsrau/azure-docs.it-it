---
title: Creare un'app di registrazione per Android con React
titleSuffix: Azure Cognitive Services
description: Informazioni su come configurare l'ambiente di sviluppo e distribuire un'app di registrazione viso per ottenere il consenso dei clienti.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: 085dd18214f795566669fb862bba63b67eb9115d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350365"
---
# <a name="build-an-enrollment-app-for-android-with-react"></a>Creare un'app di registrazione per Android con React

Questa guida illustra come iniziare a usare l'applicazione di registrazione di esempio per Face. L'app illustra le procedure consigliate per ottenere un consenso significativo per la registrazione degli utenti in un servizio di riconoscimento delle facce e per l'acquisizione di dati facciali con precisione elevata. Un sistema integrato può usare un'app di registrazione come questa per fornire controllo di accesso, verifica dell'identità, rilevamento delle presenze, chiosco di personalizzazione o verifica dell'identità, in base ai dati relativi ai visi.

Quando viene avviata, l'applicazione mostra agli utenti una schermata di consenso dettagliata. Se l'utente fornisce il consenso, l'app richiede un nome utente e una password e quindi acquisisce un'immagine viso di qualità elevata usando la fotocamera del dispositivo.

L'app di registrazione di esempio viene scritta con JavaScript e il Framework di React native. Attualmente può essere distribuito nei dispositivi Android. saranno disponibili altre opzioni di distribuzione in futuro.

## <a name="prerequisites"></a>Prerequisiti 

* Una sottoscrizione di Azure: [crearne una gratuitamente](https://azure.microsoft.com/free/cognitive-services/).  
* Dopo aver creato la sottoscrizione di Azure, [creare una risorsa Face](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.  
  * Sono necessari la chiave e l'endpoint della risorsa creata per connettere l'applicazione a API Viso.  
  * Per lo sviluppo e il test locali, è possibile incollare la chiave API e l'endpoint nel file di configurazione. Per la distribuzione finale, archiviare la chiave API in un percorso sicuro e mai nel codice.  

> [!IMPORTANT]
> Queste chiavi di sottoscrizione vengono usate per accedere all'API di Servizi cognitivi. Non condividerle. Archiviarli in modo sicuro, ad esempio usando Azure Key Vault. È inoltre consigliabile rigenerare queste chiavi regolarmente. Per effettuare una chiamata API è necessaria una sola chiave. Quando si rigenera la prima chiave, è possibile usare la seconda chiave per l'accesso continuato al servizio.

## <a name="set-up-the-development-environment"></a>Configurare l'ambiente di sviluppo

1. Clonare il repository Git per l' [app di registrazione di esempio](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample).
1. Per configurare l'ambiente di sviluppo, seguire la documentazione di <a href="https://reactnative.dev/docs/environment-setup"  title=" React Native "  target="_blank"> React <span class="docon docon-navigate-external x-hidden-focus"></span> </a> . Selezionare risposta **introduttiva CLI nativa** come sistema operativo di sviluppo e selezionare **Android** come sistema operativo di destinazione. Completare le sezioni **installazione delle dipendenze** e **dell'ambiente di sviluppo Android**.
1. Aprire il env.jsnel file nell'editor di testo preferito, ad esempio [Visual Studio Code](https://code.visualstudio.com/), e aggiungere l'endpoint e la chiave. È possibile ottenere l'endpoint e la chiave nella portale di Azure nella scheda **Panoramica** della risorsa. Questo passaggio è solo per scopi di test locali &mdash; non archiviare la chiave di API viso nel repository remoto.
1. Eseguire l'app usando l'emulatore di dispositivo virtuale Android da Android Studio o il dispositivo Android. Per eseguire il test dell'app su un dispositivo fisico, seguire la documentazione relativa a <a href="https://reactnative.dev/docs/running-on-device"  title=" React Native "  target="_blank"> React <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .  


## <a name="create-an-enrollment-experience"></a>Creare un'esperienza di registrazione  

Ora che è stata configurata l'app di registrazione di esempio, è possibile adattarla alle esigenze dell'esperienza di registrazione.

Ad esempio, potrebbe essere necessario aggiungere informazioni specifiche della situazione nella pagina di consenso:

> [!div class="mx-imgBorder"]
> ![pagina di consenso dell'app](./media/enrollment-app/1-consent-1.jpg)

Il servizio fornisce controlli della qualità dell'immagine che consentono di scegliere se l'immagine è di qualità sufficiente per registrare il cliente o tentare il riconoscimento viso. Questa app illustra come accedere ai frame dalla fotocamera del dispositivo, selezionare i frame di qualità più elevata e registrare la faccia rilevata nel servizio API Viso. 

Molti problemi di riconoscimento facciali sono causati da immagini di riferimento di bassa qualità. Di seguito sono indicati alcuni fattori che possono influire negativamente sulle prestazioni del modello:
* Dimensioni del volto (visi distanti dalla fotocamera)
* Orientamento facciale (visi rivolti o inclinati dalla fotocamera)
* Condizioni di illuminazione scarse (luce o retroilluminazione) in cui l'immagine potrebbe essere esposta in modo non corretto o avere troppi rumori
* Occlusione (visi parzialmente nascosti o ostruiti), inclusi accessori quali cappelli o occhiali di spessore.
* Sfocatura, ad esempio con movimento facciale rapido quando è stata eseguita la fotografia. 

> [!div class="mx-imgBorder"]
> ![pagina di istruzioni di acquisizione di immagini dell'app](./media/enrollment-app/4-instruction.jpg)

Si noti che l'app offre anche la funzionalità per eliminare la registrazione dell'utente e l'opzione per eseguire nuovamente la registrazione.

> [!div class="mx-imgBorder"]
> ![pagina di gestione del profilo](./media/enrollment-app/10-manage-2.jpg)

Per estendere la funzionalità dell'app per coprire l'esperienza di registrazione completa, leggere la [Panoramica](enrollment-overview.md) per le funzionalità aggiuntive da implementare e le procedure consigliate.

## <a name="deploy-the-enrollment-app"></a>Distribuire l'app di registrazione

### <a name="android"></a>Android

Assicurarsi prima di tutto che l'app sia pronta per la distribuzione di produzione: rimuovere le chiavi o i segreti dal codice dell'app e assicurarsi di aver seguito le [procedure di sicurezza consigliate](../cognitive-services-security.md?tabs=command-line%2ccsharp).

Quando si è pronti per rilasciare l'app per la produzione, verrà generato un file APK pronto per la versione, che è il formato di file del pacchetto per le app Android. Questo file APK deve essere firmato con una chiave privata. Con questa build di rilascio è possibile iniziare a distribuire direttamente l'app nei dispositivi. 

Seguire la <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" documentazione prepara per la versione "  target="_blank"> prepararsi per la versione <span class="docon docon-navigate-external x-hidden-focus"></span> </a> per informazioni su come generare una chiave privata, firmare l'applicazione e generare un file apk della versione.  

Dopo aver creato un file APK firmato, vedere la documentazione pubblicare l'app pubblicare l'app <a href="https://developer.android.com/studio/publish"  title=" "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> per altre informazioni su come rilasciare l'app.

## <a name="next-steps"></a>Passaggi successivi  

In questa guida è stato illustrato come configurare l'ambiente di sviluppo e iniziare a usare l'app di registrazione di esempio. Se non si ha familiarità con React native, è possibile leggere la [documentazione](https://reactnative.dev/docs/getting-started) introduttiva per ulteriori informazioni complementari. Può anche essere utile acquisire familiarità con [API viso](Overview.md). Leggere le altre sezioni della documentazione dell'app di registrazione prima di iniziare lo sviluppo.