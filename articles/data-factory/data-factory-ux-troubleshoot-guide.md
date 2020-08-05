---
title: Risolvere i problemi di Azure Data Factory UX
description: Informazioni su come risolvere i problemi di Azure Data Factory UX.
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9a96c385c25eea1c5e217665f162de402a73e558
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567950"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Risolvere i problemi di Azure Data Factory UX
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra i metodi comuni per la risoluzione dei problemi per Azure Data Factory UX.

## <a name="adf-ux-not-loading"></a>Non è in corso il caricamento del file ADF

> [!NOTE]
> Il Azure Data Factory UX supporta ufficialmente Microsoft Edge e Google Chrome. L'uso di altri Web browser può causare un comportamento imprevisto o non documentato.

### <a name="third-party-cookies-blocked"></a>Cookie di terze parti bloccati

L'esperienza utente di ADF usa i cookie del browser per rendere permanente la sessione utente e consentire esperienze di monitoraggio e sviluppo interattive. È possibile che il browser blocchi i cookie di terze parti perché si sta usando una sessione in incognito o è abilitato un blocco ad. Il blocco dei cookie di terze parti può causare problemi durante il caricamento del portale, ad esempio se viene reindirizzato a una pagina vuota, https://adf.azure.com/accesstoken.html o viene visualizzato un messaggio di avviso che informa che i cookie di terze parti sono bloccati. Per risolvere questo problema, abilitare le opzioni dei cookie di terze parti nel browser attenendosi alla procedura seguente:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Consenti tutti i cookie

1. Visitare **Chrome://Settings/cookies** nel browser.
1. Selezionare l'opzione **Consenti tutti i cookie** ![ Chrome-Allow-all-cookies](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Aggiornare l'esperienza utente di ADF e riprovare.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Consenti solo ad ADF UX di usare i cookie
Se non si desidera consentire tutti i cookie, facoltativamente è possibile consentire solo ad ADF UX:
1. Visitare **Chrome://Settings/cookies**.
1. Fare clic su **Aggiungi** in **siti in cui è sempre possibile utilizzare l'opzione cookie** ![ Aggiungi UX di ADF ai siti consentiti](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Aggiungere il sito di **ADF.Azure.com** , selezionare l'opzione **tutti i cookie** e salvare. ![Consenti tutti i cookie dal sito UX di ADF](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Aggiornare l'esperienza utente di ADF e riprovare.


### <a name="microsoft-edge"></a>Microsoft Edge

1. Visitare **Edge://settings/content/cookies** nel browser.
1. Assicurarsi che l'opzione **Consenti ai siti di salvare e leggere i dati dei cookie** sia abilitata e che l'opzione **Blocca cookie di terze parti** sia disabilitata ![ Consenti tutti i cookie in Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Aggiornare l'esperienza utente di ADF e riprovare.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Consenti solo ad ADF UX di usare i cookie

Se non si desidera consentire tutti i cookie, facoltativamente è possibile consentire solo ad ADF UX:

1. Visitare **Edge://settings/content/cookies**.
1. Nella sezione **Consenti** fare clic su **Aggiungi** e Aggiungi sito di **ADF.Azure.com** . ![Aggiungere l'esperienza utente di ADF ai siti consentiti](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Aggiornare l'esperienza utente di ADF e riprovare.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi, usare le risorse seguenti:

* [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
* [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)
* [Video di Azure](https://azure.microsoft.com/resources/videos/index/)
* [Pagina delle domande di Domande e risposte Microsoft](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
