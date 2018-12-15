---
title: Controllo delle versioni
titleSuffix: Language Understanding - Azure Cognitive Services
description: Informazioni sull'uso delle versioni per gestire le modifiche in LUIS (Language Understanding)
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 3dae1256e93bb15777225ada742539c5a0ebdccc
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015569"
---
# <a name="versions"></a>Versioni
Creare modelli diversi della stessa app con le [versioni](luis-how-to-manage-versions.md). 

## <a name="version-id"></a>ID versione
L'ID versione è composto da caratteri, cifre o '.' e non può superare i 10 caratteri.

## <a name="initial-version"></a>Versione iniziale
La versione iniziale (0.1) è la versione attiva predefinita. 

## <a name="active-version"></a>Versione attiva
Una [versione](luis-how-to-manage-versions.md#set-active-version) impostata come attiva viene modificata e testata nel sito Web [LUIS](luis-reference-regions.md). Impostare una versione come attiva per accedere ai relativi dati, eseguire aggiornamenti, sottoporla a test e pubblicarla.

Il nome della versione attualmente attiva viene visualizzato nel riquadro in alto a sinistra dopo il nome dell'app. 

[ ![Modificare la versione attiva](./media/luis-concept-version/version-in-nav-bar-inline.png) ](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Versioni e slot di pubblicazione
La pubblicazione avviene in slot di produzione e di staging. Ogni slot può presentare una versione diversa o la stessa versione. È utile per verificare le modifiche tra versioni modello tramite l'endpoint, disponibile ai bot o ad altre applicazioni di chiamata LUIS. 

## <a name="clone-a-version"></a>Clonare una versione
Clonare una versione per creare una copia di una versione esistente e salvarla come nuova versione. Clonare una versione per usare lo stesso contenuto della versione esistente come punto di partenza per una nuova versione. Dopo aver clonato una versione, la nuova versione diventa la versione **attiva**. 

## <a name="import-and-export-a-version"></a>Importare ed esportare una versione
È possibile importare una versione a livello di app. Quella versione diventa la versione attiva con l'ID indicato nella proprietà "versionId" del file dell'app. È anche possibile importare a livello di versione in un'app esistente. La nuova versione diventa quella attiva. 

È possibile esportare una versione a livello di app o a livello di versione. L'unica differenza è che la versione esportata a livello di app è la versione attualmente attiva, mentre a livello di versione è possibile scegliere qualsiasi versione da esportare nella pagina **[Settings](luis-how-to-manage-versions.md)** (Impostazioni). 

Il file esportato non contiene informazioni apprese automaticamente perché l'app viene nuovamente sottoposta a training dopo l'importazione. Il file esportato non contiene collaboratori. Sarà necessario riaggiungerli dopo aver importato la versione nella nuova app.

## <a name="export-each-version-as-app-backup"></a>Esportare ogni versione come backup dell'app
Per eseguire il backup dell'app LUIS, esportare ogni versione nella pagina **[Settings](luis-how-to-manage-versions.md)** (Impostazioni).

## <a name="delete-a-version"></a>Eliminare una versione
È possibile eliminare tutte le versioni ad eccezione di quella attiva dall'elenco Versions (Versioni) nella pagina Settings (Impostazioni). 

## <a name="version-availability-at-the-endpoint"></a>Disponibilità delle versioni in corrispondenza dell'endpoint
Le versioni sottoposte a training non sono automaticamente disponibili in corrispondenza dell'[endpoint](luis-glossary.md#endpoint) dell'app. È necessario [pubblicare](luis-how-to-publish-app.md) o ripubblicare una versione affinché sia disponibile in corrispondenza dell'endpoint dell'app. È possibile pubblicare in **Staging** e **Produzione** e ottenere due versioni dell'app disponibili in corrispondenza dell'endpoint. Se è necessario che a un endpoint siano disponibili più versioni dell'app, esportare la versione e reimportarla in una nuova app. La nuova app presenta un ID app diverso.

## <a name="collaborators"></a>Collaboratori
Il proprietario e tutti i [collaboratori](luis-how-to-collaborate.md) dispongono dell'accesso completo a tutte le versioni dell'app.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sul [controllo versioni](luis-how-to-manage-versions.md) nella pagina impostazioni dell'app. 

Informazioni su come progettare [finalità](luis-concept-intent.md) nel modello.