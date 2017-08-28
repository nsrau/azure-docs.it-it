---
title: Uso di castLabs per distribuire licenze Widevine a Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo illustra come usare Servizi multimediali di Azure per distribuire un flusso crittografato in modo dinamico da Servizi multimediali di Azure mediante DRM di PlayReady e Widevine. La licenza per PlayReady viene distribuita dal server licenze PlayReady di Servizi multimediali, mentre la licenza per Widevine viene distribuita dal server licenze castLabs.
services: media-services
documentationcenter: 
author: Mingfeiy
manager: cfowler
editor: 
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: Mingfeiy;willzhan;Juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 25b4da6a555fa2a5e029eadbea45eb6148518e70
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017

---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Uso di castLabs per distribuire licenze Widevine a Servizi multimediali di Azure
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Overview
Questo articolo illustra come usare Servizi multimediali di Azure per distribuire un flusso crittografato in modo dinamico da Servizi multimediali di Azure mediante DRM di PlayReady e Widevine. La licenza per PlayReady viene distribuita dal server licenze PlayReady di Servizi multimediali, mentre la licenza per Widevine viene distribuita dal server licenze **castLabs** .

Per la riproduzione di contenuti in streaming protetti da CENC (PlayReady e/o Widevine), è possibile usare [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Per informazioni dettagliate vedere la [documentazione dell’AMP](http://amp.azure.net/libs/amp/latest/docs/)

Il diagramma seguente illustra un'architettura di integrazione di alto livello tra castLabs e Servizi multimediali di Azure.

![integrazione](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Configurazione di sistema tipica
* I contenuti multimediali vengono archiviati in Servizi multimediali di Azure.
* Gli ID delle chiavi simmetriche vengono archiviati sia in castLabs sia in Servizi multimediali di Azure.
* castLabs e Servizi multimediali di Azure dispongono entrambi di un sistema di autenticazione dei token integrato. I token di autenticazione vengono illustrati nelle sezioni seguenti. 
* Quando un client deve trasmettere un video in streaming, il contenuto viene crittografato dinamicamente con la **crittografia comune** e organizzato da Servizi multimediali di Azure in pacchetti dinamici creati con i protocolli Smooth Streaming e DASH. Viene inoltre fornita la crittografia dei flussi elementari M2TS PlayReady per il protocollo di streaming HLS.
* La licenza per PlayReady viene recuperata dal server licenze di Servizi multimediali di Azure, mentre la licenza per Widevine viene recuperata dal server licenze castLabs. 
* Media Player decide automaticamente le licenze da recuperare in base alle caratteristiche della piattaforma client. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Generazione di token di autenticazione per ottenere una licenza
castLabs e Servizi multimediali di Azure supportano entrambi il formato di token JWT (JSON Web Token), necessario per autorizzare una licenza. 

### <a name="jwt-token-in-ams"></a>Token JWT in Servizi multimediali di Azure
La tabella seguente descrive il token JWT usato in Servizi multimediali di Azure. 

| Issuer | Stringa dell'autorità di certificazione rilasciata dal servizio token di sicurezza scelto |
| --- | --- |
| Audience |Stringa dei destinatari rilasciata dal servizio token di sicurezza usato |
| Claims |Set di attestazioni |
| NotBefore |Validità di inizio del token |
| Expires |Validità di fine del token |
| SigningCredentials |Chiave condivisa tra il server licenze PlayReady, il server licenze castLabs e il servizio token di sicurezza (STS); può essere una chiave simmetrica o asimmetrica. |

### <a name="jwt-token-in-castlabs"></a>Token JWT in castLabs
La tabella seguente descrive il token JWT usato in castLabs. 

| Nome | Descrizione |
| --- | --- |
| optData |Stringa JSON contenente informazioni relative all'utente. |
| crt |Stringa JSON contenente informazioni sull'asset, sulla relativa licenza e sui diritti di riproduzione. |
| iat |Data e ora corrente nel periodo. |
| jti |Identificatore univoco per il token (ogni token può essere usato una sola volta nel sistema castLabs). |

## <a name="sample-solution-set-up"></a>Configurazione della soluzione di esempio
La [soluzione di esempio](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) è costituita da due progetti:

* Un'app console che consente di impostare le restrizioni DRM su un asset già acquisito, sia per PlayReady sia per Widevine.
* Un'applicazione Web incaricata della distribuzione dei token, che possono essere considerati come una versione molto semplificata di un servizio token di sicurezza.

Per usare l'applicazione console:

1. Modificare il file app.config per impostare le credenziali di Servizi multimediali di Azure, le credenziali di castLabs, la configurazione del servizio token di sicurezza e la chiave condivisa.
2. Caricare un asset in Servizi multimediali di Azure.
3. Ottenere l'UUID dall'asset caricato e modificare la riga 32 nel file Program.cs:
   
      var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();
4. Usare un ID asset per assegnare un nome all'asset nel sistema castLabs (riga 44 nel file Program.cs).
   
   È necessario impostare l'ID asset per **castLabs**: deve essere una stringa alfanumerica univoca.
5. Eseguire il programma.

Per usare l'applicazione Web (STS):

1. Modificare il file web.config per impostare l'ID società di castlabs, la configurazione del servizio token di sicurezza e la chiave condivisa.
2. Eseguire la distribuzione nei siti Web di Azure.
3. Passare al sito Web.

## <a name="playing-back-a-video"></a>Riproduzione di un video
Per riprodurre un video crittografato con la crittografia comune (PlayReady e/o Widevine), è possibile usare [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Quando si esegue l'app console, vengono restituiti l'ID della chiave simmetrica e l'URL del manifesto.

1. Aprire una nuova scheda e avviare il servizio token di sicurezza: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Accedere a [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3. Incollare l'URL di streaming 
4. Scegliere la casella di controllo **Opzioni avanzate** .
5. Nell'elenco a discesa **Protezione** , selezionare PlayReady e/o Widevine.
6. Incollare il token ottenuto dal servizio token di sicurezza nella casella di testo Token. 
   
   Quando si usa il server licenze castLabs, non è necessario aggiungere il prefisso "Bearer=" davanti al token. Se presente, quindi, è necessario rimuoverlo prima di inviare il token.
7. Aggiornare il lettore.
8. A questo punto, dovrebbe essere possibile riprodurre il video.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


