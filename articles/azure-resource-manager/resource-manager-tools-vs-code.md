---
title: Usare Visual Studio Code per creare modelli di Azure Resource Manager
description: Informazioni su come installare e usare Visual Studio Code e l'estensione degli strumenti di Azure Resource Manager.
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 4c0fd0968ce52e50a9171eecb3dfaebd1e2a4c46
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075022"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Usare Visual Studio Code per creare modelli di Azure Resource Manager

Visual Studio Code è un editor leggero, multipiattaforma e open source. L'estensione Azure Resource Manager Template Tools è un plug-in per lo sviluppo di modelli Gestione risorse. L'estensione aggiunge il supporto della lingua per i modelli per fornire IntelliSense, l'evidenziazione della sintassi, la guida inline e molte altre funzioni del linguaggio. Insieme, offrono un'esperienza di sviluppo del modello consigliata.

## <a name="install-visual-studio-code"></a>Installare Visual Studio Code

Visual Studio Code supporta MacOS, Windows e Linux.  Può essere installato da [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-resource-manager-tools-extension"></a>Installare l'estensione degli strumenti Gestione risorse

1. Aprire Visual Studio Code.
1. Nel menu a sinistra selezionare **estensioni** . In alternativa, scegliere **estensioni** dal menu **Visualizza** per aprire il riquadro estensioni.

    ![Installare l'estensione Visual Studio Code Gestione risorse Tools](./media/resource-manager-tools-vs-code/resource-manager-visual-studio-code-tools-extension.png)
1. Cercare **Gestione risorse**.
1. Selezionare **Installa** in **strumenti Azure Resource Manager**.

## <a name="the-extension-features"></a>Funzionalità di estensione

### <a name="colorization-for-template-language-expressions"></a>Colorazione per le espressioni del linguaggio del modello

Parametri, variabili, funzioni, nomi ed espressioni sono codificati a colori come illustrato nello screenshot seguente:

![Visual Studio Code la colorazione delle estensioni degli strumenti Gestione risorse](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-colorization.png)

La visualizzazione struttura modello consente di spostarsi facilmente tra i modelli di grandi dimensioni.

### <a name="intellisense"></a>IntelliSense

L'estensione del modello di Gestione risorse conosce i possibili completamenti per i nomi di funzione, i parametri, le variabili e i riferimenti. I suggerimenti di IntelliSense compaiono durante la digitazione. Se si continua a digitare caratteri, l'elenco di membri (variabili, metodi e così via) viene filtrato in modo da includere solo i membri che contengono i caratteri tipizzati. Premere **Tab** o **invio** per inserire il membro selezionato.

- Nomi di funzione predefiniti

    ![Funzioni IntelliSense di Visual Studio Code Gestione risorse Tools Extensions](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-functions.png)

- Riferimenti ai parametri

    ![Visual Studio Code Gestione risorse Tools Extensions parametri IntelliSense](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-parameters.png)

- Riferimenti a variabili

    ![Visual Studio Code Gestione risorse Tools Extensions variabili IntelliSense](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-variables.png)

- Proprietà resourceGroup ()

    ![Funzioni IntelliSense di Visual Studio Code Gestione risorse Tools Extensions](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-resourcegroup.png)

Inoltre, IntelliSense funziona anche con le Proprietà Subscription () e le proprietà dei riferimenti a variabili che sono oggetti.

### <a name="signature-help-for-function-parameters"></a>Guida alla firma per i parametri di funzione

Quando si passa il mouse sui nomi di funzione, l'estensione Mostra la guida della firma per i parametri della funzione.

![Funzione di firma estensioni degli strumenti di Gestione risorse Visual Studio Code](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Vai a definizione per i riferimenti a variabili e parametri

È possibile passare alla definizione con **CTRL + clic**oppure usando il menu di scelta rapida, come illustrato nella schermata: ![Visual Studio Code estensioni degli strumenti Gestione risorse Vai a definizione](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-context-menu.png)

È possibile aprire la definizione sul lato con **Ctrl + Alt + clic**.

### <a name="peek-for-variable-and-parameter-definitions"></a>Visualizza le definizioni di variabili e parametri

Per aprire l'editor con visualizzazione, usare il menu di scelta rapida, come illustrato nella schermata precedente.

Lo screenshot seguente mostra l'editor di visualizzazione:

![Editor di anteprima di Visual Studio Code Gestione risorse Tools](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>Trova tutti i riferimenti per variabili e parametri

Per trovare tutti i riferimenti, usare il menu di scelta rapida, come illustrato nella schermata precedente.

Lo screenshot seguente mostra come vengono evidenziati i riferimenti:

![Visual Studio Code Gestione risorse Tools Extensions trova tutti i riferimenti](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>Rinominare tutti i riferimenti per variabili e parametri

Per rinominare tutti i riferimenti per variabili e parametri, usare il menu di scelta rapida, come illustrato nella schermata precedente.

### <a name="hover-for-parameter-description"></a>Passaggio del mouse per la descrizione del parametro

![Definizione del passaggio del mouse sulle estensioni degli strumenti di Gestione risorse Visual Studio Code](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Corrispondenza parentesi graffe

La corrispondenza tra parentesi quadre viene evidenziata non appena il cursore è vicino a uno di essi. Quando si fa clic su una parentesi graffa, viene evidenziata anche la parentesi graffa corrispondente, come illustrato nello screenshot seguente:

![Visual Studio Code la corrispondenza delle parentesi graffe delle estensioni degli strumenti Gestione risorse](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Mostra errori e avvisi

Gli errori identificati dall'estensione includono:

- Riferimenti a parametri non definiti
- Riferimenti a variabili non definite
- Nomi di funzione non riconosciuti
- utilizzo della funzione Reference () nella definizione di variabile
- Numero di argomenti non corretto nelle funzioni

Gli avvisi includono:

- Parametri inutilizzati
- Variabili inutilizzate

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla creazione di modelli di Azure Resource Manager, vedere [esercitazione: creare e distribuire il primo modello di Azure Resource Manager](template-tutorial-create-first-template.md).
- Per eseguire una guida introduttiva usando Visual Studio Code, vedere [Guida introduttiva: creare modelli di Azure Resource Manager tramite Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)
