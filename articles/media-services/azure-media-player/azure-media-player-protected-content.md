---
title: Contenuto protetto di Azure Media Player
description: Azure Media Player attualmente supporta contenuto con crittografia envelope AES a 128 bit e contenuto con crittografia comune.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: da79556b466e3511845724e969c76477ad2ba0a8
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423015"
---
# <a name="protected-content"></a>Contenuto protetto #

Azure Media Player attualmente supporta contenuto con crittografia envelope AES a 128 bit e contenuto con crittografia comune (tramite PlayReady e Widevine) oppure contenuto crittografato tramite FairPlay. Per riprodurre correttamente il contenuto protetto, è necessario indicare ad Azure Media Player `protectionInfo`. Questa informazione esiste per ogni origine e può essere aggiunta direttamente sul tag `<source>` tramite `data-setup`.  È anche possibile aggiungere `protectionInfo` direttamente come parametro se si imposta l'origine in modo dinamico.

`protectionInfo` accetta un oggetto JSON e include:

- `type`: `AES` o `PlayReady` o `Widevine` o `FairPlay`
- `certificateUrl`: deve essere un collegamento diretto al certificato FairPlay ospitato

- `authenticationToken`: campo di opzione per l'aggiunta di un token di autenticazione non codificato

> [!IMPORTANT]
> L'oggetto **certificateUrl** è necessario solo per DRM FairPlay.***
>[!NOTE]
> Il valore predefinito di techOrder è stato cambiato per supportare la nuova tecnologia `html5FairPlayHLS`, in particolare per la riproduzione nativa di contenuto FairPlay in browser che la supportano (Safari in OSX 8 e versioni successive). Se è disponibile contenuto FairPlay da riprodurre **e** il valore predefinito di techOrder è stato sostituito da uno personalizzato nell'applicazione, sarà necessario aggiungere questa nuova tecnologia nell'oggetto techOrder. È consigliabile includerlo prima di silverlightSS, in modo che il contenuto non venga riavviato tramite PlayReady.

## <a name="code-sample"></a>Esempio di codice ##

```html
Ex:

    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source
            src="//example/path/to/myVideo.ism/manifest"
            type="application/vnd.ms-sstr+xml"
            data-setup='{"protectionInfo": [{"type": "AES", "authenticationToken": "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=8130520b-c116-45a9-824e-4a0082f3cb3c&Audience=urn%3atest&ExpiresOn=1450207516&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=eV7HDgZ9msp9H9bnEPGN91sBdU7XsZ9OyB6VgFhKBAU%3d"}]}'
        />
    </video>
or

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
            type: "PlayReady",
            authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
        }] }, ]
    );
```

oppure con più DRM

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
                type: "PlayReady",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                type: "Widevine",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                   type: "FairPlay",
                  certificateUrl: "//example/path/to/myFairplay.der",
                   authenticationToken: "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1cm46bWljcm9zb2Z0OmF6dXJlOm1lZGlhc2VydmljZXM6Y29udGVudGtleWlkZW50aWZpZXIiOiIyMTI0M2Q2OC00Yjc4LTRlNzUtYTU5MS1jZWMzMDI0NDNhYWMiLCJpc3MiOiJodHRwOi8vY29udG9zbyIsImF1ZCI6InVybjp0ZXN0IiwiZXhwIjoxNDc0NTkyNDYzLCJuYmYiOjE0NzQ1ODg1NjN9.mE7UxgNhkieMMqtM_IiYQj-FK1KKIzB6lAptw4Mi67A"
        }] } ]
    );
```

> [!NOTE]
> Non tutti i browser/piattaforme sono in grado di riprodurre contenuto protetto. Per altre informazioni sulle funzionalità supportate, vedere la sezione [Tecnologia di riproduzione](azure-media-player-playback-technology.md).
> [!IMPORTANT]
> Il token passato al lettore è destinato al contenuto protetto e viene usato solo per gli utenti autenticati. Si presuppone che l'applicazione usi SSL o un altro tipo di misura di sicurezza. Inoltre, si presuppone che l'utente finale non usi in modo improprio il token; se non è questo il caso, coinvolgere gli esperti della sicurezza dell'organizzazione.

## <a name="next-steps"></a>Passaggi successivi ##

- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)