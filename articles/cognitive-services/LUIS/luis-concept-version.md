---
title: Controllo delle versioni-LUIS
titleSuffix: Azure Cognitive Services
description: Le versioni in LUIS sono simili alle versioni nella programmazione tradizionale. Ogni versione è uno snapshot temporizzato dell'app. Prima di apportare modifiche all'app, creare una nuova versione. È facile tornare all'esatta app e quindi tentare di riportare la finalità e le espressioni dell'app a uno stato precedente.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: c519b030aaee58397766ecb8658e7af08b5986e1
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256882"
---
# <a name="understand-how-and-when-to-use-a-luis-version"></a>Comprendere come e quando usare una versione di LUIS

Le versioni in LUIS sono simili alle versioni nella programmazione tradizionale. Ogni versione è uno snapshot temporizzato dell'app. Prima di apportare modifiche all'app, creare una nuova versione. È più semplice fare ritorno alla versione originale che tentare di ripristinare lo stato precedente delle finalità e delle espressioni.

Creare modelli diversi della stessa app con le [versioni](luis-how-to-manage-versions.md). 

## <a name="version-id"></a>ID versione
L'ID versione è composto da caratteri, cifre o '.' e non può superare i 10 caratteri.

## <a name="initial-version"></a>Versione iniziale
La versione iniziale (0.1) è la versione attiva predefinita. 

## <a name="active-version"></a>Versione attiva
Una [versione](luis-how-to-manage-versions.md#set-active-version) impostata come attiva viene modificata e testata nel sito Web [LUIS](luis-reference-regions.md). Impostare una versione come attiva per accedere ai relativi dati, eseguire aggiornamenti, sottoporla a test e pubblicarla.

Il nome della versione attualmente attiva viene visualizzato nel riquadro in alto a sinistra dopo il nome dell'app. 

[![Modifica versione attiva](./media/luis-concept-version/version-in-nav-bar-inline.png)](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Versioni e slot di pubblicazione
La pubblicazione avviene in slot di produzione e di staging. Ogni slot può presentare una versione diversa o la stessa versione. È utile per verificare le modifiche tra versioni modello tramite l'endpoint, disponibile ai bot o ad altre applicazioni di chiamata LUIS. 

## <a name="clone-a-version"></a>Clonare una versione
Clonare una versione per creare una copia di una versione esistente e salvarla come nuova versione. Clonare una versione per usare lo stesso contenuto della versione esistente come punto di partenza per una nuova versione. Dopo aver clonato una versione, la nuova versione diventa la versione **attiva**. 

## <a name="import-and-export-a-version"></a>Importare ed esportare una versione
È possibile importare una versione a livello di app. Quella versione diventa la versione attiva con l'ID indicato nella proprietà "versionId" del file dell'app. È anche possibile importare a livello di versione in un'app esistente. La nuova versione diventa quella attiva. 

È possibile esportare una versione a livello di app o a livello di versione. L'unica differenza è che la versione esportata a livello di app è la versione attualmente attiva, mentre a livello di versione è possibile scegliere qualsiasi versione da esportare nella pagina **[Settings](luis-how-to-manage-versions.md)** (Impostazioni). 

Il file esportato non contiene informazioni apprese automaticamente perché l'app viene nuovamente sottoposta a training dopo l'importazione. Il file esportato non contiene informazioni sul collaboratore.

## <a name="export-each-version-as-app-backup"></a>Esportare ogni versione come backup dell'app
Per eseguire il backup dell'app LUIS, esportare ogni versione nella pagina **[Settings](luis-how-to-manage-versions.md)** (Impostazioni).

## <a name="delete-a-version"></a>Eliminare una versione
È possibile eliminare tutte le versioni ad eccezione di quella attiva dall'elenco Versions (Versioni) nella pagina Settings (Impostazioni). 

## <a name="version-availability-at-the-endpoint"></a>Disponibilità delle versioni in corrispondenza dell'endpoint
Le versioni sottoposte a training non sono automaticamente disponibili in corrispondenza dell'[endpoint](luis-glossary.md#endpoint) dell'app. È necessario [pubblicare](luis-how-to-publish-app.md) o ripubblicare una versione affinché sia disponibile in corrispondenza dell'endpoint dell'app. È possibile pubblicare in **Staging** e **Produzione** e ottenere due versioni dell'app disponibili in corrispondenza dell'endpoint. Se è necessario che a un endpoint siano disponibili più versioni dell'app, esportare la versione e reimportarla in una nuova app. La nuova app presenta un ID app diverso.

## <a name="manage-multiple-versions-inside-the-same-app"></a>Gestione di più versioni all'interno della stessa app
Eseguire innanzitutto la [clonazione](luis-how-to-manage-versions.md#clone-a-version) da una versione base per ogni autore. 

Ogni autore apporta modifiche alla propria versione dell'app. Dopo che ogni autore è soddisfatto del proprio modello, esportare le nuove versioni in file JSON.  

Le app esportate sono file in formato JSON confrontabili a livello di modifiche. Combinare i file per creare un singolo file JSON della nuova versione. Modificare la proprietà **versionId** nel file JSON affinché indichi la nuova versione unita. Importare la versione nell'app originale. 

Questo metodo consente di disporre di una versione attiva, di una versione di staging e di una versione pubblicata. È possibile confrontare i risultati della versione attiva con una versione pubblicata (fase o produzione) nel [riquadro test interattivo](luis-interactive-test.md).

## <a name="manage-multiple-versions-as-apps"></a>Gestione di più versioni come app
[Esportare](luis-how-to-manage-versions.md#export-version) la versione base. Ogni autore importa la versione. L'utente che importa l'app è il proprietario della versione. Dopo aver modificato l'app, esportare la versione. 

Le app esportate sono file in formato JSON confrontabili con l'esportazione base a livello di modifiche. Combinare i file per creare un singolo file JSON della nuova versione. Modificare la proprietà **versionId** nel file JSON affinché indichi la nuova versione unita. Importare la versione nell'app originale.

## <a name="contributions-from-collaborators"></a>Contributi da collaboratori

Altre informazioni sulla creazione di contributi da [collaboratori](luis-how-to-collaborate.md).

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sul [controllo versioni](luis-how-to-manage-versions.md) nella pagina impostazioni dell'app. 

Informazioni su come progettare [finalità](luis-concept-intent.md) nel modello.
