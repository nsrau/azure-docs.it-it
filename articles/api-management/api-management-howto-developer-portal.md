---
title: Accesso e personalizzazione del nuovo portale per sviluppatori-gestione API di Azure | Microsoft Docs
description: Informazioni su come usare il nuovo portale per sviluppatori in gestione API.
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
ms.openlocfilehash: 27d5dcc99db040036ba296911aa33d8a312bb23f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851520"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Accesso e personalizzazione del nuovo portale per sviluppatori in gestione API di Azure

Questo articolo illustra come accedere al nuovo portale per sviluppatori di gestione API di Azure. Viene illustrata l'esperienza dell'editor visiva, ovvero l'aggiunta e la modifica del contenuto, oltre a personalizzare l'aspetto del sito Web.

![Nuovo portale per sviluppatori di gestione API](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a> Prerequisiti

- Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
- Importare e pubblicare un'istanza di Gestione API di Azure. Per altre informazioni, vedere [Importare e pubblicare un'API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> Il nuovo portale per sviluppatori è attualmente in versione di anteprima.

## <a name="managed-vs-self-hosted"></a>Versioni gestite e self-hosted

È possibile creare il portale per sviluppatori in due modi:

- **Versione gestita** : modifica e personalizzazione del portale, integrato nell'istanza di gestione API, accessibile tramite l'URL `<your-api-management-instance-name>.developer.azure-api.net`.
- **Versione self-hosted** : tramite la distribuzione e l'hosting automatico del portale all'esterno di un'istanza di gestione API. Questo approccio consente di modificare la codebase del portale ed estendere la funzionalità di base fornita. Per informazioni dettagliate e istruzioni, vedere il [repository GitHub con il codice sorgente del portale][1].

## <a name="managed-access"></a>Accedere alla versione gestita del portale

Attenersi alla procedura seguente per accedere alla versione gestita del portale.

1. Passare all'istanza del servizio gestione API nella portale di Azure.
1. Fare clic sul pulsante **nuovo portale per sviluppatori (anteprima)** nella barra di spostamento superiore. Viene aperta una nuova scheda del browser con una versione amministrativa del portale. Se si accede al portale per la prima volta, verrà eseguito automaticamente il provisioning del contenuto predefinito.

## <a name="managed-tutorial"></a>Modificare e personalizzare la versione gestita del portale

Nel video seguente viene illustrato come modificare il contenuto del portale, personalizzare l'aspetto del sito Web e pubblicare le modifiche.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="faq"></a>Domande frequenti

In questa sezione vengono riportate le risposte alle domande più comuni sul nuovo portale per sviluppatori, che sono di natura generale. Per domande specifiche per la versione self-hosted, vedere [la sezione wiki del repository GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-content-from-the-old-developer-portal-to-the-new-one"></a>Come è possibile eseguire la migrazione del contenuto dal portale per sviluppatori precedente a quello nuovo?

Non puoi. I portali sono incompatibili.

### <a name="when-will-the-portal-become-generally-available"></a>Quando il portale viene reso disponibile a livello generale?

Il portale è attualmente in versione di anteprima e diventerà disponibile a livello generale entro la fine dell'anno di calendario (2019). La versione di anteprima non deve essere usata per scopi di produzione.

### <a name="will-the-old-portal-be-deprecated"></a>Il vecchio portale sarà deprecato?

Sì, verrà deprecato dopo che il nuovo diventa disponibile a livello generale. In caso di problemi, è possibile generarli [in un problema dedicato di GitHub](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>Il nuovo portale dispone di tutte le funzionalità del vecchio portale?

L'obiettivo della disponibilità generale è fornire una parità di funzionalità basata sullo scenario con il vecchio portale. Fino ad allora, è possibile che nella versione di anteprima non siano state implementate le funzionalità selezionate.

Le eccezioni sono le *applicazioni* e i *problemi* del vecchio portale, che non saranno disponibili nel nuovo portale. Se si usano *problemi* nel portale precedente e ne sono necessari in una nuova, pubblicare un commento in [un problema di GitHub dedicato](https://github.com/Azure/api-management-developer-portal/issues/122).

### <a name="ive-found-bugs-andor-id-like-to-request-a-feature"></a>Ho trovato bug e/o desidero richiedere una funzionalità.

L'installazione è riuscita. È possibile fornire commenti e suggerimenti, inviare una richiesta di funzionalità o archiviare un report sui bug tramite [la sezione problemi del repository GitHub](https://github.com/Azure/api-management-developer-portal/issues). Anche in questo caso, si apprezzeranno i commenti e i suggerimenti sui problemi contrassegnati con `community` l'etichetta.

### <a name="i-want-to-move-the-content-of-the-new-portal-between-environments-how-can-i-do-that-and-do-i-need-to-go-with-the-self-hosted-version"></a>Si desidera spostare il contenuto del nuovo portale tra gli ambienti. Come è possibile eseguire questa operazione ed è necessario procedere con la versione self-hosted?

Questa operazione può essere eseguita in entrambe le versioni del portale, gestite e indipendenti. Il nuovo portale per sviluppatori supporta l'estrazione del contenuto tramite l'API di gestione del servizio gestione API. Le API sono documentate [nella sezione wiki del repository GitHub](https://github.com/Azure/api-management-developer-portal/wiki/). È stato scritto anche [uno script](https://github.com/Azure/api-management-developer-portal/blob/master/scripts/migrate.bat)che può essere utile per iniziare.

Stiamo ancora lavorando per allineare questo processo al Resource Kit DevOps di gestione API.

### <a name="how-can-i-select-a-layout-when-creating-a-new-page"></a>Come è possibile selezionare un *layout* quando si crea una nuova *pagina*?

Un *layout* viene applicato a una pagina abbinando il relativo modello URL all'URL della *pagina* . Ad esempio, *il layout* con un modello di `/wiki/*` URL di verrà applicato a ogni *pagina* con `/wiki/` il segmento `/wiki/getting-started`: `/wiki/styles`, e così via.

### <a name="why-doesnt-the-interactive-developer-console-work"></a>Perché la console per sviluppatori interattiva non funziona?

È probabile che sia correlato a CORS. La console interattiva esegue una richiesta API sul lato client dal browser. È possibile risolvere il problema CORS aggiungendo [un criterio CORS](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS) sulle API. È possibile specificare tutti i parametri manualmente, ad esempio Origin come https://contoso.com) o usare un valore jolly. `*`

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul nuovo portale per sviluppatori:

- [Repository GitHub con il codice sorgente][1]
- [Istruzioni per l'hosting automatico del portale][2]
- [Roadmap pubblica del progetto][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects