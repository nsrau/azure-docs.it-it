---
title: Creare un ambiente da un esempio di progetto
description: Usare un esempio di progetto per creare una definizione di progetto che configura due gruppi di risorse e un'assegnazione di ruolo per ciascuno.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/05/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b0d5d96ff897ac1710206eb49bca785e8809cb7d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798321"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Esercitazione: Creare un ambiente da un esempio di progetto

I progetti di esempio illustrano cosa è possibile fare con Azure Blueprints. Ogni progetto è un esempio con uno scopo specifico, ma non crea di per sé un ambiente completo. Ognuno è da intendersi come un punto di partenza per esplorare mediante Azure Blueprints con varie combinazioni di artefatti, progettazioni e parametri inclusi.

L'esercitazione seguente usa l'esempio di progetto **Gruppi di risorse con Controllo degli accessi in base al ruolo** per presentare diversi aspetti del servizio Blueprints. Vengono illustrate le procedure seguenti:

> [!div class="checklist"]
> - Creare una nuova definizione di progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente
> - Esaminare le risorse distribuite per l'assegnazione
> - Annullare l'assegnazione del progetto per rimuovere i blocchi

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-blueprint-definition-from-sample"></a>Creare la definizione di progetto dall'esempio

Come prima cosa, implementare l'esempio di progetto. Con l'importazione viene creato nell'ambiente un nuovo progetto basato sull'esempio.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare il progetto di esempio **Gruppi di risorse con Controllo degli accessi in base al ruolo** in _Altri esempi_ e selezionare **Usa questo esempio**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: specificare un nome per la copia dell'esempio di progetto. Per questa esercitazione si userà il nome _two-rgs-with-role-assignments_.
   - **Località della definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione o la sottoscrizione in cui salvare la copia dell'esempio.

1. Selezionare la scheda _Artefatti_ nella parte superiore della pagina oppure **Avanti: Elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti che costituiscono l'esempio di progetto. Questo esempio definisce due gruppi di risorse, rispettivamente con il nome visualizzato _ProdRG_ e _PreProdRG_. Il nome e la località finali di ogni gruppo di risorse vengono impostati durante l'assegnazione del progetto. Al gruppo di risorse _ProdRG_ viene assegnato il ruolo _Collaboratore_, mentre al gruppo di risorse _PreProdRG_ vengono assegnati i ruoli _Proprietario_ e _Lettore_. I ruoli assegnati nella definizione sono statici, ma l'utente, l'app o il gruppo a cui viene assegnato il ruolo viene impostato durante l'assegnazione del progetto.

1. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

Questo passaggio crea una copia della definizione del progetto di esempio nel gruppo di gestione o nella sottoscrizione selezionata. La definizione del progetto salvata viene gestita come qualsiasi progetto creato da zero. È possibile salvare l'esempio nel gruppo di gestione o nella sottoscrizione tutte le volte necessarie. A ogni copia deve però essere assegnato un nome univoco.

Quando nel portale viene visualizzata la notifica **Il salvataggio della definizione di progetto è riuscito**, proseguire con il passaggio successivo.

## <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia dell'esempio di progetto può essere personalizzata in base all'ambiente e alle specifiche esigenze. Ai fini di questa esercitazione non verrà apportata alcuna modifica.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la definizione del progetto _two-rgs-with-role-assignments_ e selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella casella **Versione** nel nuovo riquadro a destra specificare _1.0_ per la versione della copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. In **Modifica le note** specificare ad esempio "Prima versione pubblicata dall'esempio di progetto Gruppi di risorse con Controllo degli accessi in base al ruolo". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

Questo passaggio rende possibile l'assegnazione del progetto a una sottoscrizione. Dopo la pubblicazione, è ancora possibile apportare modifiche. Se si apportano altre modifiche è necessario pubblicare la copia con un nuovo valore di **Versione**, in modo da tenere traccia delle differenze tra le diverse versioni della stessa definizione di progetto.

Quando nel portale viene visualizzata la notifica **La pubblicazione della definizione di progetto è riuscita**, proseguire con il passaggio successivo.

## <a name="assign-the-sample-copy"></a>Assegnare la copia dell'esempio

Dopo che la copia dell'esempio di progetto è stata **pubblicata** correttamente, è possibile assegnarla a una sottoscrizione all'interno del gruppo di gestione in cui è stata salvata. In questo passaggio vengono specificati i parametri che rendono univoca ogni distribuzione della copia dell'esempio di progetto.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la definizione del progetto _two-rgs-with-role-assignments_ e selezionarla.

1. Selezionare **Assegna progetto** nella parte superiore della pagina della definizione di progetto.

