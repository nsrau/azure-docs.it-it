---
title: Che cos'è App Web statiche di Azure?
description: Caratteristiche e funzionalità principali di App Web statiche di Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: d416044599535e8acd363d09099e8667bba59a0a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599066"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Che cos'è App Web statiche di Azure (anteprima)?

App Web statiche di Azure è un servizio che consente di compilare e distribuire automaticamente in Azure app Web dello stack completo da un repository GitHub.

:::image type="content" source="media/overview/static-apps-overview.png" alt-text="Panoramica di App Web statiche":::

Il flusso di lavoro di App Web statiche di Azure è personalizzato in base al flusso di lavoro giornaliero di uno sviluppatore. Le app vengono compilate e distribuite sulla base di interazioni GitHub.

Quando si crea una risorsa di App Web statiche di Azure, Azure configura un flusso di lavoro di GitHub Actions nel repository del codice sorgente dell'app che monitora un ramo scelto dall'utente. Ogni volta che si esegue il push di commit o si accettano richieste pull nel ramo monitorato, l'azione di GitHub Actions compila e distribuisce automaticamente l'app e la relativa API in Azure.

Per la compilazione delle app Web statiche si usano in genere librerie e framework come Angular, React, Svelte o Vue. Queste app includono asset HTML, CSS, JavaScript e immagine che costituiscono l'applicazione. Con un server Web tradizionale, questi asset vengono gestiti da un unico server unitamente a tutti gli endpoint API necessari.

Con App Web statiche gli asset statici vengono separati da un server Web tradizionale e sono gestiti da punti distribuiti geograficamente in tutto il mondo. Grazie a questa distribuzione la gestione dei file risulta molto più rapida quanto più sono fisicamente vicini agli utenti finali. Per ospitare gli endpoint dell'API si usa inoltre un'[architettura serverless](../azure-functions/functions-overview.md), che consente di evitare l'uso di un server back-end completo.

## <a name="key-features"></a>Funzionalità principali

- **Hosting Web gratuito** per contenuti statici, come HTML, CSS, JavaScript e immagini.
- Supporto di **API integrato** fornito da Funzioni di Azure.
- **Integrazione diretta di GitHub** in cui le modifiche del repository attivano compilazioni e distribuzioni.
- Contenuto statico **distribuito a livello globale**, per avvicinare il contenuto agli utenti.
- **Certificati SSL gratuiti**, che vengono rinnovati automaticamente.
- **Domini personalizzati**\* per aggiungere personalizzazioni all'app.
- **Modello di sicurezza automatico** con proxy inverso quando si chiamano le API, che non richiede alcuna configurazione CORS.
- **Integrazioni dei provider di autenticazione** con Azure Active Directory, Facebook, Google, GitHub e Twitter.
- Definizione e assegnazione di **ruoli di autorizzazione personalizzabili**.
- **Regole di routing back-end** per il controllo completo sul contenuto e sulle route gestite.
- **Generazione di versioni di staging** basate su richieste pull che consentono di creare versioni di anteprima del sito prima della pubblicazione.

## <a name="what-you-can-do-with-static-web-apps"></a>Operazioni possibili con App Web statiche

- **Creare applicazioni JavaScript moderne** con framework e librerie quali [Angular](https://angular.io/), [React](https://reactjs.org/), [Svelte](https://svelte.dev/), [Vue](https://vuejs.org/) e [Funzioni di Azure](https://azure.microsoft.com/services/functions/) come back-end.
- **Pubblicare siti statici** con framework come [Gatsby](publish-gatsby.md), [Hugo](publish-hugo.md), [VuePress](publish-vuepress.md).
- **Distribuire applicazioni Web** con framework come [Next.js](deploy-nextjs.md) e [Nuxt.js](deploy-nuxtjs.md).

\* Le registrazioni di domini Apex non sono supportate nella versione di anteprima.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare la prima app statica](getting-started.md)
