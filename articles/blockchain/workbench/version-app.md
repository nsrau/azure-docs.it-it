---
title: Controllo delle versioni di un'applicazione blockchain in Azure Blockchain Workbench
description: Come usare le versioni dell'applicazione in Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 63f18e3ee316b9791bb62bfcd20c07a30cbebb5e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60896863"
---
# <a name="azure-blockchain-workbench-application-versioning"></a>Controllo delle versioni dell'applicazione Azure Blockchain Workbench

È possibile creare e usare più versioni di un'app Azure Blockchain Workbench. Se vengono caricate più versioni della stessa applicazione, è disponibile una cronologia delle versioni e gli utenti possono scegliere la versione da usare.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Una distribuzione di Blockchain Workbench. Per altre informazioni sulla distribuzione, vedere [Distribuzione di Azure Blockchain Workbench](deploy.md)
* Un'applicazione blockchain distribuita in Blockchain Workbench. Vedere [Creare un'applicazione blockchain in Azure Blockchain Workbench](create-app.md)

## <a name="add-an-app-version"></a>Aggiungere una versione dell'app

Per aggiungere una nuova versione, caricare i nuovi file di configurazione e del contratto intelligente in Blockchain Workbench.

1. In un Web browser passare all'indirizzo Web di Blockchain Workbench. Ad esempio, `https://{workbench URL}.azurewebsites.net/` Per informazioni su come trovare l'indirizzo Web di Blockchain Workbench, vedere [URL Web di Blockchain Workbench](deploy.md#blockchain-workbench-web-url)
2. Eseguire l'accesso come [amministratore di Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Selezionare l'applicazione blockchain che si vuole aggiornare con un'altra versione.
4. Selezionare **Aggiungi versione**. Viene visualizzato il riquadro **Aggiungi versione**.
5. Scegliere i nuovi file di configurazione del contratto della versione e di codice del contratto da caricare. Il file di configurazione viene convalidato automaticamente. Correggere eventuali errori di convalida prima di distribuire l'applicazione.
6. Selezionare **Aggiungi versione** per aggiungere la nuova versione dell'applicazione blockchain.

    ![Aggiungere una nuova versione](media/version-app/add-version.png)

La distribuzione dell'applicazione blockchain può richiedere alcuni minuti. Al termine della distribuzione, aggiornare la pagina dell'applicazione. Scegliendo l'applicazione e selezionando il pulsante **Cronologia versioni**, viene visualizzata la cronologia delle versioni dell'applicazione.

> [!IMPORTANT]
> Le versioni precedenti dell'applicazione sono disabilitate. È possibile riabilitare singolarmente le versioni precedenti.
>
> Potrebbe essere necessario aggiungere nuovamente i membri ai ruoli applicazione se sono state apportate modifiche ai ruoli applicazione nella nuova versione.

## <a name="using-app-versions"></a>Uso delle versioni dell'app

Per impostazione predefinita, in Blockchain Workbench viene usata la versione più recente abilitata dell'applicazione. Per usare una versione precedente di un'applicazione, è necessario scegliere prima di tutto la versione dalla pagina dell'applicazione.

1. Nella sezione dell'applicazione Blockchain Workbench selezionare la casella di controllo dell'applicazione che contiene il contratto che si vuole usare. Se sono abilitate versioni precedenti, il pulsante della cronologia delle versioni è disponibile.
2. Selezionare il pulsante **Cronologia versioni**.
3. Nel riquadro Cronologia versioni scegliere la versione dell'applicazione selezionando il collegamento nella colonna *Ultima modifica*.

    ![Scegliere una versione precedente](media/version-app/use-version.png)

    È possibile creare nuovi contratti o eseguire azioni sui contratti delle versioni precedenti. La versione dell'applicazione viene visualizzata dopo il nome dell'applicazione e viene visualizzato un avviso sulla versione precedente.

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi relativi ad Azure Blockchain Workbench](troubleshooting.md)
