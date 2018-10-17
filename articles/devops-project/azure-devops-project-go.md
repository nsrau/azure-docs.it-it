---
title: Creare una pipeline di CI/CD per Golang con il progetto DevOps di Azure | Guida introduttiva
description: Il progetto DevOps consente di iniziare a usare Azure in modo semplice. Con pochi rapidi passaggi, è possibile avviare un'app Golang in un servizio di Azure.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 48eb91242e24d29dd8335c9565dfcaca4d920bd4
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408964"
---
# <a name="create-a-cicd-pipeline-for-go-with-the-azure-devops-project"></a>Creare una pipeline di CI/CD per Go con il progetto DevOps di Azure

Configurare integrazione continua (CI, Continuous Integration) e recapito continuo (CD, Continuous Delivery) per un'applicazione **Go** con il **progetto DevOps di Azure**.  Il progetto DevOps di Azure semplifica la configurazione iniziale di una pipeline di compilazione e di versione di Azure DevOps Services.

Se non si ha ancora una sottoscrizione di Azure, è possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Il Progetto DevOps di Azure crea una pipeline CI/CD in Azure.  È possibile creare una **nuova organizzazione di Azure DevOps Services** o usare un'**organizzazione esistente**.  Il progetto DevOps di Azure crea anche **risorse di Azure** nella **sottoscrizione di Azure** di propria scelta.

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com).

