---
title: Visualizza e gestisce i sensori caricati in Azure Defender per l'it
description: Questo articolo descrive come visualizzare ed eliminare i sensori caricati, oltre a scaricare i nuovi file di attivazione per i sensori caricati in Azure Defender per l'it.
author: rkarlin
ms.author: rkarlin
ms.date: 10/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 6aec19d413e1730bc1599f6cbac1c62e9b304ecd
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094406"
---
# <a name="view-and-manage-onboarded-sensors"></a>Visualizzare e gestire i sensori caricati

Questo articolo descrive come visualizzare ed eliminare i sensori caricati, oltre a scaricare i nuovi file di attivazione per i sensori caricati.

## <a name="update-sensor-management-mode"></a>Aggiornare la modalità di gestione dei sensori

Potrebbe essere necessario aggiornare la modalità di gestione del sensore. Quando si esegue questa operazione, è necessario rimuovere il sensore corrente dalla pagina di gestione dei sensori e caricare un nuovo file di attivazione.

- **Usare la modalità gestita del cloud invece della modalità gestita localmente**: aggiornare il file di attivazione per il sensore gestito localmente con un file di attivazione per un sensore gestito dal cloud. Dopo la riattivazione, i rilevamenti dei sensori vengono visualizzati sia nel sensore che in Azure Defender per il portale. Dopo che il file di riattivazione è stato caricato correttamente, le informazioni di avviso appena rilevate vengono inviate ad Azure.

- **Lavorare in modalità gestita localmente anziché in modalità gestita dal cloud**: aggiornare il file di attivazione per un sensore gestito dal cloud con un file di attivazione per un sensore gestito localmente. Dopo la riattivazione, le informazioni di rilevamento del sensore vengono visualizzate solo nel sensore.

- **Associare il sensore a un nuovo hub**Internet: è possibile associare il sensore a un nuovo hub Internet. A tale scopo, ripetere la registrazione del sensore e caricare un nuovo file di attivazione.

**Per riattivare il sensore:**

1. Passare alla pagina Azure Defender per l'it, **Gestione sensori** .

2. Selezionare il sensore per il quale si vuole caricare un nuovo file di attivazione.

3. Eliminarla.

4. Caricare nuovamente il sensore dalla pagina **onboarding** nella nuova modalità o con un nuovo hub Internet.

5. Scaricare il file di attivazione dalla pagina **Scarica file di attivazione** .

6. Accedere alla console di Azure Defender per il sensore Internet.

7. Nella console del sensore selezionare **impostazioni di sistema** , quindi selezionare **riattivazione**.

   ![Screenshot della visualizzazione di riattivazione](media/updates/image14.png)

8. Selezionare **carica** e selezionare il file salvato.

9. Selezionare **Attiva**.
 
## <a name="delete-sensors"></a>Elimina sensori

Se si elimina un sensore gestito dal cloud, le informazioni non verranno inviate all'hub Internet delle cose.

Eliminare i sensori gestiti localmente quando non vengono più elaborati.

**Per eliminare i sensori:**

1. Passare alla pagina di **gestione dei sensori** di Azure Defender per l'it.

2. Selezionare i sensori che si desidera eliminare.

3. Selezionare **Elimina sensore**.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle opzioni di configurazione, passare alla Guida alle procedure per la configurazione del modulo.
> [!div class="nextstepaction"]
> [Guida alle procedure di configurazione del modulo](./how-to-agent-configuration.md)
