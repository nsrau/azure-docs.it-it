---
title: File di inclusione
description: File di inclusione
documentationcenter: partner-center-commercial-marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 08/16/2019
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: cf6a5730f92eba8f0a4e63bf40a4d2b50f76eb3a
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131110"
---
Quando si pubblica l'offerta nel Marketplace tramite il centro per i partner, è necessario connettere l'offerta al sistema CRM. In questo modo, è possibile ricevere le informazioni di contatto del cliente immediatamente dopo che un cliente esprime interesse o distribuisce il prodotto.

1. Selezionare la destinazione a cui si desidera che vengano inviati i lead. Sono supportati i sistemi CRM seguenti:

    * [Dynamics 365](../commercial-marketplace-lead-management-instructions-dynamics.md) per il coinvolgimento dei clienti
    * [Marketo](../commercial-marketplace-lead-management-instructions-marketo.md)
    * [Salesforce](../commercial-marketplace-lead-management-instructions-salesforce.md)

    Se il sistema CRM non è supportato in modo esplicito in questo elenco, è possibile archiviare i dati del cliente principale usando le opzioni seguenti. È quindi possibile esportare o importare questi dati nel sistema CRM.

    * [Tabella di Azure](../commercial-marketplace-lead-management-instructions-azure-table.md)
    * [Endpoint HTTPS](../commercial-marketplace-lead-management-instructions-https.md)

2. Per informazioni su come configurare la destinazione principale per ricevere lead dall'offerta del Marketplace, vedere la documentazione collegata per la destinazione selezionata.
3. Connettere l'offerta alla destinazione principale quando si pubblica l'offerta nel Marketplace nel centro per i partner. Per informazioni su come eseguire questa operazione, vedere la documentazione collegata.
4. Verificare che la connessione alla destinazione principale sia configurata correttamente. Dopo aver configurato correttamente la destinazione del lead, selezionare **pubblica** nell'offerta nel centro per i partner. Si convaliderà quindi la connessione e si invierà un lead di test. Quando si visualizza l'offerta prima di iniziare, è anche possibile testare la connessione del lead provando ad acquisire l'offerta nell'ambiente di anteprima.
5. Assicurarsi che la connessione alla destinazione lead rimanga aggiornata in modo da non perdere i lead. Assicurarsi di aggiornare queste connessioni ogni volta che viene apportata una modifica alla fine.
