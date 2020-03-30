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
ms.openlocfilehash: ee6ee600160349548a0e886131f1a29b90de8746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275884"
---
Durante la pubblicazione dell'offerta sul marketplace tramite il Centro per i partner, è necessario collegare l'offerta al sistema CRM (Customer Relationship Management) in modo da poter ricevere le informazioni di contatto dei clienti subito dopo che un cliente esprime interesse o distribuisce il prodotto.

1. **Selezionare una destinazione lead in cui si desidera inviare lead clienti.** Sono supportati i sistemi CRM seguenti:

    * [Dynamics 365](../commercial-marketplace-lead-management-instructions-dynamics.md) per coinvolgimento dei clienti
    * [Marketo](../commercial-marketplace-lead-management-instructions-marketo.md)
    * [Salesforce](../commercial-marketplace-lead-management-instructions-salesforce.md)

    Se il sistema CRM non è supportato in modo esplicito nell'elenco precedente, sono disponibili le opzioni seguenti che consentono di archiviare i dati dei lead del cliente e quindi è possibile esportare o importare tali dati nel sistema CRM.

    * [Tabella di AzureAzure Table](../commercial-marketplace-lead-management-instructions-azure-table.md)
    * [Https Endpoint](../commercial-marketplace-lead-management-instructions-https.md)

2. Per informazioni su come configurare la destinazione per la ricezione di lead dall'offerta del marketplace, vedere la documentazione collegata riportata in precedenza. 
3. Connettere l'offerta alla destinazione dei lead durante la pubblicazione dell'offerta sul marketplace nel Centro per i partner. Per informazioni su come eseguire questa operazione, vedere la documentazione collegata in precedenza.
4. Verificare che la connessione alla destinazione dei lead sia configurata correttamente. Dopo aver configurato correttamente la destinazione di lead e aver premuto Pubblica sull'offerta nel Centro per i partner, convalideremo la connessione e ti invieremo un responsabile di test. Quando si visualizza l'offerta prima della distribuzione, è inoltre possibile testare la connessione lead quando si tenta di acquisire l'offerta nell'ambiente di anteprima. 
5. Assicurati che la connessione alla destinazione lead rimanga aggiornata in modo da non perdere alcun lead, quindi assicurati di aggiornare queste connessioni ogni volta che qualcosa è cambiato da una parte dell'altra.
