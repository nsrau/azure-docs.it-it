---
title: Esempio di progetto Federal PBMM Canada-passaggi di distribuzione
description: Distribuire i passaggi per l'esempio Canada Federal PBMM Blueprint, inclusi i dettagli del parametro dell'artefatto del progetto.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/05/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: e2c061b69771214a770b3b42626d28672bd9fe77
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72297819"
---
# <a name="deploy-the-canada-federal-pbmm-blueprint-samples"></a>Distribuire gli esempi di progetto Federal PBMM per il Canada

Per distribuire gli esempi di progetto Federal PBMM per il Canada, è necessario eseguire i passaggi seguenti:

> [!div class="checklist"]
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="create-blueprint-from-sample"></a>Creare il progetto dall'esempio

Prima di tutto, implementare l'esempio di progetto creando un nuovo progetto nell'ambiente basato sull'esempio.

1. Selezionare **Tutti i servizi**, quindi cercare e selezionare **Criteri** nel riquadro sinistro. Nella pagina **Criteri** selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare l'esempio **Canada Federal PBMM** Blueprint in _altri esempi_ e selezionare **use this sample**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: specificare un nome per la copia dell'esempio di progetto.
   - **Posizione della definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione in cui salvare la copia dell'esempio.

1. Selezionare la scheda _Artefatti_ nella parte superiore della pagina oppure **Avanti: Elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti che costituiscono l'esempio di progetto. Molti artefatti hanno parametri che verranno definiti in seguito. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

## <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia dell'esempio di progetto può essere personalizzata in base all'ambiente e alle esigenze, ma tale modifica potrebbe allontanarsi dallo standard.

1. Selezionare **Tutti i servizi**, quindi cercare e selezionare **Criteri** nel riquadro sinistro. Nella pagina **Criteri** selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella nuova pagina a destra specificare un valore di **Versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. Fornire **Note sulle modifiche** , ad esempio "prima versione pubblicata dall'esempio di progetto Federal PBMM per il Canada". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

## <a name="assign-the-sample-copy"></a>Assegnare la copia dell'esempio

Dopo che la copia dell'esempio di progetto è stata **pubblicata** correttamente, è possibile assegnarla a una sottoscrizione all'interno del gruppo di gestione in cui è stata salvata. In questo passaggio vengono specificati i parametri che rendono univoca ogni distribuzione della copia dell'esempio di progetto.

1. Selezionare **Tutti i servizi**, quindi cercare e selezionare **Criteri** nel riquadro sinistro. Nella pagina **Criteri** selezionare **Progetti**.

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

Nome dell'artefatto|Tipo di artefatto|Nome parametro|Descrizione|
|-|-|-|-|
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux |Assegnazione di criteri |Area di lavoro Log Analytics per le macchine virtuali Linux |Per ulteriori informazioni, vedere [creare un'area di lavoro log Analytics nel portale di Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux |Assegnazione di criteri |Facoltativo: elenco delle immagini delle macchine virtuali che supportano il sistema operativo Linux da aggiungere all'ambito |È possibile utilizzare una matrice vuota per indicare che non sono presenti parametri facoltativi: `[]` |
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows |Assegnazione di criteri |Facoltativo: elenco delle immagini delle macchine virtuali che supportano il sistema operativo Windows da aggiungere all'ambito |È possibile utilizzare una matrice vuota per indicare che non sono presenti parametri facoltativi: `[]` |
|\[Anteprima\]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows |Assegnazione di criteri |Area di lavoro Log Analytics per le macchine virtuali Windows |Per ulteriori informazioni, vedere [creare un'area di lavoro log Analytics nel portale di Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Anteprima\]: Controllare i controlli PBMM federali del Canada e distribuire estensioni VM specifiche per supportare i requisiti di controllo |Assegnazione di criteri |ID dell'area di lavoro di Log Analytics per cui le macchine virtuali devono essere configurate |Si tratta dell'ID (GUID) dell'area di lavoro di Log Analytics per cui le macchine virtuali devono essere configurate. |
|\[Anteprima\]: Controllare i controlli PBMM federali del Canada e distribuire estensioni VM specifiche per supportare i requisiti di controllo |Assegnazione di criteri |Elenco dei tipi di risorsa per cui è necessario abilitare i log di diagnostica |Elenco di tipi di risorse da controllare se l'impostazione del log di diagnostica non è abilitata. I valori accettabili sono disponibili negli [schemi dei log di diagnostica di Monitoraggio di Azure](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type). |
|\[Anteprima\]: Controllare i controlli PBMM federali del Canada e distribuire estensioni VM specifiche per supportare i requisiti di controllo |Assegnazione di criteri |Gruppo Administrators |Gruppo. Esempio: `Administrator; myUser1; myUser2` |
|\[Anteprima\]: Controllare i controlli PBMM federali del Canada e distribuire estensioni VM specifiche per supportare i requisiti di controllo |Assegnazione di criteri |Elenco di utenti che devono essere inclusi nel gruppo Administrators della macchina virtuale Windows |Elenco delimitato da punto e virgola con i membri da includere nel gruppo locale Administrators. Esempio: `Administrator; myUser1; myUser2` |
|Distribuisci Advanced Threat Protection negli account di archiviazione |Assegnazione di criteri |Effetto |Per informazioni sugli effetti dei criteri, vedere [comprendere gli effetti dei criteri di Azure](../../../policy/concepts/effects.md). |
|Distribuisci il controllo nei server SQL |Assegnazione di criteri |Valore in giorni del periodo di conservazione (0 indica conservazione illimitata) |Giorni di conservazione (facoltativo, _180_ giorni se non specificato) |
|Distribuisci il controllo nei server SQL |Assegnazione di criteri |Nome del gruppo di risorse per l'account di archiviazione per il controllo del server SQL |Il controllo scrive gli eventi del database in un log di controllo nell'account di archiviazione di Azure. viene creato un account di archiviazione in ogni area in cui viene creata una SQL Server condivisa da tutti i server in tale area. Importante: per un corretto funzionamento del controllo, non eliminare o rinominare il gruppo di risorse o gli account di archiviazione. |
|Distribuire le impostazioni di diagnostica per i gruppi di sicurezza di rete |Assegnazione di criteri |Prefisso dell'account di archiviazione per la diagnostica del gruppo di sicurezza di rete |Questo prefisso viene combinato con il percorso del gruppo di sicurezza di rete per formare il nome dell'account di archiviazione creato. |
|Distribuire le impostazioni di diagnostica per i gruppi di sicurezza di rete |Assegnazione di criteri |Nome del gruppo di risorse per l'account di archiviazione per la diagnostica del gruppo di sicurezza di rete (deve esistere) |Gruppo di risorse in cui viene creato l'account di archiviazione. Questo gruppo di risorse deve già esistere. |

## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati esaminati i passaggi per distribuire l'esempio Canada Federal PBMM, vedere gli articoli seguenti per informazioni sulla panoramica e sul mapping dei controlli:

> [!div class="nextstepaction"]
> [Blueprints Federal PBMM per il Canada-panoramica](./index.md)
> [Canada Federal PBMM Blueprints-controllo mapping](./control-mapping.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).