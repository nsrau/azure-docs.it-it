---
title: Uso della finestra Azure Cloud Shell (anteprima) | Microsoft Docs
description: Informazioni sulla finestra Azure Cloud Shell.
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: a47961dfdaf178a6b793bd68105d9792a9275bb3
ms.contentlocale: it-it
ms.lasthandoff: 08/17/2017

---

# <a name="using-the-azure-cloud-shell-window"></a>Uso della finestra Azure Cloud Shell

Questo documento illustra come usare la finestra Cloud Shell.

## <a name="concurrent-sessions"></a>Sessioni simultanee
Cloud Shell abilita l'apertura di più sessioni simultanee nelle schede del browser, consentendo a ogni sessione di essere disponibile come processo Bash separato.
Alla chiusura di una sessione, assicurarsi di chiudere ogni finestra della sessione poiché ogni processo viene eseguito in modo indipendente anche se nello stesso computer.

## <a name="restart-cloud-shell"></a>Riavviare Cloud Shell
![](media/recycle.png)
> [!WARNING]
> Il riavvio della Cloud Shell reimposta lo stato della macchina e qualsiasi file non persistente nella condivisione file andrà perso.

* Fare clic sull'icona di riavvio sulla barra degli strumenti per ricevere un nuovo ambiente Cloud Shell.

## <a name="minimize--maximize-cloud-shell-window"></a>Ridurre e ingrandire la finestra Cloud Shell
![](media/minmax.png)
* Fare clic sull'icona di riduzione nella parte superiore destra della finestra per nasconderla. Fare clic di nuovo sull'icona Cloud Shell per riportare in primo piano la finestra.
* Fare clic sull'icona di ingrandimento per aprire la finestra alla grandezza massima. Per ripristinare la dimensioni precedenti, fare clic su Ripristina.

## <a name="copy-and-paste"></a>Copiare e incollare
* Windows: `Ctrl-insert` per copiare e `Shift-insert` per incollare. Facendo clic con pulsante destro del mouse sull'elenco a discesa è possibile abilitare Copia e Incolla.
  * FireFox o Internet Explorer potrebbero non supportare correttamente le autorizzazioni per gli appunti.
* Mac OS: `Cmd-c` per copiare e `Cmd-v` per incollare. Facendo clic con pulsante destro del mouse sull'elenco a discesa è possibile abilitare Copia e Incolla.

## <a name="resize-cloud-shell-window"></a>Ridimensionare la finestra Cloud Shell
* Fare clic e trascinare il bordo superiore della barra degli strumenti verso l'alto o verso il basso per ridimensionare la finestra Cloud Shell.

## <a name="scrolling-text-display"></a>Scorrimento del testo visualizzato
* Scorrere con il mouse o il touchpad per spostare il testo del terminale.

## <a name="exit-command"></a>Comando Exit
L'esecuzione di `exit` termina la sessione attiva. Questo comportamento si verifica per impostazione predefinita dopo 20 minuti senza interazione.

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di Cloud Shell](quickstart.md)

