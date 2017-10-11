---
title: Configurazione dei criteri di protezione del contenuto tramite il portale di Azure | Microsoft Docs
description: Questo articolo illustra come usare il portale di Azure per configurare i criteri di protezione del contenuto. Descrive anche come abilitare la crittografia dinamica per gli asset.
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: 67b3fa9936daebeafb7e87fe3a7b0c7e0105b3b3
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="configuring-content-protection-policies-using-the-azure-portal"></a>Configurazione dei criteri di protezione del contenuto tramite il portale di Azure
> [!NOTE]
> Per completare l'esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> 
> 

## <a name="overview"></a>Panoramica
Servizi multimediali di Microsoft Azure (AMS) consente di proteggere i file multimediali dal momento in cui escono dal computer fino alle fasi di archiviazione, elaborazione e recapito. Servizi multimediali consente di distribuire contenuti crittografati dinamicamente con AES (Advanced Encryption Standard) mediante chiavi di crittografia a 128 bit, con la crittografia comune (CENC) mediante PlayReady e/o la soluzione DRM Widevine e Apple FairPlay. 

AMS offre un servizio per la distribuzione di licenze DRM e chiavi non crittografate AES ai client autorizzati. Il portale di Azure consente di creare un **criterio di autorizzazione per chiavi e licenze** per tutti i tipi di crittografia.

Questo articolo illustra come configurare i criteri di protezione del contenuto con il portale di Azure. Descrive anche come applicare la crittografia dinamica agli asset.


