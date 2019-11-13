---
title: Risoluzione dei problemi comuni
description: Informazioni su come risolvere i problemi di creazione, assegnazione e rimozione dei progetti.
ms.date: 12/11/2018
ms.topic: troubleshooting
ms.openlocfilehash: b6f1d6c40f7268e90f09457e680a3ef33996c341
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960298"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Risoluzione errori con Azure Blueprint

È possibile riscontrare errori durante la creazione o l'assegnazione dei progetti. Questo articolo descrive i diversi errori che possono verificarsi e come risolverli.

## <a name="finding-error-details"></a>Ricerca dei dettagli di errore

L'assegnazione di un progetto a un ambito comporta una serie di errori. Quando si verifica un errore di assegnazione, il progetto fornisce i dettagli relativi alla distribuzione non riuscita. Le informazioni indicano il problema in modo da poterlo risolvere e garantire la riuscita della distribuzione successiva.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare **progetti assegnati** dalla pagina a sinistra e usare la casella di ricerca per filtrare le assegnazioni di progetto per trovare l'assegnazione non riuscita. È inoltre possibile ordinare la tabella delle assegnazioni tramite la colonna **Stato di provisioning** per visualizzare tutte le assegnazioni non riuscite raggruppate.

1. Fare clic con il pulsante destro del mouse sul progetto con lo stato _non riuscito_ oppure fare clic con il pulsante destro del mouse e selezionare **Visualizza Dettagli assegnazione**.

1. Nella parte superiore della pagina di assegnazione del progetto viene visualizzato un banner rosso che avvisa dell'esito negativo dell'assegnazione. Fare clic sul banner per ottenere altri dettagli.

In genere l'errore è dovuto a un artefatto e non al progetto stesso. Se un artefatto crea un insieme di credenziali delle chiavi e Criteri di Azure impedisce la creazione di tale insieme, l'intera assegnazione ha esito negativo.

## <a name="general-errors"></a>Errori generali

### <a name="policy-violation"></a>Scenario: violazione dei criteri

#### <a name="issue"></a>Problema

La distribuzione del modello non è riuscita a causa di una violazione dei criteri.

#### <a name="cause"></a>Causa

I criteri possono entrare in conflitto con la distribuzione per diversi motivi:

- La risorsa da creare è limitata da criteri (si tratta in genere di restrizioni relative al codice di riferimento del prodotto o al percorso)
- La distribuzione è l'impostazione di campi configurati dai criteri (comune con i tag)

#### <a name="resolution"></a>Risoluzione

Modificare il progetto in modo che non entri in conflitto con i criteri nei dettagli dell'errore. Nel caso in cui la modifica non sia possibile, l'alternativa consiste nel modificare l'ambito di assegnazione dei criteri in modo che il progetto non entri più in conflitto con i criteri.

### <a name="escape-function-parameter"></a>Scenario: il parametro Blueprint è una funzione

#### <a name="issue"></a>Problema

I parametri del progetto che sono funzioni vengono elaborati prima di essere trasmessi agli artefatti.

#### <a name="cause"></a>Causa

Il passaggio di un parametro del progetto che usa una funzione, ad esempio `[resourceGroup().tags.myTag]`, a un artefatto comporta che il risultato dell'elaborazione della funzione sia impostato sull'artefatto invece che sulla funzione dinamica.

#### <a name="resolution"></a>Risoluzione

Per passare a una funzione come parametro, l'intera stringa con carattere di escape `[` tale che il parametro del progetto è simile a `[[resourceGroup().tags.myTag]`. Il carattere di escape fa sì che i progetti trattino il valore come una stringa durante l'elaborazione di progetto. I progetti inseriscono quindi la funzione sull'artefatto in modo che possa essere dinamica come previsto. Per altre informazioni, vedere [sintassi ed espressioni nei modelli Azure Resource Manager](../../../azure-resource-manager/template-expressions.md).

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

- Ottieni risposte dagli esperti di Azure tramite i [Forum di Azure](https://azure.microsoft.com/support/forums/).
- Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
- Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.