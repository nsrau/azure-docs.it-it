---
title: Quote in App Web statiche di Azure (anteprima)
description: Informazioni sulle quote associate ad App Web statiche di Azure (anteprima)
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 6fa70eba5875c32d41fa1cfc8daf9b1cdf8f19df
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599110"
---
# <a name="quotas-in-azure-static-web-apps-preview"></a>Quote in App Web statiche di Azure (anteprima)

Per App Web statiche di Azure (anteprima) sono disponibili le quote seguenti.

> [!IMPORTANT]
> App Web statiche di Azure è disponibile in anteprima pubblica e non è utilizzabile in ambienti di produzione.

| Funzionalità                     | Piano Gratuito        |
|-----------------------------|------------------|
| Larghezza di banda inclusa          | 100 GB al mese |
| Larghezza di banda in eccedenza           | Non disponibile      |
| App per sottoscrizione di Azure | 10               |
| Dimensioni dell'app                    | 100 MB           |
| Ambienti di pre-produzione | 1                |
| Domini personalizzati              | 1                |
| Autorizzazione<br><br>Con ruoli personalizzati e regole di gestione | Massimo 25 utenti finali invitati e ruoli assegnati |
| Funzioni di Azure             | Disponibile        |
| Contratto di servizio                         | Nessuno             |

## <a name="github-storage"></a>Archiviazione GitHub

GitHub Actions e GitHub Packages usano la funzione di archiviazione di GitHub, che prevede un proprio set di quote. Quando si crea un sito di App Web statiche di Azure, GitHub archivia gli artefatti per il sito anche dopo la distribuzione.

Per maggiori dettagli, vedere le risorse seguenti:

- [Gestione dello spazio di archiviazione di Actions](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [Informazioni sulla fatturazione per GitHub Actions](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [Gestione del limite di spesa per le GitHub Actions](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica](overview.md)
