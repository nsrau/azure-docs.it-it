---
title: Selezionare un layout di pagina-Azure Active Directory B2C
description: Informazioni su come selezionare un layout di pagina in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 108d86e35422e1dc1d10aeb6b2c9488f5067232e
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389678"
---
# <a name="select-a-page-layout-in-azure-active-directory-b2c-using-custom-policies"></a>Selezionare un layout di pagina in Azure Active Directory B2C usando criteri personalizzati

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

È possibile abilitare il codice lato client JavaScript nei criteri di Azure Active Directory B2C (Azure AD B2C) se si usano flussi utente o criteri personalizzati. Per abilitare JavaScript per le applicazioni, è necessario aggiungere un elemento ai [criteri personalizzati](active-directory-b2c-overview-custom.md), selezionare un layout di pagina e usare [b2clogin.com](b2clogin.md) nelle richieste.

Un layout di pagina è un'associazione di elementi che Azure AD B2C fornisce e il contenuto fornito.

Questo articolo illustra come selezionare un layout di pagina in Azure AD B2C tramite la configurazione in un criterio personalizzato.

> [!NOTE]
> Se si vuole abilitare JavaScript per i flussi utente, vedere la pagina relativa alle [versioni del layout di pagina e JavaScript in Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="replace-datauri-values"></a>Sostituire i valori di DataUri

Nei criteri personalizzati possono essere inclusi elementi [ContentDefinition](contentdefinitions.md) che definiscono i modelli HTML usati nel percorso utente. L'elemento **ContentDefinition** contiene un elemento **DataUri** che fa riferimento agli elementi della pagina forniti da Azure AD B2C. L'elemento **LoadUri** è il percorso relativo del contenuto HTML e CSS specificato dall'utente.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Per selezionare un layout di pagina, è necessario modificare i valori di **DataUri** in [ContentDefinitions](contentdefinitions.md) nei criteri. Passando dai valori di **DataUri** precedenti a quelli nuovi, si seleziona un pacchetto non modificabile. Il vantaggio dell'uso di questo pacchetto è la certezza che non cambierà e non provocherà un comportamento imprevisto nella pagina.

Per configurare un layout di pagina, usare la tabella seguente per trovare i valori **DataUri** .

| Valore di DataUri precedente | Nuovo valore di DataUri |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>Log delle modifiche della versione

I pacchetti del layout di pagina vengono aggiornati periodicamente per includere correzioni e miglioramenti negli elementi della pagina. Nel log delle modifiche seguente vengono specificate le modifiche introdotte in ogni versione.

### <a name="120"></a>1.2.0 
- Tutte le pagine
  - Correzioni di accessibilità
  - È ora possibile aggiungere l'attributo `data-preload="true"` nei tag HTML per controllare l'ordine di caricamento per CSS e JavaScript. Gli scenari includono:
      - Usare il collegamento CSS per caricare il CSS contemporaneamente al codice HTML in modo che non venga "sfarfallio" tra il caricamento dei file
      - Questo attributo consente di controllare l'ordine in cui i tag di script vengono recuperati ed eseguiti prima del caricamento della pagina
  - Il campo posta elettronica è ora `type=email` e le tastiere mobili forniranno i suggerimenti corretti
  - Supporto per la conversione di Chrome
- Pagina unificata e autocertificata
  - I campi nome utente/indirizzo di posta elettronica e password ora usano l'elemento HTML del modulo.  In questo modo Edge e IE verranno consentiti per salvare correttamente queste informazioni
  
### <a name="110"></a>1.1.0

- Pagina eccezione (globalexception)
  - Correzione accessibilità
  - Rimosso il messaggio predefinito quando non è presente alcun contatto dal criterio
  - CSS predefinito rimosso
- Pagina multi-factor authentication (a più fattori)
  - Pulsante ' conferma codice ' rimosso
  - Il campo di input per il codice ora accetta solo un input composto da sei (6) caratteri
  - La pagina tenterà automaticamente di verificare il codice immesso quando viene immesso un codice di 6 cifre, senza che sia necessario fare clic su un pulsante
  - Se il codice è errato, il campo di input viene cancellato automaticamente
  - Dopo tre (3) tentativi con codice errato, B2C Invia un errore all'relying party
  - Correzioni di accessibilità
  - CSS predefinito rimosso
- Pagina autocertificata (selfasserted)
  - Rimosso avviso di annullamento
  - Classe CSS per gli elementi error
  - Mostra/Nascondi la logica di errore migliorata
  - CSS predefinito rimosso
- SSP unificato (unifiedssp)
  - Aggiunto il controllo Mantieni l'accesso (KMSI)

### <a name="100"></a>1.0.0

- Versione iniziale

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come personalizzare l'interfaccia utente delle applicazioni sono disponibili nell'argomento [Personalizzare l'interfaccia utente dell'applicazione usando un criterio personalizzato in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
