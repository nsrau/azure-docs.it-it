---
title: Creare elementi personalizzati per la macchina virtuale di DevTest Labs | Microsoft Docs
description: Informazioni su come creare elementi personalizzati da usare con Azure DevTest Labs.
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: v-craic
ms.openlocfilehash: 7766227d66df94eca72072f52ff02928f8ee277b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Creare elementi personalizzati per la macchina virtuale di DevTest Labs

Guardare il video seguente per una panoramica dei passaggi descritti in questo articolo:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
> 
> 

## <a name="overview"></a>Panoramica
È possibile usare gli *elementi* per distribuire e configurare l'applicazione dopo il provisioning di una macchina virtuale. Un elemento è costituito da un file di definizione dell'elemento e da altri file di script archiviati in una cartella in un repository Git. I file di definizione dell’elemento sono costituiti da JSON ed espressioni che è possibile utilizzare per specificare gli elementi da installare in una macchina virtuale. È ad esempio possibile definire il nome di un elemento, il comando da eseguire e i parametri disponibili quando si esegue il comando. È possibile fare riferimento ad altri file di script all'interno del file di definizione dell'elemento in base al nome.

## <a name="artifact-definition-file-format"></a>Formato del file di definizione dell’elemento
L'esempio seguente illustra le sezioni che compongono la struttura di base di un file di definizione:

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| Nome dell'elemento | Obbligatorio? | DESCRIZIONE |
| --- | --- | --- |
| $schema |No  |Posizione del file di schema JSON. Il file di schema JSON aiuta a testare la validità del file di definizione. |
| title |Sì |Nome dell'elemento visualizzato nel lab. |
| description |Sì |Descrizione dell'elemento visualizzato nel lab. |
| iconUri |No  |URI dell'icona visualizzata nel lab. |
| targetOsType |Sì |Sistema operativo della macchina virtuale in cui è installato l'elemento. Le opzioni supportate sono Windows e Linux. |
| Parametri |No  |Valori forniti quando viene eseguito il comando di installazione dell'elemento in un computer. Questi valori consentono di personalizzare l'elemento. |
| runCommand |Sì |Il comando di installazione dell’elemento che viene eseguito in una macchina virtuale. |

### <a name="artifact-parameters"></a>Parametri dell'elemento
Nella sezione dei parametri del file di definizione è possibile specificare i valori che un utente può immettere quando installa un elemento. È possibile fare riferimento a questi valori nel comando di installazione dell'elemento.

Per definire i parametri, usare la struttura seguente:

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| Nome dell'elemento | Obbligatorio? | DESCRIZIONE |
| --- | --- | --- |
| type |Sì |Tipo di valore del parametro. Vedere l'elenco seguente per informazioni sui tipi consentiti. |
| displayName |Sì |Nome del parametro che viene visualizzato a un utente nel lab. | |
| description |Sì |Descrizione del parametro che viene visualizzato nel lab. |

I tipi consentiti sono:

* string (tutte le stringhe JSON valide)
* int (tutti i valori integer JSON validi)
* bool (tutti i valori booleani JSON validi)
* array (tutte le matrici JSON valide)

## <a name="artifact-expressions-and-functions"></a>Espressioni e funzioni dell’elemento
È possibile usare espressioni e funzioni per costruire il comando di installazione dell'elemento.
Le espressioni sono racchiuse tra parentesi quadre ([ e ]) e vengono valutate al momento dell’installazione dell’elemento. Le espressioni possono trovarsi in qualsiasi punto in un valore stringa JSON. Le espressioni restituiscono sempre un altro valore JSON. Se è necessario usare una stringa letterale che inizia con una parentesi quadra ([), usare due parentesi quadre ([[).
In genere, si usano espressioni con funzioni per costruire un valore. Proprio come in JavaScript, le chiamate di funzione sono formattate come **functionName(arg1,arg2,arg3)**.

L'elenco seguente riporta le funzioni comuni:

* **parameters(parameterName)**: restituisce un valore di parametro fornito quando viene eseguito il comando dell'elemento.
* **concat(arg1,arg2,arg3, …..)**: combina più valori stringa. Questa funzione può accettare diversi argomenti.

L'esempio seguente mostra come usare espressioni e funzioni per costruire un valore:

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Creare un elemento personalizzato

1. Installare un editor JSON. È necessario un editor JSON per lavorare con i file di definizione dell'elemento. È consigliabile usare [Visual Studio Code](https://code.visualstudio.com/) disponibile per Windows, Linux e OS X.
2. Ottenere un file di definizione artifactfile.json di esempio. Esaminare gli elementi creati dal team di DevTest Labs nel [repository GitHub](https://github.com/Azure/azure-devtestlab). È stata creata una libreria completa di elementi utili per creare i propri elementi. Scaricare un file di definizione dell’elemento e apportare modifiche al codice per creare i propri elementi.
3. Usare IntelliSense. Usare IntelliSense per vedere gli elementi validi che è possibile usare per costruire un file di definizione dell'elemento. È anche possibile vedere le diverse opzioni per i valori di un elemento. Quando, ad esempio, si modifica l'elemento **targetOsType**, IntelliSense mostra due opzioni, per Windows o Linux.
4. Archiviare l'elemento in un [repository Git](devtest-lab-add-artifact-repo.md).
   
   1. Creare una directory distinta per ogni elemento. Il nome della directory deve corrispondere a quello dell'elemento.
   2. Archiviare il file di definizione dell'elemento (artifactfile.json) nella directory creata.
   3. Archiviare gli script a cui viene fatto riferimento tramite il comando di installazione dell'elemento.
      
      Di seguito è riportato un esempio di come potrebbe apparire una cartella di elementi:
      
      ![Esempio di cartella di elementi](./media/devtest-lab-artifact-author/git-repo.png)
5. Aggiungere il repository di elementi al lab. Vedere [Aggiungere un repository Git per elementi e modelli](devtest-lab-add-artifact-repo.md).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-articles"></a>Articoli correlati
* [Come diagnosticare gli errori di elemento in DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Join a VM to an existing Active Directory domain by using a Resource Manager template in DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs) (Aggiungere una macchina virtuale a un dominio di Active Directory esistente usando un modello di Resource Manager in DevTest Labs)

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [Aggiungere un archivio elementi Git a un lab](devtest-lab-add-artifact-repo.md).

