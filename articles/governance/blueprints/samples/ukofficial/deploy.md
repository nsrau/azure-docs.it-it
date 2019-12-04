---
title: Distribuire gli esempi di progetti UK OFFICIAL e UK NHS
description: Procedura di distribuzione degli esempi di progetti UK OFFICIAL e UK NHS, inclusi i dettagli dei parametri degli artefatti dei progetti.
ms.date: 06/26/2019
ms.topic: sample
ms.openlocfilehash: 3d69f4477163618aa225150ac6352a16e901f826
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546388"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>Distribuire gli esempi di progetti UK OFFICIAL e UK NHS

Per distribuire gli esempi UK OFFICIAL e UK NHS, è necessario seguire questa procedura:

> [!div class="checklist"]
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="create-blueprint-from-sample"></a>Creare il progetto dall'esempio

Prima di tutto, implementare l'esempio di progetto creando un nuovo progetto nell'ambiente basato sull'esempio.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare l'esempio di progetto **UK OFFICIAL** o **UK NHS** in _Altri esempi_ e selezionare **Usa questo esempio**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: specificare un nome per la copia dell'esempio di progetto.
   - **Posizione della definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione in cui salvare la copia dell'esempio.

1. Selezionare la scheda _Artefatti_ nella parte superiore della pagina oppure **Avanti: Elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti che costituiscono l'esempio di progetto. Molti artefatti hanno parametri che verranno definiti in seguito. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

## <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia dell'esempio di progetto può essere personalizzata in base all'ambiente e alle esigenze, ma le modifiche potrebbero renderla non conforme allo standard.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella nuova pagina a destra specificare un valore di **Versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. In **Modifica le note** specificare ad esempio "Prima versione pubblicata dell'esempio di progetto UK OFFICIAL o UK NHS". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

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

Nome dell'artefatto|Tipo di artefatto|Nome parametro|DESCRIZIONE|
|-|-|-|-|
|Iniziativa progetto per UK OFFICIAL o UK NHS|Assegnazione di criteri |Tipi di risorsa per il controllo dei log di diagnostica (criteri: iniziativa progetto per UK OFFICIAL o UK NHS) |Elenco dei tipi di risorse per il controllo, se l'impostazione dei log di diagnostica non è abilitata.  Per informazioni sui valori accettabili, vedere [Servizi, schemi e categorie supportati per i log di diagnostica di Azure](../../../../azure-monitor/platform/diagnostic-logs-schema.md) |
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux |Assegnazione di criteri |Facoltativo: elenco delle immagini delle macchine virtuali che supportano il sistema operativo Linux da aggiungere all'ambito (Criteri: \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux) |(Facoltativo) Il valore predefinito è _nessuno_. Per altre informazioni, vedere [Creare un'area di lavoro Log Analytics nel portale di Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows |Assegnazione di criteri |Facoltativo: elenco delle immagini delle macchine virtuali che supportano il sistema operativo Windows da aggiungere all'ambito (Criteri: \[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows) |(Facoltativo) Il valore predefinito è _nessuno_. Per altre informazioni, vedere [Creare un'area di lavoro Log Analytics nel portale di Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato la procedura di distribuzione degli esempi di progetti UK OFFICIAL e UK NHS, vedere gli articoli seguenti per informazioni generali e sul mapping dei controlli:

> [!div class="nextstepaction"]
> [Progetti UK OFFICIAL e UK NHS - Panoramica](./index.md)
> [Progetti UK OFFICIAL e UK NHS - Mapping dei controlli](./control-mapping.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).