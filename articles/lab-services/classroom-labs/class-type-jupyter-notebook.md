---
title: Configurare un laboratorio per insegnare la scienza dei dati con Python e Jupyter Notebooks Documenti Microsoft
description: Scopri come configurare un laboratorio per insegnare l'analisi scientifica dei dati usando Python e Jupyter Notebooks.
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
ms.openlocfilehash: 222f5647248d27c3bdfabd0feaeb66dd9f543652
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257726"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Configurare un laboratorio per insegnare la scienza dei dati con Python e Jupyter Notebooks

In questo articolo viene descritto come configurare un computer modello in Lab Services con gli strumenti necessari per insegnare agli studenti come utilizzare [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io).  Jupyter Notebooks è un progetto open source che consente di combinare facilmente testo RTF e codice sorgente [Python](https://www.python.org/) eseguibile in un'unica area di disegno denominata notebook.  L'esecuzione di un notebook comporta un record lineare di input e output.  Questi output possono includere testo, tabelle di informazioni, grafici a dispersione e altro ancora.

## <a name="lab-configuration"></a>Configurazione del lab

Per configurare questo lab, sono necessari una sottoscrizione di Azure e un account lab per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Dopo aver ottenuto una sottoscrizione di Azure, è possibile creare un nuovo account lab in Azure Lab Services.Once you get an Azure subscription, you can create a new lab account in Azure Lab Services. Per ulteriori informazioni sulla creazione di un nuovo account lab, vedere [esercitazione per l'impostazione](tutorial-setup-lab-account.md)di un account lab .  È inoltre possibile utilizzare un account lab esistente.

### <a name="lab-account-settings"></a>Impostazioni dell'account lab

Abilitare le impostazioni descritte nella tabella seguente per l'account lab. Per ulteriori informazioni su come abilitare le immagini del marketplace, consultate Specificare le immagini del [Marketplace disponibili per i creatori di laboratori.](specify-marketplace-images.md)

| Impostazione dell'account lab | Istruzioni |
| ------------------- | ------------ |
| Immagine del Marketplace | Abilitare l'immagine [data science virtual - Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) per l'uso all'interno dell'account lab. |

>[!TIP]
>Questo articolo si concentrerà sulla configurazione di un computer modello che utilizza il sistema operativo Windows Server.  È anche possibile configurare una classe di data science con python e blocchi appunti di Jupyter usando immagini [data science Virtual Machine for Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) da Azure Marketplace.

### <a name="lab-settings"></a>Impostazioni lab

Utilizza le impostazioni nella tabella seguente quando configuri un laboratorio di classe.  Per ulteriori informazioni su come creare un lab della classe, vedere [Configurare un'esercitazione](tutorial-setup-classroom-lab.md)del laboratorio in classe .

| Impostazioni lab | Valore/istruzioni |
| ------------ | ------------------ |
|Dimensioni della macchina virtuale| GPU piccola (Compute). Questa dimensione è più adatta per applicazioni ad alta intensità di elaborazione e di rete come intelligenza artificiale e Deep Learning. |
|Immagine di macchina virtuale| Macchina virtuale data science - Windows 2016|

## <a name="template-machine"></a>Macchina modello

L'immagine [Data Science Virtual Machine - Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) fornisce i framework e gli strumenti di deep learning necessari per questo tipo di classe.  L'immagine include Jupyter Notebooks e Visual Studio Code.  [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io) è un'applicazione web che consente agli scienziati dei dati di prendere dati grezzi, eseguire calcoli e visualizzare i risultati tutti nello stesso ambiente.  Per la nostra macchina modello, l'applicazione web verrà eseguita in locale.  [Visual Studio Code](https://code.visualstudio.com/) è un IDE che offre un'esperienza interattiva avanzata durante la scrittura e il test di un blocco appunti.  Per ulteriori informazioni, vedere Utilizzo dei blocchi appunti di [Jupyter nel codice di Visual Studio](https://code.visualstudio.com/docs/python/jupyter-support).

L'attività rimanente per configurare la classe consiste nel fornire blocchi appunti locali.  Per istruzioni su come usare gli esempi di Azure Machine Learning, vedere come configurare un ambiente con i blocchi appunti di [Jupyter.](../../machine-learning/how-to-configure-environment.md#jupyter)  È inoltre possibile fornire i propri blocchi appunti nella macchina modello.  I blocchi appunti verranno copiati in tutte le macchine studentesche quando il modello viene pubblicato.

## <a name="cost-estimate"></a>Preventivo

Copriamo una possibile stima dei costi per questa classe.  Useremo una classe di 25 studenti.  Ci sono 20 ore di tempo programmato per la lezione.  Inoltre, ogni studente riceve 10 ore di quota per i compiti a casa o i compiti al di fuori dell'orario previsto.  La dimensione della macchina virtuale scelta era GPU piccola (calcolo), ovvero 139 unità lab.

Ecco un esempio di una possibile stima dei costi per questa classe:

25 \* studenti (20 ore programmate e 10 ore di quota) \* 139 unità \* di laboratorio 0,01 USD all'ora - 1042,5 USD

Ulteriori ulteriori dettagli sui prezzi, vedere Prezzi di [Servizi di laboratorio di Azure](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusioni

In questo articolo sono stati illustrati i passaggi per creare un lab per una classe Jupyter Notebooks.In this article, we walked through the steps to create a lab for a Jupyter Notebooks class. È possibile usare una configurazione simile per altre classi di machine learning.

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni alla configurazione di qualsiasi lab.

- [Creare e gestire un modello](how-to-create-manage-template.md)
- [Aggiungi utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Imposta quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Link di registrazione via e-mail agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)
