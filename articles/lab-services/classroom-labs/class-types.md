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
ms.topic: overview
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 0e8b203240b4b2d6b67534ab52a7cd4ccf5df571
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976589"
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

In un corso di hacking etico gli studenti possono apprendere tecniche moderne per la difesa dalle vulnerabilità. Ogni studente ottiene una macchina virtuale host Windows Server con due macchine virtuali annidate, una con un'immagine **Metasploitable** e l'altra con un'immagine [Kali Linux](https://www.kali.org/). La macchina virtuale Metasploitable viene usata a scopo di exploit, mentre la macchina virtuale Kali fornisce l'accesso agli strumenti necessari per eseguire attività forensi.

Per informazioni dettagliate su come configurare questo tipo di lab, vedere [Configurare un lab per un corso di hacking etico](class-type-ethical-hacking.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Configurare un lab incentrato sul Deep Learning nell'elaborazione del linguaggio naturale con Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Script della shell in Linux](class-type-shell-scripting-linux.md)