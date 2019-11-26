---
title: Configurare un Lab per insegnare data science con i notebook Python e Jupyter | Microsoft Docs
description: Informazioni su come configurare un Lab per insegnare data science uso di Python e Jupyter notebook.
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: b69abf098ba7646ebc98d126c7c0d949205d6275
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383968"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Configurare un Lab per insegnare data science con i notebook Python e Jupyter

Questo articolo illustra come configurare un computer modello in Lab Services con gli strumenti necessari per insegnare agli studenti come usare i [notebook di Jupyter](http://jupyter-notebook.readthedocs.io).  Jupyter Notebooks è un progetto open source che consente di combinare facilmente testo RTF e codice sorgente [Python](https://www.python.org/) eseguibile in un'unica area di disegno denominata notebook.  L'esecuzione di un notebook comporta un record lineare di input e output.  Questi output possono includere testo, tabelle di informazioni, grafici a dispersione e altro ancora.

## <a name="lab-configuration"></a>Configurazione del lab

Per configurare questo Lab, è necessario disporre di una sottoscrizione di Azure e di un account Lab per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Una volta ottenuto una sottoscrizione di Azure, è possibile creare un nuovo account Lab in Azure Lab Services. Per ulteriori informazioni sulla creazione di un nuovo account Lab, vedere [esercitazione per la configurazione di un account Lab](tutorial-setup-lab-account.md).  È anche possibile usare un account lab esistente.

### <a name="lab-account-settings"></a>Impostazioni dell'account Lab

Abilitare le impostazioni descritte nella tabella seguente per l'account Lab. Per altre informazioni su come abilitare le immagini del Marketplace, vedere [specificare le immagini del Marketplace disponibili per gli autori del Lab](tutorial-setup-lab-account.md#specify-marketplace-images-available-to-lab-creators).

| Impostazione dell'account Lab | Istruzioni |
| ------------------- | ------------ |
| Immagine del Marketplace | Abilitare l'immagine [Data Science Virtual Machine-Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) per l'uso nell'account Lab. |

>[!TIP]
>Questo articolo si concentra sulla configurazione di un computer modello che usa il sistema operativo Windows Server.  È anche possibile configurare una classe data science con i notebook Python e Jupyter usando [Data Science Virtual Machine per le immagini Linux (CentOS)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm) o [Data Science Virtual Machine per Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) da Azure Marketplace.

### <a name="lab-settings"></a>Impostazioni Lab

Usare le impostazioni nella tabella seguente quando si configura un Lab della classe.  Per altre informazioni su come creare un Lab per le aule, vedere [l'esercitazione sulla configurazione di una classe Lab](tutorial-setup-classroom-lab.md).

| Impostazioni Lab | Valore/istruzioni |
| ------------ | ------------------ |
|Dimensioni della macchina virtuale| Small GPU (Compute). Queste dimensioni sono ideali per applicazioni a elevato utilizzo di calcolo e di rete, come l'intelligenza artificiale e l'apprendimento avanzato. |
|Immagine della macchina virtuale| SQL Server 2019 standard in Windows Server 2019|

## <a name="template-machine"></a>Computer modello

L'immagine [Data Science Virtual Machine-Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) fornisce gli strumenti e i Framework di apprendimento avanzato necessari per questo tipo di classe.  L'immagine include Jupyter notebook e Visual Studio Code.  [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io) è un'applicazione Web che consente ai data scientist di utilizzare dati non elaborati, eseguire calcoli e visualizzare tutti i risultati nello stesso ambiente.  Per il computer modello, l'applicazione Web verrà eseguita localmente.  [Visual Studio Code](https://code.visualstudio.com/) è un IDE che offre un'esperienza interattiva avanzata per la scrittura e il test di un notebook.  Per altre informazioni, vedere [uso di notebook di Jupyter in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

L'attività rimanente per la configurazione della classe consiste nel fornire notebook locali.  Per istruzioni su come usare gli esempi di Azure Machine Learning, vedere [come configurare un ambiente con notebook di Jupyter](../../machine-learning/service/how-to-configure-environment.md#jupyter).  È anche possibile specificare Notebook personalizzati nel computer modello.  I notebook verranno copiati in tutti i computer degli studenti durante la pubblicazione del modello.

## <a name="cost-estimate"></a>Stima dei costi

Verrà ora trattata una possibile stima dei costi per questa classe.  Verrà usata una classe di 25 studenti.  Sono previste 20 ore di tempo della classe pianificata.  Ogni studente riceve inoltre una quota di 10 ore per il lavoro o assegnazioni al di fuori dell'orario di classe pianificato.  Le dimensioni della macchina virtuale scelte sono Small GPU (Compute), ovvero 139 unità Lab.

Di seguito è riportato un esempio di una possibile stima dei costi per questa classe:

25 studenti \* (20 ore pianificate + 10 ore di quota) \* unità Lab 139 \* 0,01 USD all'ora = 1042,5 USD

Per ulteriori informazioni sui prezzi, vedere [Azure Lab Services prezzi](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusione

In questo articolo sono stati illustrati i passaggi per creare un Lab per una classe Jupyter Notebooks. È possibile usare una configurazione simile per altre classi di machine learning.

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni per la configurazione di qualsiasi Lab.

- [Creare e gestire un modello](how-to-create-manage-template.md)
- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Imposta quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Collegamenti per la registrazione tramite posta elettronica agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)
