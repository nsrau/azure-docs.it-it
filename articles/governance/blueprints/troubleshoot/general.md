---
title: Risoluzione errori con Azure Blueprint
description: Informazioni su come risolvere i problemi di creazione e assegnazione dei progetti
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dd1163ece225c2e9a9b082f5e8364f34b06a10ae
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982291"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Risoluzione errori con Azure Blueprint

Possono verificarsi errori durante la creazione o l'assegnazione dei progetti. Questo articolo descrive i diversi errori che possono verificarsi e come risolverli.

## <a name="finding-error-details"></a>Ricerca dei dettagli errore

L'assegnazione di un progetto a un ambito comporta una serie di errori. Quando si verifica un errore di assegnazione, il progetto fornisce i dettagli relativi alla distribuzione non riuscita. Le informazioni indicano il problema in modo da poterlo risolvere e garantire la riuscita della successiva distribuzione.

1. Avviare il servizio Azure Blueprint nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri** nel riquadro a sinistra. Nella pagina **Criteri** fare clic su **Progetti**.

1. Selezionare **Progetti assegnati** nella pagina a sinistra e usare la casella di ricerca per filtrare le assegnazioni di progetto in modo da individuare l'assegnazione non riuscita. È inoltre possibile ordinare la tabella delle assegnazioni tramite la colonna **Stato di provisioning** per visualizzare tutte le assegnazioni non riuscite raggruppate.

1. Fare clic con il pulsante sinistro sul progetto con stato _Non riuscito_ oppure fare clic con il pulsante destro e selezionare **Visualizza i dettagli dell'assegnazione**.

1. Nella parte superiore della pagina di assegnazione del progetto viene visualizzato un banner rosso per avvisare che l'assegnazione non è riuscita. Fare clic sul banner per ottenere altri dettagli.

In genere l'errore è dovuto a un artefatto incluso nel progetto e non al progetto stesso. Ad esempio, se il progetto contiene un artefatto per creare un insieme di credenziali delle chiavi, ma tale creazione non è consentita dai Criteri di Azure, l'intera assegnazione avrà esito negativo.

## <a name="general-errors"></a>Errori generali

### <a name="policy-violation"></a>Scenario: violazione dei criteri

#### <a name="issue"></a>Problema

La distribuzione del modello non è riuscita a causa di una violazione dei criteri.

#### <a name="cause"></a>Causa

I criteri possono entrare in conflitto con la distribuzione per diversi motivi:

- La risorsa da creare è limitata da criteri (si tratta in genere di restrizioni relative al codice di riferimento del prodotto o al percorso)
- La distribuzione è l'impostazione di campi configurati dai criteri (comune con i tag)

#### <a name="resolution"></a>Risoluzione

Modificare il progetto per evitare conflitti con i criteri elencati nelle informazioni sull'errore. Nel caso in cui ciò non fosse possibile, l'alternativa consiste nel modificare l'ambito di assegnazione dei criteri in modo che il progetto non entri più in conflitto con i criteri.

## <a name="next-steps"></a>Passaggi successivi

Se il problema che si riscontra non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ricevere assistenza:

- Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
- Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
- Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.