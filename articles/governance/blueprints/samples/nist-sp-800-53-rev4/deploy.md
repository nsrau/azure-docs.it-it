---
title: Esempio - Progetto NIST SP 800-53 R4 - Passaggi per la distribuzione
description: Passaggi per la distribuzione dell'esempio di progetto NIST SP 800-53 R4.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c1a928076b3900739886b93d1105c6294821bf66
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802368"
---
# <a name="deploy-the-nist-sp-800-53-r4-blueprint-sample"></a>Distribuire l'esempio di progetto NIST SP 800-53 R4

Per distribuire l'esempio di progetto Azure Blueprints NIST SP 800-53 R4, seguire questa procedura:

> [!div class="checklist"]
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="create-blueprint-from-sample"></a>Creare il progetto dall'esempio

Prima di tutto, implementare l'esempio di progetto creando un nuovo progetto nell'ambiente basato sull'esempio.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare il progetto **Network Information Service SP 800-53 R4** in _Altri esempi_ e selezionare **Usa questo esempio**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: specificare un nome per la copia dell'esempio di progetto NIST SP 800-53 R4.
   - **Posizione della definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione in cui salvare la copia dell'esempio.

1. Selezionare la scheda _Artefatti_ nella parte superiore della pagina oppure **Avanti: Elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti che costituiscono l'esempio di progetto. Molti artefatti hanno parametri che verranno definiti in seguito. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

## <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia dell'esempio di progetto può essere personalizzata in base all'ambiente e alle esigenze, ma le modifiche potrebbero renderla non allineata ai controlli NIST SP 800-53.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella nuova pagina a destra specificare un valore di **Versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. In **Modifica le note** specificare ad esempio "Prima versione pubblicata dall'esempio di progetto NIST SP 800-53 R4". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

## <a name="assign-the-sample-copy"></a>Assegnare la copia dell'esempio

Dopo che la copia dell'esempio di progetto è stata **pubblicata** correttamente, è possibile assegnarla a una sottoscrizione all'interno del gruppo di gestione in cui è stata salvata. In questo passaggio vengono specificati i parametri che rendono univoca ogni distribuzione della copia dell'esempio di progetto.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Assegna progetto** nella parte superiore della pagina della definizione di progetto.

