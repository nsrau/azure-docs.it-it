---
title: Versioni del layout di pagina
titleSuffix: Azure AD B2C
description: Cronologia delle versioni del layout di pagina per la personalizzazione dell'interfaccia utente nei criteri personalizzati.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 868d99a82009dc8545fc24ad1cfa1da3959da131
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852066"
---
# <a name="page-layout-versions"></a>Versioni del layout di pagina

I pacchetti del layout di pagina vengono aggiornati periodicamente per includere correzioni e miglioramenti negli elementi della pagina. Nel log delle modifiche seguente vengono specificate le modifiche introdotte in ogni versione.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="self-asserted-page-selfasserted"></a>Pagina autocertificata (selfasserted)

**2.1.0**

- Correzioni per la localizzazione e l'accessibilità.

**2.0.0**

- Aggiunta del supporto per la [visualizzazione dei controlli](display-controls.md) nei criteri personalizzati.

**1.2.0**

- I campi nome utente/indirizzo di posta elettronica e password ora usano l' `form` elemento HTML per consentire a Edge e Internet Explorer di salvare correttamente queste informazioni.
- È stato aggiunto un ritardo di convalida dell'input utente configurabile per migliorare l'esperienza utente.
-  Correzioni di accessibilità
- È ora possibile aggiungere l' `data-preload="true"` attributo [nei tag HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) per controllare l'ordine di caricamento per CSS e JavaScript.
  - Caricare i file CSS collegati allo stesso tempo del modello HTML in modo che non venga "sfarfallio" tra il caricamento dei file.
  - Controllare l'ordine in cui i `script` tag vengono recuperati ed eseguiti prima del caricamento della pagina.
- Il campo posta elettronica è ora `type=email` e le tastiere per dispositivi mobili forniranno i suggerimenti corretti
- Supporto per la conversione di Chrome

**1.1.0**

- Rimosso avviso di annullamento
- Classe CSS per gli elementi error
- Mostra/Nascondi la logica di errore migliorata
- CSS predefinito rimosso

**1.0.0**

- Versione iniziale

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>Pagina di iscrizione dell'accesso unificato con collegamento per la reimpostazione della password (unifiedssp)

**2.1.0**

- Aggiunta del supporto per più collegamenti di iscrizione.
- Aggiunta del supporto per la convalida dell'input dell'utente in base alle regole del predicato definite nei criteri.

**1.2.0**

- I campi nome utente/indirizzo di posta elettronica e password ora usano l' `form` elemento HTML per consentire a Edge e Internet Explorer di salvare correttamente queste informazioni.
-  Correzioni di accessibilità
- È ora possibile aggiungere l' `data-preload="true"` attributo [nei tag HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) per controllare l'ordine di caricamento per CSS e JavaScript.
  - Caricare i file CSS collegati allo stesso tempo del modello HTML in modo che non venga "sfarfallio" tra il caricamento dei file.
  - Controllare l'ordine in cui i `script` tag vengono recuperati ed eseguiti prima del caricamento della pagina.
- Il campo posta elettronica è ora `type=email` e le tastiere per dispositivi mobili forniranno i suggerimenti corretti
- Supporto per la conversione di Chrome

**1.1.0**

- Aggiunto il controllo Mantieni l'accesso (KMSI)

**1.0.0**

- Versione iniziale

## <a name="mfa-page-multifactor"></a>Pagina multi-factor authentication (a più fattori)

**1.2.1**

- Correzioni di accessibilità sui modelli predefiniti

**1.2.0**

-  Correzioni di accessibilità
- È ora possibile aggiungere l' `data-preload="true"` attributo [nei tag HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) per controllare l'ordine di caricamento per CSS e JavaScript.
  - Caricare i file CSS collegati allo stesso tempo del modello HTML in modo che non venga "sfarfallio" tra il caricamento dei file.
  - Controllare l'ordine in cui i `script` tag vengono recuperati ed eseguiti prima del caricamento della pagina.
- Il campo posta elettronica è ora `type=email` e le tastiere per dispositivi mobili forniranno i suggerimenti corretti
- Supporto per la conversione di Chrome

**1.1.0**

- Pulsante ' conferma codice ' rimosso
- Il campo di input per il codice ora accetta solo un input composto da sei (6) caratteri
- La pagina tenterà automaticamente di verificare il codice immesso quando viene immesso un codice di 6 cifre, senza che sia necessario fare clic su un pulsante
- Se il codice è errato, il campo di input viene cancellato automaticamente
- Dopo tre (3) tentativi con codice errato, B2C Invia un errore all'relying party
-  Correzioni di accessibilità
- CSS predefinito rimosso

**1.0.0**

- Versione iniziale

## <a name="exception-page-globalexception"></a>Pagina eccezione (globalexception)

**1.2.0**

-  Correzioni di accessibilità
- È ora possibile aggiungere l' `data-preload="true"` attributo [nei tag HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) per controllare l'ordine di caricamento per CSS e JavaScript.
  - Caricare i file CSS collegati allo stesso tempo del modello HTML in modo che non venga "sfarfallio" tra il caricamento dei file.
  - Controllare l'ordine in cui i `script` tag vengono recuperati ed eseguiti prima del caricamento della pagina.
- Il campo posta elettronica è ora `type=email` e le tastiere per dispositivi mobili forniranno i suggerimenti corretti
- Supporto per la conversione di Chrome

**1.1.0**

- Correzione accessibilità
- Rimosso il messaggio predefinito quando non è presente alcun contatto dal criterio
- CSS predefinito rimosso

**1.0.0**

- Versione iniziale

## <a name="other-pages-providerselection-claimsconsent-unifiedssd"></a>Altre pagine (ProviderSelection, ClaimsConsent, UnifiedSSD)

**1.2.0**

-  Correzioni di accessibilità
- È ora possibile aggiungere l' `data-preload="true"` attributo [nei tag HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) per controllare l'ordine di caricamento per CSS e JavaScript.
  - Caricare i file CSS collegati allo stesso tempo del modello HTML in modo che non venga "sfarfallio" tra il caricamento dei file.
  - Controllare l'ordine in cui i `script` tag vengono recuperati ed eseguiti prima del caricamento della pagina.
- Il campo posta elettronica è ora `type=email` e le tastiere per dispositivi mobili forniranno i suggerimenti corretti
- Supporto per la conversione di Chrome

**1.0.0**

- Versione iniziale

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate su come personalizzare l'interfaccia utente delle applicazioni in criteri personalizzati, vedere [personalizzare l'interfaccia utente dell'applicazione usando un criterio personalizzato](custom-policy-ui-customization.md).
