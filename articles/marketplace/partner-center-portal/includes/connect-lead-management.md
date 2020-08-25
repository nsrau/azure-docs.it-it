---
title: includere file
description: includere file
documentationcenter: partner-center-commercial-marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 08/24/2020
ms.custom: include file
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 7709f26b30d41b932569227b8b6f05b5725dc98f
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815666"
---
Quando si pubblica l'offerta nel Marketplace commerciale tramite il centro per i partner, è necessario connettere l'offerta al sistema CRM. In questo modo, è possibile ricevere le informazioni di contatto del cliente immediatamente dopo che un cliente esprime interesse o distribuisce il prodotto.

1. Selezionare la destinazione a cui si desidera che vengano inviati i lead. Sono supportati i sistemi CRM seguenti:

    * [Dynamics 365](../commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    * [Marketo](../commercial-marketplace-lead-management-instructions-marketo.md)
    * [Salesforce](../commercial-marketplace-lead-management-instructions-salesforce.md)

    Se il sistema CRM non è supportato in modo esplicito in questo elenco, è possibile archiviare i dati del cliente principale usando le opzioni seguenti. È quindi possibile esportare o importare questi dati nel sistema CRM.

    * [Tabella di Azure](../commercial-marketplace-lead-management-instructions-azure-table.md)
    * [Endpoint HTTPS](../commercial-marketplace-lead-management-instructions-https.md)

1. Per informazioni su come configurare la destinazione lead e ricevere lead dalle offerte del Marketplace commerciale, vedere la documentazione collegata applicabile.
1. Dopo aver connesso l'offerta alla destinazione principale, selezionare **pubblica** nell'offerta nel centro per i partner. La connessione verrà convalidata e verrà inviato un lead di test. Quando si visualizza l'offerta prima di iniziare, è anche possibile testare la connessione del lead provando ad acquisire l'offerta nell'ambiente di anteprima.
1. Assicurarsi che la connessione alla destinazione lead rimanga aggiornata in modo da non perdere i lead. Assicurarsi di aggiornare queste connessioni ogni volta che viene modificato un elemento.
