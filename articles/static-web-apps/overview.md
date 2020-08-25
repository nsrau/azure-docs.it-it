---
title: Che cos'è App Web statiche di Azure?
description: Caratteristiche e funzionalità principali di App Web statiche di Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 884aa18b1a29b1c4f1bbf1159a5d48870529c72b
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565707"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Che cos'è App Web statiche di Azure (anteprima)?

App Web statiche di Azure è un servizio che consente di compilare e distribuire automaticamente in Azure app Web dello stack completo da un repository GitHub.

:::image type="content" source="media/overview/static-apps-overview.png" alt-text="Panoramica di App Web statiche":::

Il flusso di lavoro di App Web statiche di Azure è personalizzato in base al flusso di lavoro giornaliero di uno sviluppatore. Le app vengono compilate e distribuite sulla base di interazioni GitHub.

Quando si crea una risorsa di App Web statiche di Azure, Azure configura un flusso di lavoro di GitHub Actions nel repository del codice sorgente dell'app che monitora un ramo scelto dall'utente. Ogni volta che si esegue il push di commit o si accettano richieste pull nel ramo monitorato, l'azione di GitHub Actions compila e distribuisce automaticamente l'app e la relativa API in Azure.

Per la compilazione delle app Web statiche si usano in genere librerie e framework come Angular, React, Svelte o Vue. Queste app includono asset HTML, CSS, JavaScript e immagine che costituiscono l'applicazione. Con un server Web tradizionale, questi asset vengono gestiti da un unico server unitamente a tutti gli endpoint API necessari.

Con App Web statiche gli asset statici vengono separati da un server Web tradizionale e sono gestiti da punti distribuiti geograficamente in tutto il mondo. Grazie a questa distribuzione la gestione dei file risulta molto più rapida quanto più sono fisicamente vicini agli utenti finali. Per ospitare gli endpoint dell'API si usa inoltre un'[architettura serverless](../azure-functions/functions-overview.md), che consente di evitare l'uso di un server back-end completo.

## <a name="key-features"></a>Funzionalità principali

- **Hosting Web** per contenuti statici, come HTML, CSS, JavaScript e immagini.
- Supporto di **API integrato** fornito da Funzioni di Azure.
- **Integrazione di prima classe di GitHub** in cui le modifiche del repository attivano compilazioni e distribuzioni.
- Contenuto statico **distribuito a livello globale**, per avvicinare il contenuto agli utenti.
- **Certificati SSL gratuiti**, che vengono rinnovati automaticamente.
- **Domini personalizzati** per aggiungere personalizzazioni all'app.
- **Modello di sicurezza automatico** con proxy inverso quando si chiamano le API, che non richiede alcuna configurazione CORS.
- **Integrazioni dei provider di autenticazione** con Azure Active Directory, Facebook, Google, GitHub e Twitter.
- Definizione e assegnazione di **ruoli di autorizzazione personalizzabili**.
- **Regole di routing back-end** per il controllo completo sul contenuto e sulle route gestite.
- **Generazione di versioni di staging** basate su richieste pull che consentono di creare versioni di anteprima del sito prima della pubblicazione.

## <a name="what-you-can-do-with-static-web-apps"></a>Operazioni possibili con App Web statiche

- **Creare applicazioni JavaScript moderne** con framework e librerie quali [Angular](getting-started.md?tabs=angular), [React](getting-started.md?tabs=react), [Svelte](https://docs.microsoft.com/learn/modules/publish-app-service-static-web-app-api/), [Vue](getting-started.md?tabs=react) e [Funzioni di Azure](apis.md) come back-end.
- **Pubblicare siti statici** con framework come [Gatsby](publish-gatsby.md), [Hugo](publish-hugo.md), [VuePress](publish-vuepress.md).
- **Distribuire applicazioni Web** con framework come [Next.js](deploy-nextjs.md) e [Nuxt.js](deploy-nuxtjs.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare la prima app statica](getting-started.md)
