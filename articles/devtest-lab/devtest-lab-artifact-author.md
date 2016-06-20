<properties 
	pageTitle="Creare elementi personalizzati per le macchine virtuali di lab di sviluppo/test | Microsoft Azure"
	description="Informazioni su come creare i propri elementi per l'uso nei Lab di sviluppo/test"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/01/2016"
	ms.author="tarcher"/>

#Creare elementi personalizzati per le macchine virtuali di lab di sviluppo e test

> [AZURE.NOTE] Visualizzare il video associato a questo articolo, relativo alla [procedura di creazione di elementi personalizzati](/documentation/videos/how-to-author-custom-artifacts)

## Panoramica
Gli **Elementi** vengono utilizzati per distribuire e configurare l'applicazione dopo il provisioning di una macchina virtuale. Un elemento è costituito da un file di definizione dell’elemento e altri file di script archiviati in una cartella in un archivio git. I file di definizione dell’elemento sono costituiti da JSON ed espressioni che è possibile utilizzare per specificare gli elementi da installare in una macchina virtuale. Ad esempio, è possibile definire il nome dell’elemento, il comando da eseguire e i parametri che vengono resi disponibili quando si esegue il comando. È possibile fare riferimento ad altri file di script all'interno del file di definizione dell'elemento in base al nome.

##Formato del file di definizione dell’elemento
L'esempio seguente illustra le sezioni che compongono la struttura di base di un file di definizione.

	{
	  "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2015-01-01/dtlArtifacts.json",
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

| Nome dell'elemento | Obbligatorio? | Descrizione
| ------------ | --------- | -----------
| $schema | No | Percorso del file di schema JSON che aiuta a testare la validità del file di definizione.
| title | Sì | Nome dell'elemento visualizzato nel lab.
| description | Sì | Descrizione dell'elemento visualizzato nel lab.
| iconUri | No | URI dell'icona visualizzato nel lab.
| targetOsType | Sì | Sistema operativo della macchina virtuale in cui verrà installato l'elemento. Opzioni supportate: Windows e Linux.
| parameters | No | Valori forniti quando viene eseguito il comando di installazione dell’elemento in un computer. Ciò consente di personalizzare l'elemento.
| runCommand | Sì | Il comando di installazione dell’elemento che viene eseguito in una macchina virtuale.

###Parametri dell'elemento

Nella sezione dei parametri del file di definizione è possibile specificare i valori che un utente può immettere durante l’installazione di un elemento. È possibile fare riferimento a questi valori nel comando di installazione dell'elemento.

I parametri vengono definiti con la struttura seguente:

	"parameters": {
	    "<parameterName>": {
	      "type": "<type-of-parameter-value>",
	      "displayName": "<display-name-of-parameter>",
	      "description": "<description-of-parameter>"
	    }
	  }

| Nome dell'elemento | Obbligatorio? | Descrizione
| ------------ | --------- | -----------
| type | Sì | Tipo di valore del parametro. Vedere di seguito l'elenco dei tipi consentiti:
| displayName Sì | Nome del parametro che viene visualizzato a un utente nel lab.
| description | Sì | Descrizione del parametro che viene visualizzato nel lab.

I tipi consentiti sono:

- string: tutte le stringhe JSON valide
- int: tutti i valori integer JSON validi
- bool: tutti i valori booleani JSON validi
- array: tutte le matrici JSON valide

##Espressioni e funzioni dell’elemento

È possibile utilizzare espressioni e funzioni per la creazione del comando di installazione dell'elemento. Le espressioni sono racchiuse tra parentesi quadre ([ e ]) e vengono valutate al momento dell’installazione dell’elemento. Le espressioni possono trovarsi in qualsiasi punto in un valore stringa JSON e restituiscono sempre un altro valore JSON. Se è necessario usare una stringa letterale che inizia con una parentesi quadra [, usare due parentesi quadre [[. In genere, si usano espressioni con funzioni per costruire un valore. Proprio come in JavaScript, le chiamate di funzione sono formattate come functionName(arg1,arg2,arg3).

Nell'elenco seguente vengono riportate le funzioni comuni.

- parameters(parameterName) - restituisce un valore di parametro fornito quando viene eseguito il comando dell'elemento.
- concat(arg1,arg2,arg3, …..) - Combina più valori di stringa. Questa funzione può accettare qualsiasi numero di argomenti.

Nell'esempio seguente viene illustrato come utilizzare espressioni e funzioni per costruire un valore.

	runCommand": {
	     "commandToExecute": "[concat('powershell.exe -File startChocolatey.ps1'
	, ' -RawPackagesList ', parameters('packages')
	, ' -Username ', parameters('installUsername')
	, ' -Password ', parameters('installPassword'))]"
	}

##Creare un elemento personalizzato

Creare l'elemento personalizzato eseguendo i passaggi seguenti:

1. Installare un editor JSON: è necessario un editor JSON per lavorare con i file di definizione dell'elemento. Si consiglia di utilizzare [Visual Studio Code](https://code.visualstudio.com/), che è disponibile per Windows, Linux e OS X.

1. Ottenere un esempio di artifactfile.json: Controllare gli elementi creati dal team di Lab di sviluppo e test di Azure nell’ [archivio GitHub](https://github.com/Azure/azure-devtestlab) in cui è stata creata una libreria completa degli elementi che aiuta nella creazione dei propri elementi. Scaricare un file di definizione dell’elemento e apportare modifiche al codice per creare i propri elementi.

1. Utilizzo di IntelliSense - utilizzare IntelliSense per visualizzare elementi validi che possono essere utilizzati per costruire un file di definizione dell'elemento. È inoltre possibile visualizzare le diverse opzioni per i valori di un elemento. Ad esempio, IntelliSense mostra le due opzioni di Windows o Linux quando si modifica l’elemento **targetOsType**.

1. Archiviare l'elemento in un archivio git
	1. Creare una directory distinta per ogni elemento in cui il nome della directory è identico al nome dell’elemento.
	1. Archiviare il file di definizione dell’elemento (artifactfile.json) nella directory che è stato creata.
	1. Archiviare gli script a cui viene fatto riferimento tramite il comando di installazione dell'elemento.

	Di seguito è riportato un esempio di come potrebbe apparire una cartella di elementi:

	![Esempio di archivio git dell’elemento](./media/devtest-lab-artifact-author/git-repo.png)

1. Aggiungere l'archivio di elementi al lab, vedere l'articolo sull'[aggiunta di un archivio elementi Git a un lab](devtest-lab-add-artifact-repo.md).

## Passaggi successivi

- Informazioni su come [aggiungere un archivio elementi Git a un lab](devtest-lab-add-artifact-repo.md).

<!---HONumber=AcomDC_0608_2016-->