---
title: 'Esercitazione: Proteggere le nuove risorse con blocchi'
description: Questa esercitazione illustra come usare i blocchi delle risorse in Azure Blueprints in modalità Sola lettura e Non eliminare per proteggere le risorse appena distribuite.
ms.date: 11/21/2019
ms.topic: tutorial
ms.openlocfilehash: ee57ff0c08f4fb8aa710dd2fa4dcef664484973d
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327453"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Esercitazione: Proteggere le nuove risorse con blocchi delle risorse in Azure Blueprints

Il [blocco delle risorse](../concepts/resource-locking.md) di Azure Blueprints consente di proteggere le risorse appena distribuite da eventuali manomissioni, anche da parte di un account con il ruolo _Proprietario_. È possibile aggiungere questo tipo di protezione per le risorse create da un elemento del modello di Resource Manager nelle definizioni del progetto.

In questa esercitazione si completeranno i passaggi seguenti:

> [!div class="checklist"]
> - Creare una definizione di progetto
> - Contrassegnare la definizione di progetto come **pubblicato**
> - Assegnare la definizione del progetto a una sottoscrizione esistente
> - Esaminare il nuovo gruppo di risorse
> - Annullare l'assegnazione progetto per rimuovere i blocchi

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="create-a-blueprint-definition"></a>Creare una definizione di progetto

Creare prima la definizione di progetto.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Introduzione** a sinistra selezionare **Crea** in **Creare un progetto**.

1. Trovare l'esempio di progetto **Progetto vuoto** nella parte superiore della pagina. Selezionare **Inizia con un progetto vuoto**.

1. Immettere queste informazioni nella scheda **Nozioni di base**:

   - **Nome progetto**: specificare un nome per la copia dell'esempio di progetto. Per questa esercitazione si userà il nome **locked-storageaccount**.
   - **Descrizione del progetto**: aggiungere una descrizione per la definizione di progetto. Usare **Per il test del blocco delle risorse del progetto sulle risorse distribuite**.
   - **Posizione della definizione**: selezionare i puntini di sospensione (...) e quindi selezionare il gruppo di gestione o la sottoscrizione in cui salvare la definizione del progetto.

1. Selezionare la scheda **Artefatti** nella parte superiore della pagina oppure selezionare **Avanti: Elementi** nella parte inferiore della pagina.

1. Aggiungere un gruppo di risorse al livello di sottoscrizione:
   1. Selezionare la riga **Aggiungi artefatto** sotto la voce **Sottoscrizione**.
   1. Selezionare **Gruppo di risorse** in **Tipo di artefatto**.
   1. Impostare il **Nome visualizzato dell'artefatto** su **RGtoLock**.
   1. Lasciare vuote le caselle **Nome gruppo di risorse** e **Località**, ma assicurarsi che sia selezionata la casella di controllo per ogni proprietà in modo che i parametri siano **parametri dinamici**.
   1. Selezionare **Aggiungi** per aggiungere l'artefatto al progetto.

1. Aggiungere un modello nel gruppo di risorse:
   1. Selezionare la riga **Aggiungi artefatto** sotto la voce **RGtoLock**.
   1. Selezionare **Modello di Azure Resource Manager** in **Tipo di artefatto**, impostare **Nome visualizzato dell'artefatto** su **Account di archiviazione** e lasciare vuoto il campo **Descrizione**.
   1. Nella scheda **Modello** incollare il modello di Resource Manager seguente nella casella dell'editor.
      Dopo aver incollato il modello, selezionare **Aggiungi** per aggiungere l'artefatto al progetto.

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

La definizione del progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario pubblicarla per poterla assegnare e distribuire.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la definizione del progetto **locked-storageaccount** e selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nel nuovo riquadro a destra immettere **1.0** come **Versione**. Questa proprietà è utile se si apporta una modifica in un secondo momento. In **Modifica le note** specificare ad esempio **Prima versione pubblicata per bloccare le risorse distribuite del progetto**. Quindi selezionare **Pubblica** nella parte inferiore della pagina.

Questo passaggio rende possibile l'assegnazione del progetto a una sottoscrizione. Dopo aver pubblicato la definizione di progetto, è ancora possibile apportare modifiche. Se si apportano modifiche, è necessario pubblicare la definizione con un nuovo valore di versione, in modo da tenere traccia delle differenze tra le versioni della stessa definizione di progetto.

Quando nel portale viene visualizzata la notifica **La pubblicazione della definizione di progetto è riuscita**, proseguire con il passaggio successivo.

## <a name="assign-the-blueprint-definition"></a>Assegnare la definizione di progetto

Dopo aver pubblicato la definizione di progetto, è possibile assegnarla a una sottoscrizione all'interno del gruppo di gestione in cui è stata salvata. In questo passaggio vengono specificati i parametri che rendono univoca ogni distribuzione della definizione del progetto.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la definizione del progetto **locked-storageaccount** e selezionarla.

1. Selezionare **Assegna progetto** nella parte superiore della pagina della definizione di progetto.

1. Specificare i valori dei parametri per l'assegnazione del progetto:

   - **Nozioni di base**

     - **Sottoscrizioni**: selezionare una o più sottoscrizioni presenti nel gruppo di gestione in cui è stata salvata la definizione di progetto. Se si selezionano più sottoscrizioni, viene creata un'assegnazione per ogni sottoscrizione usando i parametri immessi.
     - **Nome dell'assegnazione**: il nome viene prepopolato in base al nome della definizione di progetto. Questa assegnazione dovrà rappresentare il blocco del nuovo gruppo di risorse, quindi modificare il nome dell'assegnazione in **assignment-locked-storageaccount-TestingBPLockss**.
     - **Località**: selezionare un'area in cui creare l'identità gestita. Azure Blueprint usa questa identità gestita per distribuire tutti gli elementi nel progetto assegnato. Per altre informazioni, vedere [Managed identities for Azure resources](../../../active-directory/managed-identities-azure-resources/overview.md) (Identità gestite per risorse di Azure).
       Per questa esercitazione selezionare **Stati Uniti orientali 2**.
     - **Versione della definizione di progetto**: selezionare la versione pubblicata **1.0** della definizione di progetto.

   - **Blocca assegnazione**

     Selezionare la modalità di blocco del progetto **Sola lettura**. Per altre informazioni, vedere [Blueprints resource locking](../concepts/resource-locking.md) (Blocco delle risorse del progetto).

   - **Identità gestita**

     Usare l'opzione predefinita: **Assegnata dal sistema**. Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](../../../active-directory/managed-identities-azure-resources/overview.md).

   - **Parametri artefatto**

     I parametri definiti in questa sezione si applicano all'artefatto in cui sono definiti. Si tratta di [parametri dinamici](../concepts/parameters.md#dynamic-parameters), in quanto vengono definiti durante l'assegnazione del progetto. Per ogni artefatto impostare il parametro sul valore definito nella colonna **Valore**.

     |Nome dell'artefatto|Tipo di artefatto|Nome parametro|Valore|DESCRIZIONE|
     |-|-|-|-|-|
     |Gruppo di risorse RGtoLock|Resource group|NOME|TestingBPLocks|Definisce il nome del nuovo gruppo di risorse a cui applicare i blocchi del progetto.|
     |Gruppo di risorse RGtoLock|Resource group|Location|Stati Uniti occidentali 2|Definisce la posizione del nuovo gruppo di risorse a cui applicare i blocchi del progetto.|
     |StorageAccount|Modello di Resource Manager|storageAccountType (Account di archiviazione)|Standard_GRS|SKU di archiviazione. Il valore predefinito è _Standard_LRS_.|

1. Dopo avere immesso tutti i parametri, selezionare **Assegna** in fondo alla pagina.

Questo passaggio distribuisce le risorse definite e configura l'opzione selezionata in **Blocca assegnazione**. L'applicazione dei blocchi dei progetti può richiedere fino a 30 minuti.

Quando nel portale viene visualizzata la notifica **L'assegnazione della definizione di progetto è riuscita**, proseguire con il passaggio successivo.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Esaminare le risorse distribuite per l'assegnazione

L'assegnazione crea il gruppo di risorse _TestingBPLocks_ e l'account di archiviazione distribuito dall'artefatto del modello di Resource Manager. Il nuovo gruppo di risorse e lo stato di blocco selezionato vengono visualizzati nella pagina dei dettagli dell'assegnazione.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Progetti assegnati** a sinistra. Usare i filtri per trovare l'assegnazione del progetto **assignment-locked-storageaccount-TestingBPLocks** e selezionarla.

   In questa pagina è possibile vedere l'assegnazione riuscita e le risorse distribuite insieme al nuovo stato di blocco del progetto. Se l'assegnazione viene aggiornata, nell'elenco a discesa **Operazione di assegnazione** vengono visualizzati i dettagli della distribuzione di ogni versione di definizione. È possibile selezionare il gruppo di risorse per aprire la pagina delle proprietà.

