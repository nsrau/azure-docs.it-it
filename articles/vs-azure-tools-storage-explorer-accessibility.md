---
title: Accessibilità di Azure Storage Explorer (anteprima) | Microsoft Docs
description: Accessibilità di Azure Storage Explorer (anteprima)
services: storage
documentationcenter: na
author: MrayermannMSFT
manager: jinglouMSFT
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2018
ms.author: marayerm
ms.openlocfilehash: aa42d6b898a3cc474575f6c8dae65ff420fc8e30
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60457267"
---
# <a name="storage-explorer-accessibility"></a>Accessibilità di Storage Explorer

## <a name="screen-readers"></a>Utilità per la lettura dello schermo

Storage Explorer supporta l'uso dell'utilità per la lettura dello schermo su Windows e Mac. Le utilità per la lettura dello schermo seguenti sono consigliate per ogni piattaforma:

Piattaforma | Lettura dello schermo
---------|--------------
Windows  | NVDA
Mac      | Voice Over
Linux    | (i lettori dello schermo non sono supportati in Linux)

Se si verifica un problema di accessibilità durante l'uso di Storage Explorer, [aprire un problema in GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="zoom"></a>Zoom

In Storage Explorer è possibile ingrandire il testo tramite lo zoom avanti. Per fare zoom avanti, fare clic su **Zoom avanti** nel menu Guida. È inoltre possibile usare il menu Guida per fare zoom indietro e reimpostare il livello di zoom al livello predefinito.

![Opzioni di zoom nel menu Guida][0]

L'impostazione dello zoom aumenta la dimensione della maggior parte degli elementi dell'interfaccia utente. È consigliabile anche abilitare le impostazioni per il testo di grandi dimensioni e lo zoom del sistema operativo per assicurarsi che tutti gli elementi dell'interfaccia utente siano ridimensionati correttamente.

## <a name="high-contrast-themes"></a>Temi a contrasto elevato

Storage Explorer dispone di due temi a contrasto elevato, **Chiaro a contrasto elevato** e **Scuro a contrasto elevato**. È possibile modificare il tema selezionando della Guida > dal menu di temi.

![Sottomenu Temi][1]

L'impostazione del tema cambia il colore della maggior parte degli elementi dell'interfaccia utente. È consigliabile abilitare anche il tema a contrasto elevato corrispondente del sistema operativo per garantire che tutti gli elementi dell'interfaccia utente abbiano colori appropriati.

## <a name="shortcut-keys"></a>Tasti di scelta rapida

### <a name="window-commands"></a>Comandi della finestra

Comando       | Tasto di scelta rapida
--------------|--------------------
Nuova finestra    | **CTRL+MAIUSC+N**
Chiudi editor  | **CTRL+F4**
Esci          | **CTRL+MAIUSC+W**

### <a name="navigation-commands"></a>Comandi di spostamento

Comando                | Tasto di scelta rapida
-----------------------|----------------------
Sposta stato attivo sul pannello successivo       | **F6**
Sposta stato attivo sul pannello precedente   | **MAIUSC+F6**
Explorer               | **CTRL+MAIUSC+E**
Gestione degli account     | **CTRL+MAIUSC+A**
Attiva/Disattiva barra laterale        | **CTRL+B**
Log attività           | **CTRL+MAIUSC+L**
Azioni e proprietà | **CTRL+MAIUSC+P**
Editor corrente         | **CTRL+HOME**
Editor successivo            | **CTRL+PGGIÙ**
Editor precedente        | **CTRL+PGSU**

### <a name="zoom-commands"></a>Comandi zoom

Comando  | Tasto di scelta rapida
---------|------------------
Zoom avanti  | **CTRL+=**
Zoom indietro | **CTRL +-**

### <a name="blob-and-file-share-editor-commands"></a>Comandi dell'editor per BLOB e condivisione file

Comando | Tasto di scelta rapida
--------|--------------------
Indietro    | **ALT+freccia SINISTRA**
Avanti | **ALT+freccia DESTRA**
Su      | **ALT+freccia SU**

### <a name="editor-commands"></a>Comandi dell'editor

Comando | Tasto di scelta rapida
--------|------------------
Copia    | **CTRL+C**
Taglia     | **CTRL+X**
Incolla   | **CTRL+V**
Aggiorna  | **CTRL+R**

### <a name="other-commands"></a>Altri comandi

Comando                | Tasto di scelta rapida
-----------------------|------------------
Attiva/Disattiva strumenti di sviluppo | **F12**
Ricarica                 | **CTRL + ALT + R**

[0]: ./media/vs-azure-tools-storage-explorer-accessibility/Zoom.png
[1]: ./media/vs-azure-tools-storage-explorer-accessibility/HighContrast.png
