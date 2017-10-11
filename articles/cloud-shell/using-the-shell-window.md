---
title: Uso della finestra Azure Cloud Shell (anteprima) | Microsoft Docs
description: Panoramica su come usare la finestra di Azure Cloud Shell.
services: azure
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
ms.date: 09/25/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fb242abfbea79bc8c242a7a89b3d775cf74a0617
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="using-the-azure-cloud-shell-window"></a>Uso della finestra Azure Cloud Shell

Questo documento illustra come usare la finestra Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Scambio tra ambienti Bash e PowerShell
![](media/using-the-shell-window/env-selector.png)

Usare il selettore di ambiente nella barra degli strumenti di Cloud Shell per passare dall’ambiente Bash a PowerShell e viceversa.

## <a name="restart-cloud-shell"></a>Riavviare Cloud Shell
![](media/using-the-shell-window/restart.png)
> [!WARNING]
> Il riavvio della Cloud Shell reimposta lo stato della macchina e qualsiasi file non persistente nella condivisione file andrà perso.

* Fare clic sull'icona di riavvio sulla barra degli strumenti di Cloud Shell per reimpostare lo stato del computer.

## <a name="minimize--maximize-cloud-shell-window"></a>Ridurre e ingrandire la finestra Cloud Shell
![](media/using-the-shell-window/minmax.png)
* Fare clic sull'icona di riduzione nella parte superiore destra della finestra per nasconderla. Fare clic di nuovo sull'icona Cloud Shell per riportare in primo piano la finestra.
* Fare clic sull'icona di ingrandimento per aprire la finestra alla grandezza massima. Per ripristinare la dimensioni precedenti, fare clic su Ripristina.

## <a name="concurrent-sessions"></a>Sessioni simultanee
Cloud Shell abilita l'apertura di più sessioni simultanee nelle schede del browser, consentendo a ogni sessione di essere disponibile come processo Bash separato.
Alla chiusura di una sessione, assicurarsi di chiudere ogni finestra della sessione poiché ogni processo viene eseguito in modo indipendente anche se nello stesso computer.

## <a name="copy-and-paste"></a>Copiare e incollare
[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Ridimensionare la finestra Cloud Shell
* Fare clic e trascinare il bordo superiore della barra degli strumenti verso l'alto o verso il basso per ridimensionare la finestra Cloud Shell.

## <a name="scrolling-text-display"></a>Scorrimento del testo visualizzato
* Scorrere con il mouse o il touchpad per spostare il testo del terminale.

## <a name="changing-the-text-size"></a>Modificare la dimensione del testo
![](media/using-the-shell-window/text-size.png)
* Fare clic sull'icona delle impostazioni in alto a sinistra nella finestra, quindi passare il mouse sull'opzione "Text size" e selezionare le dimensioni del testo desiderate.

## <a name="exit-command"></a>Comando Exit
L'esecuzione di `exit` termina la sessione attiva. Questo comportamento si verifica per impostazione predefinita dopo 10 minuti senza interazione.

## <a name="next-steps"></a>Passaggi successivi

[Avvio rapido di Bash in Cloud Shell](quickstart.md)
[Avvio rapido di PowerShell in Cloud Shell](quickstart-powershell.md)
