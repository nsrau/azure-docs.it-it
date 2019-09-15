---
title: Creare elementi personalizzati per la macchina virtuale di DevTest Labs | Microsoft Docs
description: Informazioni su come creare elementi personalizzati da usare con Azure DevTest Labs.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: 69b83590fb9b25c68d231b732b985ba633bb6884
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399207"
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

| Nome dell'elemento | Obbligatorio? | Descrizione |
| --- | --- | --- |
| $schema |No |Posizione del file di schema JSON. Il file di schema JSON aiuta a testare la validità del file di definizione. |
| title |Yes |Nome dell'elemento visualizzato nel lab. |
| description |Yes |Descrizione dell'elemento visualizzato nel lab. |
| iconUri |No |URI dell'icona visualizzata nel lab. |
| targetOsType |Yes |Sistema operativo della macchina virtuale in cui è installato l'elemento. Le opzioni supportate sono Windows e Linux. |
| parameters |No |Valori forniti quando viene eseguito il comando di installazione dell'elemento in un computer. Questi valori consentono di personalizzare l'elemento. |
| runCommand |Yes |Il comando di installazione dell’elemento che viene eseguito in una macchina virtuale. |

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

| Nome dell'elemento | Obbligatorio? | Descrizione |
| --- | --- | --- |
| type |Yes |Tipo di valore del parametro. Vedere l'elenco seguente per informazioni sui tipi consentiti. |
| displayName |Yes |Nome del parametro che viene visualizzato a un utente nel lab. |
| description |Yes |Descrizione del parametro che viene visualizzato nel lab. |

I tipi consentiti sono:

* string (tutte le stringhe JSON valide)
* int (tutti i valori integer JSON validi)
* bool (tutti i valori booleani JSON validi)
* array (tutte le matrici JSON valide)

## <a name="secrets-as-secure-strings"></a>Segreti come le stringhe sicure
Dichiarare i segreti come le stringhe sicure. Di seguito è riportata la sintassi per la dichiarazione di un parametro di stringa sicura all'interno di `parameters` sezione del **artifactfile. JSON** file:

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

Per l'artefatto di comando di installazione, eseguire lo script di PowerShell che accetta la stringa sicura creata usando il comando ConvertTo-SecureString. 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

Per l'esempio completo di artifactfile. JSON e artifact.ps1 (script di PowerShell), vedere [in questo esempio su GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

Un altro punto importante da tenere presente è non eseguire l'accesso dei segreti nella console perché l'output viene acquisito per il debug di utente. 

## <a name="artifact-expressions-and-functions"></a>Espressioni e funzioni dell’elemento
È possibile usare espressioni e funzioni per costruire il comando di installazione dell'elemento.
Le espressioni sono racchiuse tra parentesi quadre ([ e ]) e vengono valutate al momento dell’installazione dell’elemento. Le espressioni possono trovarsi in qualsiasi punto in un valore stringa JSON. Le espressioni restituiscono sempre un altro valore JSON. Se è necessario usare una stringa letterale che inizia con una parentesi quadra ([), usare due parentesi quadre ([[).
In genere, si usano espressioni con funzioni per costruire un valore. Proprio come in JavaScript, le chiamate di funzione sono formattate come **functionName(arg1, arg2, arg3)** .

L'elenco seguente riporta le funzioni comuni:

* **parameters(parameterName)** : restituisce un valore di parametro fornito quando viene eseguito il comando dell'elemento.
* **concat(arg1, arg2, arg3,….. )** : combina più valori stringa. Questa funzione può accettare diversi argomenti.

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
4. Archiviare l'artefatto nel [repository Git pubblico per DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) o nel [repository Git personale](devtest-lab-add-artifact-repo.md). Nel repository pubblico, è possibile visualizzare gli elementi condivisi da altri utenti che è possibile usare direttamente o personalizzare in base alle esigenze.
   
   1. Creare una directory distinta per ogni elemento. Il nome della directory deve corrispondere a quello dell'elemento.
   2. Archiviare il file di definizione dell'elemento (artifactfile.json) nella directory creata.
   3. Archiviare gli script a cui viene fatto riferimento tramite il comando di installazione dell'elemento.
      
      Di seguito è riportato un esempio di come potrebbe apparire una cartella di elementi:
      
      ![Esempio di cartella di elementi](./media/devtest-lab-artifact-author/git-repo.png)
5. Se si usa il repository personale per archiviare gli artefatti, aggiungere il repository al lab seguendo la procedura descritta nell'articolo: [Aggiungere un repository Git per elementi e modelli](devtest-lab-add-artifact-repo.md).

## <a name="related-articles"></a>Articoli correlati
* [Come diagnosticare gli errori di elemento in DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Join a VM to an existing Active Directory domain by using a Resource Manager template in DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs) (Aggiungere una macchina virtuale a un dominio di Active Directory esistente usando un modello di Resource Manager in DevTest Labs)

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [Aggiungere un archivio elementi Git a un lab](devtest-lab-add-artifact-repo.md).
