---
title: Creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure | Microsoft Docs
description: Questo articolo illustra i passaggi per creare una definizione dei criteri per identificare le risorse non conformi.
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 11/02/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: db5112c858d2a2c54813d9c9a3670a45fcbdb993
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>Creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure
Il primo passaggio per ottenere informazioni sulla conformità in Azure è sapere qual è lo stato delle risorse correnti. Questa guida introduttiva illustra il processo di creazione di un'assegnazione criteri per identificare le macchine virtuali che non usano dischi gestiti.

Alla fine di questo processo saranno state identificate correttamente le macchine virtuali che non usano dischi gestiti e pertanto *non conformi*.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="opt-in-to-azure-policy"></a>Consenso esplicito per Criteri di Azure

Criteri di Azure è attualmente disponibile in anteprima pubblica ed è necessario registrarsi per richiedere l'accesso.

1. Passare a Criteri di Azure all'indirizzo https://aka.ms/getpolicy e selezionare **Iscrizione** nel riquadro a sinistra.

   ![Ricerca di criteri](media/assign-policy-definition/sign-up.png)

2. Acconsentire esplicitamente all'uso di Criteri di Azure selezionando le sottoscrizioni che si vuole usare nell'elenco **Sottoscrizione**. Selezionare quindi **Register** (Registra).

   ![Consenso esplicito per l'uso di Criteri di Azure](media/assign-policy-definition/preview-opt-in.png)

   La richiesta viene approvata automaticamente per l'anteprima. Attendere fino a 30 minuti che il sistema elabori la registrazione.

## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

In questa guida introduttiva si crea un'assegnazione di criteri e si assegna la definizione dei criteri *Audit Virtual Machines without Managed Disks* (Controllare le macchine virtuali senza i dischi gestiti).

1. Selezionare **Assegnazioni**nel riquadro a sinistra della pagina Criteri di Azure.
2. Selezionare **Assign Policy** (Assegna criterio) nella parte superiore del riquadro **Assignments** (Assegnazioni).

   ![Assegnare una definizione di criteri](media/assign-policy-definition/select-assign-policy.png)

3. Nella pagina **Assegna criterio** fare clic sul ![pulsante per la definizione dei criteri](media/assign-policy-definition/definitions-button.png) accanto al campo **Policy** (Criterio) per aprire l'elenco delle definizioni disponibili.

   ![Aprire le definizioni di criteri disponibili](media/assign-policy-definition/open-policy-definitions.png)

   Criteri di Azure include definizioni di criteri predefinite che è possibile usare. Sono disponibili definizioni di criteri predefinite come le seguenti:

   - Enforce tag and its value (Imporre un tag e il relativo valore)
   - Applicare un tag e il relativo valore
   - Require SQL Server Version 12.0 (Richiedere SQL Server versione 12.0)

4. Cercare tra le definizioni di criteri per trovare la definizione *Audit VMs that do not use managed disks* (Controllare le macchine virtuali che non usano i dischi gestiti). Fare clic su tale criterio e su **Assegna**.

   ![Trovare la definizione di criteri corretta](media/assign-policy-definition/select-available-definition.png)

5. Specificare un nome visualizzato per l'assegnazione del criterio in **Nome**. In questo caso è possibile usare *Audit VMs that do not use managed disks* (Controllare le macchine virtuali che non usano i dischi gestiti). È anche possibile aggiungere una **descrizione** facoltativa. La descrizione fornisce informazioni dettagliate su come l'assegnazione criteri identifica tutte le macchine virtuali create in questo ambiente che non usano i dischi gestiti.
6. Modificare il piano tariffario impostando **Standard** per garantire che i criteri vengano applicati alle risorse esistenti.

   Per Criteri di Azure esistono due piani tariffari, ovvero *Gratuito* e *Standard*. Con il piano Gratuito è possibile applicare i criteri solo alle risorse future, mentre con quello Standard è possibile applicarli anche a risorse esistenti per ottenere una migliore comprensione dello stato di conformità. Trattandosi di un'anteprima limitata, non è ancora stato rilasciato un modello di determinazione dei prezzi, quindi non si riceverà fattura selezionando *Standard*. Per altre informazioni sui prezzi, vedere [Prezzi di Criteri di Azure](https://acom-milestone-ignite.azurewebsites.net/pricing/details/azure-policy/).

7. Selezionare l'ambito di applicazione dei criteri in **Scope** (Ambito).  L'ambito determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri e può variare da una sottoscrizione a gruppi di risorse.
8. Selezionare la sottoscrizione (o il gruppo di risorse) registrato in precedenza al momento del consenso esplicito all'uso di Criteri di Azure. In questo esempio viene usata la sottoscrizione **Azure Analytics Capacity Dev**, ma le opzioni disponibili saranno diverse.

   ![Trovare la definizione di criteri corretta](media/assign-policy-definition/assign-policy.png)

9. Selezionare **Assegna**.

A questo punto si è pronti per identificare le risorse non conformi per comprendere lo stato di conformità dell'ambiente.

## <a name="identify-non-compliant-resources"></a>Identificare le risorse non conformi

Selezionare **Compliance** (Conformità) nel riquadro sinistro e cercare l'assegnazione di criteri creata.

![Conformità ai criteri](media/assign-policy-definition/policy-compliance.png)

Le eventuali risorse esistenti non conformi a questa nuova assegnazione verranno visualizzate nella scheda **Non-compliant resources** (Risorse non conformi).

Se viene valutata una condizione per le risorse esistenti e tale condizione risulta soddisfatta per alcune di esse, tali risorse vengono contrassegnate come non conformi ai criteri. Di seguito è riportata una tabella del funzionamento delle diverse azioni attualmente disponibili con il risultato della valutazione della condizione e lo stato di conformità delle risorse.

|Risorsa  |Se la condizione nei criteri risulta  |Azione nei criteri   |Stato di conformità  |
|-----------|---------|---------|---------|
|Exists     |True      |NEGA     |Non conforme |
|Exists     |False    |NEGA     |Conforme     |
|Exists     |True      |Append   |Non conforme |
|Exists     |False    |Append   |Conforme     |
|Exists     |True      |Audit    |Non conforme |
|Exists     |False    |Audit    |Non conforme |

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide in questa raccolta si basano su questa guida introduttiva. Se si prevede di continuare a usare le esercitazioni successive, non eliminare le risorse create in questa guida introduttiva. Se non si prevede di continuare, seguire questa procedura per eliminare tutte le risorse create da questa guida di avvio rapido nel portale di Azure.
1. Selezionare **Assegnazioni** nel riquadro a sinistra.
2. Cercare l'assegnazione appena creata.

   ![Eliminare un'assegnazione](media/assign-policy-definition/delete-assignment.png)

3.  Selezionare **Delete Assignment** (Elimina assegnazione).

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato spiegato come assegnare una definizione di criteri a un ambito per assicurarsi che tutte le risorse in tale ambito siano conformi e per identificare quelle che non lo sono.

Per altre informazioni sull'assegnazione di criteri per assicurarsi che le risorse **future** create siano conformi, continuare con l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Creazione e gestione dei criteri](./create-manage-policy.md)
