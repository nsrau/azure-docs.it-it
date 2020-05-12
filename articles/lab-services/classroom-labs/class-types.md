---
title: Tipi di classe di esempio in Azure Lab Services | Microsoft Docs
description: Fornisce alcuni tipi di classe per cui è possibile configurare lab con Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 45043718664eb1b3b6b865821b3f6b2a51199194
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115031"
---
# <a name="class-types-overview---azure-lab-services"></a>Panoramica dei tipi di classi - Azure Lab Services

Azure Lab Services consente di configurare rapidamente ambienti di lab per le classi nel cloud. Gli articoli di questa sezione forniscono indicazioni su come configurare diversi tipi di lab in aula usando Azure Lab Services.

## <a name="deep-learning-in-natural-language-processing"></a>Deep Learning nell'elaborazione del linguaggio naturale

È possibile configurare un lab incentrato sul Deep Learning nell'elaborazione del linguaggio naturale con Azure Lab Services. L'elaborazione del linguaggio naturale è una forma di intelligenza artificiale che dota i computer di strumenti di traduzione, riconoscimento vocale e altre funzionalità di comprensione del linguaggio. Gli studenti che frequentano una classe di elaborazione del linguaggio naturale lavorano su una macchina virtuale Linux per apprendere come applicare gli algoritmi di rete neurale al fine di sviluppare modelli di Deep Learning usati per l'analisi del linguaggio umano scritto.

Per informazioni dettagliate su come configurare questo tipo di lab, vedere [Configurare un lab incentrato sul Deep Learning nell'elaborazione del linguaggio naturale con Azure Lab Services](class-type-deep-learning-natural-processing.md).

## <a name="shell-scripting-on-linux"></a>Script della shell in Linux

È possibile configurare un lab per insegnare lo script della shell in Linux. Lo scripting è una parte utile dell'amministrazione del sistema che consente agli amministratori di evitare attività ripetitive. In questo scenario di esempio, la classe illustra gli script bash tradizionali e gli script avanzati. Gli script avanzati sono script che combinano i comandi bash e Ruby. Questo approccio consente a Ruby di passare dati e comandi bash per interagire con la shell.

Gli studenti che frequentano queste classi di scripting lavorano su una macchina virtuale Linux per apprendere le nozioni di base di Linux e acquisire familiarità con gli script della shell bash. La macchina virtuale Linux viene fornita con accesso tramite Desktop remoto abilitato e con gli editor di testo [gedit](https://help.gnome.org/users/gedit/stable/) e [Visual Studio Code](https://code.visualstudio.com/) installati.

Per informazioni dettagliate su come configurare questo tipo di lab, vedere [Script della shell in Linux](class-type-shell-scripting-linux.md).

## <a name="ethical-hacking"></a>Hacking etico

È possibile configurare un lab per un corso incentrato sul lato forense dell'hacking etico. I test di penetrazione, una pratica usata dalla community di hacking etico, si verificano quando un utente tenta di accedere al sistema o alla rete per dimostrare le vulnerabilità che possono essere sfruttate da un utente malintenzionato.

In un corso di hacking etico gli studenti possono apprendere tecniche moderne per la difesa dalle vulnerabilità. Ogni studente ottiene una macchina virtuale host Windows Server con due macchine virtuali annidate, una con l'immagine [Metasploitable3](https://github.com/rapid7/metasploitable3) e l'altra con l'immagine [Kali Linux](https://www.kali.org/). La macchina virtuale Metasploitable viene usata per i test sugli expoit.  La macchina virtuale Kali Linux fornisce l'accesso agli strumenti necessari per le attività forensi.

Per informazioni dettagliate su come configurare questo tipo di lab, vedere [Configurare un lab per un corso di hacking etico](class-type-ethical-hacking.md).

## <a name="database-management"></a>Gestione di database
I concetti relativi ai database sono uno dei corsi introduttivi offerti nella maggior parte delle facoltà di scienze informatiche. È possibile configurare un lab per una lezionedi gestione di database di base in Azure Lab Services. Ad esempio, è possibile configurare un modello di macchina virtuale in un lab con un server di database [MySQL](https://www.mysql.com/) o un server [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019).

Per informazioni dettagliate su come configurare questo tipo di lab, vedere [Configurare un lab per spiegare la gestione dei database relazionali](class-type-database-management.md).

## <a name="python-and-jupyter-notebooks"></a>Python e Jupyter Notebook
È possibile configurare un computer modello in Azure Lab Services con gli strumenti necessari per insegnare agli studenti come usare [Jupyter Notebook](http://jupyter-notebook.readthedocs.io). Jupyter Notebooks è un progetto open source che consente di combinare facilmente testo RTF e codice sorgente [Python](https://www.python.org/) eseguibile in un'unica area di disegno denominata notebook. L'esecuzione di un notebook comporta un record lineare di input e output.  Questi output possono includere testo, tabelle di informazioni, grafici a dispersione e altro ancora.

Per informazioni dettagliate su come configurare questo tipo di lab, vedere [Configurare un lab per spiegare il data science con Python e Jupyter Notebook](class-type-jupyter-notebook.md).

## <a name="mobile-app-development-with-android-studio"></a>Sviluppo di app per dispositivi mobili con Android Studio
È possibile configurare un lab in Azure Lab Services per impartire una lezione di sviluppo di applicazioni per dispositivi mobili introduttiva. Questa lezione è incentrata sulle applicazioni per dispositivi mobili Android che possono essere pubblicate nel [Google Play Store](https://play.google.com/store/apps).  Gli studenti imparano a usare [Android Studio](https://developer.android.com/studio) per creare applicazioni.  Per testare l'applicazione a livello locale viene usato l'[Emulatore di Visual Studio per Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/).

Per informazioni dettagliate su come configurare questo tipo di lab, vedere [Configurare un lab per spiegare lo sviluppo di applicazioni per dispositivi mobili con Android Studio](class-type-mobile-dev-android-studio.md).

## <a name="big-data-analytics"></a>Analisi dei Big Data
È possibile configurare un Lab per insegnare una classe Big Data Analytics. Con questo tipo di classe, gli studenti imparano a gestire volumi elevati di dati e applicano algoritmi di apprendimento statistico e del computer per derivare informazioni dettagliate sui dati. Uno degli obiettivi principali per gli studenti consiste nell'imparare a usare gli strumenti di analisi dei dati, ad esempio il pacchetto software open source di Apache Hadoop, che fornisce strumenti per l'archiviazione, la gestione e l'elaborazione di Big Data. 

Per informazioni dettagliate su come configurare questo tipo di Lab, vedere [configurare un Lab per Big Data Analytics con la distribuzione di Docker di HortonWorks Data Platform](class-type-big-data-analytics.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti:

- [Configurare un lab incentrato sul Deep Learning nell'elaborazione del linguaggio naturale con Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Script della shell in Linux](class-type-shell-scripting-linux.md)
- [Hacking etico](class-type-ethical-hacking.md)