> [!NOTE]
> Se è stato usato portale di Azure classico per creare criteri di protezione, i criteri potrebbero non essere visualizzati nel [portale di Azure](https://portal.azure.com/). Questi criteri sono tuttavia ancora disponibili. È possibile esaminarli mediante Azure Media Services .NET SDK o lo [strumento di esplorazione di Servizi multimediali di Azure](https://github.com/Azure/Azure-Media-Services-Explorer/releases). Per visualizzare i criteri, fare clic con il pulsante destro del mouse sull'asset, scegliere Display information (Visualizza informazioni) (F4), fare clic sulla scheda Content keys (Chiavi simmetriche), quindi sulla chiave. 
> 
> Se si vuole crittografare l'asset con i nuovi criteri, configurarli con il portale di Azure, fare clic su Salva e riapplicare la crittografia dinamica. 
> 
> 

## <a name="start-configuring-content-protection"></a>Avviare la configurazione della protezione del contenuto
Per usare il portale per avviare la configurazione della protezione del contenuto per l'intero account AMS, eseguire questa procedura:

1. Nel [portale di Azure ](https://portal.azure.com/) selezionare l'account Servizi multimediali di Azure.
2. Selezionare **Impostazioni** > **Protezione del contenuto**.

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>criterio di autorizzazione per chiavi e licenze
AMS supporta più modalità di autenticazione degli utenti che eseguono richieste di chiavi o licenze. I criteri di autorizzazione delle chiavi simmetriche devono essere configurati dall'utente e soddisfatti dal client perché la chiave o la licenza possa essere distribuita al client. I criteri di autorizzazione delle chiavi simmetriche possono avere una o più restrizioni di tipo **Open** o **Token**.

Il portale di Azure consente di creare un **criterio di autorizzazione per chiavi e licenze** per tutti i tipi di crittografia.

### <a name="open"></a>Apri
Limitazione aperta indica che il sistema distribuirà la chiave a chiunque ne faccia richiesta. Questo tipo di limitazione può risultare utile ai fini dei test. 

### <a name="token"></a>token
I criteri con restrizione Token devono essere accompagnati da un token rilasciato da un servizio STS (Secure Token Service, servizio token di sicurezza). Servizi multimediali supporta i token nei formati Simple Web Tokens (SWT) e JSON Web Token (JWT). Servizi multimediali non fornisce servizi token di sicurezza. Per il rilascio di token è possibile creare un servizio token di sicurezza personalizzato oppure usare il Servizio di controllo di accesso di Microsoft Azure. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione token. Il servizio di distribuzione di chiavi di Servizi multimediali restituirà al client la chiave o la licenza richiesta se il token è valido e le attestazioni del token corrispondono a quelle configurate per la chiave o la licenza.

Quando si configurano i criteri di restrizione token, è necessario specificare i parametri primary verification key, issuer e audience. Il parametro primary verification key include la chiave usata per firmare il token. Il parametro issuer è il servizio token di sicurezza che emette il token. Il parametro audience (talvolta denominato scope) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-rights-template"></a>Modello dei diritti PlayReady
Per informazioni dettagliate sul modello dei diritti PlayReady, vedere [Panoramica del modello di licenza PlayReady di Servizi multimediali](media-services-playready-license-template-overview.md).

### <a name="non-persistent"></a>Non persistente
Se viene configurata come non persistente, la licenza verrà conservata in memoria solo mentre il lettore la usa.  

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Persistente
Se si configura la licenza come persistente, questa verrà salvata nell'archivio persistente del client.

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-rights-template"></a>Modello dei diritti Widevine
Per informazioni dettagliate sul modello dei diritti Widevine, vedere [Panoramica del modello di licenza Widevine](media-services-widevine-license-template-overview.md).

### <a name="basic"></a>Basic
Se si seleziona **Basic**, il modello verrà creato con tutti i valori predefiniti.

### <a name="advanced"></a>Avanzate
Per informazioni dettagliate sull'opzione avanzata delle configurazioni Widevine, vedere [questo](media-services-widevine-license-template-overview.md) argomento.

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Configurazione di FairPlay
Per abilitare la crittografia FairPlay, è necessario indicare il certificato dell'app e la chiave privata dell'applicazione (ASK) tramite l'opzione di configurazione FairPlay. Per informazioni dettagliate sulla configurazione e i requisiti di FairPlay, vedere [questo](media-services-protect-hls-with-fairplay.md) articolo.

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Applicare la crittografia dinamica agli asset
Per sfruttare i vantaggi della crittografia dinamica, è necessario codificare il file di origine in un set di file MP4 a velocità in bit adattiva.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Selezionare un asset da crittografare
Per visualizzare tutti gli asset, selezionare **Impostazioni** > **Asset**.

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Crittografare con AES o DRM
Quando si seleziona **Crittografa** per un asset, sono disponibili due opzioni: **AES** o **DRM**. 

#### <a name="aes"></a>AES
La crittografia con chiave non crittografata AES sarà abilitata su tutti i protocolli di streaming: Smooth Streaming, HLS e MPEG-DASH.

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
Nella scheda DRM sono disponibili diverse opzioni per i criteri di protezione del contenuto (che è necessario aver configurato a questo punto) e un set di protocolli di streaming.

* **PlayReady e Widevine con MPEG-DASH** : il flusso MPEG-DASH verrà crittografato dinamicamente con le soluzioni DRM PlayReady e Widevine.
* **PlayReady e Widevine con MPEG-DASH + FairPlay con HLS** : il flusso MPEG-DASH verrà crittografato dinamicamente con le soluzioni DRM PlayReady e Widevine. Verranno anche crittografati i flussi HLS con FairPlay.
* **PlayReady solo con Smooth Streaming, HLS e MPEG-DASH** : i flussi Smooth Streaming, HLS e MPEG-DASH verranno crittografati dinamicamente con la soluzione DRM PlayReady.
* **Solo Widevine con MPEG-DASH** : il flusso MPEG-DASH verrà crittografato dinamicamente con la soluzione DRM Widevine.
* **Solo FairPlay con HLS** : il flusso HLS verrà crittografato dinamicamente con FairPlay.

Per abilitare la crittografia FairPlay, è necessario indicare il certificato dell'app e la chiave privata dell'applicazione (ASK) tramite l'opzione di configurazione FairPlay del pannello delle impostazioni di Protezione del contenuto.

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection009.png)

Dopo aver selezionato la crittografia, fare clic su **Applica**.

>[!NOTE] 
>Se si prevede di eseguire un flusso HLS crittografato con AES in Safari, vedere [questo blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Passaggi successivi
Analizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

