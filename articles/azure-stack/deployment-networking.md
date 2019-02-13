---
title: Traffico di rete di Azure Stack deployment | Microsoft Docs
description: Questo articolo descrive cosa accade sui processi rete di distribuzione Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: 5f4f76f87718ddcc81f8fae8b043b73a4dbd6b0a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189277"
---
# <a name="about-deployment-network-traffic"></a>Sul traffico di rete di distribuzione
Comprendere il flusso del traffico di rete Azure Stack durante la distribuzione è fondamentale per garantire una corretta distribuzione. Questo articolo illustra il traffico di rete previsto durante il processo di distribuzione per consentire la comprensione di cosa aspettarsi.

Questa illustrazione mostra tutti i componenti e connessioni coinvolti nel processo di distribuzione:

![Topologia di rete di Azure Stack deployment](media/deployment-networking/figure1.png)

> [!NOTE]
> Questo articolo descrive i requisiti per una distribuzione connesso, per informazioni su altri metodi di distribuzione, vedere [modelli di Azure Stack distribuzione connessione](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>La distribuzione della macchina virtuale
La soluzione di Azure Stack include un gruppo di server usati per ospitare i componenti di Azure Stack e un server aggiuntivo chiamato l'Hardware del ciclo di vita Host (HLH). Questo server viene usato per distribuire e gestire il ciclo di vita della soluzione e ospita la distribuzione della macchina virtuale DVM () durante la distribuzione.

## <a name="deployment-requirements"></a>Requisiti di distribuzione
Prima di avvio della distribuzione, esistono alcuni requisiti minimi che possono essere convalidati dall'OEM per garantire la distribuzione viene completata correttamente. Comprendere che i requisiti consentono di preparare l'ambiente e assicurarsi che la convalida ha esito positivo, questi sono:

-   [Certificati](azure-stack-pki-certs.md)
-   [Sottoscrizione di Azure](https://azure.microsoft.com/free/?b=17.06)
-   Accesso a Internet
-   DNS
-   NTP

> [!NOTE]
> Questo articolo è incentrato sui requisiti di ultimi tre. Per altre informazioni sui primi due, vedere i collegamenti riportati sopra.

## <a name="deployment-network-traffic"></a>Traffico di rete di distribuzione
Di DVM è configurato con un indirizzo IP dalla rete BMC e richiede l'accesso di rete a internet. Anche se non tutti i componenti di rete BMC richiedono routing esterno o l'accesso a Internet, alcuni componenti di specifica dell'OEM che usano indirizzi IP da questa rete potrebbero inoltre richiedere.

Durante la distribuzione, di DVM effettua l'autenticazione con Azure Active Directory (Azure AD) usando un account Azure dall'abbonamento. Per eseguire questa operazione, il DVM richiede l'accesso a internet per un elenco di specifiche porte e URL. È possibile trovare l'elenco completo nel [pubblicare endpoint](azure-stack-integrate-endpoints.md) documentazione. Di DVM userà un server DNS per inoltrare le richieste DNS effettuate dai componenti interni agli URL esterni. Il DNS interno inoltra le richieste per l'indirizzo del server d'inoltro DNS che viene fornito a OEM prima della distribuzione. Lo stesso vale per il server NTP, un Server di riferimento ora affidabile è necessario per mantenere la coerenza e l'ora di sincronizzazione per tutti i componenti di Azure Stack.

L'accesso a internet necessario per il DVM durante la distribuzione è solo in uscita, non le chiamate in ingresso vengono eseguite durante la distribuzione. Tenere presente che usa relativo indirizzo IP come origine e che Azure Stack non supporta configurazioni proxy. Pertanto, se necessario, è necessario specificare un proxy trasparente o NAT per l'accesso a internet. Durante la distribuzione, alcuni componenti interni inizierà l'accesso a internet tramite la rete esterna usando gli indirizzi VIP pubblici. Al termine della distribuzione, tutte le comunicazioni tra Azure e Azure Stack viene effettuata tramite la rete esterna usando gli indirizzi VIP pubblici.

Le configurazioni di rete in Azure Stack commutatori contengono Access Control Lists (ACL) che limitano il traffico tra determinati origini di rete e le destinazioni. Di DVM è l'unico componente con un accesso senza restrizioni; anche il HLH è molto limitato. È possibile chiedere l'OEM sulle opzioni di personalizzazione per semplificare la gestione e l'accesso dalle reti. A causa di questi elenchi, è importante evitare di modificare gli indirizzi di server DNS e NTP in fase di distribuzione. Se in questo caso, è necessario riconfigurare tutte le opzioni per la soluzione.

Una volta completata la distribuzione, gli indirizzi di server DNS e NTP forniti continuerà a essere usata direttamente dai componenti del sistema. Ad esempio, se si archiviano le richieste DNS dopo la distribuzione è completata, l'origine cambierà dall'indirizzo IP di DVM a un indirizzo dell'intervallo di rete esterna.

Una volta completata la distribuzione, gli indirizzi di server DNS e NTP forniti continuerà a essere usato dai componenti del sistema attraverso la rete SDN usando la rete esterna. Ad esempio, se si archiviano le richieste DNS dopo la distribuzione è completata, l'origine cambierà dallo DVM IP a un indirizzo VIP pubblico.

## <a name="next-steps"></a>Passaggi successivi
[Convalidare la registrazione di Azure](azure-stack-validate-registration.md)
