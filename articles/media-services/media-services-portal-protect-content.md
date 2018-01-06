---
title: Configurare i criteri di protezione del contenuto tramite il portale di Azure | Documenti Microsoft
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Configurare i criteri di protezione del contenuto tramite il portale di Azure
 Con servizi multimediali di Azure, è possibile proteggere gli elementi multimediali dal momento in che cui lascia computer tramite l'archiviazione, elaborazione e il recapito. Servizi multimediali consente di distribuire il contenuto crittografato in modo dinamico con Advanced Encryption Standard (AES) con chiavi di crittografia a 128 bit. È inoltre possibile utilizzarlo con common encryption (CENC) con PlayReady e/o Widevine gestione dei diritti digitali (DRM) e FairPlay Apple. 

Servizi multimediali fornisce un servizio per la distribuzione di licenze DRM e chiavi ai client autorizzati non crittografata AES. È possibile utilizzare il portale di Azure per creare un criterio di autorizzazione chiave licenza per tutti i tipi di crittografia.

In questo articolo viene illustrato come configurare un criterio di protezione del contenuto tramite il portale. Descrive anche come applicare la crittografia dinamica agli asset.

## <a name="start-to-configure-content-protection"></a>Iniziare a configurare la protezione del contenuto
Per utilizzare il portale per configurare la protezione del contenuto globale usando l'account di servizi multimediali, procedere come segue:

1. Nel [portal](https://portal.azure.com/), selezionare l'account di servizi multimediali.

2. Selezionare **Impostazioni** > **Protezione del contenuto**.

    ![Protezione del contenuto](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>criterio di autorizzazione per chiavi e licenze
Servizi multimediali supporta più modalità di autenticazione degli utenti che eseguono richieste di licenza o di chiave. È necessario configurare i criteri di autorizzazione chiave del contenuto. Il client quindi deve soddisfare i criteri prima che la chiave di licenza può essere recapitata a esso. I criteri di autorizzazione chiave del contenuto possono avere uno o più restrizioni, le restrizioni aperte o token.

È possibile utilizzare il portale per creare un criterio di autorizzazione chiave licenza per tutti i tipi di crittografia.

### <a name="open-authorization"></a>Autorizzazione aperta
Restrizione Open significa che il sistema invia la chiave per qualsiasi utente che effettua una richiesta di chiave. Questo tipo di limitazione può risultare utile ai fini dei test. 

### <a name="token-authorization"></a>Autorizzazione con token
I criteri di token con restrizioni devono essere accompagnato da un token rilasciato da un servizio token di sicurezza (STS). Servizi multimediali supporta i token in cui i token web semplice (SWT) e i formati di JSON Web Token (JWT). Servizi multimediali non fornisce un servizio token di sicurezza. È possibile creare un servizio token di sicurezza personalizzato o utilizzare Azure Access Control Service al rilascio di token. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione token. Se il token è valido e le attestazioni nel token corrispondono a quelli configurati per la chiave (o licenza), il servizio di distribuzione delle chiavi di servizi multimediali restituisce la chiave richiesta (o licenza) al client.

Quando si configura il criterio con restrizioni token, è necessario specificare la chiave di verifica primaria, l'autorità emittente e parametri pubblico. La chiave di verifica primaria contiene il token è stato firmato con la chiave. L'autorità emittente è il servizio token di sicurezza che emette il token. Il parametro audience (talvolta denominato scope) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

![criterio di autorizzazione per chiavi e licenze](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Modello di licenza PlayReady
Il modello di licenza PlayReady imposta la funzionalità è abilitata nella propria licenza PlayReady. Per ulteriori informazioni sul modello di licenza PlayReady, vedere il [Cenni preliminari sui modelli di licenza PlayReady di servizi multimediali](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Non persistenti
Se si configura una licenza come non persistenti, viene mantenuto in memoria solo quando il lettore utilizza la licenza.  

![Protezione del contenuto non persistente](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Persistente
Se si configura una licenza come permanente, esso viene salvato nell'archivio permanente nel client.

![Protezione del contenuto persistente](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Modello di licenza Widevine
Il modello di licenza Widevine imposta la funzionalità è abilitata nelle licenze Widevine.

### <a name="basic"></a>Basic
Quando si seleziona **base**, il modello viene creato con tutti i valori predefiniti.

### <a name="advanced"></a>Avanzate
Per ulteriori informazioni sul modello di diritti Widevine, vedere il [Cenni preliminari sui modelli di licenza Widevine](media-services-widevine-license-template-overview.md).

![Protezione avanzata del contenuto](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Configurazione di FairPlay
Per abilitare la crittografia FairPlay, selezionare **FairPlay configurazione**. Selezionare quindi il **certificato App** e immettere il **chiave segreto applicazione**. Per ulteriori informazioni sulla configurazione di FairPlay e requisiti, vedere [proteggere il contenuto con Microsoft PlayReady o di FairPlay Apple HLS](media-services-protect-hls-with-FairPlay.md).

![Configurazione di FairPlay](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Applicare la crittografia dinamica agli asset
Per sfruttare i vantaggi della crittografia dinamica, codificare il file di origine in un set di file MP4 a velocità in bit adattiva.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Selezionare un asset da crittografare
Per visualizzare tutti gli asset, selezionare **Impostazioni** > **Asset**.

![Opzione Asset](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Crittografare con AES o DRM
Quando si seleziona **Encrypt** per un asset, sono visualizzate due opzioni: **AES** o **DRM**. 

#### <a name="aes"></a>AES
Non è abilitata la crittografia con chiave in tutti i protocolli di streaming crittografata AES: Smooth Streaming, HLS e MPEG-DASH.

![Configurazione della crittografia](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. Dopo aver selezionato **DRM**, vedrai i criteri di protezione del contenuto diverso (che devono essere configurati a questo punto) e un set di protocolli di flusso:

    a. **PlayReady e Widevine con MPEG-DASH** crittografa in modo dinamico il flusso MPEG-DASH con PlayReady e Widevine DRMs.

    b. **PlayReady e Widevine con MPEG-DASH + FairPlay con HLS** crittografare dinamicamente il flusso MPEG-DASH con PlayReady e Widevine DRMs. Questa opzione consente di crittografare anche i flussi HLS con FairPlay.

    c. **PlayReady solo con Smooth Streaming, HLS e MPEG-DASH** crittografa in modo dinamico i flussi Smooth Streaming, HLS e MPEG-DASH con PlayReady DRM.

    d. **Widevine solo con MPEG-DASH** crittografa in modo dinamico il MPEG-DASH con DRM Widevine.
    
    e. **FairPlay solo con HLS** crittografa in modo dinamico il flusso HLS con FairPlay.

2. Per attivare la crittografia, FairPlay il **impostazioni globali di protezione del contenuto** pannello seleziona **FairPlay configurazione**. Selezionare quindi il **certificato App**e immettere il **chiave segreto applicazione**.

    ![Tipo di crittografia](./media/media-services-portal-content-protection/media-services-content-protection009.png)

3. Dopo aver effettuato la selezione di crittografia, selezionare **applica**.

>[!NOTE] 
>Se si prevede di riprodurre un HLS crittografato con AES in Safari, vedere il post di blog [HLS crittografato in Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

