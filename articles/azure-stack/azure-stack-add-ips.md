---
title: Aggiungere gli indirizzi IP pubblici in Azure Stack | Microsoft Docs
description: Informazioni su come aggiungere ulteriori indirizzi IP pubblici in Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2019
ms.author: jeffgilb
ms.reviewer: scottnap
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 09805719262f0a1d30f3b38af4b5209667d25e5a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195371"
---
# <a name="add-public-ip-addresses"></a>Aggiungere gli indirizzi IP pubblici
*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*  

Informazioni su come aggiungere ulteriori indirizzi IP pubblici in Azure Stack.  In questo articolo si fa riferimento agli indirizzi esterni come indirizzi IP pubblici, ma in Azure Stack si fa riferimento all'aggiunta di blocchi di indirizzi IP alla rete esterna.  Se tale rete esterna è instradabili a Internet pubblico o si trova in una rete Intranet e utilizza spazio di indirizzi privato non è veramente importante ai fini di questo articolo.  I passaggi sono gli stessi. 

## <a name="add-a-public-ip-address-pool"></a>Aggiungere un Pool di indirizzi IP pubblici
È possibile aggiungere gli indirizzi IP pubblici al sistema di Azure Stack in qualsiasi momento dopo la distribuzione iniziale del sistema Azure Stack. Scoprire come eseguire [consumo di indirizzi IP pubblici visualizzazione](azure-stack-viewing-public-ip-address-consumption.md) per vedere quali l'utilizzo corrente e indirizzo IP pubblico è la disponibilità in Azure Stack.

A livello generale, il processo di aggiunta di un nuovo blocco di indirizzi IP pubblici in Azure Stack è simile al seguente:

 ![Aggiungi flusso IP](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>Ottenere il blocco di indirizzi dal proprio provider
Innanzitutto, occorre eseguire consiste nell'ottenere il blocco di indirizzi da aggiungere ad Azure Stack.  A seconda di dove si ottiene il blocco di indirizzi da, è necessario considerare ciò che è il lead time e gestiscono questa operazione con la frequenza con cui si utilizzano indirizzi IP pubblici in Azure Stack.  

> [!IMPORTANT]
> Azure Stack accetterà qualsiasi blocco di indirizzi che fornisce, purché è un blocco di indirizzi valido e non si sovrapponga con un intervallo di indirizzi esistente in Azure Stack.  Assicurarsi di che ottenere un blocco di indirizzi validi che è instradabile e non sovrapposti con la rete esterna a cui è connesso Azure Stack.  Dopo aver aggiunto l'intervallo in Azure Stack, è possibile rimuoverlo.

## <a name="add-the-ip-address-range-to-azure-stack"></a>Aggiungere l'intervallo di indirizzi IP in Azure Stack

1. In un browser Internet passare al dashboard del portale di amministrazione.  In questo esempio si userà https://adminportal.local.azurestack.external.  
2.  Accedere al portale di amministrazione di Azure Stack come operatore di cloud.
3.  Nel dashboard predefinito: trovare l'elenco di gestione di area e selezionare l'area in cui che si desidera gestire, in questo esempio locali.
4.  Trovare i provider di risorse riquadro e fare clic sul Provider di risorse di rete.
5.  Riquadro utilizzo del pool di fare clic sull'indirizzo IP pubblico.
6.  Fare clic sul pulsante Aggiungi indirizzo IP del pool.
7.  Specificare un nome per il Pool IP.  Il nome che scelto è solo per consentire di identificare facilmente il pool IP in modo che è possibile assegnare qualsiasi.  È buona norma verificare il nome di quello utilizzato per l'intervallo di indirizzi, ma che non è obbligatorio.
8.   Immettere il blocco di indirizzi da aggiungere nella notazione CIDR.  Ad esempio:  192.168.203.0/24
9.  Quando si specifica un intervallo CIDR valido nel campo indirizzo di intervallo (blocco CIDR) l'indirizzo IP iniziale, indirizzo IP finale e i campi di indirizzi IP disponibili verranno popolati automaticamente.  Sono di sola lettura e generati automaticamente in modo che non è possibile modificare queste senza modificare il valore nel campo intervallo dell'indirizzo.
10. Dopo aver esaminato le informazioni sul pannello e confermare tutti i dati sono correggere, fare clic su Ok per eseguire il commit della modifica e aggiungere l'intervallo di indirizzi per Azure Stack.


## <a name="next-steps"></a>Passaggi successivi 
[Esaminare le azioni nodo unità di scala](azure-stack-node-actions.md) 
