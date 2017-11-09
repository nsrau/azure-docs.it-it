---
title: Usare l'interfaccia della riga di comando di Azure per creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure | Microsoft Docs
description: Usare PowerShell per creare un'assegnazione di criteri di Azure per identificare le risorse non conformi nell'ambiente.
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 10/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 92b532691986e72eca68d9bc3033e20ff8ffef3b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure usando l'interfaccia della riga di comando di Azure

Il primo passaggio per ottenere informazioni sulla conformità in Azure è sapere qual è lo stato delle risorse correnti. Questa guida introduttiva illustra il processo di creazione di un'assegnazione di criteri per identificare le risorse non conformi alla definizione del criterio - *Richiedere SQL Server versione 12.0*. Alla fine di questo processo saranno stati identificati i server con una versione diversa e pertanto non conformi.

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida descrive l'uso dell'interfaccia della riga di comando di Azure per creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).
 
## <a name="opt-in-to-azure-policy"></a>Consenso esplicito per Criteri di Azure

Criteri di Azure è attualmente disponibile in anteprima limitata, quindi è necessario registrarsi per richiedere l'accesso.

1. Passare a Criteri di Azure all'indirizzo https://aka.ms/getpolicy e selezionare **Iscrizione** nel riquadro a sinistra.

   ![Ricerca di criteri](media/assign-policy-definition/sign-up.png)

2. Acconsentire esplicitamente all'uso di Criteri di Azure selezionando le sottoscrizioni che si vuole usare nell'elenco **Sottoscrizione**. Selezionare quindi **Registra**.

   ![Consenso esplicito per l'uso di Criteri di Azure](media/assign-policy-definition/preview-opt-in.png)

   Potrebbero trascorrere un paio di giorni prima che Microsoft accetti la richiesta di registrazione, a seconda della domanda. Dopo l'accettazione della richiesta si riceverà conferma tramite posta elettronica che è possibile iniziare a usare il servizio.

## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

In questa guida introduttiva viene descritta la procedura per creare un'assegnazione di criteri e assegnare la definizione Richiedere SQL Server versione 12.0. Questa definizione di criteri identifica le risorse che non rispettano le condizioni impostate nella definizione dei criteri.

Per creare una nuova assegnazione di criteri, attenersi alla procedura seguente.

Visualizzare tutte le definizioni dei criteri e individuare la definizione Richiedere SQL Server versione 12.0.

```azurecli
az policy definition list
```

Criteri di Azure include definizioni di criteri predefinite che è possibile usare. Sono disponibili definizioni di criteri predefinite come le seguenti:

- Imporre un tag e il relativo valore
- Applicare un tag e il relativo valore
- Richiedere SQL Server versione 12.0

Fornire le informazioni seguenti, quindi eseguire il comando seguente per assegnare la definizione dei criteri:

- **Nome** visualizzato per l'assegnazione del criterio. In questo caso è possibile usare l'assegnazione *Richiedere SQL Server versione 12.0*.
- **Policy** (Criterio): si tratta della definizione del criterio, in base a quello che si sta usando per creare l'assegnazione. In questo caso, è la definizione del criterio: *Richiedere SQL Server versione 12.0*.
- Un **ambito**: determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di un criterio e può variare da una sottoscrizione a gruppi di risorse.

  Usare la sottoscrizione (o gruppo di risorse) registrata in precedenza quando è stato dato il consenso esplicito all'uso di Criteri di Azure. In questo esempio viene usato l'ID di sottoscrizione **bc75htn-a0fhsi-349b-56gh-4fghti-f84852** e il nome del gruppo di risorse: **FabrikamOMS**. Assicurarsi di sostituire queste informazioni con l'ID della sottoscrizione e il nome del gruppo di risorse in uso. 

L'output di questo comando dovrebbe essere simile al seguente:

```azurecli
az policy assignment create --name Require SQL Server version 12.0 Assignment --policy Require SQL Server version 12.0 --scope /subscriptions/ 
bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

Un'assegnazione di criterio è un criterio che è stato assegnato per l'implementazione in un ambito specifico. L'ambito può anche comprendere da un gruppo di gestione a un gruppo di risorse.

## <a name="identify-non-compliant-resources"></a>Identificare le risorse non conformi

Per visualizzare le risorse che non sono conformi a questa nuova assegnazione:

1. Ritornare alla pagina Criteri di Azure.
2. Selezionare **Conformità** nel riquadro a sinistra e cercare l'**Assegnazione criteri** creata.

   ![Conformità ai criteri](media/assign-policy-definition/policy-compliance.png)

   Le eventuali risorse esistenti non conformi a questa nuova assegnazione verranno visualizzate nella scheda **Risorse non conformi**.

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide di questa raccolta si basano sulla presente guida di avvio rapido. Se si prevede di continuare a usare le esercitazioni successive, non eliminare le risorse create in questa guida introduttiva. Se non si prevede di continuare, eliminare l'assegnazione creata eseguendo questo comando:

```azurecli
az policy assignment delete –name Require SQL Server version 12.0 Assignment --scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852 resourceGroups/ FabrikamOMS
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata assegnata una definizione dei criteri per identificare le risorse non conformi nell'ambiente Azure.

Per altre informazioni sull'assegnazione di criteri per assicurarsi che le risorse create in **futuro** siano conformi, proseguire con l'esercitazione:

> [!div class="nextstepaction"]
> [Creazione e gestione dei criteri](./create-manage-policy.md)

