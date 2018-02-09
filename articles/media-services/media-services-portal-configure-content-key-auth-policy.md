---
title: Configurare un criterio di autorizzazione chiave del contenuto tramite il portale di Azure | Documenti Microsoft
description: Informazioni su come configurare i criteri di autorizzazione per una chiave simmetrica.
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: a0ab954bda3340b9010b16f54e343933808cc463
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="configure-a-content-key-authorization-policy"></a>Configurare un criterio di autorizzazione chiave del contenuto
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Panoramica
 È possibile utilizzare servizi multimediali di Azure per distribuire i flussi MPEG-DASH, Smooth Streaming e HTTP Live Streaming (HLS) protetti con Advanced Encryption Standard (AES) con chiavi di crittografia a 128 bit o [gestione dei diritti digitali (DRM)PlayReady](https://www.microsoft.com/playready/overview/). Con servizi multimediali, è anche possibile recapitare i flussi DASH crittografati con DRM Widevine. PlayReady sia Widevine vengono crittografati in base alla specifica di crittografia (ISO/IEC 23001-7 CENC) comuni.

Servizi multimediali fornisce anche un servizio di recapito chiave/licenza da cui i client possono ottenere le chiavi AES o PlayReady/Widevine licenze per riprodurre il contenuto crittografato.

Questo articolo illustra come usare il Portale di Azure per configurare i criteri di autorizzazione della chiave simmetrica. La chiave può essere usata in un secondo momento per crittografare i contenuti dinamicamente. Attualmente, è possibile crittografare i formati di Smooth Streaming, MPEG-DASH e HLS. È possibile crittografare il download progressivo.

Quando un lettore richiede un flusso che è impostato in modo dinamico da crittografare, servizi multimediali Usa la chiave configurata per crittografare dinamicamente il contenuto mediante la crittografia AES o DRM. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi. Per determinare se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione per la chiave specificata.

Se si prevede di avere più chiavi simmetriche o per specificare un URL di servizio di recapito chiave licenza diverso dal servizio di distribuzione delle chiavi di servizi multimediali, utilizzare la Media Services .NET SDK o le API REST. Per altre informazioni, vedere:

* [Configurare un criterio di autorizzazione chiave del contenuto con Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)
* [Configurare un criterio di autorizzazione chiave del contenuto tramite l'API REST di servizi multimediali](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Le considerazioni
* Quando viene creato l'account di servizi multimediali, endpoint di streaming predefinito viene aggiunto al tuo account nello stato "Stopped". Per avviare lo streaming del contenuto e sfruttare i vantaggi di creazione dinamica dei pacchetti e la crittografia dinamica, l'endpoint di streaming deve essere nello stato "Running". 
* L'asset deve contenere un set di file MP4 o Smooth Streaming a velocità in bit adattiva. Per altre informazioni, vedere l'articolo relativo alla [codifica di un asset](media-services-encode-asset.md).
* Il servizio di distribuzione delle chiavi memorizza nella cache ContentKeyAuthorizationPolicy e gli oggetti correlati (opzioni di criteri e restrizioni) per 15 minuti. È possibile creare un oggetto ContentKeyAuthorizationPolicy e specificare se usare una restrizione token, testarlo e quindi aggiornare i criteri di restrizione open. Questo processo richiede circa 15 minuti prima le opzioni di criteri per la versione aperta.
* Un endpoint di streaming di servizi multimediali imposta il valore dell'intestazione CORS Access-Control-Allow-Origin nella risposta preliminare come il carattere jolly "\*". Questo valore funziona bene con la maggior parte dei lettori, tra cui Azure Media Player, Roku e JWPlayer e altri. Tuttavia, alcuni lettori che utilizzano dash.js non funzionano perché, in modalità di credenziali impostata su "include" XMLHttpRequest nella loro dash.js non consente il carattere jolly "\*" come valore di Access-Control-Allow-Origin. Come soluzione alternativa per questo limite di dash.js, se si ospita il client da un singolo dominio, servizi multimediali può specificare tale dominio nell'intestazione della risposta preliminare. Per assistenza, aprire un ticket di supporto tramite il portale di Azure.

## <a name="configure-the-key-authorization-policy"></a>Configurare i criteri di autorizzazione della chiave
Per configurare i criteri di autorizzazione della chiave simmetrica, selezionare la pagina **PROTEZIONE DEL CONTENUTO** .

Servizi multimediali supporta più modalità per autenticare gli utenti che eseguono richieste di chiavi. I criteri di autorizzazione chiave del contenuto possono essere aperti, token o restrizioni IP. (IP può essere configurato con REST o .NET SDK).

### <a name="open-restriction"></a>Restrizione Open
La restrizione open, che il sistema invia la chiave per qualsiasi utente che effettua una richiesta di chiave. Questa restrizione può essere utile a scopo di test.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Restrizione Token
Per scegliere i criteri con restrizione token, selezionare il **TOKEN** pulsante.

I criteri con restrizione token devono essere accompagnato da un token rilasciato da un servizio token di sicurezza (STS). Servizi multimediali supporta i token nel token web semplice ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e formati diversi da JSON Web Token (JWT). Per ulteriori informazioni, vedere [autenticazione JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Servizi multimediali non fornisce servizio token di sicurezza. È possibile creare un servizio token di sicurezza personalizzato o utilizzare Azure Access Control Service al rilascio di token. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione token. Se il token è valido e le attestazioni nel token corrispondono a quelli configurati per la chiave simmetrica, il servizio di distribuzione delle chiavi di servizi multimediali restituisce la chiave di crittografia al client. Per ulteriori informazioni, vedere [utilizzare Azure Access Control Service al rilascio di token](http://mingfeiy.com/acs-with-key-services).

Quando si configura il criterio con restrizioni token, è necessario specificare la chiave di verifica primaria, l'autorità emittente e parametri pubblico. La chiave di verifica primaria contiene il token è stato firmato con la chiave. L'autorità emittente è il servizio token di sicurezza che emette il token. Il parametro audience (talvolta denominato scope) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

### <a name="playready"></a>PlayReady
Quando si proteggono i contenuti con PlayReady, una delle operazioni che è necessario specificare nei criteri di autorizzazione è una stringa XML che definisce il modello di licenza PlayReady. Per impostazione predefinita, vengono definiti i seguenti criteri:

    <PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
          <LicenseTemplates>
            <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
              <ContentKey i:type="ContentEncryptionKeyFromHeader" />
              <LicenseType>Nonpersistent</LicenseType>
              <PlayRight>
                <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
              </PlayRight>
            </PlayReadyLicenseTemplate>
          </LicenseTemplates>
        </PlayReadyLicenseResponseTemplate>

È possibile selezionare il **importare criteri xml** pulsante e fornire un diverso XML conforme allo schema XML definito nel [Cenni preliminari sui modelli di licenza PlayReady di servizi multimediali](media-services-playready-license-template-overview.md).

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

