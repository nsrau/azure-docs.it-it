---
title: Risoluzione dei problemi comuni
description: Informazioni su come risolvere i problemi relativi alla creazione, all'assegnazione e alla rimozione di blueprint, ad esempio violazioni dei criteri e funzioni dei parametri del blueprint.
ms.date: 01/15/2020
ms.topic: troubleshooting
ms.openlocfilehash: 7306e344a479008a87164a954c4444d375950b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157084"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Risoluzione errori con Azure Blueprint

È possibile che si verifichino errori durante la creazione, l'assegnazione o la rimozione di blueprint. Questo articolo descrive i diversi errori che possono verificarsi e come risolverli.

## <a name="finding-error-details"></a>Ricerca dei dettagli di errore

L'assegnazione di un progetto a un ambito comporta una serie di errori. Quando si verifica un errore di assegnazione, il progetto fornisce i dettagli relativi alla distribuzione non riuscita. Le informazioni indicano il problema in modo da poterlo risolvere e garantire la riuscita della distribuzione successiva.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare **Blueprint assegnati** dalla pagina a sinistra e utilizzare la casella di ricerca per filtrare le assegnazioni dei blueprint e trovare l'assegnazione non riuscita. È inoltre possibile ordinare la tabella delle assegnazioni tramite la colonna **Stato di provisioning** per visualizzare tutte le assegnazioni non riuscite raggruppate.

1. Fare clic con il pulsante sinistro del mouse sul blueprint con lo stato _Non riuscito_ oppure fare clic con il pulsante destro del mouse e scegliere Visualizza **dettagli assegnazione**.

1. Nella parte superiore della pagina di assegnazione del progetto viene visualizzato un banner rosso che avvisa dell'esito negativo dell'assegnazione. Fare clic sul banner per ottenere altri dettagli.

In genere l'errore è dovuto a un artefatto e non al progetto stesso. Se un artefatto crea un insieme di credenziali delle chiavi e Criteri di Azure impedisce la creazione di tale insieme, l'intera assegnazione ha esito negativo.

## <a name="general-errors"></a>Errori generali

### <a name="scenario-policy-violation"></a><a name="policy-violation"></a>Scenario: violazione dei criteri

#### <a name="issue"></a>Problema

La distribuzione del modello non è riuscita a causa di una violazione dei criteri.

#### <a name="cause"></a>Causa

I criteri possono entrare in conflitto con la distribuzione per diversi motivi:

- La risorsa da creare è limitata da criteri (si tratta in genere di restrizioni relative al codice di riferimento del prodotto o al percorso)
- La distribuzione è l'impostazione di campi configurati dai criteri (comune con i tag)

#### <a name="resolution"></a>Risoluzione

Modificare il progetto in modo che non entri in conflitto con i criteri nei dettagli dell'errore. Nel caso in cui la modifica non sia possibile, l'alternativa consiste nel modificare l'ambito di assegnazione dei criteri in modo che il progetto non entri più in conflitto con i criteri.

### <a name="scenario-blueprint-parameter-is-a-function"></a><a name="escape-function-parameter"></a>Scenario: il parametro Blueprint è una funzione

#### <a name="issue"></a>Problema

I parametri del progetto che sono funzioni vengono elaborati prima di essere trasmessi agli artefatti.

#### <a name="cause"></a>Causa

Il passaggio di un parametro del progetto che usa una funzione, ad esempio `[resourceGroup().tags.myTag]`, a un artefatto comporta che il risultato dell'elaborazione della funzione sia impostato sull'artefatto invece che sulla funzione dinamica.

#### <a name="resolution"></a>Risoluzione

Per passare a una funzione come parametro, l'intera stringa con carattere di escape `[` tale che il parametro del progetto è simile a `[[resourceGroup().tags.myTag]`. Il carattere di escape fa sì che i progetti trattino il valore come una stringa durante l'elaborazione di progetto. I progetti inseriscono quindi la funzione sull'artefatto in modo che possa essere dinamica come previsto. Per altre informazioni, vedere [Sintassi ed espressioni nei modelli](../../../azure-resource-manager/templates/template-expressions.md)di Azure Resource Manager.

## <a name="delete-errors"></a>Eliminare gli errori

### <a name="scenario-assignment-deletion-timeout"></a><a name="assign-delete-timeout"></a>Scenario: timeout eliminazione assegnazioneScenario: Assignment deletion timeout

#### <a name="issue"></a>Problema

L'eliminazione di un'assegnazione di blueprint non viene completata.

#### <a name="cause"></a>Causa

Un'assegnazione del blueprint potrebbe rimanere bloccata in uno stato non terminale quando viene eliminata. Questo stato si verifica quando le risorse create dall'assegnazione del blueprint sono ancora in attesa di eliminazione o non restituiscono un codice di stato a Azure Blueprints.This state is caused when resources created by the blueprint assignment are still pending deletion or don't return a status code to Azure Blueprints.

#### <a name="resolution"></a>Risoluzione

Le assegnazioni del blueprint in uno stato non terminale vengono contrassegnate automaticamente come **non riuscite** dopo un timeout _di 6 ore._ Una volta che il timeout ha regolato lo stato dell'assegnazione del blueprint, l'eliminazione può essere ritentata.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

- Ottieni risposte dagli esperti di Azure tramite i forum di [Azure.](https://azure.microsoft.com/support/forums/)
- Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.
- Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.