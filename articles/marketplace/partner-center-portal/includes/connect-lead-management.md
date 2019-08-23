---
title: File di inclusione
description: File di inclusione
services: commercial marketplace
documentationcenter: partner-center-commercial-marketplace
author: qianw211
manager: evansma
editor: ''
ms.service: marketplace
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/16/2019
ms.author: v-qiwe
ms.custom: include file
ms.openlocfilehash: 43b551639fface699b1c0ec95500de8ea2089b26
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900116"
---
Durante la pubblicazione dell'offerta nel Marketplace tramite il centro per i partner, è necessario connettere l'offerta al sistema CRM (Customer Relationship Management) in modo che sia possibile ricevere le informazioni di contatto del cliente immediatamente dopo che un cliente ha espresso l'interesse o distribuisce il prodotto.

1. **Selezionare una destinazione principale in cui si vuole inviare i lead dei clienti**. Sono supportati i sistemi CRM seguenti:

    * [Dynamics 365](../commercial-marketplace-lead-management-instructions-dynamics.md) per il coinvolgimento dei clienti
    * [Marketo](../commercial-marketplace-lead-management-instructions-marketo.md)
    * [Salesforce](../commercial-marketplace-lead-management-instructions-salesforce.md)

    Se il sistema CRM non è supportato in modo esplicito nell'elenco precedente, sono disponibili le opzioni seguenti che consentono di archiviare i dati del cliente principale, quindi è possibile esportare o importare questi dati nel sistema CRM.

    * [Tabella di Azure](../commercial-marketplace-lead-management-instructions-azure-table.md)
    * [Endpoint HTTPS](../commercial-marketplace-lead-management-instructions-https.md)

2. Per informazioni su come configurare la destinazione principale per la ricezione di lead dall'offerta del Marketplace, vedere la documentazione collegata in precedenza alla destinazione principale selezionata. 
3. Connetti l'offerta alla destinazione principale durante la pubblicazione dell'offerta nel Marketplace nel centro per i partner. Per informazioni su come eseguire questa operazione, vedere la documentazione collegata in precedenza.
4. Verificare che la connessione alla destinazione principale sia configurata correttamente. Dopo aver configurato correttamente la destinazione del lead e aver fatto clic su pubblica sull'offerta nel centro per i partner, la connessione viene convalidata e viene inviato un lead di test. Quando si visualizza l'offerta prima della distribuzione, è inoltre possibile testare la connessione lead quando si tenta di acquisire l'offerta nell'ambiente di anteprima. 
5. Assicurarsi che la connessione alla destinazione principale rimanga aggiornata in modo da non perdere i lead, quindi assicurarsi di aggiornare queste connessioni ogni volta che un elemento viene modificato.
