---
title: Proteggere le nuove risorse con blocchi per le risorse del progetto
description: Informazioni su come usare i blocchi delle risorse in Azure Blueprints in modalità Sola lettura e Non eliminare per proteggere le risorse appena distribuite.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d315fb5fe3ce7844946e6a9405a9a5f6a0be8b9d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272275"
---
# <a name="protect-new-resources-with-azure-blueprints-resource-locks"></a>Proteggere le nuove risorse con blocchi delle risorse in Azure Blueprints

Il [blocco delle risorse](../concepts/resource-locking.md) di Azure Blueprints consente di proteggere le risorse appena distribuite da eventuali manomissioni, anche da parte di un account con il ruolo _Proprietario_. È possibile aggiungere questo tipo di protezione per le risorse create da un elemento del modello di Resource Manager nella definizione del progetto.

Vengono illustrate le procedure seguenti:

> [!div class="checklist"]
> - Creare una nuova definizione di progetto
> - Contrassegnare la definizione di progetto come **pubblicato**
> - Assegnare la definizione del progetto a una sottoscrizione esistente
> - Esaminare il nuovo gruppo di risorse
> - Annullare l'assegnazione progetto per rimuovere i blocchi

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-new-blueprint-definition"></a>Creare una nuova definizione di progetto

Creare prima una nuova definizione di progetto.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare l'esempio di progetto **Blank Blueprint** (Progetto vuoto) nella parte superiore della pagina e selezionare **Start with blank blueprint** (Inizia con progetto vuoto).

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: specificare un nome per la copia dell'esempio di progetto. Per questa esercitazione si userà il nome _locked-storageaccount_.
   - **Descrizione del progetto**: descrivere la definizione di progetto. Usare "Per il test del blocco delle risorse del progetto sulle risorse distribuite".
   - **Posizione della definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione o la sottoscrizione in cui salvare la definizione del progetto.

1. Selezionare la scheda _Artefatti_ nella parte superiore della pagina oppure **Avanti: Elementi** nella parte inferiore della pagina.

1. Aggiungere un gruppo di risorse alla sottoscrizione: selezionare la riga **+ Aggiungi artefatto...** sotto la voce **Sottoscrizione**.
   Selezionare "Gruppo di risorse" per _Tipo di elemento_. Impostare il _Nome visualizzato dell'artefatto_ su **RGtoLock**.
   Lasciare vuoti i campi _Nome gruppo di risorse_ e _Località_, ma assicurarsi che sia selezionata la casella di controllo per ogni proprietà in modo che i parametri siano **parametri dinamici**. Fare clic su **Aggiungi** per aggiungere questo elemento al progetto.

