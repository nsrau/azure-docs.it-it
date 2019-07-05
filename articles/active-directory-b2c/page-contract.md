---
title: Selezionare un contratto di pagina - Azure Active Directory B2C
description: Informazioni su come selezionare un contratto di pagina in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f7098d805b0e3f1527587fc3411cd4c3b234b057
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540385"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>Selezionare un contratto di pagina in Azure Active Directory B2C tramite criteri personalizzati

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

È possibile abilitare il codice JavaScript lato client nei criteri di Azure Active Directory (Azure AD) B2C, indipendentemente dall'uso di flussi degli utenti o i criteri personalizzati. Per abilitare JavaScript per le applicazioni, è necessario aggiungere un elemento per il [criteri personalizzati](active-directory-b2c-overview-custom.md), selezionare un contratto di pagina e usare [b2clogin.com](b2clogin.md) nelle richieste.

Un contratto di pagina è un'associazione di elementi forniti da Azure AD B2C e del contenuto fornito dall'utente.

Questo articolo illustra come selezionare un contratto di pagina in Azure AD B2C mediante la configurazione in un criterio personalizzato.

> [!NOTE]
> Se si desidera abilitare JavaScript per i flussi utente, vedere [JavaScript e pagina Contratto di versioni in Azure Active Directory B2C](user-flow-javascript-overview.md).

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

Per selezionare un contratto di pagina, è necessario modificare i valori di **DataUri** negli elementi [ContentDefinition](contentdefinitions.md) nei criteri. Passando dai valori di **DataUri** precedenti a quelli nuovi, si seleziona un pacchetto non modificabile. Il vantaggio dell'uso di questo pacchetto è la certezza che non cambierà e non provocherà un comportamento imprevisto nella pagina.

Per configurare un contratto di pagina, usare la tabella seguente per trovare i valori di **DataUri**.

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

## <a name="version-change-log"></a>Log delle modifiche di versione

I pacchetti di pagina del contratto vengono aggiornati periodicamente in modo da includere correzioni e miglioramenti nei relativi elementi di pagina. Il log delle modifiche seguente specifica le modifiche introdotte in ogni versione.

### <a name="110"></a>1.1.0

- Pagina delle eccezioni (globalexception)
  - Correzione di accessibilità
  - Rimuovere il messaggio predefinito quando non vi è alcun contatto dai criteri
  - Default CSS rimosso
- Pagina MFA (a più fattori)
  - Pulsante 'Confirm codice' rimosso
  - Il campo di input per l'accetta attualmente solo codice immettere caratteri fino a sei (6)
  - La pagina tenterà automaticamente di verificare il codice inserito quando viene immesso un codice di 6 cifre, senza dover fare clic su un pulsante qualsiasi
  - Se il codice non è corretto quindi il campo di input viene cancellato automaticamente
  - Dopo tre (3) tentativi con un codice non corretto, B2C invia un errore al servizio
  - Correzioni per l'accessibilità
  - Default CSS rimosso
- Pagina autocertificato (selfasserted)
  - Avviso Annulla rimozione
  - Classe CSS per gli elementi di errore
  - Mostra/Nascondi la logica degli errori migliorata
  - Default CSS rimosso
- SSP unificato (unifiedssp)
  - Aggiunta Mantieni che l'accesso nel controllo (KMSI)

### <a name="100"></a>1.0.0

- Versione iniziale

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come personalizzare l'interfaccia utente delle applicazioni sono disponibili nell'argomento [Personalizzare l'interfaccia utente dell'applicazione usando un criterio personalizzato in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
