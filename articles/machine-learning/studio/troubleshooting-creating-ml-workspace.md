---
title: Risoluzione dei problemi di un'area di lavoroTroubleshooting
titleSuffix: ML Studio (classic) - Azure
description: Questa guida fornisce soluzioni per alcune difficoltà frequenti durante la configurazione di aree di lavoro di Azure Machine Learning Studio (classiche).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 58ccd63e16382aca8e16eb67efba951a055eb254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217831"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>Guida alla risoluzione dei problemi: Creare e connettersi a un'area di lavoro di Azure Machine Learning Studio (classica)Troubleshooting guide: Create and connect to an Azure Machine Learning Studio (classic) workspace

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Questa guida fornisce soluzioni per alcune difficoltà frequenti durante la configurazione di aree di lavoro di Azure Machine Learning Studio (classiche).

## <a name="workspace-owner"></a>Proprietario dell'area di lavoro
Per aprire un'area di lavoro in Machine Learning Studio (classica), è necessario accedere all'account Microsoft usato per creare l'area di lavoro oppure ricevere un invito dal proprietario per partecipare all'area di lavoro. Dal portale di Azure è possibile gestire l'area di lavoro e anche configurare l'accesso.

Per altre informazioni sulla gestione di un'area di lavoro, vedere Gestire un'area di lavoro di [Azure Machine Learning Studio (classica).]

[Gestire un'area di lavoro di Azure Machine Learning Studio (classica)Manage an Azure Machine Learning Studio (classic) workspace]: manage-workspace.md

## <a name="allowed-regions"></a>Aree geografiche disponibili
Machine Learning è attualmente disponibile in un numero limitato di aree. Se la sottoscrizione non include una di queste aree, è possibile che venga visualizzato il messaggio di errore "Non sono presenti sottoscrizioni nelle aree consentite".

Per richiedere l'aggiunta di un'area alla sottoscrizione, creare una nuova richiesta al supporto tecnico Microsoft dal portale di Azure, scegliere **Fatturazione** come tipo di problema, quindi seguire le istruzioni per inviare la richiesta.

## <a name="storage-account"></a>Account di archiviazione
Il servizio Machine Learning necessita di un account di archiviazione per archiviare i dati. È possibile usare un account di archiviazione esistente oppure creare un nuovo account di archiviazione quando si crea la nuova area di lavoro di Machine Learning Studio (classica) (se si dispone della quota per creare un nuovo account di archiviazione).

Dopo aver creato la nuova area di lavoro di Machine Learning Studio (classica), è possibile accedere a Machine Learning Studio (classico) usando l'account Microsoft usato per creare l'area di lavoro. Se viene visualizzato il messaggio di errore "Area di lavoro non trovata" (simile alla seguente schermata), utilizzare la procedura seguente per eliminare i cookie del browser.

![Area di lavoro non trovata](media/troubleshooting-creating-ml-workspace/screen3.png)

**Per eliminare i cookie dal browser**

1. Se si usa Internet Explorer, fare clic sul pulsante **Strumenti** nell'angolo superiore destro, quindi selezionare **Opzioni Internet**.  

   ![Opzioni Internet](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Nella scheda **Generale** fare clic su **Elimina**.

   ![Scheda Generale](media/troubleshooting-creating-ml-workspace/screen5.png)

3. Nella finestra di dialogo **Elimina cronologia esplorazioni** assicurarsi che l'opzione **Cookie e dati di siti Web** sia selezionata, quindi fare clic su **Elimina**.

   ![Eliminare i cookie](media/troubleshooting-creating-ml-workspace/screen6.png)

Dopo l'eliminazione dei cookie, riavviare il browser e quindi passare alla pagina [di Microsoft Azure Machine Learning Studio (classico).](https://studio.azureml.net) Quando vengono richiesti nome utente e password, immettere lo stesso account Microsoft usato per creare l'area di lavoro.

## <a name="comments"></a>Commenti

L'obiettivo consiste nel semplificare al massimo l'esperienza di Machine Learning. Inserire eventuali commenti e problemi nel [forum su Azure Machine Learning](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) per contribuire al miglioramento del prodotto.