1. Aggiungere un modello nel gruppo di risorse: selezionare la riga **+ Aggiungi artefatto...** sotto la voce **RGtoLock**. Selezionare "Modello di Azure Resource Manager" per _Tipo di elemento_, impostare _Nome visualizzato dell'artefatto_ su "Account di archiviazione" e lasciare vuoto il campo _Descrizione_. Nella scheda **Modello** nella casella dell'editor incollare il modello di Resource Manager seguente. Dopo aver incollato il modello, selezionare **Aggiungi** per aggiungere questo elemento al progetto.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "storageAccountType": {
               "type": "string",
               "defaultValue": "Standard_LRS",
               "allowedValues": [
                   "Standard_LRS",
                   "Standard_GRS",
                   "Standard_ZRS",
                   "Premium_LRS"
               ],
               "metadata": {
                   "description": "Storage Account type"
               }
           }
       },
       "variables": {
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroup().location]",
           "apiVersion": "2018-07-01",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "StorageV2",
           "properties": {}
       }],
       "outputs": {
           "storageAccountName": {
               "type": "string",
               "value": "[variables('storageAccountName')]"
           }
       }
   }
   ```

1. Selezionare **Salva bozza** nella parte inferiore della pagina.

Questo passaggio crea la definizione del progetto nel gruppo di gestione o nella sottoscrizione selezionata.

Quando nel portale viene visualizzata la notifica **Il salvataggio della definizione di progetto è riuscito**, proseguire con il passaggio successivo.

## <a name="publish-the-blueprint-definition"></a>Pubblicare la definizione di progetto

La definizione del progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la definizione del progetto _locked-storageaccount_ e selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nel nuovo riquadro a destra, specificare la **Versione** come _1.0_. Questa proprietà è utile se si apporta una modifica successivamente. In **Modifica le note** specificare ad esempio "Prima versione pubblicata per bloccare le risorse distribuite del progetto". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

Questo passaggio rende possibile l'assegnazione del progetto a una sottoscrizione. Dopo la pubblicazione, è ancora possibile apportare modifiche. Se si apportano altre modifiche è necessario pubblicare la copia con un nuovo valore di **Versione**, in modo da tenere traccia delle differenze tra le diverse versioni della stessa definizione di progetto.

Quando nel portale viene visualizzata la notifica **La pubblicazione della definizione di progetto è riuscita**, proseguire con il passaggio successivo.

## <a name="assign-the-blueprint-definition"></a>Assegnare la definizione di progetto

Dopo che la definizione del progetto è stata **pubblicata** correttamente, è possibile assegnarla a una sottoscrizione all'interno del gruppo di gestione in cui è stata salvata. In questo passaggio vengono specificati i parametri che rendono univoca ogni distribuzione della definizione del progetto.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la definizione del progetto _locked-storageaccount_ e selezionarla.

1. Selezionare **Assegna progetto** nella parte superiore della pagina della definizione di progetto.

1. Specificare i valori dei parametri per l'assegnazione del progetto:

   - Nozioni di base

     - **Sottoscrizioni**: selezionare una o più sottoscrizioni presenti nel gruppo di gestione in cui è stata salvata la definizione di progetto. Se si selezionano più sottoscrizioni, viene creata un'assegnazione per ognuna usando i parametri immessi.
     - **Nome dell'assegnazione**: il nome viene prepopolato in base al nome della definizione di progetto. Questa assegnazione dovrà rappresentare il blocco del nuovo gruppo di risorse, quindi modificare il nome dell'assegnazione in _assignment-locked-storageaccount-TestingBPLockss_.
     - **Località**: selezionare un'area in cui creare l'identità gestita. Azure Blueprint usa questa identità gestita per distribuire tutti gli elementi nel progetto assegnato. Per altre informazioni, vedere [Managed identities for Azure resources](../../../active-directory/managed-identities-azure-resources/overview.md) (Identità gestite per risorse di Azure).
       Per questa esercitazione selezionare _Stati Uniti orientali 2_.
     - **Versione della definizione di progetto**: selezionare la versione **pubblicata** _1.0_ della definizione di progetto.

   - Blocca assegnazione

     Selezionare la modalità di blocco del progetto _Sola lettura_. Per altre informazioni, vedere [Blueprints resource locking](../concepts/resource-locking.md) (Blocco delle risorse del progetto).

   - Identità gestita

     Lasciare l'opzione predefinita _Assegnata dal sistema_. Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Parametri dell'elemento

     I parametri definiti in questa sezione si applicano all'artefatto in cui sono definiti. Si tratta di [parametri dinamici](../concepts/parameters.md#dynamic-parameters), in quanto vengono definiti durante l'assegnazione del progetto. Per ogni artefatto impostare il parametro sul valore definito nella colonna **Valore**.

     |Nome dell'artefatto|Tipo di artefatto|Nome parametro|Valore|DESCRIZIONE|
     |-|-|-|-|-|
     |Gruppo di risorse RGtoLock|Gruppo di risorse|NOME|TestingBPLocks|Definisce il nome del nuovo gruppo di risorse a cui applicare i blocchi del progetto.|
     |Gruppo di risorse RGtoLock|Gruppo di risorse|Località|Stati Uniti occidentali 2|Definisce la posizione del nuovo gruppo di risorse a cui applicare i blocchi del progetto.|
     |StorageAccount|Modello di Resource Manager|storageAccountType (Account di archiviazione)|Standard_GRS|Selezionare lo SKU di archiviazione. Il valore predefinito è _Standard_LRS_.|

1. Dopo avere immesso tutti i parametri, selezionare **Assegna** in fondo alla pagina.

Questo passaggio distribuisce le risorse definite e configura l'opzione selezionata in **Blocca assegnazione**. L'applicazione dei blocchi dei progetti può richiedere fino a 30 minuti.

Quando nel portale viene visualizzata la notifica **L'assegnazione della definizione di progetto è riuscita**, proseguire con il passaggio successivo.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Esaminare le risorse distribuite per l'assegnazione

L'assegnazione ha creato il gruppo di risorse _TestingBPLocks_ e l'account di archiviazione distribuito dall'elemento del modello di Resource Manager. Il nuovo gruppo di risorse e lo stato di blocco selezionato vengono visualizzati nella pagina dei dettagli dell'assegnazione.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Progetti assegnati** a sinistra. Usare i filtri per trovare l'assegnazione del progetto _assignment-locked-storageaccount-TestingBPLocks_ e selezionarla.

   In questa pagina è possibile vedere l'assegnazione riuscita e le risorse distribuite insieme al nuovo stato di blocco del progetto. Se l'assegnazione viene aggiornata, nell'elenco a discesa **Operazione di assegnazione** vengono visualizzati i dettagli della distribuzione di ogni versione di definizione. È possibile fare clic sul gruppo di risorse per aprire direttamente la pagina delle proprietà.

1. Selezionare il gruppo di risorse **TestingBPLocks**.

1. Selezionare la pagina **Controllo di accesso (IAM)** a sinistra e quindi la scheda **Assegnazioni di ruoli**.

   Qui è possibile vedere che l'assegnazione di progetto _assignment-locked-storageaccount-TestingBPLocks_ ha il ruolo _proprietario_ poiché è stata usata per distribuire e bloccare il gruppo di risorse.

1. Selezionare la scheda **Assegnazioni di rifiuto**.

   L'assegnazione del progetto ha creato un'[assegnazione di rifiuto](../../../role-based-access-control/deny-assignments.md) nel gruppo di risorse distribuito per rafforzare la modalità di blocco del progetto _Sola lettura_. L'assegnazione di rifiuto impedisce agli utenti che dispongono di diritti appropriati nella scheda _Assegnazioni di ruolo_ di eseguire azioni specifiche. L'assegnazione di rifiuto si applica a _tutte le entità_.

   Per informazioni su come escludere un'entità di sicurezza da un'assegnazione di rifiuto, vedere l'articolo sul [blocco risorse di Azure Blueprints](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Selezionare l'assegnazione di rifiuto, quindi selezionare la pagina **Autorizzazioni negate** a sinistra.

   L'assegnazione di rifiuto impedisce tutte le operazioni con la configurazione **\*** e **Action**, ma consente l'accesso in lettura escludendo **\*/read** tramite **NotActions**.

1. Nella struttura di navigazione del portale di Azure selezionare **TestingBPLocks - Controllo di accesso (IAM)**. Selezionare quindi la pagina **Panoramica** a sinistra e quindi il pulsante **Elimina gruppo di risorse**. Immettere il nome _TestingBPLocks_ per confermare l'eliminazione e selezionare **Elimina** in fondo al riquadro.

   Nel portale viene visualizzata la notifica **L'eliminazione del gruppo di risorse TestingBPLocks non è riuscita**. Questo errore si verifica perché, sebbene l'account abbia l'autorizzazione necessaria per eliminare il gruppo di risorse, l'accesso è negato dall'assegnazione del progetto. È stata infatti selezionata la modalità di blocco del progetto _Sola lettura_ durante l'assegnazione del progetto. Il blocco del progetto impedisce a un account di eliminare la risorsa, anche se dispone dell'autorizzazione _Proprietario_. Per altre informazioni, vedere [Blueprints resource locking](../concepts/resource-locking.md) (Blocco delle risorse del progetto).

Questi passaggi mostrano che le risorse distribuite sono ora protette con blocchi del progetto che ne impediscono l'eliminazione indesiderata, persino da un account dotato dell'autorizzazione appropriata.

## <a name="unassign-the-blueprint"></a>Annullare l'assegnazione del progetto

L'ultimo passaggio consiste nel rimuovere l'assegnazione della definizione del progetto. Con la rimozione dell'assegnazione non vengono rimossi gli artefatti interessati.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Progetti assegnati** a sinistra. Usare i filtri per trovare l'assegnazione del progetto _assignment-locked-storageaccount-TestingBPLocks_ e selezionarla.

1. Selezionare il pulsante **Annulla assegnazione progetto** nella parte superiore della pagina. Leggere l'avviso nella finestra di dialogo di conferma, quindi scegliere **OK**.

   Con la rimozione dell'assegnazione del progetto vengono rimossi anche i relativi blocchi. Le risorse create possono essere di nuovo eliminate da un account dotato di autorizzazioni.

1. Scegliere **Gruppi di risorse** dal menu di Azure, quindi selezionare **TestingBPLocks**.

1. Selezionare la pagina **Controllo di accesso (IAM)** a sinistra e quindi la scheda **Assegnazioni di ruoli**.

La sicurezza per il gruppo di risorse mostra che l'assegnazione del progetto non dispone più dell'accesso _Proprietario_.

Quando nel portale viene visualizzata la notifica **La rimozione dell'assegnazione progetto è riuscita**, proseguire con il passaggio successivo.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'esercitazione, eliminare le risorse seguenti:

- Gruppo di risorse _TestingBPLocks_
- Definizione del progetto _locked-storageaccount_

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).