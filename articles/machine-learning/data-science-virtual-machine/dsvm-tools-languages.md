---
title: Lingue supportate
titleSuffix: Azure Data Science Virtual Machine
description: I linguaggi dei programmi supportati e gli strumenti correlati preinstallati nella macchina virtuale di data science.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: e7b32579712e89c0d5595303ee7e03d8b2462607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283654"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Linguaggi supportati dalla macchina virtuali per data science 

Data Science Virtual Machine (DSVM) include diversi linguaggi e strumenti di sviluppo predefiniti per la creazione di applicazioni di intelligenza artificiale (AI). Ecco alcuni dei più importanti.

## <a name="python-windows-server-2016-edition"></a>Python (windows Server 2016 edition)

|    |           |
| ------------- | ------------- |
| Versioni linguistiche supportate | Pitone 2.7 e 3.7 |
| Edizioni DSVM supportate      | Windows Server 2016     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Vengono `conda` creati due ambienti globali: <br /> L'ambiente `root` che `/anaconda/` si trova in è Python 3.7. <br/> L'ambiente `python2` che `/anaconda/envs/python2` si trova in è Python 2.7.       |
| Collegamenti agli esempi      | Sono inclusi i notebook Jupyter di esempio per Python.     |
| Strumenti correlati su DSVM      | PySpark, R, Julia.      |

> [!NOTE]
> Le build di Windows Server 2016 create prima di marzo 2018 contengono Python 3.5 e Python 2.7. Python 2.7 è l'ambiente **radice** conda, e **py37** è l'ambiente Python 3.7.

### <a name="how-to-use-and-run-it"></a>Come usarlo ed eseguirlo    

* Esegui al prompt dei comandi:

  Aprire un prompt dei comandi e utilizzare uno dei seguenti metodi, a seconda della versione di Python che si desidera eseguire:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* Usare in un IDE:Use in an IDE:

  Utilizzare Python Tools per Visual Studio (PTVS), installato nell'edizione community di Visual Studio. Per impostazione predefinita, l'unico ambiente configurato automaticamente in PTVS è Python 3.6. 

    > [!NOTE]
    > Per puntare PTVS a Python 2.7, è necessario creare un ambiente personalizzato in PTVS. Per impostare questo percorso di ambiente in Visual Studio Community Edition, passare a Strumenti Python Tools Python Environments **(Strumenti** -> **Python)** -> Ambienti Python **(Strumenti Python)** e selezionare **.** Quindi, **impostare**il percorso su **c:**

* Utilizzare in Jupyter:

  Aprire Jupyter e selezionare **Nuovo** per creare un nuovo blocco appunti. È possibile impostare il tipo di kernel come _Python [Conda Root]_ per Python 3.7 e _Python [Conda env:python2]_ per Python 2.7.

* Installare i pacchetti Python:

  Gli ambienti Python predefiniti nella dSVM sono ambienti globali leggibili da tutti gli utenti. Ma solo gli amministratori possono scrivere e installare pacchetti globali. Per installare i pacchetti nell'ambiente globale, attivare l'ambiente root o python2 utilizzando il `activate` comando come amministratore. Quindi, è possibile utilizzare `conda` un `pip` gestore di pacchetti come o per installare o aggiornare i pacchetti.

## <a name="python-linux-edition"></a>Python (edizione Linux)

|    |           |
| ------------- | ------------- |
| Versioni linguistiche supportate | Python 2.7 e 3.5 |
| Edizioni DSVM supportate      | Linux   |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Vengono `conda` creati due ambienti globali: <br /> * `root`l'ambiente `/anaconda/` che si trova in è Python 2.7. <br/> * `py35`l'ambiente `/anaconda/envs/py35`che si trova in è Python 3.5.       |
| Collegamenti agli esempi      | Sono inclusi i notebook Jupyter di esempio per Python.     |
| Strumenti correlati su DSVM      | PySpark, R, Julia      |
### <a name="how-to-use-and-run-it"></a>Come usarlo ed eseguirlo    

* Eseguire in un terminale:

  Aprire il terminale ed effettuare una delle seguenti operazioni, a seconda della versione di Python che si desidera eseguire:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Usare in un IDE:Use in an IDE:

  Utilizzare PyCharm, installato nell'edizione della community di Visual Studio.Use PyCharm, installed in the Visual Studio Community edition. 

* Utilizzare in Jupyter:

  Aprire Jupyter e selezionare **Nuovo** per creare un nuovo blocco appunti. È possibile impostare il tipo di kernel come **Python [Conda Root]** per Python 2.7 e **Python [Conda env:py35]** per l'ambiente Python 3.5. 

* Installare i pacchetti Python:

  Gli ambienti Python predefiniti nella macchina virtuale per data science sono ambienti globali leggibili da tutti gli utenti. Ma solo gli amministratori possono scrivere e installare pacchetti globali. Per installare i pacchetti nell'ambiente globale, attivare nell'ambiente radice o py35 utilizzando il `source activate` comando come amministratore o come utente con autorizzazioni sudo. Quindi, è possibile utilizzare `conda` un `pip` gestore di pacchetti come o per installare o aggiornare i pacchetti.


## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Versioni linguistiche supportate | Microsoft R Open 3.x (100% compatibile con CRAN-R)<br /> Microsoft R Server 9.x Developer edition (piattaforma R scalabile per l'enterprise)|
| Edizioni DSVM supportate      | Linux, Windows     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Collegamenti agli esempi      | Sono inclusi i quaderni Jupyter di esempio per R.     |
| Strumenti correlati su DSVM      | SparkR, Python, Julia      |
### <a name="how-to-use-and-run-it"></a>Come usarlo ed eseguirlo    

