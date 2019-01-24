---
title: Come configurare un'applicazione proxy dell'applicazione | Microsoft Docs
description: Informazioni su come creare e configurare un'applicazione proxy dell'applicazione in pochi semplici passaggi
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 3470f7e68b095c087d188b61cea0586e0435d772
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474745"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Come configurare un'applicazione proxy dell'applicazione

Questo articolo spiega come configurare un'applicazione proxy dell'applicazione in Azure AD per esporre le applicazioni locali nel cloud.

## <a name="recommended-documents"></a>Documenti consigliati 

Per altre informazioni sulle configurazioni iniziali e sulla creazione di un'applicazione proxy dell'applicazione tramite il portale di amministrazione, vedere [Pubblicare applicazioni mediante il proxy dell'applicazione Azure AD](application-proxy-add-on-premises-application.md).

Per i dettagli sulla configurazione dei connettori, vedere [Abilitare il proxy di applicazione nel portale di Azure](application-proxy-add-on-premises-application.md).

Per informazioni sul caricamento di certificati e sull'uso di domini personalizzati, vedere [Utilizzo di domini personalizzati nel proxy dell'applicazione di Azure AD](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Creare l'applicazione/impostazione degli URL

Se si seguono i passaggi nel documento [Pubblicare applicazioni mediante il proxy dell'applicazione di Azure AD](application-proxy-add-on-premises-application.md) e si riceve un errore durante la creazione dell'applicazione, vedere i dettagli dell'errore per informazioni e suggerimenti per la correzione dell'applicazione. La maggior parte dei messaggi di errore include una correzione suggerita. Per evitare errori comuni, verificare quanto segue:

-   Si dispone del ruolo di amministratore con l'autorizzazione a creare un'applicazione proxy dell'applicazione

-   L'URL interno è univoco

-   L'URL esterno è univoco

-   Gli URL iniziano con http o https e terminano con un carattere "/"

-   L'URL deve essere un nome di dominio e non un indirizzo IP

Quando si crea l'applicazione, il messaggio di errore dovrebbe essere visualizzato in alto a destra. È anche possibile selezionare l'icona di notifica per visualizzare i messaggi di errore.

   ![Prompt di notifica](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Configurare connettori/gruppi di connettori

Se si riscontrano difficoltà nella configurazione dell'applicazione a causa di un'avvertenza sui connettori e sui gruppi di connettori, vedere le istruzioni per abilitare il proxy dell'applicazione per informazioni dettagliate su come scaricare i connettori. Per altre informazioni sui connettori, vedere la [documentazione relativa ai connettori](application-proxy-connectors.md).

Se i connettori sono inattivi, non sono in grado di raggiungere il servizio. Spesso questa situazione si verifica poiché non sono aperte tutte le porte necessarie. Per visualizzare un elenco delle porte necessarie, vedere la sezione dei prerequisiti della documentazione relativa all'abilitazione del proxy dell'applicazione.

## <a name="upload-certificates-for-custom-domains"></a>Caricare certificati per domini personalizzati

I domini personalizzati consentono di specificare il dominio degli URL esterni. Per usare un dominio personalizzato, è necessario caricare il certificato per tale dominio. Per informazioni sull'uso di certificati e domini personalizzati, vedere [Utilizzo di domini personalizzati nel proxy dell'applicazione di Azure AD](application-proxy-configure-custom-domain.md). 

Se si verificano problemi durante il caricamento del certificato, cercare i messaggi di errore nel portale per altre informazioni sul problema con il certificato. Problemi comuni relativi ai certificati:

-   Certificato scaduto

-   Certificato autofirmato

-   Certificato privo di chiave privata

Quando si tenta di caricare il certificato, il messaggio di errore viene visualizzato nell'angolo superiore destro. È anche possibile selezionare l'icona di notifica per visualizzare i messaggi di errore.

   ![Prompt di notifica](./media/application-proxy-config-how-to/error-message2.png)

## <a name="next-steps"></a>Passaggi successivi
[Pubblicare applicazioni mediante il proxy di applicazione AD Azure](application-proxy-add-on-premises-application.md)
