---
title: Creare un membro della blockchain del servizio Azure Blockchain usando il portale di Azure
description: Usare il servizio Azure Blockchain per creare un membro della blockchain.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 08/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 9f58379acd88fd93a00edc11b73b41dc3d6226a5
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241032"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Guida introduttiva: Creare un membro della blockchain del servizio Azure Blockchain usando il portale di Azure

Il servizio Azure Blockchain è una piattaforma per blockchain che consente di eseguire la logica di business in un contratto intelligente. Questo argomento di avvio rapido illustra come iniziare a usare il servizio creando un membro della blockchain tramite il portale di Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Creare un membro della blockchain

Creare un membro della blockchain che esegue il protocollo di libro mastro Quorum in un consorzio nuovo o esistente.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Crea risorsa** nell'angolo superiore sinistro del portale di Azure.
1. Selezionare **Blockchain** > **Servizio Azure Blockchain**.
1. Completare il modello.

    ![Creazione di un servizio](./media/create-member/create-member.png)

    Impostazione | DESCRIZIONE
    --------|------------
    Blockchain Member (Membro della blockchain) | Scegliere un nome univoco che identifica il membro della blockchain del servizio Azure Blockchain. Il nome del membro della blockchain può contenere solo lettere minuscole e numeri. Il primo carattere deve essere una lettera. Il valore deve avere una lunghezza compresa tra 2 e 20 caratteri.
    Subscription | Selezionare la sottoscrizione di Azure da usare per il servizio. Se si hanno più sottoscrizioni, scegliere quella in cui viene fatturata la risorsa.
    Resource group | Nuovo nome di gruppo di risorse o uno esistente nella sottoscrizione.
    Region | La località deve essere la stessa per tutti i membri del consorzio.
    Member account password (Password dell'account del membro) | La password dell'account del membro viene usata per crittografare la chiave privata per l'account Ethereum creato per il membro. L'account del membro account e la password dell'account del membro si usano per la gestione del consorzio.
    Nome del consorzio | Immettere un nome univoco per un nuovo consorzio. Se si accede a un consorzio tramite invito, il valore da immettere è il consorzio a cui si sta accedendo.
    DESCRIZIONE | Descrizione del consorzio.
    Protocollo |  La versione di anteprima supporta il protocollo Quorum.
    Prezzi | Configurazione del nodo per il nuovo servizio. Selezionare **Standard**. L'impostazione predefinita prevede 2 nodi di convalida e 1 nodo della transazione.
    Password del nodo della transazione | La password per il nodo della transazione predefinito del membro. Usare la password per l'autenticazione di base quando ci si connette all'endpoint pubblico del nodo della transazione predefinito per il membro della blockchain.

1. Selezionare **Create** (Crea) per effettuare il provisioning del servizio. Il provisioning richiede circa 10 minuti.
1. Per monitorare il processo di distribuzione, selezionare **Notifications** (Notifiche) sulla barra degli strumenti.
1. Dopo la distribuzione, passare al membro della blockchain.

Selezionare **Overview** (Panoramica), in cui è possibile visualizzare le informazioni di base sul servizio tra cui account del membro e indirizzo RootContract.

![Panoramica del membro della blockchain](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile usare il membro creato per l'esercitazione o la guida di avvio rapido successiva. Quando non sono più necessarie, è possibile eliminare le risorse eliminando il gruppo di risorse `myResourceGroup` creato dal servizio Azure Blockchain.

Per eliminare il gruppo di risorse:

1. Nel portale di Azure passare a **Gruppo di risorse** nel riquadro di spostamento a sinistra e selezionare il gruppo di risorse da eliminare.
2. Selezionare **Elimina gruppo di risorse**. Confermare l'eliminazione immettendo il nome del gruppo di risorse e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare MetaMask per connettersi e distribuire un contratto intelligente](connect-metamask.md)