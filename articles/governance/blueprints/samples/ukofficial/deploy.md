---
title: Esempi ufficiali del Regno Unito & UK NHS Blueprint-passaggi di distribuzione
description: Consente di distribuire i passaggi per gli esempi ufficiali del Regno Unito e del piano NHS UK, inclusi i dettagli del parametro dell'artefatto
ms.date: 06/26/2019
ms.topic: conceptual
ms.openlocfilehash: 1905e5fee894575d5ee85ce1bffb536813d34287
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037741"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>Distribuire gli esempi ufficiali del Regno Unito e del piano NHS UK

Per distribuire gli esempi ufficiali del Regno Unito e del piano di NHS UK, è necessario eseguire i passaggi seguenti:

> [!div class="checklist"]
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="create-blueprint-from-sample"></a>Creare il progetto dall'esempio

Prima di tutto, implementare l'esempio di progetto creando un nuovo progetto nell'ambiente basato sull'esempio.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare l'esempio **ufficiale del Regno Unito** o il piano **NHS del Regno Unito** in _altri esempi_ e selezionare **usare questo esempio**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: specificare un nome per la copia dell'esempio di progetto.
   - **Percorso definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione in cui salvare la copia dell'esempio.

1. Selezionare la scheda _elementi_ nella parte superiore della pagina o **Avanti: elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti che costituiscono l'esempio di progetto. Molti artefatti hanno parametri che verranno definiti in seguito. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

## <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia dell'esempio di progetto può essere personalizzata in base all'ambiente e alle esigenze, ma tale modifica potrebbe allontanarsi dallo standard.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella nuova pagina a destra specificare un valore di **Versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. Fornire le **Note sulle modifiche** , ad esempio "prima versione pubblicata dall'esempio ufficiale del Regno Unito o del SSN. Quindi selezionare **Pubblica** nella parte inferiore della pagina.

## <a name="assign-the-sample-copy"></a>Assegnare la copia dell'esempio

Dopo che la copia dell'esempio di progetto è stata **pubblicata** correttamente, è possibile assegnarla a una sottoscrizione all'interno del gruppo di gestione in cui è stata salvata. In questo passaggio vengono specificati i parametri che rendono univoca ogni distribuzione della copia dell'esempio di progetto.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Assegna progetto** nella parte superiore della pagina della definizione di progetto.

1. Specificare i valori dei parametri per l'assegnazione del progetto:

   - Nozioni di base

     - **Sottoscrizioni**: selezionare una o più sottoscrizioni nel gruppo di gestione in cui è stata salvata la copia dell'esempio di progetto. Se si selezionano più sottoscrizioni, viene creata un'assegnazione per ognuna usando i parametri immessi.
     - **Nome assegnazione**: il nome viene prepopolato automaticamente in base al nome del progetto.
       Cambiarlo, se necessario, o lasciarlo inalterato.
     - **Località**: selezionare un'area per l'identità gestita da creare in. Azure Blueprint usa questa identità gestita per distribuire tutti gli elementi nel progetto assegnato. Per altre informazioni, vedere [Managed identities for Azure resources](../../../../active-directory/managed-identities-azure-resources/overview.md) (Identità gestite per risorse di Azure).
     - **Versione definizione progetto**: selezionare una versione **pubblicata** della copia dell'esempio di progetto.

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

Nome dell'artefatto|Tipo di artefatto|Nome parametro|Descrizione|
|-|-|-|-|
|Progetto Initiative per il Regno Unito ufficiale o il NHS UK|Assegnazione di criteri |Tipi di risorse per controllare i log di diagnostica (criteri: progetto Initiative per il Regno Unito ufficiale o UK NHS) |Elenco di tipi di risorse da controllare se l'impostazione del log di diagnostica è nota abilitata.  Per i valori accettabili, vedere [servizi, schemi e categorie supportati per i log di diagnostica di Azure](../../../../azure-monitor/platform/diagnostic-logs-schema.md). |
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux |Assegnazione di criteri |Facoltativo: elenco di immagini di macchina virtuale con sistema operativo Linux supportato da aggiungere all'ambito (criteri: \[anteprima\]: Distribuisci agente di Log Analytics per VM Linux) |Opzionale Il valore predefinito è _None_. Per ulteriori informazioni, vedere [creare un'area di lavoro log Analytics nel portale di Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows |Assegnazione di criteri |Facoltativo: elenco di immagini di macchina virtuale con sistema operativo Windows supportato da aggiungere all'ambito (criteri: \[anteprima\]: Distribuisci agente di Log Analytics per macchine virtuali Windows) |Opzionale Il valore predefinito è _None_. Per ulteriori informazioni, vedere [creare un'area di lavoro log Analytics nel portale di Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |

## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati esaminati i passaggi per distribuire gli esempi ufficiale del Regno Unito e del SSN UK, vedere gli articoli seguenti per informazioni sulla panoramica e sul mapping dei controlli:

> [!div class="nextstepaction"]
> [Cianografia del Regno Unito e](./index.md) dei piani NHS del Regno Unito-Panoramica
> [ufficiale del Regno Unito e il mapping del SSN](./control-mapping.md) del Regno Unito

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
