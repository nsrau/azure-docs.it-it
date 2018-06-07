---
title: Traffico di rete di Azure Stack distribuzione | Documenti Microsoft
description: Questo articolo descrive cosa accade sui processi di rete di Azure Stack distribuzione.
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
ms.date: 05/21/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: b808875e66e867b84e2971c6a5bd031d108d003b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656142"
---
# <a name="about-deployment-network-traffic"></a>Sul traffico di rete di distribuzione
Informazioni sulle modalità di flussi di traffico di rete durante Azure Stack distribuzione è fondamentale per garantire una corretta distribuzione. Questo articolo viene illustrato il traffico di rete previsto durante il processo di distribuzione per consentire la comprensione di ciò che accade.

In questa illustrazione mostra tutti i componenti e le connessioni coinvolti nel processo di distribuzione:

![Topologia della rete di distribuzione Azure Stack](media/deployment-networking/figure1.png)

> [!NOTE]
> In questo articolo vengono descritti i requisiti per una distribuzione connesso, per informazioni su altri metodi di distribuzione, vedere [modelli di connessione di distribuzione Azure Stack](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>La distribuzione VM
La soluzione Azure Stack include un gruppo di server in cui vengono utilizzati per ospitare i componenti dello Stack di Azure e un server aggiuntivo denominato l'Hardware del ciclo di vita Host (HLH). Questo server viene utilizzato per distribuire e gestire il ciclo di vita della soluzione e ospita la distribuzione VM DVM () durante la distribuzione.

## <a name="deployment-requirements"></a>Requisiti di distribuzione
Prima di avvia la distribuzione, esistono alcuni requisiti minimi che possono essere convalidate dal proprio OEM per garantire la distribuzione viene completata correttamente. La comprensione di che questi requisiti consentono di preparare l'ambiente e assicurarsi che la convalida ha esito positivo, questi sono:

-   [Certificati](azure-stack-pki-certs.md)
-   [Sottoscrizione di Azure](https://azure.microsoft.com/free/?b=17.06)
-   Accesso a Internet
-   DNS
-   NTP

> [!NOTE]
> Questo articolo è incentrato sui requisiti di ultime tre. Per ulteriori informazioni sulle prime due, vedere i collegamenti riportati sopra.

## <a name="deployment-network-traffic"></a>Traffico di rete di distribuzione
Il DVM è configurato con un indirizzo IP dalla rete BMC e richiede l'accesso alla rete a internet. Anche se non tutti i componenti di rete BMC richiede routing esterno o l'accesso a Internet, alcuni componenti specifico dell'OEM utilizzando gli indirizzi IP da questa rete potrebbero inoltre richiedere.

Durante la distribuzione, di DVM autentica in Azure Active Directory (Azure AD) utilizzando un account Azure dalla sottoscrizione. Per eseguire questa operazione, il DVM richiede l'accesso a internet per un elenco di URL e porta specifica. È possibile trovare l'elenco completo nel [pubblicare endpoint](azure-stack-integrate-endpoints.md) documentazione. Il DVM utilizzerà un server DNS per inoltrare le richieste DNS effettuate dai componenti interni a URL esterni. Il DNS interno inoltra le richieste per l'indirizzo del server d'inoltro DNS fornito a OEM prima della distribuzione. Lo stesso vale per il server NTP, è necessario per mantenere la coerenza e l'ora di sincronizzazione per tutti i componenti dello Stack di Azure un Server ora affidabile.

L'accesso Internet richiesto di DVM durante la distribuzione è in uscita, nessun chiamate in ingresso vengono eseguite durante la distribuzione. Tenere presente che il relativo IP viene utilizzato come origine e Stack di Azure non supporta le configurazioni di proxy. Pertanto, se necessario, è necessario specificare un proxy trasparente o NAT per accedere a internet. Al termine della distribuzione, tutte le comunicazioni tra Azure e Azure Stack sono eseguite tramite la rete esterna tramite VIP pubblici.

Configurazioni di rete in Azure Stack commutatori contengono ACL () che limitano il traffico tra determinate origini di rete e le destinazioni. Il DVM è l'unico componente con accesso senza restrizioni; anche il HLH è molto limitato. È possibile chiedere l'OEM sulle opzioni di personalizzazione per facilitare la gestione e l'accesso dalla rete. A causa di questi ACL, è importante evitare di modificare gli indirizzi di server DNS e NTP in fase di distribuzione. Se esegue questa operazione, sarà necessario riconfigurare tutte le opzioni per la soluzione.

Dopo la distribuzione è completata, gli indirizzi di server DNS e NTP forniti continuerà a essere utilizzate direttamente dai componenti del sistema. Ad esempio, se si archiviano le richieste DNS dopo la distribuzione è completata, l'origine cambierà da DVM IP a un indirizzo dell'intervallo di rete esterna.

Dopo dello Stack di Azure viene distribuito correttamente, il partner OEM potrebbe utilizzare il DVM per eseguire altre attività post-distribuzione. Tuttavia, quando vengono completate tutte le attività di distribuzione e le configurazioni post-distribuzione, OEM deve rimuovere ed eliminare il DVM dal HLH.

## <a name="next-steps"></a>Passaggi successivi
[Convalidare una registrazione di Azure](azure-stack-validate-registration.md)
