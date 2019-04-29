---
title: Supporto delle licenze di Servizi multimediali di Azure e Apple FairPlay | Microsoft Docs
description: Questo argomento offre una panoramica di una licenza FairPlay di Apple requisiti e la configurazione.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6d4b7ba842d08723b90a4f2491d9e79e68dd932e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733573"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Configurazione e requisiti della licenza Apple FairPlay 

Servizi multimediali di Azure consente di crittografare il contenuto HLS con **Apple FairPlay** (AES-128 CBC). Servizi multimediali offre inoltre un servizio per la distribuzione delle licenze FairPlay. Quando un lettore tenta di riprodurre un contenuto protetto da FairPlay, viene inviata una richiesta al servizio di distribuzione delle licenze per ottenere una licenza. Se il servizio licenze approva la richiesta, la licenza viene rilasciata e inviata al client e verrà usata per decrittografare e riprodurre il contenuto specificato.

Servizi multimediali offre anche API utili per configurare le licenze FairPlay. Questo argomento illustra i requisiti delle licenze FairPlay e come è possibile configurare una licenza **FairPlay** tramite le API di Servizi multimediali. 

## <a name="requirements"></a>Requisiti

Se si usa Servizi multimediali per crittografare il contenuto HLS crittografato con **Apple FairPlay** e per distribuire licenze FairPlay, è necessario:

* Eseguire l'iscrizione all' [Apple Development Program](https://developer.apple.com/).
* Apple richiede che il proprietario del contenuto ottenga il [pacchetto di distribuzione](https://developer.apple.com/contact/fps/). Indicare che è già stato implementato il modulo KSM (Key Security Module) con Servizi multimediali e che si sta richiedendo il pacchetto FPS finale. Il pacchetto FPS finale contiene istruzioni per generare la certificazione e ottenere la chiave privata dell'applicazione, che verrà usata per configurare FairPlay.
* È necessario impostare quanto segue in aggiunta alla distribuzione delle chiavi/licenze di Servizi multimediali:

    * **App Cert (AC)**: file con estensione pfx contenente la chiave privata. Creare il file e crittografarlo con una password. Il file con estensione pfx deve avere il formato Base64.

        La procedura seguente descrive come generare un file di certificato pfx per FairPlay:

        1. Installare OpenSSL da https://slproweb.com/products/Win32OpenSSL.html.

            Passare alla cartella contenente il certificato FairPlay e altri file forniti da Apple.
        2. Eseguire il comando seguente dalla riga di comando. Il comando converte il file con estensione cer in un file con estensione pem.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
        3. Eseguire il comando seguente dalla riga di comando. Questo comando converte il file con estensione pem in un file con estensione pfx con la chiave privata. La password per il file con estensione pfx viene quindi richiesta da OpenSSL.

            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
            
    * **Password di App Cert**: password per creare il file con estensione pfx.
    * **ASK**: chiave ricevuta quando si genera la certificazione usando il portale Apple Developer. Ogni team di sviluppo riceve una chiave ASK univoca. Salvare una copia della chiave ASK e archiviarla in un luogo sicuro. È necessario configurare la chiave ASK come FairPlayAsk in Servizi multimediali.
    
* Sul lato client FPS è necessario impostare quanto segue:

  * **App Cert (AC)**: file con estensione cer/der contenente la chiave pubblica usata dal sistema operativo per crittografare alcuni payload. È necessario che Servizi multimediali lo riconosca perché è richiesto dal lettore. Il servizio di distribuzione delle chiavi lo decrittografa usando la chiave privata corrispondente.

* Per riprodurre un flusso crittografato FairPlay, ottenere prima una chiave privata dell'applicazione reale, quindi generare un certificato reale. Questo processo crea tutte le 3 parti:

  * file con estensione der
  * file con estensione pfx
  * password per il file pfx

## <a name="fairplay-and-player-apps"></a>App di FairPlay e del lettore

Se il contenuto viene crittografato con **Apple FairPlay**, i singoli campioni audio e video vengono crittografati con la modalità **AES-128 CBC**. **FairPlay Streaming** (FPS) è integrato nei sistemi operativi dei dispositivi, con supporto nativo per iOS e Apple TV. Safari su OS X abilita FPS con il supporto dell'interfaccia EME (Encrypted Media Extensions).

Azure Media Player supporta inoltre la riproduzione FairPlay. Vedere la [documentazione di Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) per altre informazioni.

È possibile sviluppare applicazioni lettore usando l'SDK per iOS. Per riprodurre contenuto FairPlay, è necessario implementare il protocollo di scambio delle licenze. Questo protocollo non è specificato da Apple. Spetta a ogni app scegliere come inviare le richieste di distribuzione delle chiavi. Il servizio di distribuzione delle chiavi FairPlay di Servizi multimediali prevede che SPC venga indicato in un messaggio codificato come www-form-url nel formato seguente:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>Configurazione di FairPlay: esempio .NET

È possibile usare l'API di Servizi multimediali per configurare le licenze FairPlay. Quando il lettore tenta di riprodurre un contenuto protetto da FairPlay, viene inviata una richiesta al servizio di distribuzione delle licenze per ottenere la licenza. Se il servizio di licenza approva la richiesta, genera la licenza. Viene inviata al client e usata per decrittografare e riprodurre il contenuto specificato.

> [!NOTE]
> In genere è opportuno configurare le opzioni dei criteri FairPlay una sola volta, dato che sarà presente un solo set di certificazione e chiave privata dell'applicazione.

L'esempio seguente usa [SDK .NET di Servizi multimediali](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) per configurare la licenza.

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>Passaggi successivi

Scoprire come [proteggere con DRM](protect-with-drm.md)
