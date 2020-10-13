---
title: Distribuzione offline
description: La distribuzione offline consente di estrarre le immagini del contenitore da un registro contenitori privato anziché eseguire il pull da Microsoft Container Registry.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 575903654a165bef0d09ac6abf0793af3f6784e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940611"
---
# <a name="offline-deployment-overview"></a>Panoramica della distribuzione offline

In genere le immagini del contenitore usate per la creazione del controller di dati di Azure Arc, delle istanze gestite di SQL e dei gruppi di server di scalabilità di PostgreSQL vengono direttamente estratte da Microsoft Container Registry. In alcuni casi, l'ambiente in cui si esegue la distribuzione non disporrà di connettività a Microsoft Container Registry.  Per le situazioni di questo tipo, è possibile eseguire il pull delle immagini del contenitore usando un computer, che ha accesso al Container Registry Microsoft e quindi contrassegnarle e inserirle in un registro contenitori privato che _è_ collegabile dall'ambiente _in cui si_ vuole distribuire i servizi dati abilitati per Azure Arc.

Poiché gli aggiornamenti mensili vengono forniti per i servizi dati abilitati per Azure Arc e sono disponibili numerose immagini del contenitore, è consigliabile eseguire questo processo di pull, assegnazione di tag e push delle immagini del contenitore in un registro contenitori privato mediante uno script.  Lo script può essere automatizzato o eseguito manualmente.

Uno [script di esempio](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/scripts/pull-and-push-arc-data-services-images-to-private-registry.py) è disponibile nel repository GitHub di Azure Arc.

> [!NOTE]
> Questo script richiede l'installazione di Python e dell'interfaccia della riga di comando di [Docker](https://docs.docker.com/install/).

Lo script richiederà in modo interattivo le informazioni seguenti.  In alternativa, se si desidera eseguire lo script senza prompt interattivi, è possibile impostare le variabili di ambiente corrispondenti prima di eseguire lo script.

|Prompt|Variabile di ambiente|Note|
|---|---|---|
|Fornire il registro contenitori di origine-premere INVIO per usare `mcr.microsoft.com`|SOURCE_DOCKER_REGISTRY|In genere, è possibile eseguire il pull delle immagini dal Container Registry Microsoft, ma se si partecipa a un'anteprima privata con un altro registro, è possibile usare le informazioni fornite come parte del programma di anteprima.|
|Fornire il repository del registro contenitori di origine: premere INVIO per usare `arcdata` :|SOURCE_DOCKER_REPOSITORY|Se si esegue il pull dal Container Registry Microsoft, il repository sarà `arcdata` .|
|Specificare il nome utente per il registro contenitori di origine-premere INVIO per usare nessuno:|SOURCE_DOCKER_USERNAME|Fornire un valore solo se si esegue il pull di immagini del contenitore da un'origine che richiede l'accesso.  Il Container Registry Microsoft non richiede un account di accesso.|
|Fornire la password per il registro contenitori di origine-premere INVIO per usare nessuno:|SOURCE_DOCKER_PASSWORD|Fornire un valore solo se si esegue il pull di immagini del contenitore da un'origine che richiede l'accesso.  Il Container Registry Microsoft non richiede un account di accesso. Si tratta di una richiesta di password nascosta.  La password non verrà visualizzata se digitata o incollata in.|
|Specificare il tag dell'immagine del contenitore per le immagini nell'origine: premere INVIO per l'uso di ' `<current monthly release tag>` ':|SOURCE_DOCKER_TAG|Il nome del tag predefinito verrà aggiornato mensilmente per riflettere il mese e l'anno della versione corrente nel Container Registry Microsoft.|
|Specificare il nome DNS o l'indirizzo IP del registro contenitori di destinazione:|TARGET_DOCKER_REGISTRY|Il nome DNS o l'indirizzo IP del registro di sistema di destinazione.  Si tratta del registro _di sistema in_cui verrà eseguito il push delle immagini.|
|Fornire il repository del registro contenitori di destinazione:|TARGET_DOCKER_REPOSITORY|Repository nel registro di sistema di destinazione in cui eseguire il push delle immagini.|
|Specificare il nome utente per il registro contenitori di destinazione-premere INVIO per usare nessuno:|TARGET_DOCKER_USERNAME|Nome utente, se presente, utilizzato per accedere al registro contenitori di destinazione.|
|Fornire la password per il registro contenitori di destinazione-premere INVIO per usare nessuno:|TARGET_DOCKER_PASSWORD|La password, se presente, usata per accedere al registro contenitori di destinazione. Si tratta di una richiesta di password nascosta.  La password non verrà visualizzata se digitata o incollata in.|
|Specificare il tag dell'immagine del contenitore per le immagini nella destinazione:|TARGET_DOCKER_TAG|In genere, è possibile usare lo stesso tag dell'origine per evitare confusione.|