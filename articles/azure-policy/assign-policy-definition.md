---
title: Creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure | Microsoft Docs
description: Questo articolo illustra i passaggi per creare una definizione dei criteri per identificare le risorse non conformi.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/18/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 6bb9eddb6a663e1f230c9c46835661ad20c02cfd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>Creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure
Il primo passaggio per comprendere la conformità in Azure consiste nell'identificare lo stato delle risorse. Questa guida introduttiva illustra il processo di creazione di un'assegnazione criteri per identificare le macchine virtuali che non usano dischi gestiti.

Alla fine di questo processo, sarà possibile identificare le macchine virtuali che non usano Managed Disks. Tali macchine sono *non conformi* all'assegnazione dei criteri.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

In questa guida introduttiva si crea un'assegnazione dei criteri e si assegna la definizione dei criteri *Audit Virtual Machines without Managed Disks* (Controlla macchine virtuali senza Managed Disks).

1. Selezionare **Assegnazioni**nel riquadro a sinistra della pagina Criteri di Azure.
2. Selezionare **Assign Policy** (Assegna criterio) nella parte superiore del riquadro **Assignments** (Assegnazioni).

   ![Assegnare una definizione di criteri](media/assign-policy-definition/select-assign-policy.png)

3. Nella pagina **Assegna criterio** fare clic sul ![pulsante per la definizione dei criteri](media/assign-policy-definition/definitions-button.png) accanto al campo **Policy** (Criterio) per aprire l'elenco delle definizioni disponibili.

   ![Aprire le definizioni di criteri disponibili](media/assign-policy-definition/open-policy-definitions.png)

   Criteri di Azure include definizioni di criteri predefinite che è possibile usare. Sono disponibili definizioni di criteri predefinite come le seguenti:

   - Enforce tag and its value (Imporre un tag e il relativo valore)
   - Applicare un tag e il relativo valore
   - Richiedere SQL Server versione 12.0

    Per un elenco completo di tutti i criteri predefiniti disponibili, vedere [Modelli di criteri](json-samples.md).

4. Cercare tra le definizioni di criteri per trovare la definizione *Audit VMs that do not use managed disks* (Controllare le macchine virtuali che non usano i dischi gestiti). Fare clic su tale criterio e fare clic su **Select** (Seleziona).

   ![Trovare la definizione di criteri corretta](media/assign-policy-definition/select-available-definition.png)

5. Specificare un nome visualizzato per l'assegnazione del criterio in **Nome**. In questo caso è possibile usare *Audit VMs that do not use managed disks* (Controllare le macchine virtuali che non usano i dischi gestiti). È anche possibile aggiungere una **descrizione** facoltativa. La descrizione fornisce informazioni dettagliate su come l'assegnazione dei criteri identifica tutte le macchine virtuali che non usano Managed Disks.
6. Modificare il piano tariffario impostando **Standard** per garantire che i criteri vengano applicati alle risorse esistenti.

   Per Criteri di Azure esistono due piani tariffari, ovvero *Gratuito* e *Standard*. Con il piano Gratuito è possibile applicare i criteri solo alle risorse future, mentre con quello Standard è possibile applicarli anche a risorse esistenti per ottenere una migliore comprensione dello stato di conformità. Per altre informazioni sui prezzi, vedere [Prezzi di Criteri di Azure](https://azure.microsoft.com/pricing/details/azure-policy/).

7. Selezionare l'ambito di applicazione dei criteri in **Scope** (Ambito).  L'ambito determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri e può variare da una sottoscrizione a gruppi di risorse.
8. Selezionare l'abbonamento (o gruppo di risorse) registrato in precedenza. In questo esempio viene usata la sottoscrizione **Azure Analytics Capacity Dev**, ma le opzioni possono essere diverse. Fare clic su **Seleziona**.

   ![Trovare la definizione di criteri corretta](media/assign-policy-definition/assign-policy.png)

9. Per il momento lasciare vuota la casella **Esclusioni** e fare clic su **Assegna**.

A questo punto si è pronti per identificare le risorse non conformi per comprendere lo stato di conformità dell'ambiente.

## <a name="identify-non-compliant-resources"></a>Identificare le risorse non conformi

Selezionare **Compliance** (Conformità) nel riquadro sinistro e cercare l'assegnazione di criteri creata.

![Conformità ai criteri](media/assign-policy-definition/policy-compliance.png)

Le eventuali risorse esistenti non conformi a questa nuova assegnazione verranno visualizzate nella scheda **Non-compliant resources** (Risorse non conformi).

Quando viene valutata una condizione per le risorse esistenti e tale condizione risulta soddisfatta, le risorse vengono contrassegnate come non conformi ai criteri. L'immagine di esempio precedente mostra le risorse non conformi. La tabella seguente illustra il funzionamento delle diverse azioni dei criteri in base alla valutazione della condizione per lo stato di conformità risultante. Anche se nel portale di Azure non viene visualizzata la logica di valutazione, sono visualizzati i risultati relativi allo stato di conformità. Lo stato di conformità può risultare conforme o non conforme.

| **Stato della risorsa** | **Azione** | **Valutazione dei criteri** | **Stato di conformità** |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True  | Non conforme |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Conforme |
| Nuovo | Audit, AuditIfNotExist\* | True  | Non conforme |
| Nuovo | Audit, AuditIfNotExist\* | False | Conforme |

\* Per le azioni Append, DeployIfNotExist e AuditIfNotExist l'istruzione IF deve essere TRUE. Per non essere conformi, è anche necessario che la condizione di esistenza per le azioni sia FALSE. Se è TRUE, la condizione IF attiva la valutazione della condizione di esistenza per le risorse correlate.
## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide in questa raccolta si basano su questa guida introduttiva. Se si prevede di continuare a usare le esercitazioni successive, non eliminare le risorse create in questa guida introduttiva. Se non si prevede di continuare, seguire questa procedura per eliminare tutte le risorse create da questa guida di avvio rapido nel portale di Azure.
1. Selezionare **Assegnazioni** nel riquadro a sinistra.
2. Cercare l'assegnazione creata e quindi fare clic su di essa con il pulsante destro del mouse.

   ![Eliminare un'assegnazione](media/assign-policy-definition/delete-assignment.png)

3.  Selezionare **Delete Assignment** (Elimina assegnazione).

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata assegnata una definizione dei criteri a un ambito. La definizione dei criteri garantisce che tutte le risorse nell'ambito siano conformi e identifica quelle che non lo sono.

Per altre informazioni sull'assegnazione di criteri per assicurarsi che le risorse **future** create siano conformi, continuare con l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Creazione e gestione dei criteri](./create-manage-policy.md)
