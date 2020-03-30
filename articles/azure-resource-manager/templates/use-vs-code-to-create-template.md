---
title: Usare il codice di Visual Studio per creare modelliUse Visual Studio Code to create templates
description: Informazioni su come installare e usare Visual Studio Code e l'estensione Azure Resource Manager Tools.Learn how to install and use Visual Studio Code and the Azure Resource Manager Tools extension.
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 585e2773ea5d6af184f85e65b63d39b60d632146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273631"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Usare il codice di Visual Studio per creare modelli di Azure Resource ManagerUse Visual Studio Code to create Azure Resource Manager templates

Visual Studio Code è un editor open source leggero, multipiattaforma. L'estensione Strumenti modello di Azure Resource Manager è un plug-in per lo sviluppo di modelli di Resource Manager.The Azure Resource Manager Template Tools extension is a plugin for Resource Manager template development. L'estensione aggiunge il supporto del linguaggio per i modelli per fornire intellisense, evidenziazione della sintassi, guida in linea e molte altre funzioni del linguaggio. Insieme, forniscono l'esperienza di sviluppo di modelli consigliata.

## <a name="install-visual-studio-code"></a>Installare Visual Studio Code

Visual Studio Code supporta MacOS, Windows e Linux.  Può essere installato da [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-resource-manager-tools-extension"></a>Installare l'estensione degli strumenti di Resource ManagerInstall Resource Manager Tools extension

1. Aprire Visual Studio Code.
1. Selezionare **Estensioni** dal menu a sinistra. In alternativa, scegliere **Estensioni** dal menu **Visualizza** per aprire il riquadro Estensioni.

    ![Installare l'estensione Strumenti di Visual Studio Code Resource ManagerInstall Visual Studio Code Resource Manager Tools extension](./media/use-vs-code-to-create-template/resource-manager-visual-studio-code-tools-extension.png)
1. Cercare **Gestione risorse**.
1. Selezionare **Installa** in Strumenti di **Azure Resource Manager**.

## <a name="the-extension-features"></a>Le funzioni di estensione

### <a name="colorization-for-template-language-expressions"></a>Colorazione per le espressioni del linguaggio dei modelliColorization for Template Language Expressions

Parametri, variabili, funzioni, nomi ed espressioni sono contraddistinti da colori diversi, come illustrato nella schermata seguente:Parameters, variables, functions, names, names, and expressions are color-coded as shown in the following screenshot:

![Colorazione delle estensioni degli strumenti di Visual Studio Code Resource Manager](./media/use-vs-code-to-create-template/resource-manager-tools-extension-colorization.png)

La visualizzazione struttura del modello semplifica la navigazione tra modelli di grandi dimensioni.

### <a name="intellisense"></a>Intellisense

L'estensione del modello Resource Manager conosce i possibili completamenti per nomi di funzione, parametri, variabili e riferimenti. Durante la digitazione vengono visualizzati i suggerimenti di IntelliSense. Se si continua a digitare caratteri, l'elenco dei membri (variabili, metodi e così via) viene filtrato in modo da includere solo i membri contenenti i caratteri digitati. Premendo **Tab** o **Invio** si inserisce il membro selezionato.

- Nomi delle funzioni incorporate

    ![Funzioni di intellisense delle estensioni degli strumenti di Visual Studio Code Resource Manager](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-functions.png)

- Riferimenti ai parametri

    ![Parametri intellisense delle estensioni delle estensioni degli strumenti di Visual Studio Code Resource Manager](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-parameters.png)

- Riferimenti variabili

    ![Gli strumenti di Visual Studio Code Resource Manager estensioni intellisense variabili](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-variables.png)

- resourceGroup() proprietà

    ![Funzioni di intellisense delle estensioni degli strumenti di Visual Studio Code Resource Manager](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-resourcegroup.png)

Inoltre, intellisense funziona anche con le proprietà subscription() e le proprietà dei riferimenti a variabili che sono oggetti.

### <a name="signature-help-for-function-parameters"></a>Guida alla firma per i parametri di funzione

Quando si posiziona il puntatore del mouse sui nomi delle funzioni, l'estensione mostra la Guida alla firma per i parametri della funzione.

![Funzione di firma delle estensioni delle estensioni degli strumenti di Visual Studio Code Resource Manager](./media/use-vs-code-to-create-template/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Vai a definizione per i riferimenti a variabili e parametri

È possibile passare alla definizione con **Ctrl , fare clic**o ![utilizzando il menu di scelta rapida come illustrato nella schermata: Le estensioni degli strumenti di Visual Studio Code Resource Manager vanno alla definizione](./media/use-vs-code-to-create-template/resource-manager-tools-extension-context-menu.png)

È possibile aprire la definizione sul lato con **Ctrl , Alt e Clic**.

### <a name="peek-for-variable-and-parameter-definitions"></a>Visualizzazione per definizioni di variabili e parametri

Per aprire l'editor con visualizzazione, usare il menu di scelta rapida come illustrato nella schermata precedente.

La schermata seguente mostra l'editor di anteprima:The following screenshot shows the peek editor:

![Peek editor di estensioni degli strumenti di Visual Studio Code Resource Manager](./media/use-vs-code-to-create-template/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>Trova tutti i riferimenti per variabili e parametri

Per trovare tutti i riferimenti, utilizzare il menu di scelta rapida come illustrato nella schermata precedente.

La schermata seguente mostra come vengono evidenziati i riferimenti:The following screenshot shows how the references are highlighted:

![Le estensioni degli strumenti di Visual Studio Code Resource Manager trovano tutti i riferimenti](./media/use-vs-code-to-create-template/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>Rinominare tutti i riferimenti per variabili e parametri

Per rinominare tutti i riferimenti per variabili e parametri, utilizzare il menu di scelta rapida come illustrato nella schermata precedente.

### <a name="hover-for-parameter-description"></a>Passaggio del mouse per la descrizione del parametro

![Definizione del passaggio del mouse delle estensioni degli strumenti di Visual Studio Code Resource Manager](./media/use-vs-code-to-create-template/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Corrispondenza parentesi graffe

Le parentesi corrispondenti vengono evidenziate non appena il cursore si trova vicino a uno di essi. Quando si fa clic su una parentesi graffa, anche la parentesi graffa corrispondente viene evidenziata come illustrato nella schermata seguente:

![Corrispondenza delle estensioni delle estensioni degli strumenti di Visual Studio Code Resource Manager](./media/use-vs-code-to-create-template/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Mostra errori e avvisi

Gli errori identificati dall'estensione includono:

- Riferimenti ai parametri non definiti
- Riferimenti a variabili non definiti
- Nomi di funzioni non riconosciute
- utilizzo della funzione reference() nella definizione della variabile
- Numero errato di argomenti nelle funzioni

Gli avvisi includono:

- Parametri inutilizzati
- Variabili inutilizzate

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla creazione di modelli di Azure Resource Manager, vedere Esercitazione: Creare e distribuire il primo modello di Azure Resource Manager.To learn about creating Azure Resource Manager templates, see [Tutorial: Create and deploy your first Azure Resource Manager template.](template-tutorial-create-first-template.md)
- Per usare una guida introduttiva tramite Visual Studio Code, vedere Guida introduttiva: Creare modelli di Azure Resource Manager usando il codice di Visual StudioTo go through a quickstart by using Visual Studio Code, see [Quickstart: Create Azure Resource Manager templates by using Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)
