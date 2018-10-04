---
title: Che cos’è il Riconoscimento del parlante?
titlesuffix: Azure Cognitive Services
description: Usare gli algoritmi avanzati per la verifica e l'identificazione della voce con l'API di Riconoscimento del parlante.
services: cognitive-services
author: dwlin
manager: cgronlun
ms.service: cognitive-services
ms.component: speaker-recognition
ms.topic: overview
ms.date: 03/20/2016
ms.author: dwlin
ms.openlocfilehash: 13a95aff8b2b0d5dad0574e6107958a20576702a
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227334"
---
# <a name="speaker-recognition-api"></a>API Riconoscimento voce

Introduzione alle API Riconoscimento del parlante. Le API di riconoscimento della voce sono API basate sul cloud dotate di algoritmi estremamente avanzati per la verifica e l'identificazione della voce. Le funzionalità di riconoscimento del parlante possono essere suddivise in due categorie: verifica e identificazione della voce.


## <a name="speaker-verification"></a>Verifica voce


La voce presenta caratteristiche peculiari che possono essere usate per identificare una persona, esattamente come un'impronta digitale.  L'uso della voce come segnale per gli scenari di autenticazione e controllo di accesso è emerso come strumento innovativo, che offre in pratica un livello di sicurezza in più che semplifica l'esperienza di autenticazione.

Le API Verifica voce possono verificare e autenticare gli utenti automaticamente attraverso la loro voce.

### <a name="enrollment"></a>Registrazione

La registrazione per la verifica voce dipende dal testo. Ciò significa che l'utente deve scegliere una passphrase specifica da usare durante le fasi di registrazione e verifica. 

Durante la registrazione, la voce dell'utente viene registrata mentre l'utente pronuncia una frase specifica, quindi viene estratto un certo numero di caratteristiche per il riconoscimento della frase scelta. La combinazione delle caratteristiche estratte e della frase scelta costituisce una firma vocale univoca.

### <a name="verification"></a>Verifica
###
Durante la verifica, la voce e la frase di input vengono confrontate con la firma vocale e la frase di registrazione per verificare se provengono dalla stessa persona e se la frase è corretta.

Per altri dettagli sulla verifica voce, fare riferimento all'API [Verifica voce](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Identificazione voce

Le API Identificazione voce sono in grado di identificare automaticamente all'interno di un gruppo la persona che parla in un file audio. L'audio di input viene confrontato con il gruppo di voci selezionate e, se viene trovata una corrispondenza, viene restituita l'identità di chi parla.

Tutti gli utenti devono prima passare attraverso un processo di registrazione della propria voce nel sistema, per consentire la creazione di un'impronta vocale.


### <a name="enrollment"></a>Registrazione

La registrazione per l'identificazione voce non dipende dal testo. Ciò significa che non ci sono limitazioni relative a quello che l'utente dice nell'audio. La voce dell'utente viene registrata e vengono estratte alcune caratteristiche per creare una firma vocale univoca. 


### <a name="recognition"></a>Riconoscimento

Durante il riconoscimento viene fornito l'audio dell'utente sconosciuto insieme al potenziale gruppo di utenti. La voce di input viene confrontata con tutti gli utenti per stabilire a chi appartiene. Se viene trovata una corrispondenza, viene restituita l'identità di chi parla.


Per altri dettagli sull'identificazione voce, fare riferimento all'API [Identificazione voce](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).
