---
title: "Eseguire attività in background con Processi Web in Servizio app di Azure"
description: "Informazioni su come usare Processi Web per eseguire attività in background in app Web del servizio app di Azure, app per le API e app per dispositivi mobili."
services: app-service
documentationcenter: 
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2017
ms.author: glenga
ms.openlocfilehash: 1594a27d50df9abdac27b342ad18363b974c8972
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2017
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Eseguire attività in background con Processi Web in Servizio app di Azure

## <a name="overview"></a>Panoramica
Processi Web è una funzionalità di [Servizio app di Azure](https://docs.microsoft.com/azure/app-service/) che consente di eseguire un programma o uno script nello stesso contesto di un'app Web, un'app per le API o un'app per dispositivi mobili. Non sono previsti costi aggiuntivi per l'uso di Processi Web.

L'articolo descrive come distribuire Processi Web usando il [portale di Azure](https://portal.azure.com) per caricare un eseguibile o uno script. Per informazioni su come sviluppare e distribuire processi Web usando Visual Studio, vedere [Deploy WebJobs using Visual Studio](websites-dotnet-deploy-webjobs.md) (Distribuire processi Web usando Visual Studio).

Azure WebJobs SDK può essere usato con Processi Web per semplificare diverse attività di programmazione. Per ulteriori informazioni, vedere [Definizione dell'SDK di Processi Web](https://github.com/Azure/azure-webjobs-sdk/wiki).

Funzioni di Azure consente di eseguire programmi e script in un altro modo. Per un confronto tra Processi Web e Funzioni, vedere [Scegliere tra Flow, App per la logica, Funzioni e Processi Web](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Tipi di processi Web

La tabella seguente descrive le differenze tra processi Web *continui* e *attivati*.


|Continuo  |Attivato  |
|---------|---------|
| Viene avviato immediatamente quando il processo Web viene creato. Per evitare che il processo termini, il programma o lo script in genere opera in un ciclo infinito. Se il processo termina, è possibile riavviarlo. | Viene avviato solo se attivato manualmente o in base a una pianificazione. |
| Viene eseguito in tutte le istanze in cui viene eseguita l'app Web. È facoltativamente possibile limitare il processo Web a una sola istanza. |Viene eseguito in una singola istanza selezionata da Azure per il bilanciamento del carico.|
| Supporta il debug remoto. | Non supporta il debug remoto.|

> [!NOTE]
> Un'app Web può raggiungere il timeout dopo 20 minuti di inattività. Solo le richieste al sito scm (distribuzione) o alle pagine dell'app Web nel portale reimpostano il timer. Le richieste al sito effettivo non comportano la reimpostazione del timer. Se l'app esegue processi Web continui o pianificati, abilitare **Sempre online** per assicurarsi che i processi Web vengano eseguiti in modo affidabile. Questa funzionalità è disponibile solo nei [piani tariffari](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Basic, Standard e Premium.

## <a name="acceptablefiles"></a>Tipi di file supportati per script e programmi

Sono supportati i tipi di file seguenti:

* .cmd, .bat, .exe (prompt dei comandi di Windows)
* .ps1 (PowerShell)
* .sh (Bash)
* .php (PHP)
* .py (Python)
* .js (Node.js)
* .jar (Java)

## <a name="CreateContinuous"></a> Creare un processo Web continuo

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Nel [portale di Azure](https://portal.azure.com) andare alla pagina **Servizio app** dell'app Web del servizio app, dell'app per le API o dell'app per dispositivi mobili.

2. Selezionare **Processi Web**.

   ![Selezionare Processi Web](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Nella pagina **Processi Web** selezionare **Aggiungi**.

    ![Pagina Processi Web](./media/web-sites-create-web-jobs/wjblade.png)

3. Usare le impostazioni di **Aggiungi processo Web** specificate nella tabella.

   ![Pagina Aggiungi processo Web](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Impostazione      | Valore di esempio   | Descrizione  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myContinuousWebJob | Nome univoco nell'app del servizio app. Deve iniziare con una lettera o un numero e non può contenere caratteri speciali diversi da "-" e "_". |
   | **Caricamento file** | ConsoleApp.zip | File *ZIP* che contiene il file eseguibile o il file di script e gli eventuali file di supporto necessari per eseguire il programma o lo script. I tipi di file di script o eseguibili supportati sono elencati nella sezione [Tipi di file supportati](#acceptablefiles). |
   | **Tipo** | Continuo | I [tipi di processi Web](#webjob-types) sono descritti più indietro in questo articolo. |
   | **Ridimensionare** | Istanze multiple | Disponibile solo per i processi Web continui. Determina se il programma o lo script viene eseguito in tutte le istanze o in una sola istanza. L'opzione per l'esecuzione in più istanze non si applica ai [piani tariffari](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Gratuito o Condiviso. | 

4. Fare clic su **OK**.

   Il nuovo processo Web viene visualizzato nella pagina **Processi Web**.

   ![Elenco dei processi Web](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Per arrestare o riavviare un processo Web continuo, fare clic con il pulsante destro del mouse sul processo Web nell'elenco e scegliere **Arresta** o **Avvia**.

    ![Arrestare un processo Web continuo](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a> Creare un processo Web attivato manualmente

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Nel [portale di Azure](https://portal.azure.com) andare alla pagina **Servizio app** dell'app Web del servizio app, dell'app per le API o dell'app per dispositivi mobili.

2. Selezionare **Processi Web**.

   ![Selezionare Processi Web](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Nella pagina **Processi Web** selezionare **Aggiungi**.

    ![Pagina Processi Web](./media/web-sites-create-web-jobs/wjblade.png)

3. Usare le impostazioni di **Aggiungi processo Web** specificate nella tabella.

   ![Pagina Aggiungi processo Web](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Impostazione      | Valore di esempio   | Descrizione  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myTriggeredWebJob | Nome univoco nell'app del servizio app. Deve iniziare con una lettera o un numero e non può contenere caratteri speciali diversi da "-" e "_".|
   | **Caricamento file** | ConsoleApp.zip | File *ZIP* che contiene il file eseguibile o il file di script e gli eventuali file di supporto necessari per eseguire il programma o lo script. I tipi di file di script o eseguibili supportati sono elencati nella sezione [Tipi di file supportati](#acceptablefiles). |
   | **Tipo** | Attivato | I [tipi di processi Web](#webjob-types) sono descritti più indietro in questo articolo. |
   | **Trigger** | Manuale | |

4. Fare clic su **OK**.

   Il nuovo processo Web viene visualizzato nella pagina **Processi Web**.

   ![Elenco dei processi Web](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Per eseguire il processo Web, fare clic con il pulsante destro del mouse sul nome visualizzato nell'elenco e scegliere **Esegui**.
   
    ![Eseguire un processo Web](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a> Creare un processo Web pianificato

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Nel [portale di Azure](https://portal.azure.com) andare alla pagina **Servizio app** dell'app Web del servizio app, dell'app per le API o dell'app per dispositivi mobili.

2. Selezionare **Processi Web**.

   ![Selezionare Processi Web](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Nella pagina **Processi Web** selezionare **Aggiungi**.

   ![Pagina Processi Web](./media/web-sites-create-web-jobs/wjblade.png)

3. Usare le impostazioni di **Aggiungi processo Web** specificate nella tabella.

   ![Pagina Aggiungi processo Web](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Impostazione      | Valore di esempio   | Descrizione  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myScheduledWebJob | Nome univoco nell'app del servizio app. Deve iniziare con una lettera o un numero e non può contenere caratteri speciali diversi da "-" e "_". |
   | **Caricamento file** | ConsoleApp.zip | File *ZIP* che contiene il file eseguibile o il file di script e gli eventuali file di supporto necessari per eseguire il programma o lo script. I tipi di file di script o eseguibili supportati sono elencati nella sezione [Tipi di file supportati](#acceptablefiles). |
   | **Tipo** | Attivato | I [tipi di processi Web](#webjob-types) sono descritti più indietro in questo articolo. |
   | **Trigger** | Pianificato | Per il corretto funzionamento della pianificazione, abilitare la funzionalità Sempre online. Sempre online è disponibile solo nei piani tariffari Basic, Standard e Premium.|
   | **Espressione CRON** | 0 0/20 * * * * | Le [espressioni CRON](#cron-expressions) vengono descritte nella sezione seguente. |

4. Fare clic su **OK**.

   Il nuovo processo Web viene visualizzato nella pagina **Processi Web**.

   ![Elenco dei processi Web](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="cron-expressions"></a>Espressioni CRON

Un'[espressione CRON](https://en.wikipedia.org/wiki/Cron) è composta da sei campi: `{second} {minute} {hour} {day} {month} {day of the week}`.  Di seguito sono riportati alcuni esempi:

* Ogni 15 minuti: `0 */15 * * * *`
* Ogni ora (ad esempio ogni volta che il numero di minuti è 0): `0 0 * * * *` 
* Ogni ora dalle 9 di mattina alle 5 del pomeriggio: `0 0 9-17 * * *` 
* Alle 9:30 di mattina ogni giorno: `0 30 9 * * *`
* Alle 9:30 ogni giorno della settimana: `0 30 9 * * 1-5`

È possibile immettere l'espressione CRON nel portale o includere un file `settings.job` nella radice del file *ZIP* del processo Web, come nell'esempio seguente:

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

> [!NOTE]
> Quando si distribuisce un processo Web da Visual Studio, contrassegnare le proprietà del file `settings.job` come **Copia se più recente**.

## <a name="ViewJobHistory"></a> Visualizzare la cronologia processo

1. Selezionare il processo Web di cui si vuole visualizzare la cronologia e quindi selezionare il pulsante **Log**.
   
   ![Pulsante Log](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. Nella pagina **WebJob Details** (Dettagli processo Web) selezionare un'ora per visualizzare i dettagli di un'esecuzione.
   
   ![Dettagli del processo Web](./media/web-sites-create-web-jobs/webjobdetails.png)

3. Nella pagina **WebJob Run Details** (Dettagli esecuzione processo Web) selezionare **Toggle Output** (Attiva/Disattiva output) per visualizzare il testo dei contenuti del log.
   
    ![Dettagli dell'esecuzione del processo Web](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Per visualizzare il testo dell'output in una finestra del browser separata, selezionare **download**. Per scaricare il testo stesso, fare clic con il pulsante destro del mouse su **download** e usare le opzioni del browser in uso per salvare il contenuto del file.
   
5. Selezionare il collegamento del percorso **Processi Web** nella parte superiore della pagina per passare a un elenco di processi Web.

    ![Percorso Processi Web](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Elenco dei processi nel dashboard della cronologia](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a> Passaggi successivi

Azure WebJobs SDK può essere usato con Processi Web per semplificare diverse attività di programmazione. Per ulteriori informazioni, vedere [Definizione dell'SDK di Processi Web](https://github.com/Azure/azure-webjobs-sdk/wiki).
