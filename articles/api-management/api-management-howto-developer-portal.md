---
title: Accedere e personalizzare il nuovo portale per sviluppatori - gestione API di Azure | Microsoft Docs
description: Informazioni su come usare il nuovo portale per sviluppatori in Gestione API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: deb3fdf3069aaad4982d71806c209fe516e3c3d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743576"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Accedere e personalizzare il nuovo portale per sviluppatori in Gestione API di Azure

Questo articolo illustra come accedere al nuovo portale per sviluppatori di gestione API di Azure. Ne descrive l'esperienza di editor visivo - aggiunta e modifica contenuto, nonché di personalizzazione dell'aspetto del sito Web.

![Nuovo portale per sviluppatori di gestione API](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a>Prerequisiti

- Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
- Importare e pubblicare un'istanza di Gestione API di Azure. Per altre informazioni, vedere [Importare e pubblicare un'API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> Il nuovo portale per sviluppatori è attualmente in anteprima.

## <a name="managed-and-self-hosted-versions"></a>Versioni di self-hosted e non gestite

È possibile compilare il portale per sviluppatori in due modi:

- **Versione gestita** - modifica e personalizzazione del portale integrate in istanza di gestione API ed è accessibile tramite l'URL `<your-api-management-instance-name>.developer.azure-api.net`.
- **Versione self-hosted** - distribuzione e self-hosting del portale di fuori di un'istanza di gestione API. Questo approccio consente di modificare il portale codebase ed estendere le funzionalità di base specificato. Per informazioni dettagliate e istruzioni, vedere la [repository GitHub con il codice sorgente del portale][1].

## <a name="access-the-managed-version-of-the-portal"></a>Accedere alla versione gestita del portale

Attenersi alla procedura seguente per accedere alla versione gestita del portale.

1. Passare all'istanza del servizio Gestione API nel portale di Azure.
1. Fare clic sui **nuovo portale per sviluppatori (anteprima)** pulsante nella barra di spostamento superiore. Verrà aperta una nuova scheda del browser con una versione del portale di amministrazione. Se si accede al portale per la prima volta, il contenuto predefinito viene possibile automaticamente il provisioning.

## <a name="edit-and-customize-the-managed-version-of-the-portal"></a>Modificare e personalizzare la versione gestita del portale

Nel video seguente viene illustrato come modificare il contenuto del portale, personalizzare l'aspetto del sito Web e pubblicare le modifiche.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul nuovo portale per sviluppatori:

- [Repository GitHub con il codice sorgente][1]
- [Istruzioni in modalità self-hosting il portale][2]
- [Roadmap pubblica del progetto][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects