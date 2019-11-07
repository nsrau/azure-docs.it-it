---
title: Informazioni sull'API Riconoscimento del parlante
titleSuffix: Azure Cognitive Services
description: Verifica e identificazione della voce con l'API Riconoscimento del parlante di Servizi cognitivi.
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f354793e3779f4d9f7be9bae8a21545a15ed1c4c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464908"
---
# <a name="speaker-recognition-api---preview"></a>API Riconoscimento del parlante (anteprima)

Le API Riconoscimento del parlante sono API basate sul cloud che forniscono gli algoritmi di intelligenza artificiale per la verifica e l'identificazione della voce. Il servizio Riconoscimento del parlante è diviso in due categorie, ossia verifica voce e identificazione voce.

## <a name="speaker-verification"></a>Verifica voce

La voce ha caratteristiche univoche che possono essere associate a un singolo individuo.  Le applicazioni possono usare la voce come fattore aggiuntivo per la verifica, in scenari come call center e servizi Web.

Le API Verifica voce sono uno strumento intelligente per verificare gli utenti in base alla relativa voce e a passphrase vocali.

### <a name="enrollment"></a>Registrazione

La registrazione per la verifica voce dipende dal testo. Ciò significa che l'utente deve scegliere una passphrase specifica da usare durante le fasi di registrazione e verifica.

Nella fase di registrazione viene registrata la voce del parlante che pronuncia una specifica frase. Le caratteristiche vocali vengono estratte per formare una firma vocale univoca mentre viene riconosciuta la frase scelta. Nell'insieme, questi dati di registrazione vengono usati per verificare il parlante. I dati di registrazione dei parlanti vengono archiviati in un sistema protetto. Il cliente controlla per quanto tempo devono essere conservati. I clienti possono creare, aggiornare e rimuovere i dati di registrazione per i singoli parlanti tramite chiamate API.  Quando la sottoscrizione viene eliminata, verranno eliminati anche tutti i dati di registrazione dei parlanti associati.

I clienti dovranno verificare di aver ricevuto le autorizzazioni appropriate dagli utenti per la verifica della voce.

### <a name="verification"></a>Verifica

Nella fase di verifica, il cliente deve chiamare l'API di verifica voce con l'ID associato all'individuo da verificare.  Il servizio estrae le caratteristiche vocali e la passphrase dalla registrazione vocale di input. Quindi confronta le caratteristiche con gli elementi corrispondenti dei dati di registrazione del parlante che il cliente vuole verificare e determina eventuali corrispondenze.  La risposta restituisce "accept" o "reject" con diversi livelli di attendibilità.  Il cliente determina quindi come usare i risultati per decidere se la persona è il parlante registrato.

Il livello di soglia di attendibilità deve essere impostato in base allo scenario e ad altri fattori di verifica in uso. È consigliabile sperimentare con il livello di attendibilità e valutare l'impostazione appropriata per ogni applicazione. Lo scopo delle API non è di determinare se l'audio proviene da una persona reale, da un'imitazione o da una registrazione di un parlante registrato.

Il servizio non conserva la registrazione vocale o le caratteristiche vocali estratte inviate durante la fase di verifica.

Per altri dettagli sulla verifica voce, fare riferimento all'API [Verifica voce](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Identificazione voce

Le applicazioni possono usare la voce per identificare "chi sta parlando" in un gruppo di parlanti registrati. Le API Identificazione voce possono essere usate in scenari come produttività delle riunioni, personalizzazione e trascrizione di chiamate di call center.

### <a name="enrollment"></a>Registrazione

La registrazione per l'identificazione voce non dipende dal testo. Ciò significa che non ci sono limitazioni relative a quello che l'utente dice nell'audio. Non è richiesta alcuna passphrase.

Nella fase di registrazione viene registrata la voce del parlante e vengono estratte le caratteristiche vocali per formare una firma vocale univoca. L'audio e le caratteristiche vocali estratti vengono archiviati in un sistema protetto. Il cliente controlla per quanto tempo devono essere conservati. I clienti possono creare, aggiornare e rimuovere i dati di registrazione per i singoli parlanti tramite chiamate API. Quando la sottoscrizione viene eliminata, verranno eliminati anche tutti i dati di registrazione dei parlanti associati.

I clienti dovranno verificare di aver ricevuto le autorizzazioni appropriate dagli utenti per l'identificazione della voce.

### <a name="identification"></a>Identificazione

Nella fase di identificazione il servizio di identificazione voce estrae caratteristiche vocali dalla registrazione vocale di input. Quindi confronta le caratteristiche con i dati di registrazione dell'elenco di parlanti specificato. Quando viene trovata una corrispondenza con un parlante registrato, la risposta restituisce l'ID del parlante con un livello di attendibilità.  In caso contrario, se nessun parlante corrisponde a un parlante registrato, la risposta restituisce "reject".

Il livello di soglia di attendibilità deve essere impostato in base allo scenario. È consigliabile sperimentare con il livello di attendibilità e valutare l'impostazione appropriata per ogni applicazione. Lo scopo delle API non è di determinare se l'audio proviene da una persona reale, da un'imitazione o da una registrazione di un parlante registrato.

Il servizio non conserva la registrazione vocale o le caratteristiche vocali estratte inviate durante la fase di identificazione.

Per altri dettagli sull'identificazione voce, fare riferimento all'API  [Identificazione voce](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).