1. Scegliere l'icona **+ Crea una risorsa** sulla barra di spostamento di sinistra e quindi cercare **Progetto DevOps**.  Scegliere **Create**.

    ![Avvio del recapito continuo](_img/azure-devops-project-go/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selezionare un'applicazione di esempio e un servizio di Azure

1. Selezionare l'applicazione di esempio **Go** e quindi selezionare **Avanti**.

1. Il framework predefinito è **Simple Go app**.  Scegliere **Avanti**. 

1. Il framework applicazione, scelto nei passaggi precedenti, determina il tipo di destinazioni della distribuzione del servizio di Azure disponibili.  Lasciare il servizio di Azure predefinito e quindi scegliere **Avanti**.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Configurare Azure DevOps Services e una sottoscrizione di Azure 

1. Creare gratuitamente una **nuova** organizzazione Azure DevOps Services oppure selezionare un'organizzazione **esistente**.  Scegliere un **nome** per il progetto Azure DevOps.  Selezionare la **sottoscrizione di Azure** e la **posizione** e scegliere un **nome** per l'applicazione.  Al termine, scegliere **Fine**.

1. In pochi minuti, il **dashboard del progetto DevOps** viene caricato nel portale di Azure.  Viene configurata un'applicazione di esempio in un repository nell'organizzazione Azure DevOps Services, viene eseguita una compilazione e l'applicazione viene distribuita in Azure.  Questo dashboard fornisce visibilità su **repository di codice**, **pipeline di CI/CD di Azure** e **applicazione in Azure**.  A destra del dashboard selezionare **Sfoglia** per visualizzare l'applicazione in esecuzione.

    ![Visualizzazione dashboard](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Eseguire il commit delle modifiche al codice e la pipeline di CI/CD

Il progetto DevOps di Azure ha creato un repository nell'organizzazione Azure DevOps Services o nell'account GitHub.  Attenersi alla procedura seguente per visualizzare il repository e apportare modifiche al codice nell'applicazione.

1. Sul lato sinistro del dashboard del progetto DevOps selezionare il collegamento per il ramo **master**.  Questo collegamento apre una visualizzazione del repository Git appena creato.

1. Per visualizzare l'URL clone del repository, selezionare **Clona** in alto a destra nel browser. È possibile clonare il repository Git nell'IDE preferito.  Nei passaggi successivi, è possibile usare il Web browser per apportare modifiche al codice ed eseguirne il commit direttamente nel ramo master.

1. Sul lato sinistro del browser passare al file **views/index.html**.

1. Selezionare **Modifica** e apportare una modifica al testo.  Modificare, ad esempio, il testo all'interno di uno dei tag div.

1. Scegliere **Esegui commit** e quindi salvare le modifiche.

1. Nel browser passare al **dashboard del progetto DevOps di Azure**.  Dovrebbe venire visualizzata una compilazione in corso.  Le modifiche apportate vengono compilate e distribuite automaticamente tramite una pipeline di CI/CD di Azure.

## <a name="examine-the-azure-cicd-pipeline"></a>Esaminare la pipeline di CI/CD di Azure

Il Progetto DevOps di Azure ha configurato automaticamente una pipeline CI/CD di Azure completa nell'organizzazione di DevOps Services.  Esplorare e personalizzare la pipeline in base alle esigenze.  Attenersi alla procedura seguente per acquisire familiarità con le pipeline di compilazione e di versione di Azure DevOps Services.

1. Selezionare **Pipeline di compilazione** nella parte **superiore** del dashboard del progetto DevOps di Azure.  Questo collegamento apre una scheda del browser e la pipeline di compilazione di Azure DevOps Services per il nuovo progetto.

1. Selezionare i **puntini di sospensione**.  Questa azione apre un menu da cui è possibile avviare diverse attività, ad esempio accodare una nuova compilazione, sospendere una compilazione e modificare la pipeline di compilazione.

1. Selezionare **Modifica**.

1. In questa visualizzazione **esaminare le diverse attività** per la pipeline di compilazione.  La compilazione esegue diverse attività, ad esempio il recupero delle origini dal repository Git, il ripristino delle dipendenze e la pubblicazione degli output usati per le distribuzioni.

1. Nella parte superiore della pipeline di compilazione, selezionare il **nome della pipeline di compilazione**.

1. Modificare il **nome** della pipeline di compilazione scegliendo un testo più descrittivo.  Selezionare **Salva e accoda** e quindi **Salva**.

1. Sotto il nome della pipeline di compilazione selezionare **Cronologia**.  Verrà visualizzato un log di controllo delle modifiche recenti per la compilazione.  Azure DevOps Services tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

1. Selezionare **Trigger**.  Progetto Azure DevOps ha creato automaticamente un trigger di CI e ogni commit nel repository avvia una nuova compilazione.  Facoltativamente, è possibile scegliere di includere o escludere rami dal processo di integrazione continua.

1. Selezionare **Conservazione**.  In base allo scenario specifico, è possibile indicare i criteri per conservare o rimuovere un determinato numero di compilazioni.

1. Selezionare **Compilazione e versione** e quindi scegliere **Versioni**.  Progetto di Azure DevOps ha creato una pipeline di versione di Azure DevOps Services per gestire le distribuzioni in Azure.

1. Sul lato sinistro del browser selezionare i **puntini di sospensione** accanto alla pipeline di versione e quindi scegliere **Modifica**.

1. La pipeline di versione contiene una **pipeline** che definisce il processo per la versione.  In **Artefatti** selezionare **Elimina**.  La pipeline di compilazione esaminata nei passaggi precedenti produce l'output usato per l'artefatto. 

1. A destra dell'icona **Elimina** selezionare **Trigger di distribuzione continua**.  Questa pipeline di versione ha un trigger di distribuzione continua abilitato, che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione.  Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale. 

1. Sul lato sinistro del browser selezionare **Attività**.  Le attività sono le operazioni eseguite dal processo di distribuzione.  In questo esempio è stata creata un'attività per la distribuzione in **Servizio app di Azure**.

1. Sul lato destro del browser selezionare **Visualizza versioni**.  Questa visualizzazione mostra una cronologia delle versioni.

1. Selezionare i **puntini di sospensione** accanto a una delle versioni e scegliere **Apri**.  In questa visualizzazione ci sono diversi menu da esplorare, ad esempio un riepilogo delle versioni, gli elementi di lavoro associati e i test.

1. Selezionare **Commit**.  Questa visualizzazione mostra i commit di codice associati alla distribuzione specifica. 

1. Selezionare **Log**.  I log contengono informazioni utili sul processo di distribuzione.  Possono essere visualizzati durante e dopo le distribuzioni.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando Servizio app di Azure e le risorse correlate create in questa guida introduttiva non sono più necessari, è possibile eliminarli usando la funzione **Elimina** nel dashboard del progetto DevOps di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla modifica delle pipeline di compilazione e di versione in base alle esigenze del team, vedere questa esercitazione:

> [!div class="nextstepaction"]
> [Personalizzare il processo di distribuzione continua](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
