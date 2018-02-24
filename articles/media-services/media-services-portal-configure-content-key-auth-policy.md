---
title: Configurare criteri di autorizzazione di una chiave simmetrica tramite il portale di Azure | Microsoft Docs
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
# <a name="configure-a-content-key-authorization-policy"></a>Configurare i criteri di autorizzazione di una chiave simmetrica
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Panoramica
 Servizi multimediali di Azure consente di distribuire flussi MPEG-DASH, Smooth Streaming e HTTP Live Streaming (HLS) protetti con Advanced Encryption Standard (AES) tramite chiavi di crittografia a 128 bit o [Digital Rights Management (DRM) PlayReady](https://www.microsoft.com/playready/overview/). Servizi multimediali consente anche di distribuire flussi DASH crittografati con DRM Widevine. Sia per PlayReady che per Widevine la crittografia avviene in base alla specifica di crittografia comune (ISO/IEC 23001-7 CENC).

Servizi multimediali offre anche un servizio di distribuzione di chiavi/licenze dal quale i client possono ottenere chiavi AES o licenze PlayReady/Widevine per riprodurre contenuti crittografati.

Questo articolo illustra come usare il Portale di Azure per configurare i criteri di autorizzazione della chiave simmetrica. La chiave può essere usata in un secondo momento per crittografare i contenuti dinamicamente. Attualmente è possibile crittografare i formati HLS, MPEG-DASH e Smooth Streaming. Non è possibile crittografare i download progressivi.

Quando un lettore richiede un flusso impostato per la crittografia dinamica, Servizi multimediali usa la chiave configurata per crittografare dinamicamente i contenuti tramite crittografia DRM o AES. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi. Per determinare se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificati per la chiave.

Se si prevede di usare più chiavi simmetriche o si desidera specificare un URL per il servizio di distribuzione di chiavi/licenze diverso dal servizio di distribuzione delle chiavi di Servizi multimediali, usare le API REST o l'SDK di Servizi multimediali per .NET. Per altre informazioni, vedere:

* [Configurare criteri di autorizzazione di una chiave simmetrica tramite l'SDK di Servizi multimediali per .NET](media-services-dotnet-configure-content-key-auth-policy.md)
* [Configurare criteri di autorizzazione di una chiave simmetrica tramite le API REST di Servizi multimediali](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Considerazioni applicabili
* Quando viene creato l'account di Servizi multimediali, viene aggiunto all'account un endpoint di streaming predefinito con stato "Arrestato". Per avviare lo streaming dei contenuti e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming deve avere lo stato "In esecuzione". 
* L'asset deve contenere un set di file MP4 o Smooth Streaming a velocità in bit adattiva. Per altre informazioni, vedere l'articolo relativo alla [codifica di un asset](media-services-encode-asset.md).
* Il servizio di distribuzione delle chiavi memorizza nella cache l'oggetto ContentKeyAuthorizationPolicy e gli oggetti correlati (opzioni e restrizioni) per 15 minuti. È possibile creare un oggetto ContentKeyAuthorizationPolicy e specificare l'uso di una restrizione del token, testarlo e quindi aggiornare i criteri alla restrizione aperta. Per aggiornare i criteri alla restrizione aperta sono richiesti circa 15 minuti.
* L'endpoint di streaming di Servizi multimediali imposta il valore dell'intestazione CORS Access-Control-Allow-Origin nella risposta preliminare come carattere jolly "\*". Questo valore funziona bene con la maggior parte dei lettori, tra cui Azure Media Player, Roku, JW Player e altri. Tuttavia, alcuni lettori che usano dash.js non funzionano perché, con la modalità delle credenziali impostata su "include", XMLHttpRequest in dash.js non consente il carattere jolly "\*" come valore di Access-Control-Allow-Origin. Come soluzione alternativa a questa limitazione in dash.js, se si ospita il client da un singolo dominio, Servizi multimediali può specificare tale dominio nell'intestazione della risposta preliminare. Per assistenza, aprire un ticket di supporto tramite il portale di Azure.

## <a name="configure-the-key-authorization-policy"></a>Configurare i criteri di autorizzazione di una chiave
Per configurare i criteri di autorizzazione della chiave simmetrica, selezionare la pagina **PROTEZIONE DEL CONTENUTO** .

Servizi multimediali supporta più modalità di autenticazione degli utenti che eseguono richieste di chiavi. I criteri di autorizzazione di una chiave simmetrica possono avere una o più restrizioni di autorizzazione Open, Token o IP (IP può essere configurato con REST o SDK per .NET).

### <a name="open-restriction"></a>Restrizione Open
Se si applica una restrizione Open, il sistema distribuisce la chiave a chiunque ne faccia richiesta. Questa restrizione può essere utile a scopo di test.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Restrizione Token
Per scegliere il criterio con restrizione Token, selezionare il pulsante **TOKEN**.

I criteri con restrizione Token richiedono la presenza di un token rilasciato da un servizio token di sicurezza. Servizi multimediali supporta i token nei formati [SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (Simple Web Token, token Web semplice) e JWT (JSON Web Token, token JSON Web). Per altre informazioni, vedere [JWT authentication](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/) (Autenticazione token JWT).

Servizi multimediali non offre un servizio token di sicurezza. È possibile creare un servizio token di sicurezza personalizzato o usare il Servizio di controllo di accesso di Azure per il rilascio di token. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione token. Se il token è valido e le attestazioni nel token corrispondono a quelle configurate per la chiave simmetrica, il servizio di distribuzione delle chiavi di Servizi multimediali restituisce la chiave di crittografia al client. Per altre informazioni, vedere [Use Azure Access Control Service to issue tokens](http://mingfeiy.com/acs-with-key-services) (Usare Servizio di controllo di accesso di Azure per il rilascio di token).

Quando si configurano i criteri di restrizione del token, è necessario specificare i parametri primary verification key, issuer e audience. Il parametro primary verification key include la chiave usata per firmare il token. Il parametro issuer è il servizio token di sicurezza che rilascia il token. Il parametro audience (talvolta denominato scope) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

### <a name="playready"></a>PlayReady
Quando si protegge il contenuto con PlayReady, è necessario includere nei criteri di autorizzazione una stringa XML che definisce il modello di licenza PlayReady. Per impostazione predefinita, vengono definiti i seguenti criteri:

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

È possibile selezionare il pulsante **importa xml criterio** e fornire un codice XML diverso conforme allo schema XML definito in [Panoramica del modello di licenza PlayReady di Servizi multimediali](media-services-playready-license-template-overview.md).

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

