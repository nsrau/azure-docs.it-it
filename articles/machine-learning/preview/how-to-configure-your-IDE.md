---
title: Come configurare Azure Machine Learning Workbench per usare un IDE  | Microsoft Docs
description: Una guida alla configurazione di Azure Machine Learning Workbench per usare l'IDE.
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: caafc626d42ea3f1514c36be04507adf31bbc5c9
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="how-to-configure-azure-machine-learning-workbench-to-work-with-an-ide"></a>Come configurare Azure Machine Learning Workbench per usare un IDE 

Azure Machine Learning Workbench può essere configurato per interagire con gli IDE, ovvero gli ambienti di sviluppo integrato, più diffusi di Python. Consente una semplice esperienza di sviluppo di data science che passa dalla preparazione dati, alla creazione di codice, al rilevamento di esecuzione e all'operazionalizzazione. Attualmente, gli IDE supportati sono:
- Microsoft Visual Studio Code 
- JetBrain PyCharm 

## <a name="configure-workbench"></a>Configurare l'area di lavoro
1. Fare clic sul menu **File** nell'angolo in alto a sinistra dell'app. 
2. Selezionare l'opzione **Configure Project IDE** (Configura l'IDE del progetto) dal riquadro a comparsa 
3. Digitare `VS Code` o `PyCharm` nel campo **Nome**, il nome è arbitrario
4. Immettere il percorso del file eseguibile dell'IDE, completare inserendo il nome e l'estensione del file eseguibile, in **Percorso di esecuzione**

### <a name="default-install-path-for-visual-studio-code"></a>Percorso di installazione predefinito per Visual Studio Code  

* Windows a 32 bit: `C:\Program Files (x86)\Microsoft VS Code\Code.exe`
* Windows a 64 bit: `C:\Program Files\Microsoft VS Code\Code.exe`
* macOS: selezionare il percorso .app, ad esempio `/Applications/Visual Studio Code.app`; l'app aggiungerà il resto del percorso per l'utente. Il percorso completo del file eseguibile per impostazione predefinita è `/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code`. Se è stato eseguito il comando `Shell Command: Install 'code' command in PATH` nel codice in VS Code, è anche possibile fare riferimento allo script di VS Code in `/usr/local/bin/code`

### <a name="default-install-path-for-pycharm"></a>Percorso di installazione predefinito per PyCharm 

* Windows a 32 bit: `C:\Program Files (x86)\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm.exe`. 
* Windows a 64 bit: `C:\Program Files\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm64.exe`.
* macOS: selezionare il percorso .app, ad esempio "/Applicazionitions/PyCharm CE.app"; l'app aggiungerà il resto del percorso per l'utente. Il percorso completo del file eseguibile per impostazione predefinita è `/Applications/PyCharm CE.app/Contents/MacOS/pycharm`. È anche possibile trovare PyCharm nella cartella Bin, `/usr/local/bin/charm`

## <a name="open-project-in-ide"></a>Aprire il progetto nell'IDE 
Dopo aver completato la configurazione, è possibile aprire un progetto di Azure Machine Learning aprendo il menu **File** in Azure Machine Learning Workbench e quindi selezionando **Apri progetto (<IDE_Name>)**. Questa azione apre il progetto attivo corrente nell'IDE configurato. _Nota: l'opzione **Apri progetto (< IDE_Name >)** risulta disabilitata se non ci si trova all'interno di un progetto._

## <a name="configuring-the-integrated-terminal-in-visual-studio-code"></a>Configurazione del terminale integrato in Visual Studio Code

### <a name="windows"></a>Windows 
La shell predefinita è stata sostituita con cmd anziché usare PowerShell. Facendo clic su **Apri progetto (<IDE_Name>)**, viene visualizzato un prompt: 

_Do you allow shell: `C:\windows\System32\cmd.exe` (defined as a workspace setting) to be launched in the terminal?_ (Consentire alla shell: `C:\windows\System32\cmd.exe`, definita come impostazione dell'area di lavoro, di essere avviata nel terminale?)

Rispondere `yes` per consentire di configurare la shell per integrarsi perfettamente con l'interfaccia della riga di comando di Azure ML Workbench.

### <a name="mac"></a>Mac
Per eseguire un comando `az` con il terminale integrato di Visual Studio Code su Mac, è necessario impostare manualmente `PATH` affinché abbia lo stesso valore di `PATH` nel file `.vscode/settings.json` del progetto, all'interno della chiave `terminal.integrated.env.osx`. A questo scopo, eseguire il comando seguente nel terminale: `PATH=<PATH in .vscode/settings>`
