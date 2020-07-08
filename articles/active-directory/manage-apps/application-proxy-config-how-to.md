---
title: Come configurare un'applicazione proxy dell'applicazione | Microsoft Docs
description: Informazioni su come creare e configurare un'applicazione proxy di applicazione in pochi semplici passaggi
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/18/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c63137c6943d9adc0ea7c19f7551d1f31587f42a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84764996"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Come configurare un'applicazione proxy dell'applicazione

Questo articolo consente di comprendere come configurare un'applicazione proxy di applicazione all'interno Azure AD per esporre le applicazioni locali nel cloud.

## <a name="recommended-documents"></a>Documenti consigliati

Per altre informazioni sulle configurazioni iniziali e sulla creazione di un'applicazione proxy dell'applicazione tramite il portale di amministrazione, vedere [Pubblicare applicazioni mediante il proxy dell'applicazione Azure AD](application-proxy-add-on-premises-application.md).

Per i dettagli sulla configurazione dei connettori, vedere [Abilitare il proxy di applicazione nel portale di Azure](application-proxy-add-on-premises-application.md).

Per informazioni sul caricamento di certificati e sull'uso di domini personalizzati, vedere [Utilizzo di domini personalizzati nel proxy dell'applicazione di Azure AD](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Creare l'applicazione/impostazione degli URL

Se si seguono i passaggi nel documento [Pubblicare applicazioni mediante il proxy dell'applicazione di Azure AD](application-proxy-add-on-premises-application.md) e si riceve un errore durante la creazione dell'applicazione, vedere i dettagli dell'errore per informazioni e suggerimenti per la correzione dell'applicazione. La maggior parte dei messaggi di errore include una correzione suggerita. Per evitare errori comuni, verificare quanto segue:

- Si dispone del ruolo di amministratore con l'autorizzazione a creare un'applicazione proxy dell'applicazione
- L'URL interno è univoco
- L'URL esterno è univoco
- Gli URL iniziano con http o https e terminano con un carattere "/"
- L'URL deve essere un nome di dominio e non un indirizzo IP

Il messaggio di errore dovrebbe essere visualizzato nell'angolo in alto a destra quando si crea l'applicazione. È anche possibile selezionare l'icona di notifica per visualizzare i messaggi di errore.

![Mostra dove trovare la richiesta di notifica nella portale di Azure](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Configurare connettori/gruppi di connettori

Se si riscontrano difficoltà nella configurazione dell'applicazione a causa di un'avvertenza sui connettori e sui gruppi di connettori, vedere le istruzioni per abilitare il proxy dell'applicazione per informazioni dettagliate su come scaricare i connettori. Per altre informazioni sui connettori, vedere la [documentazione relativa ai connettori](application-proxy-connectors.md).

Se i connettori sono inattivi, non sono in grado di raggiungere il servizio. Spesso questa situazione si verifica poiché non sono aperte tutte le porte necessarie. Per visualizzare un elenco delle porte necessarie, vedere la sezione dei prerequisiti della documentazione relativa all'abilitazione del proxy dell'applicazione.

## <a name="upload-certificates-for-custom-domains"></a>Caricare certificati per domini personalizzati

I domini personalizzati consentono di specificare il dominio degli URL esterni. Per usare un dominio personalizzato, è necessario caricare il certificato per tale dominio. Per informazioni sull'uso di certificati e domini personalizzati, vedere [Utilizzo di domini personalizzati nel proxy dell'applicazione di Azure AD](application-proxy-configure-custom-domain.md).

Se si verificano problemi durante il caricamento del certificato, cercare i messaggi di errore nel portale per altre informazioni sul problema con il certificato. Problemi comuni relativi ai certificati:

- Certificato scaduto
- Certificato autofirmato
- Certificato privo di chiave privata

Il messaggio di errore viene visualizzato nell'angolo in alto a destra quando si tenta di caricare il certificato. È anche possibile selezionare l'icona di notifica per visualizzare i messaggi di errore.

## <a name="next-steps"></a>Passaggi successivi

[Pubblicare applicazioni mediante il proxy di applicazione AD Azure](application-proxy-add-on-premises-application.md)
