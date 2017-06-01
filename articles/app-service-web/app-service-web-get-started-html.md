---
title: Creare un&quot;app Web HTML statica in Azure in cinque minuti | Microsoft Docs
description: Informazioni su come eseguire facilmente app Web nel servizio app mediante la distribuzione di un&quot;app di esempio.
services: app-service\web
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/08/2017
ms.author: riande
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 895906e1ab4bc50093ed3b18f043c3dd515ca054
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="create-a-static-html-web-app-in-azure-in-five-minutes"></a>Creare un'app Web HTML statica in Azure in cinque minuti

Questa guida introduttiva illustra come distribuire un semplice sito HTML e CSS in Azure. L'app verrà eseguita usando un [piano di servizio app di Azure](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview), in cui verrà creata un'app Web usando l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli). Verrà usato Git per distribuire l'app in Azure. Una volta installati i prerequisiti, sono necessari circa cinque minuti per completare l'esercitazione.

![Hello World nel browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="prerequisites"></a>Prerequisiti

Prima di creare questo esempio, scaricare e installare i componenti seguenti:

- [Git](https://git-scm.com/)
- [Interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Scaricare l'esempio

In una finestra del terminale clonare l'archivio dell'app di esempio nel computer locale:

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="view-the-html"></a>Visualizzare il codice HTML

Passare alla directory contenente il codice HTML di esempio. Aprire il file *index.html* nel browser.

![Hello World nel browser](media/app-service-web-get-started-html/hello-world-in-browser.png)

[!INCLUDE [login-to-azure](../../includes/login-to-azure.md)] 
[!INCLUDE [configure-deployment-user](../../includes/configure-deployment-user.md)] 

[!INCLUDE [app-service-web-quickstart1](../../includes/app-service-web-quickstart1.md)] 

Creare un'[app Web](app-service-web-overview.md) nel piano di servizio app `quickStartPlan`. L'app Web fornisce uno spazio host per il codice e un URL per visualizzare l'app distribuita.

[!INCLUDE [app-service-web-quickstart2](../../includes/app-service-web-quickstart2.md)] 

La pagina è in esecuzione come un'app Web del servizio app di Azure:

![Hello World nel browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="update-and-redeploy-the-app"></a>Aggiornare e ridistribuire l'app

Aprire il file *index.html* . Apportare una modifica al markup. Ad esempio, modificare `Hello world!` in `Hello Azure!`.

Eseguire il commit delle modifiche in Git e quindi effettuare il push delle modifiche al codice in Azure.

```bash
git commit -am "updated HTML"
git push azure master
```

Al termine della distribuzione, aggiornare il browser per visualizzare le modifiche.

[!INCLUDE [manage-azure-web-app](../../includes/manage-azure-web-app.md)]


[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

- Esplorare gli [script dell'interfaccia della riga di comando per App Web](app-service-cli-samples.md) di esempio.
- Leggere le informazioni su come [eseguire il mapping di un nome di dominio personalizzato](app-service-web-tutorial-custom-domain.md), ad esempio contoso.com, a un'[app del servizio app](app-service-web-tutorial-custom-domain.md).