1. Selezionare il gruppo di risorse **TestingBPLocks**.

1. Selezionare la pagina **Controllo di accesso (IAM)** a sinistra. Selezionare quindi la scheda **Assegnazioni di ruolo**.

   Qui è possibile vedere che l'assegnazione di progetto _assignment-locked-storageaccount-TestingBPLocks_ ha il ruolo _Proprietario_ poiché questo ruolo è stato usato per distribuire e bloccare il gruppo di risorse.

1. Selezionare la scheda **Assegnazioni di rifiuto**.

   L'assegnazione del progetto ha creato un'[assegnazione di rifiuto](../../../role-based-access-control/deny-assignments.md) nel gruppo di risorse distribuito per rafforzare la modalità di blocco del progetto **Sola lettura**. L'assegnazione di rifiuto impedisce agli utenti che dispongono di diritti appropriati nella scheda **Assegnazioni di ruolo** di eseguire azioni specifiche. L'assegnazione di rifiuto si applica a _tutte le entità_.

   Per informazioni su come escludere un'entità di sicurezza da un'assegnazione di rifiuto, vedere l'articolo sul [blocco risorse di Azure Blueprints](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Selezionare l'assegnazione di rifiuto e quindi selezionare la pagina **Autorizzazioni negate** a sinistra.

   L'assegnazione di rifiuto impedisce tutte le operazioni con la configurazione **\*** e **Action**, ma consente l'accesso in lettura escludendo **\*/read** tramite **NotActions**.

1. Nella struttura di navigazione del portale di Azure selezionare **TestingBPLocks - Controllo di accesso (IAM)** . Selezionare la pagina **Panoramica** a sinistra e quindi il pulsante **Elimina gruppo di risorse**. Immettere il nome **TestingBPLocks** per confermare l'eliminazione e quindi selezionare **Elimina** in fondo al riquadro.

   Nel portale viene visualizzata la notifica **L'eliminazione del gruppo di risorse TestingBPLocks non è riuscita**. Questo errore si verifica perché, sebbene l'account abbia l'autorizzazione necessaria per eliminare il gruppo di risorse, l'accesso è negato dall'assegnazione del progetto. È stata infatti selezionata la modalità di blocco del progetto **Sola lettura** durante l'assegnazione del progetto. Il blocco del progetto impedisce a un account di eliminare la risorsa, anche se dispone dell'autorizzazione _Proprietario_. Per altre informazioni, vedere [Blueprints resource locking](../concepts/resource-locking.md) (Blocco delle risorse del progetto).

Questi passaggi mostrano che le risorse distribuite sono ora protette con blocchi del progetto che ne impediscono l'eliminazione indesiderata, persino da un account dotato dell'autorizzazione per eliminare le risorse.

## <a name="unassign-the-blueprint"></a>Annullare l'assegnazione del progetto

L'ultimo passaggio consiste nel rimuovere l'assegnazione della definizione del progetto. Con la rimozione dell'assegnazione non vengono rimossi gli artefatti interessati.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Progetti assegnati** a sinistra. Usare i filtri per trovare l'assegnazione del progetto **assignment-locked-storageaccount-TestingBPLocks** e selezionarla.

1. Selezionare **Annulla assegnazione progetto** nella parte superiore della pagina. Leggere l'avviso nella finestra di dialogo di conferma e quindi scegliere **OK**.

   Con la rimozione dell'assegnazione del progetto vengono rimossi anche i relativi blocchi. Le risorse possono ancora una volta essere eliminate da un account dotato di autorizzazioni appropriate.

1. Scegliere **Gruppi di risorse** dal menu di Azure e quindi selezionare **TestingBPLocks**.

1. Selezionare la pagina **Controllo di accesso (IAM)** a sinistra e quindi selezionare la scheda **Assegnazioni di ruoli**.

La sicurezza per il gruppo di risorse mostra che l'assegnazione del progetto non dispone più dell'accesso _Proprietario_.

Quando nel portale viene visualizzata la notifica **La rimozione dell'assegnazione progetto è riuscita**, proseguire con il passaggio successivo.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'esercitazione, eliminare le risorse seguenti:

- Gruppo di risorse _TestingBPLocks_
- Definizione del progetto _locked-storageaccount_

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come proteggere le nuove risorse distribuite con Azure Blueprints. Per altre informazioni su Azure Blueprints, passare all'articolo relativo al ciclo di vita di un progetto.

> [!div class="nextstepaction"]
> [Informazioni sul ciclo di vita di un progetto](../concepts/lifecycle.md)