1. Specificare i valori dei parametri per l'assegnazione del progetto:

   - Nozioni di base

     - **Sottoscrizioni**: selezionare una o più sottoscrizioni presenti nel gruppo di gestione in cui è stata salvata la copia dell'esempio di progetto. Se si selezionano più sottoscrizioni, viene creata un'assegnazione per ciascuna usando i parametri immessi.
     - **Nome dell'assegnazione**: il nome viene prepopolato in base al nome della definizione di progetto.
     - **Località**: selezionare un'area in cui creare l'identità gestita. Azure Blueprint usa questa identità gestita per distribuire tutti gli elementi nel progetto assegnato. Per altre informazioni, vedere [Managed identities for Azure resources](../../../active-directory/managed-identities-azure-resources/overview.md) (Identità gestite per risorse di Azure).
       Per questa esercitazione selezionare _Stati Uniti orientali 2_.
     - **Versione della definizione di progetto**: selezionare la versione **pubblicata** _1.0_ della copia della definizione del progetto di esempio.

   - Blocca assegnazione

     Selezionare la modalità di blocco del progetto _Sola lettura_. Per altre informazioni, vedere [Blueprints resource locking](../concepts/resource-locking.md) (Blocco delle risorse del progetto).

   - Identità gestita

     Lasciare l'opzione predefinita _Assegnata dal sistema_. Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Parametri dell'elemento

     I parametri definiti in questa sezione si applicano all'artefatto in cui sono definiti. Si tratta di [parametri dinamici](../concepts/parameters.md#dynamic-parameters), in quanto vengono definiti durante l'assegnazione del progetto. Per ogni artefatto impostare il parametro sul valore definito nella colonna **Valore**. Per `{Your ID}` selezionare il proprio account utente di Azure.

     |Nome artefatto|Tipo di artefatto|Nome parametro|Valore|DESCRIZIONE|
     |-|-|-|-|-|
     |Gruppo di risorse ProdRG|Gruppo di risorse|NOME|ProductionRG|Definisce il nome del primo gruppo di risorse.|
     |Gruppo di risorse ProdRG|Gruppo di risorse|Località|Stati Uniti occidentali 2|Imposta la località del primo gruppo di risorse.|
     |Collaboratore|Assegnazione di ruolo|Utente o gruppo|{ID}|Definisce l'utente o il gruppo a cui assegnare il ruolo _Collaboratore_ nel primo gruppo di risorse.|
     |Gruppo di risorse PreProdRG|Gruppo di risorse|NOME|PreProductionRG|Definisce il nome del secondo gruppo di risorse.|
     |Gruppo di risorse PreProdRG|Gruppo di risorse|Località|Stati Uniti occidentali|Imposta la località del secondo gruppo di risorse.|
     |Proprietario|Assegnazione di ruolo|Utente o gruppo|{ID}|Definisce l'utente o il gruppo a cui assegnare il ruolo _Proprietario_ nel secondo gruppo di risorse.|
     |Lettori|Assegnazione di ruolo|Utente o gruppo|{ID}|Definisce l'utente o il gruppo a cui assegnare il ruolo _Lettore_ nel secondo gruppo di risorse.|

1. Dopo avere immesso tutti i parametri, selezionare **Assegna** in fondo alla pagina.

Questo passaggio distribuisce le risorse definite e configura l'opzione selezionata in **Blocca assegnazione**. L'applicazione dei blocchi dei progetti può richiedere fino a 30 minuti.

Quando nel portale viene visualizzata la notifica **L'assegnazione della definizione di progetto è riuscita**, proseguire con il passaggio successivo.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Esaminare le risorse distribuite per l'assegnazione

L'assegnazione del progetto crea e tiene traccia degli artefatti definiti nella definizione del progetto. È possibile verificare lo stato delle risorse nella pagina di assegnazione del progetto o controllando le risorse direttamente.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Progetti assegnati** a sinistra. Usare i filtri per trovare l'assegnazione del progetto _Assignment-two-rgs-with-role-assignments_ e selezionarla.

   In questa pagina è possibile vedere l'assegnazione riuscita e l'elenco delle risorse create insieme al relativo stato di blocco del progetto. Se l'assegnazione viene aggiornata, nell'elenco a discesa **Operazione di assegnazione** vengono visualizzati i dettagli della distribuzione di ogni versione di definizione. È possibile fare clic su ogni risorsa elencata per aprire la relativa pagina delle proprietà.

1. Selezionare il gruppo di risorse **ProductionRG**.

   Come si può notare, il nome del gruppo di risorse è **ProductionRG** e non il nome visualizzato dell'artefatto _ProdRG_. Questo nome corrisponde al valore impostato durante l'assegnazione del progetto.

1. Selezionare la pagina **Controllo di accesso (IAM)** a sinistra e quindi la scheda **Assegnazioni di ruolo**.

   Qui è possibile vedere che all'account è stato assegnato il ruolo _Collaboratore_ nell'ambito di _Questa risorsa_. L'assegnazione di progetto _Assignment-two-rgs-with-role-assignments_ ha il ruolo _Proprietario_ poiché è stata usata per creare il gruppo di risorse. Queste autorizzazioni vengono usate anche per gestire le risorse per cui sono stati configurati blocchi del progetto.

