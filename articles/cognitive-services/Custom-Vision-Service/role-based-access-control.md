---
title: Controllo degli accessi in base al ruolo-Visione personalizzata
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come configurare il controllo degli accessi in base al ruolo per i progetti Visione personalizzata.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: fc536b3b0c7af113919f274fc4bdd5fad63cbd06
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90057216"
---
# <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Visione personalizzata supporta il controllo degli accessi in base al ruolo di Azure (RBAC di Azure), un sistema di autorizzazione per la gestione dei singoli accessi alle risorse di Azure. Utilizzando il controllo degli accessi in base al ruolo, è possibile assegnare diversi livelli di autorizzazioni ai membri del team per i progetti Visione personalizzata Per ulteriori informazioni su RBAC, vedere la [documentazione relativa a RBAC di Azure](https://docs.microsoft.com/azure/role-based-access-control/).

## <a name="add-role-assignment-to-custom-vision-resource"></a>Aggiungere un'assegnazione di ruolo alla risorsa Visione personalizzata

Il controllo degli accessi in base al ruolo può essere assegnato a una risorsa Visione personalizzata. Per concedere l'accesso a una risorsa di Azure, è necessario aggiungere un'assegnazione di ruolo.
1. Nel [portale di Azure](https://ms.portal.azure.com/) selezionare **Tutti i servizi**. 
1. Selezionare quindi **Servizi cognitivi**e passare alla risorsa di training visione personalizzata specifica.
   > [!NOTE]
   > È anche possibile configurare il controllo degli accessi in base al ruolo per interi gruppi di risorse, sottoscrizioni o gruppi di gestione A tale scopo, selezionare il livello di ambito desiderato, quindi passare all'elemento desiderato, ad esempio selezionando **gruppi di risorse** e quindi facendo clic sul gruppo di risorse desiderato.
1. Selezionare **controllo di accesso (IAM)** nel riquadro di spostamento a sinistra.
1. Selezionare la scheda **assegnazioni di ruolo** per visualizzare le assegnazioni di ruolo per questo ambito.
1. Selezionare **Aggiungi**  ->  **assegnazione ruolo**.
1. Nell'elenco a discesa **ruolo** selezionare un ruolo che si desidera aggiungere.
1. Nell'elenco **Seleziona** selezionare un utente, gruppo, entità servizio o identità gestita. Se l'entità di sicurezza non viene visualizzata nell'elenco, è possibile digitare la casella Seleziona per cercare i nomi visualizzati, gli indirizzi di posta elettronica e gli identificatori di oggetto.
1. Selezionare **Salva** per assegnare il ruolo.

Entro pochi minuti, alla destinazione verrà assegnato il ruolo selezionato nell'ambito selezionato.

## <a name="custom-vision-role-types"></a>Tipi di ruolo Visione personalizzata

Usare la tabella seguente per determinare le esigenze di accesso per le risorse di Visione personalizzata.

|Ruolo  |Autorizzazioni  |
|---------|---------|
|`Cognitive Service Custom Vision Contributor`     | Accesso completo ai progetti, inclusa la possibilità di creare, modificare o eliminare un progetto.        |
|`Cognitive Service Custom Vision Trainer`     | Accesso completo ad eccezione della possibilità di creare o eliminare un progetto. I trainer possono visualizzare e modificare i progetti, eseguire il training, pubblicare, annullare la pubblicazione o esportare i modelli.        |
|`Cognitive Service Custom Vision Labeler`     | Possibilità di caricare, modificare o eliminare immagini di training e creare, aggiungere, rimuovere o eliminare tag. Gli etichettatori possono visualizzare i progetti ma non possono aggiornare nient'altro che i tag e le immagini di training.         |
|`Cognitive Service Custom Vision Deployment`     | Possibilità di pubblicare, annullare la pubblicazione o esportare i modelli. I deployer possono visualizzare i progetti ma non possono aggiornare un progetto, le immagini di training o i tag.        |
|`Cognitive Service Custom Vision Reader`     | Possibilità di visualizzare i progetti. I lettori non possono apportare modifiche.        |
