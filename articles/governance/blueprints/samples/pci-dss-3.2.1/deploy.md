---
title: Esempio di progetto PCI-DSS v 3.2.1-passaggi di distribuzione
description: Consente di distribuire i passaggi per l'esempio di progetto della carta di credito Industry Data Security Standard v 3.2.1, inclusi i dettagli del parametro dell'elemento Blueprint.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: b765782adfa5fd63bc270ad28b74cf3bd1ca1d4a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163042"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>Distribuire l'esempio di progetto PCI-DSS v 3.2.1

Per distribuire l'esempio di progetto di Azure Blueprints PCI-DSS v 3.2.1, è necessario eseguire i passaggi seguenti:

> [!div class="checklist"]
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="create-blueprint-from-sample"></a>Creare il progetto dall'esempio

Prima di tutto, implementare l'esempio di progetto creando un nuovo progetto nell'ambiente basato sull'esempio.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare l'esempio di progetto **PCI-DSS v 3.2.1** in _altri esempi_ e selezionare **Usa questo esempio**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: specificare un nome per la copia dell'esempio di progetto PCI-DSS v 3.2.1.
   - **Percorso definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione in cui salvare la copia dell'esempio.

1. Selezionare la scheda _elementi_ nella parte superiore della pagina o **Avanti: elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti che costituiscono l'esempio di progetto. Molti artefatti hanno parametri che verranno definiti in seguito. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

## <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia dell'esempio di progetto può essere personalizzata in base all'ambiente e alle esigenze, ma tale modifica potrebbe allontanarsi dallo standard PCI-DSS v 3.2.1.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella nuova pagina a destra specificare un valore di **Versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. Fornire **Note sulle modifiche** , ad esempio "prima versione pubblicata dall'esempio di progetto PCI-DSS v 3.2.1". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

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

|Nome dell'artefatto|Tipo di artefatto|Nome parametro|Description|
|-|-|-|-|
|\[Preview\] controllare PCI v 3.2.1:2018 controlla e distribuire estensioni VM specifiche per supportare i requisiti di controllo|Assegnazione dei criteri|Elenco di tipi di risorse | Controlla l'impostazione di diagnostica per i tipi di risorse selezionati. Il valore predefinito è tutte le risorse selezionate| 
|Percorsi consentiti|Assegnazione dei criteri|Elenco di percorsi consentiti|Elenco di percorsi di data center consentiti per la distribuzione di qualsiasi risorsa in. Questo elenco è personalizzabile nelle località di Azure desiderate a livello globale. Selezionare le località che si desidera consentire.| 
|Percorsi consentiti per i gruppi di risorse|Assegnazione dei criteri |Località consentita |Questo criterio consente di limitare le posizioni in cui l'organizzazione può creare gruppi di risorse. Usare per imporre requisiti di conformità geografica.| 
|Distribuisci il controllo nei server SQL|Assegnazione dei criteri|Giorni di conservazione|Conservazione dei dati in numero di giorni. Il valore predefinito è 180, ma PCI richiede 365.| 
|Distribuisci il controllo nei server SQL|Assegnazione dei criteri|Nome del gruppo di risorse per l'account di archiviazione|Il controllo esegue la scrittura degli eventi del database in un log di controllo nell'account di archiviazione di Azure. Verrà creato un account di archiviazione in ogni area in cui viene creato un server SQL che verrà condiviso da tutti i server di tale area.| 

## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati esaminati i passaggi per distribuire l'esempio di progetto PCI-DSS v 3.2.1, vedere gli articoli seguenti per informazioni sulla panoramica e sul mapping dei controlli:

> [!div class="nextstepaction"]
> [PCI-DSS v 3.2.1 Blueprint-panoramica](./index.md)
> [PCI-DSS v 3.2.1 Blueprint-Control mapping](./control-mapping.md)

Articoli aggiuntivi sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