1. Nella struttura di navigazione del portale di Azure selezionare **Assignment-two-rgs-with-role-assignments** per tornare indietro di una pagina, quindi selezionare il gruppo di risorse **PreProductionRG**.

1. Selezionare la pagina **Controllo di accesso (IAM)** a sinistra e quindi la scheda **Assegnazioni di ruolo**.

   Qui è possibile vedere che all'account sono stati assegnati i ruoli _Proprietario_ e _Lettore_, entrambi nell'ambito di _Questa risorsa_. Anche l'assegnazione del progetto ha il ruolo_Proprietario_ come il primo gruppo di risorse.

1. Selezionare la scheda **Assegnazioni di rifiuto**.

   L'assegnazione del progetto ha creato un'[assegnazione di rifiuto](../../../role-based-access-control/deny-assignments.md) nel gruppo di risorse distribuito per rafforzare la modalità di blocco del progetto _Sola lettura_. L'assegnazione di rifiuto impedisce agli utenti che dispongono di diritti appropriati nella scheda _Assegnazioni di ruolo_ di eseguire azioni specifiche. L'assegnazione di rifiuto si applica a _tutte le entità_.

1. Selezionare l'assegnazione di rifiuto, quindi selezionare la pagina **Autorizzazioni negate** a sinistra.

   L'assegnazione di rifiuto impedisce tutte le operazioni con la configurazione di **\*** e **Action**, ma consente l'accesso in lettura escludendo **\*/read** tramite **NotActions**.

1. Nella struttura di navigazione del portale di Azure selezionare **PreProductionRG - Controllo di accesso (IAM)**. Selezionare la pagina **Panoramica** a sinistra e quindi il pulsante **Elimina gruppo di risorse**. Immettere il nome _PreProductionRG_ per confermare l'eliminazione e selezionare **Elimina** in fondo al riquadro.

   Nel portale viene visualizzata la notifica **L'eliminazione del gruppo di risorse PreProductionRG non è riuscita**. Questo errore si verifica perché, sebbene l'account abbia l'autorizzazione necessaria per eliminare il gruppo di risorse, l'accesso è negato dall'assegnazione del progetto. È stata infatti selezionata la modalità di blocco del progetto _Sola lettura_ durante l'assegnazione del progetto. Il blocco del progetto impedisce a un account di eliminare la risorsa, anche se dispone dell'autorizzazione _Proprietario_. Per altre informazioni, vedere [Blueprints resource locking](../concepts/resource-locking.md) (Blocco delle risorse del progetto).

Questi passaggi mostrano che le risorse sono state create in base ai parametri definiti e che il blocco del progetto ne ha impedito l'eliminazione indesiderata, persino da un account dotato dell'autorizzazione appropriata.

## <a name="unassign-the-blueprint"></a>Annullare l'assegnazione del progetto

L'ultimo passaggio consiste nel rimuovere l'assegnazione del progetto e le risorse che ha distribuito.
Con la rimozione dell'assegnazione non vengono rimossi gli artefatti distribuiti.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Progetti assegnati** a sinistra. Usare i filtri per trovare l'assegnazione del progetto _Assignment-two-rgs-with-role-assignments_ e selezionarla.

1. Selezionare il pulsante **Annulla assegnazione progetto** nella parte superiore della pagina. Leggere l'avviso nella finestra di dialogo di conferma, quindi scegliere **OK**.

   Con la rimozione dell'assegnazione del progetto vengono rimossi anche i relativi blocchi. Le risorse create possono essere di nuovo eliminate da un account dotato di autorizzazioni.

1. Scegliere **Gruppi di risorse** dal menu di Azure, quindi selezionare **ProductionRG**.

1. Selezionare la pagina **Controllo di accesso (IAM)** a sinistra e quindi la scheda **Assegnazioni di ruolo**.

La sicurezza per ogni gruppo di risorse ha ancora le assegnazioni di ruolo distribuite, ma l'assegnazione del progetto non dispone più dell'accesso come _Proprietario_.

Quando nel portale viene visualizzata la notifica **La rimozione dell'assegnazione progetto è riuscita**, proseguire con il passaggio successivo.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'esercitazione, eliminare le risorse seguenti:

- Gruppo di risorse _ProductionRG_
- Gruppo di risorse _PreProductionRG_
- Definizione di progetto _two-rgs-with-role-assignments_

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md)
- Comprendere come usare i [parametri statici e dinamici](../concepts/parameters.md)
- Scoprire come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md)
- Imparare a personalizzare l'[ordine in sequenza del progetto](../concepts/sequencing-order.md)
- Informazioni su come [aggiornare le assegnazioni esistenti](../how-to/update-existing-assignments.md)
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md)