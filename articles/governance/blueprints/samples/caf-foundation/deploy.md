---
title: Esempi - Progetto per le proprietà di base di Cloud Adoption Framework - Procedura di distribuzione
description: Procedura di distribuzione del progetto di esempio per le proprietà di base di Cloud Adoption Framework.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/20/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.custom: fasttrack-new
ms.openlocfilehash: 98ade4e5c8a14c2d4f5c2a22c2c973fac844861f
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802544"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Distribuire il progetto di esempio per le proprietà di base di Microsoft Cloud Adoption Framework per Azure

Per distribuire il progetto di esempio per le proprietà di base di Microsoft Cloud Adoption Framework (CAF) per Azure, seguire questa procedura:

> [!div class="checklist"]
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="create-blueprint-from-sample"></a>Creare il progetto dall'esempio

Prima di tutto, implementare l'esempio di progetto creando un nuovo progetto nell'ambiente basato sull'esempio.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare il progetto di esempio **Proprietà di base - Cloud Adoption Framework** in _Altri esempi_ e selezionare **Usa questo esempio**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: specificare un nome per la copia del progetto di esempio per le proprietà di base di Cloud Adoption Framework.
   - **Posizione della definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione in cui salvare la copia dell'esempio.

1. Selezionare la scheda _Artefatti_ nella parte superiore della pagina oppure **Avanti: Elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti che costituiscono l'esempio di progetto. Molti artefatti hanno parametri che verranno definiti in seguito. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

## <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia del progetto di esempio può essere personalizzata in base all'ambiente e alle esigenze, ma le modifiche potrebbero renderla non conforme al progetto per le proprietà di base di Cloud Adoption Framework.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella nuova pagina a destra specificare un valore di **Versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. In **Modifica le note** specificare informazioni come "Prima versione pubblicata del progetto per le proprietà di base di Cloud Adoption Framework". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

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
     - **Località**: selezionare un'area in cui creare l'identità gestita.
     - Azure Blueprint usa questa identità gestita per distribuire tutti gli elementi nel progetto assegnato.
       Per altre informazioni, vedere [Managed identities for Azure resources](../../../../active-directory/managed-identities-azure-resources/overview.md) (Identità gestite per risorse di Azure).
     - **Versione della definizione di progetto**: Scegliere una versione **pubblicata** della copia dell'esempio di progetto.

   - Blocca assegnazione

     Selezionare l'impostazione di blocco del progetto per l'ambiente. Per altre informazioni, vedere [Blueprints resource locking](../../concepts/resource-locking.md) (Blocco delle risorse del progetto).

   - Identità gestita

     Scegliere l'opzione predefinita _Assegnata dal sistema_ per l'identità gestita oppure l'opzione _Assegnata dall'utente_ per l'identità.

   - Parametri di progetto

     I parametri definiti in questa sezione vengono usati da molti artefatti nella definizione del progetto per assicurare coerenza.

     - **Organizzazione**: immettere il nome dell'organizzazione, ad esempio Contoso. Questo nome deve essere univoco.
     - **Area di Azure**: selezionare l'area di Azure per la distribuzione.
     - **Località consentite**: indicare le aree di Azure in cui è consentita la creazione di risorse.
     
   - Parametri dell'elemento

     I parametri definiti in questa sezione si applicano all'artefatto in cui sono definiti. Si tratta di [parametri dinamici](../../concepts/parameters.md#dynamic-parameters), in quanto vengono definiti durante l'assegnazione del progetto. Per un elenco completo di parametri di artefatti e relative descrizioni, vedere la [tabella di parametri degli artefatti](#artifact-parameters-table).

1. Dopo avere immesso tutti i parametri, selezionare **Assegna** in fondo alla pagina. Viene creata l'assegnazione del progetto e inizia la distribuzione degli artefatti. La distribuzione richiede circa un'ora. Per controllare lo stato della distribuzione, aprire l'assegnazione del progetto.

> [!WARNING]
> Il servizio Azure Blueprints e gli esempi di progetti predefiniti sono **gratuiti**. Le risorse di Azure hanno un [prezzo in base al prodotto](https://azure.microsoft.com/pricing/). Usare la [calcolatrice dei prezzi](https://azure.microsoft.com/pricing/calculator/) per stimare il costo dell'esecuzione delle risorse distribuite da questo esempio di progetto.

## <a name="artifact-parameters-table"></a>Tabella di parametri degli artefatti

La tabella seguente contiene un elenco dei parametri degli artefatti del progetto:

|Nome dell'artefatto|Tipo di artefatto|Nome parametro|DESCRIZIONE|
|-|-|-|-|
|SKU degli account di archiviazione consentiti|Assegnazione di criteri|Policy_Allowed-StorageAccount-SKUs|SKU usato negli account di archiviazione dei log di diagnostica|
|SKU delle macchine virtuali consentiti|Assegnazione di criteri|Policy_Allowed-VM-SKUs|SKU delle macchine virtuali consentiti|
|Aggiungi il tag del centro di costo ai gruppi di risorse|Assegnazione di criteri|Policy_CostCenter_Tag|Aggiungi tag e relativo valore dal gruppo di risorse|
|Tipi di risorse che non verranno consentiti nell'ambiente in uso|Assegnazione di criteri|Policy _Allowed-Resource-Types|Risorse di Azure che si vuole consentire nell'ambiente in uso|
|Distribuisci Key Vault|Modello di Resource Manager|KV-AccessPolicy|**Bloccato** - Gruppo di Azure AD o <Object ID> utente per la concessione delle autorizzazioni in Key Vault|
|Distribuisci Log Analytics|Modello di Resource Manager|LogAnalytics_DataRetention|**Bloccato** - Numero di giorni di conservazione dei dati in Log Analytics|
|Distribuisci Log Analytics|Modello di Resource Manager|LogAnalytics_Location|**Bloccato** - Area usata al momento della creazione dell'area di lavoro|

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato la procedura per la distribuzione del progetto per le proprietà di base di Cloud Adoption Framework, vedere l'articolo seguente per informazioni sull'architettura:

> [!div class="nextstepaction"]
> [Progetto per le proprietà di base di Cloud Adoption Framework - Panoramica](./index.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).