---
title: Configurare i criteri di protezione dei contenuti tramite il portale di Azure | Microsoft Docs
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
ms.openlocfilehash: 805e1246dbc984582528d2b351d2f14ab2e811fc
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Configurare i criteri di protezione dei contenuti tramite il portale di Azure
 Con Servizi multimediali di Azure, è possibile proteggere i file multimediali dal momento in cui escono dal computer fino alle fasi di archiviazione, elaborazione e distribuzione. È possibile usare Servizi multimediali per distribuire contenuti crittografati dinamicamente con Advanced Encryption Standard (AES), tramite chiavi di crittografia a 128 bit. È inoltre possibile usarlo con crittografia comune (CENC) tramite Digital Rights Management (DRM) PlayReady e/o Widevine e Apple FairPlay. 

Servizi multimediali offre un servizio per la distribuzione di licenze DRM e chiavi non crittografate AES ai client autorizzati. È possibile usare il portale di Azure per creare un criterio di autorizzazione per chiavi/licenze per tutti i tipi di crittografia.

Questo articolo illustra come configurare i criteri di protezione del contenuto con il portale. Descrive anche come applicare la crittografia dinamica agli asset.

## <a name="start-to-configure-content-protection"></a>Iniziare a configurare la protezione del contenuto
Per usare il portale al fine di configurare la protezione globale del contenuto usando l'account di Servizi multimediali, procedere come segue:

1. Nel [portale](https://portal.azure.com/) selezionare l'account Servizi multimediali.

2. Selezionare **Impostazioni** > **Protezione del contenuto**.

    ![Protezione del contenuto](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>criterio di autorizzazione per chiavi e licenze
Servizi multimediali supporta più modalità di autenticazione degli utenti che eseguono richieste di chiavi o licenze. È necessario configurare i criteri di autorizzazione della chiave simmetrica. Il client deve soddisfare i criteri prima che la chiave/la licenza possa essere distribuita al client stesso. I criteri di autorizzazione della chiave simmetrica possono avere una o più restrizioni di autorizzazione, ad esempio restrizione aperta o di tipo token.

È possibile usare il portale per creare un criterio di autorizzazione per chiavi/licenze per tutti i tipi di crittografia.

### <a name="open-authorization"></a>Autorizzazione aperta
Se si applica una restrizione open, il sistema distribuisce la chiave a chiunque ne faccia richiesta. Questo tipo di limitazione può risultare utile ai fini dei test. 

### <a name="token-authorization"></a>Autorizzazione con token
I criteri con restrizione del token richiedono la presenza di un token rilasciato da un servizio token di sicurezza. Servizi multimediali supporta i token nei formati SWT (Simple Web Token, token Web semplice) e JWT (JSON Web Token, token JSON Web). Servizi multimediali non offre un servizio token di sicurezza. È possibile creare un servizio token di sicurezza personalizzato o usare il Servizio di controllo di accesso di Azure per il rilascio di token. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione token. Se il token è valido e le attestazioni del token corrispondono a quelle configurate per la chiave o la licenza, il servizio di distribuzione delle chiavi di Servizi multimediali restituirà al client la chiave o la licenza richiesta.

Quando si configurano i criteri di restrizione del token, è necessario specificare i parametri primary verification key, issuer e audience. Il parametro primary verification key include la chiave usata per firmare il token. Il parametro issuer è il servizio token di sicurezza che rilascia il token. Il parametro audience (talvolta denominato scope) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

![criterio di autorizzazione per chiavi e licenze](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Modello di licenza PlayReady
Il modello di licenza PlayReady imposta la funzionalità abilitata nella licenza PlayReady. Per altre informazioni sul modello di licenza PlayReady, vedere [Panoramica del modello di licenza PlayReady di Servizi multimediali](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Non persistente
Se viene configurata come non persistente, la licenza verrà conservata in memoria solo mentre il lettore la usa.  

![Protezione del contenuto non persistente](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Persistente
Se viene configurata come persistente, la licenza verrà salvata nell'archivio persistente del client.

![Protezione del contenuto persistente](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Modello di licenza Widevine
Il modello di licenza Widevine imposta la funzionalità abilitata nelle licenze Widevine.

### <a name="basic"></a>Basic
Se si seleziona **Basic**, il modello verrà creato con tutti i valori predefiniti.

### <a name="advanced"></a>Avanzate
Per altre informazioni sul modello dei diritti Widevine, vedere [Panoramica del modello di licenza Widevine](media-services-widevine-license-template-overview.md).

![Protezione del contenuto avanzata](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Configurazione di FairPlay
Per abilitare la crittografia FairPlay, selezionare **Configurazione FairPlay**. Selezionare quindi il **certificato dell'app** e immettere la **chiave privata dell'applicazione**. Per altre informazioni sui requisiti e sulla configurazione di FairPlay, vedere [Proteggere il contenuto HLS con Apple FairPlay o Microsoft PlayReady](media-services-protect-hls-with-FairPlay.md).

![Configurazione di FairPlay](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Applicare la crittografia dinamica agli asset
Per sfruttare i vantaggi della crittografia dinamica, è necessario codificare il file di origine in un set di file MP4 a velocità in bit adattiva.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Selezionare un asset da crittografare
Per visualizzare tutti gli asset, selezionare **Impostazioni** > **Asset**.

![Opzione Asset](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Crittografare con AES o DRM
Quando si seleziona **Crittografa** per un asset, vengono visualizzate due opzioni: **AES** o **DRM**. 

#### <a name="aes"></a>AES
La crittografia con chiave non crittografata AES è abilitata su tutti i protocolli di streaming: Smooth Streaming, HLS e MPEG-DASH.

![Configurazione della crittografia](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. Dopo aver selezionato **DRM**, verranno visualizzati criteri diversi di protezione del contenuto (che devono essere configurati a questo punto) e un set di protocolli di flusso:

    a. **PlayReady e Widevine con MPEG-DASH**: il flusso MPEG-DASH viene crittografato dinamicamente con le soluzioni DRM PlayReady e Widevine.

    b. **PlayReady e Widevine con MPEG-DASH + FairPlay con HLS**: il flusso MPEG-DASH viene crittografato dinamicamente con le soluzioni DRM PlayReady e Widevine. Vengono anche crittografati i flussi HLS con FairPlay.

    c. **PlayReady solo con Smooth Streaming, HLS e MPEG-DASH**: i flussi Smooth Streaming, HLS e MPEG-DASH vengono crittografati dinamicamente con la soluzione DRM PlayReady.

    d. **Solo Widevine con MPEG-DASH**: il flusso MPEG-DASH viene crittografato dinamicamente con la soluzione DRM Widevine.
    
    e. **Solo FairPlay con HLS**: il flusso HLS viene crittografato dinamicamente con FairPlay.

2. Per abilitare la crittografia, nel pannello **Content Protection Global Settings** (Impostazioni globali protezione del contenuto) selezionare **Configurazione FairPlay**. Selezionare quindi il **certificato dell'app** e immettere la **chiave privata dell'applicazione**.

    ![Tipo di crittografia](./media/media-services-portal-content-protection/media-services-content-protection009.png)

3. Dopo aver selezionato la crittografia, fare clic su **Applica**.

>[!NOTE] 
>Se si prevede di eseguire un flusso HLS crittografato con AES in Safari, vedere il post di blog sul [flusso HLS crittografato in Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