**Finestre di windows**:

* Esegui al prompt dei comandi:

  Aprire un prompt dei comandi e digitare `R`.

* Usare in un IDE:Use in an IDE:

  Usare gli strumenti R Tools per Visual Studio (RTVS) installati in Visual Studio Community Edition o RStudio. Questi sono disponibili nel menu Start o come icona del desktop. 

* Uso in Jupyter

  Aprire Jupyter e selezionare **Nuovo** per creare un nuovo blocco appunti. È possibile impostare il tipo di kernel su **R** per utilizzare il kernel Jupyter R (IRKernel).

* Installare i pacchetti R:Install R packages:

  R viene installato nella DSVM in un ambiente globale leggibile da tutti gli utenti. Ma solo gli amministratori possono scrivere e installare pacchetti globali. Per installare i pacchetti nell'ambiente globale, eseguire R usando uno dei metodi precedenti. Quindi, è possibile eseguire `install.packages()` il gestore di pacchetti R per installare o aggiornare i pacchetti.

**Linux**:

* Esegui nel terminale:

  Aprire un terminale ed eseguire `R`.  

* Usare in un IDE:Use in an IDE:

  Utilizzare RStudio, installato in Linux DSVM.  

* Utilizzare in Jupyter:

  Aprire Jupyter e selezionare **Nuovo** per creare un nuovo blocco appunti. È possibile impostare il tipo di kernel su **R** per utilizzare il kernel Jupyter R (IRKernel). 

* Installare i pacchetti R:Install R packages:

  R viene installato nella DSVM in un ambiente globale leggibile da tutti gli utenti. Ma solo gli amministratori possono scrivere e installare pacchetti globali. Per installare i pacchetti nell'ambiente globale, eseguire R usando uno dei metodi precedenti. Quindi, è possibile eseguire `install.packages()` il gestore di pacchetti R per installare o aggiornare i pacchetti.


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Versioni linguistiche supportate | 0,6 |
| Edizioni DSVM supportate      | Linux, Windows     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Windows: installato in `C:\JuliaPro-VERSION`<br /> Linux: installato in `/opt/JuliaPro-VERSION`    |
| Collegamenti agli esempi      | Sono inclusi i quaderni Jupyter di esempio per Julia.     |
| Strumenti correlati su DSVM      | Python, R      |
### <a name="how-to-use-and-run-it"></a>Come usarlo ed eseguirlo    

**Finestre di windows**:

* Esegui al prompt dei comandi

  Aprire un prompt `julia`dei comandi ed eseguire .
* Usare in un IDE:Use in an IDE:

  Utilizzare `Juno` con l'IDE Julia installato nella DSVM e disponibile come collegamento sul desktop.

* Utilizzare in Jupyter:

  Aprire Jupyter e selezionare **Nuovo** per creare un nuovo blocco appunti. È possibile impostare il tipo di kernel come **Versione di Julia**.

* Installare i pacchetti Julia:

  Il percorso predefinito di Julia è un ambiente globale leggibile da tutti gli utenti. Ma solo gli amministratori possono scrivere e installare pacchetti globali. Per installare i pacchetti nell'ambiente globale, eseguire Julia utilizzando uno dei metodi precedenti. È quindi possibile eseguire i `Pkg.add()` comandi di Gestione pacchetti Julia, ad esempio per installare o aggiornare i pacchetti.


**Linux**:
* Eseguire in un terminale:

  Aprire un terminale ed eseguire `julia`.
* Usare in un IDE:Use in an IDE:

  Utilizzare `Juno`, con l'IDE Julia installato nella DSVM e disponibile come collegamento al menu **dell'applicazione.**

* Utilizzare in Jupyter:

  Aprire Jupyter e selezionare **Nuovo** per creare un nuovo blocco appunti. È possibile impostare il tipo di kernel come **Versione di Julia**.

* Installare i pacchetti Julia:

  Il percorso predefinito di Julia è un ambiente globale leggibile da tutti gli utenti. Ma solo gli amministratori possono scrivere e installare pacchetti globali. Per installare i pacchetti nell'ambiente globale, eseguire Julia utilizzando uno dei metodi precedenti. È quindi possibile eseguire i `Pkg.add()` comandi di Gestione pacchetti Julia, ad esempio per installare o aggiornare i pacchetti.

## <a name="other-languages"></a>Altri linguaggi

**C'è:** Disponibile in Windows e accessibile tramite l'edizione della community di Visual Studio o in `Developer Command Prompt for Visual Studio`, dove è possibile eseguire il `csc` comando.

**Java**: OpenJDK è disponibile su entrambe le edizioni Linux e Windows di DSVM ed è impostato sul percorso. Per utilizzare Java, `javac` `java` digitare il comando o al prompt dei comandi in Windows o nella shell bash in Linux.

**Node.js**: Node.js è disponibile nelle edizioni Linux e Windows di DSVM ed è impostato sul percorso. Per accedere a Node.js, digitare il `node` comando o `npm` al prompt dei comandi in Windows o nella shell bash in Linux. In Windows, l'estensione di Visual Studio per gli strumenti Node.js viene installata per fornire un IDE grafico per sviluppare l'applicazione Node.js.On Windows, the Visual Studio extension for the Node.js tools is installed to provide a graphical IDE to develop your Node.js application.

**F :** Disponibile in Windows e accessibile tramite `Developer Command Prompt for Visual Studio`l'edizione della `fsc` community di Visual Studio o in un, dove è possibile eseguire il comando.
