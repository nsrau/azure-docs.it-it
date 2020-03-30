---
title: Uso della finestra di Azure Cloud Shell | Microsoft Docs
description: Panoramica su come usare la finestra di Azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 3435d958049012750e775e2a17f43f463a0cc358
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70860314"
---
# <a name="using-the-azure-cloud-shell-window"></a>Uso della finestra Azure Cloud Shell

Questo documento illustra come usare la finestra Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Scambio tra ambienti Bash e PowerShell

Usare il selettore di ambiente nella barra degli strumenti di Cloud Shell per passare dall’ambiente Bash a PowerShell e viceversa.  
![Selezionare l'ambiente](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Riavviare Cloud Shell
Fare clic sull'icona di riavvio sulla barra degli strumenti di Cloud Shell per reimpostare lo stato del computer.  
![Riavviare Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> Il riavvio della Cloud Shell reimposta lo stato della macchina e qualsiasi file non persistente nella condivisione file di Azure andrà perso.

## <a name="change-the-text-size"></a>Modificare le dimensioni del testo
Fare clic sull'icona delle impostazioni in alto a sinistra nella finestra, quindi passare il mouse sull'opzione "Text size" e selezionare le dimensioni del testo desiderate. La selezione verrà salvata in modo permanente tra le sessioni.
![Dimensione del testo](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Modificare il tipo di carattere
Fai clic sull'icona delle impostazioni in alto a sinistra nella finestra, quindi passa il mouse sopra l'opzione "Font" e seleziona il font desiderato.  La selezione verrà salvata in modo permanente tra le sessioni.
![Carattere](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Caricare e scaricare file
Fai clic sull'icona dei file di caricamento/download in alto a sinistra nella finestra, quindi seleziona Carica o scarica.  
![Caricare/scaricare file](media/using-the-shell-window/uploaddownload.png)
* Per caricare i file, utilizzare il pop-up per individuare il file sul computer locale, selezionare il file desiderato e fare clic sul pulsante "Apri".  Il file verrà caricato `/home/user` nella directory.
* Per scaricare il file, inserire il percorso completo del file nella finestra pop-up `/home/user` (cioè, fondamentalmente un percorso sotto la directory che appare per impostazione predefinita), e selezionare il pulsante "Download".  
> [!NOTE] 
> In Cloud Shell viene fatta distinzione tra maiuscole e minuscole. Controllare la combinazione di maiuscole/minuscole nel percorso del file.

## <a name="open-another-cloud-shell-window"></a>Aprire un'altra finestra di Cloud Shell
Cloud Shell abilita più sessioni simultanee tra le schede del browser consentendo a ogni sessione di esistere come processo separato.
Alla chiusura di una sessione, assicurarsi di chiudere ogni finestra della sessione poiché ogni processo viene eseguito in modo indipendente anche se nello stesso computer.  
Fare clic sull'icona Apri nuova sessione in alto a sinistra nella finestra. Si aprirà una nuova scheda con un'altra sessione connessa al contenitore esistente.
![Apri nuova sessione](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Editor di Cloud Shell
* Fare riferimento alla pagina [Utilizzo dell'editor di Azure Cloud Shell.](using-cloud-shell-editor.md)

## <a name="web-preview"></a>Anteprima Web
Fare clic sull'icona di anteprima Web in alto a sinistra della finestra, selezionare "Configura", specificare la porta desiderata da aprire.  Selezionare "Apri porta" per aprire solo la porta o "Apri e sfoglia" per aprire la porta e visualizzare l'anteprima della porta in una nuova scheda.  
![Anteprima Web](media/using-the-shell-window/preview.png)  
<br>
![Configurare la porta](media/using-the-shell-window/preview-configure.png)  
Fare clic sull'icona di anteprima web in alto a sinistra della finestra, selezionare "Porta di anteprima ..." per visualizzare l'anteprima di una porta aperta in una nuova scheda. per chiudere la porta aperta.  
![Porta di anteprima/chiusura](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Ridurre e ingrandire la finestra Cloud Shell
Fare clic sull'icona di riduzione nella parte superiore destra della finestra per nasconderla. Fare clic di nuovo sull'icona Cloud Shell per riportare in primo piano la finestra.
Fare clic sull'icona di ingrandimento per aprire la finestra alla grandezza massima. Per ripristinare la dimensioni precedenti, fare clic su Ripristina.  
![Ridurre a icona o ingrandire la finestra](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Copiare e incollare
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Ridimensionare la finestra Cloud Shell
Fare clic e trascinare il bordo superiore della barra degli strumenti verso l'alto o verso il basso per ridimensionare la finestra Cloud Shell.

## <a name="scrolling-text-display"></a>Scorrimento del testo visualizzato
Scorrere con il mouse o il touchpad per spostare il testo del terminale.

## <a name="exit-command"></a>Comando Exit
L'esecuzione di `exit` termina la sessione attiva. Questo comportamento si verifica per impostazione predefinita dopo 20 minuti senza interazione.

## <a name="next-steps"></a>Passaggi successivi

[Guida introduttiva di Bash in Cloud Shell](quickstart.md) <br>
[Guida introduttiva di PowerShell in Cloud Shell](quickstart-powershell.md)