1. Specificare i valori dei parametri per l'assegnazione del progetto:

   - Nozioni di base

     - **Sottoscrizioni**: selezionare una o più sottoscrizioni presenti nel gruppo di gestione in cui è stata salvata la copia dell'esempio di progetto. Se si selezionano più sottoscrizioni, viene creata un'assegnazione per ciascuna usando i parametri immessi.
     - **Nome dell'assegnazione**: il nome viene prepopolato in base al nome del progetto.
       Cambiarlo, se necessario, o lasciarlo inalterato.
     - **Località**: selezionare un'area in cui creare l'identità gestita. Azure Blueprint usa questa identità gestita per distribuire tutti gli elementi nel progetto assegnato. Per altre informazioni, vedere [Managed identities for Azure resources](../../../../active-directory/managed-identities-azure-resources/overview.md) (Identità gestite per risorse di Azure).
     - **Versione della definizione di progetto**: Scegliere una versione **pubblicata** della copia dell'esempio di progetto.

   - Blocca assegnazione

     Selezionare l'impostazione di blocco del progetto per l'ambiente. Per altre informazioni, vedere [Blueprints resource locking](../../concepts/resource-locking.md) (Blocco delle risorse del progetto).

   - Identità gestita

     Lasciare invariata l'opzione predefinita _Identità gestita assegnata dal sistema_.

   - Parametri dell'elemento

     I parametri definiti in questa sezione si applicano all'artefatto in cui sono definiti. Si tratta di [parametri dinamici](../../concepts/parameters.md#dynamic-parameters), in quanto vengono definiti durante l'assegnazione del progetto. Per un elenco completo di parametri di artefatti e relative descrizioni, vedere la [tabella di parametri degli artefatti](#artifact-parameters-table).

1. Dopo avere immesso tutti i parametri, selezionare **Assegna** in fondo alla pagina. Viene creata l'assegnazione del progetto e inizia la distribuzione degli artefatti. La distribuzione richiede circa un'ora. Per controllare lo stato della distribuzione, aprire l'assegnazione del progetto.

> [!WARNING]
> Il servizio Azure Blueprints e gli esempi di progetti predefiniti sono **gratuiti**. Le risorse di Azure hanno un [prezzo in base al prodotto](https://azure.microsoft.com/pricing/). Usare la [calcolatrice dei prezzi](https://azure.microsoft.com/pricing/calculator/) per stimare il costo dell'esecuzione delle risorse distribuite da questo esempio di progetto.

## <a name="artifact-parameters-table"></a>Tabella di parametri degli artefatti

La tabella seguente contiene un elenco dei parametri degli artefatti del progetto:

|Nome dell'artefatto|Tipo di artefatto|Nome parametro|DESCRIZIONE|
|-|-|-|-|
|\[Anteprima\]: Verificare i controlli NIST SP 800-53 R4 e distribuire estensioni macchina virtuale specifiche per supportare i requisiti di controllo|Assegnazione di criteri|ID dell'area di lavoro di Log Analytics per cui le macchine virtuali devono essere configurate|Si tratta dell'ID (GUID) dell'area di lavoro di Log Analytics per cui le macchine virtuali devono essere configurate.|
|\[Anteprima\]: Verificare i controlli NIST SP 800-53 R4 e distribuire estensioni macchina virtuale specifiche per supportare i requisiti di controllo|Assegnazione di criteri|Elenco dei tipi di risorsa per cui è necessario abilitare i log di diagnostica|Elenco dei tipi di risorse per il controllo, se l'impostazione dei log di diagnostica non è abilitata. I valori accettabili sono disponibili negli [schemi dei log di diagnostica di Monitoraggio di Azure](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|\[Anteprima\]: Verificare i controlli NIST SP 800-53 R4 e distribuire estensioni macchina virtuale specifiche per supportare i requisiti di controllo|Assegnazione di criteri|Elenco di utenti che devono essere esclusi dal gruppo Administrators della macchina virtuale Windows|Elenco delimitato da punto e virgola con i membri da escludere dal gruppo locale Administrators, ad esempio Administrator; utente1; utente2|
|\[Anteprima\]: Verificare i controlli NIST SP 800-53 R4 e distribuire estensioni macchina virtuale specifiche per supportare i requisiti di controllo|Assegnazione di criteri|Elenco di utenti che devono essere inclusi nel gruppo Administrators della macchina virtuale Windows|Elenco delimitato da punto e virgola con i membri da includere nel gruppo locale Administrators. ad esempio Administrator; utente1; utente2|
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Linux|Assegnazione di criteri|Area di lavoro Log Analytics per i set di scalabilità di macchine virtuali Linux|Se quest'area di lavoro non rientra nell'ambito dell'assegnazione, è necessario concedere manualmente le autorizzazioni di "Collaboratore di Log Analytics" (o simile) all'ID entità di sicurezza dell'assegnazione dei criteri.|
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Linux|Assegnazione di criteri|Facoltativo: elenco delle immagini delle macchine virtuali che supportano il sistema operativo Linux da aggiungere all'ambito|Una matrice vuota può essere usata per indicare l'assenza di parametri facoltativi: \[\]|
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux|Assegnazione di criteri|Area di lavoro Log Analytics per VM Linux|Se quest'area di lavoro non rientra nell'ambito dell'assegnazione, è necessario concedere manualmente le autorizzazioni di "Collaboratore di Log Analytics" (o simile) all'ID entità di sicurezza dell'assegnazione dei criteri.|
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux|Assegnazione di criteri|Facoltativo: elenco delle immagini delle macchine virtuali che supportano il sistema operativo Linux da aggiungere all'ambito|Una matrice vuota può essere usata per indicare l'assenza di parametri facoltativi: \[\]|
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Windows|Assegnazione di criteri|Area di lavoro Log Analytics per i set di scalabilità di macchine virtuali|Se quest'area di lavoro non rientra nell'ambito dell'assegnazione, è necessario concedere manualmente le autorizzazioni di "Collaboratore di Log Analytics" (o simile) all'ID entità di sicurezza dell'assegnazione dei criteri.|
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Windows|Assegnazione di criteri|Facoltativo: elenco delle immagini delle macchine virtuali che supportano il sistema operativo Windows da aggiungere all'ambito|Una matrice vuota può essere usata per indicare l'assenza di parametri facoltativi: \[\]|
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows|Assegnazione di criteri|Area di lavoro Log Analytics per VM Windows|Se quest'area di lavoro non rientra nell'ambito dell'assegnazione, è necessario concedere manualmente le autorizzazioni di "Collaboratore di Log Analytics" (o simile) all'ID entità di sicurezza dell'assegnazione dei criteri.|
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows|Assegnazione di criteri|Facoltativo: elenco delle immagini delle macchine virtuali che supportano il sistema operativo Windows da aggiungere all'ambito|Una matrice vuota può essere usata per indicare l'assenza di parametri facoltativi: \[\]|
|Distribuisci Advanced Threat Protection negli account di archiviazione|Assegnazione di criteri|Effetto|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md)|
|Distribuisci il controllo nei server SQL|Assegnazione di criteri|Valore in giorni del periodo di conservazione (0 indica conservazione illimitata)|Giorni di conservazione (facoltativo, 180 giorni se non è specificato alcun valore)|
|Distribuisci il controllo nei server SQL|Assegnazione di criteri|Nome gruppo di risorse per l'account di archiviazione per il controllo di SQL Server|Il controllo esegue la scrittura degli eventi del database in un log di controllo nell'account di archiviazione di Azure. Verrà creato un account di archiviazione in ogni area in cui viene creato un server SQL che verrà condiviso da tutti i server di tale area. Importante: per il corretto funzionamento del controllo, non eliminare o rinominare il gruppo di risorse o gli account di archiviazione.|
|Distribuire le impostazioni di diagnostica per i gruppi di sicurezza di rete|Assegnazione di criteri|Prefisso dell'account di archiviazione per la diagnostica del gruppo di sicurezza di rete|Questo prefisso verrà combinato con la posizione del gruppo di sicurezza di rete per formare il nome dell'account di archiviazione creato.|
|Distribuire le impostazioni di diagnostica per i gruppi di sicurezza di rete|Assegnazione di criteri|Nome del gruppo di risorse per l'account di archiviazione per la diagnostica del gruppo di sicurezza di rete (deve esistere)|Il gruppo di risorse in cui verrà creato l'account di archiviazione. Questo gruppo di risorse deve già esistere.|

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato i passaggi per distribuire il progetto di esempio NIST SP 800-53 R4, vedere gli articoli seguenti per informazioni sul progetto e sul mapping dei controlli:

> [!div class="nextstepaction"]
> [Progetto NIST SP 800-53 R4 - Panoramica](./index.md)
> [Progetto NIST SP 800-53 R4 - Mapping dei controlli](./control-mapping.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
