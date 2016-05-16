<properties
   pageTitle="Panoramica: API di riconoscimento della voce dei servizi cognitivi | Microsoft Azure"
   description="Informazioni su come il riconoscimento della voce possa migliorare le applicazioni. Le API di riconoscimento della voce usano algoritmi avanzatiper la verifica e l'identificazione degli utenti."
   services="cognitive-services"
   documentationCenter="na"
   authors="cjgronlund"
   manager="paulettem"
   editor="cjgronlund"/>

<tags
   ms.service="cognitive-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/02/2016"
   ms.author="cgronlun"/>

# Panoramica: API di riconoscimento della voce dei servizi cognitivi di Azure

Informazioni su come le funzionalità di riconoscimento della voce possono migliorare le applicazioni. Le API di riconoscimento della voce sono API basate sul cloud dotate di algoritmi estremamente avanzati per la verifica e l'identificazione della voce. Le funzionalità di riconoscimento della voce possono essere suddivise in due categorie:

  * Verifica voce
  * Identificazione voce

## Verifica voce

La voce presenta caratteristiche peculiari che possono essere usate per identificare una persona, esattamente come un'impronta digitale. L'uso della voce come segnale per gli scenari di autenticazione e controllo di accesso è emerso come strumento innovativo, che offre in pratica un livello di sicurezza in più che semplifica l'esperienza di autenticazione.

Le API Verifica voce possono verificare e autenticare gli utenti automaticamente attraverso la loro voce.

### Registrazione per verifica voce

La registrazione per la verifica voce è dipendente dal testo. In altre parole, gli utenti devono scegliere una passphrase specifica da usare sia nella fase di registrazione che in quella di verifica.

Durante la registrazione, la voce dell'utente viene registrata mentre l'utente pronuncia una frase specifica, quindi viene estratto un certo numero di caratteristiche per il riconoscimento della frase scelta. La combinazione delle caratteristiche estratte e della frase scelta costituisce una firma vocale univoca.

### Verifica della voce e della frase di input

Durante la verifica la voce e la frase di input vengono confrontate con la firma vocale e la frase di registrazione per verificare se provengono dalla stessa persona e se la frase è corretta.

Per altri dettagli sulla verifica voce, fare riferimento alla [parte della documentazione dell'API relativa alla verifica della funzione di riconoscimento della voce](https://dev.projectoxford.ai/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## Identificazione voce

Le API Identificazione voce sono in grado di identificare automaticamente all'interno di un gruppo la persona che parla in un file audio. L'audio di input viene confrontato con il gruppo di voci selezionate e, se viene trovata una corrispondenza, viene restituita l'identità di chi parla.

Tutti gli utenti devono prima passare attraverso un processo di registrazione della propria voce nel sistema, per consentire la creazione di un'impronta vocale.

### Registrazione per identificazione voce

La registrazione per identificazione voce è indipendente dal testo. In altre parole, non esistono restrizioni su ciò che l'utente pronuncia nel file audio. La voce dell'utente viene registrata e viene estratto un certo numero di caratteristiche che formano una firma vocale univoca.

### Riconoscimento della voce: corrispondenza della voce di input

Per il riconoscimento vengono forniti l'audio della voce sconosciuta e il gruppo di voci per il confronto. La voce di input viene confrontata con tutte le voci di confronto per determinare a chi appartiene. Se viene trovata una corrispondenza, viene restituita l'identità dell'utente corrispondente.

Per altri dettagli sull'identificazione voce, fare riferimento alla [parte relativa all'identificazione della documentazione dell'API di riconoscimento della voce](https://dev.projectoxford.ai/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).

<!---HONumber=AcomDC_0504_2016-->