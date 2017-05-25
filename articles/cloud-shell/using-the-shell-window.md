---
title: Uso della finestra Azure Cloud Shell (anteprima) | Documentazione Microsoft
description: Informazioni sulla finestra Azure Cloud Shell.
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 4e78dadb7fa036dcb4c5706fe774cbd85d57a746
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="using-the-shell-window"></a>Uso della finestra della shell
Questo documento illustra come usare la finestra di Azure Cloud Shell.

## <a name="concurrent-sessions"></a>Sessioni simultanee
Cloud Shell abilita l'apertura di più sessioni simultanee nelle schede del browser, consentendo a ogni sessione di essere disponibile come processo Bash separato.
Alla chiusura di una sessione, assicurarsi di chiudere ogni finestra della sessione poiché ogni processo viene eseguito in modo indipendente anche se nello stesso computer.

## <a name="restart-cloud-shell"></a>Riavviare Cloud Shell
![](media/recycle.png)
* Fare clic sull'icona di riavvio sulla barra degli strumenti per reimpostare Cloud Shell.

> ![AVVISO] Il riavvio della Cloud Shell reimposta lo stato della macchina e qualsiasi file non persistente in clouddrive andrà perso.

## <a name="minimize--maximize-cloud-shell-window"></a>Ridurre e ingrandire la finestra Cloud Shell
![](media/minmax.png)
* Fare clic sull'icona di riduzione nella parte superiore destra della finestra per nasconderla. Fare clic di nuovo sull'icona Cloud Shell per riportare in primo piano la finestra.
* Fare clic sull'icona di ingrandimento per aprire la finestra alla grandezza massima. Per ripristinare la dimensioni precedenti, fare clic su Ripristina.

## <a name="copy-and-paste"></a>Copiare e incollare
* Windows: `Ctrl-insert` e `Shift-insert` oppure fare clic nell'elenco a discesa per copiare e incollare
  * FireFox o Internet Explorer potrebbero non supportare correttamente le autorizzazioni per gli appunti
* Mac OS: `Cmd-c` e `Cmd-v` oppure fare clic nell'elenco a discesa per copiare e incollare

## <a name="resize-cloud-shell-window"></a>Ridimensionare la finestra Cloud Shell
* Fare clic e trascinare il bordo superiore della barra degli strumenti verso l'alto o verso il basso per ridimensionare la finestra Cloud Shell.

## <a name="scrolling-text-display"></a>Scorrimento del testo visualizzato
* Scorrere con il mouse o il touchpad

## <a name="exit-command"></a>Comando Exit
L'esecuzione di `exit` termina la sessione attiva. Questo comportamento si verifica per impostazione predefinita dopo 10 minuti senza interazione.

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di Cloud Shell](quickstart.md)  
