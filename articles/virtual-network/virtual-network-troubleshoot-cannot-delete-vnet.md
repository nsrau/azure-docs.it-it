---
title: Impossibile eliminare una rete virtuale in Azure | Microsoft Docs
description: Informazioni su come risolvere il problema in cui non è possibile eliminare una rete virtuale in Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c2152b872c82c224c786e56db0318c9df994ac25
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801587"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Risoluzione dei problemi: Impossibile eliminare una rete virtuale in Azure
<p class="alert is-flex is-primary"><span class="has-padding-left-medium has-padding-top-extra-small"><a class="button is-primary" href="https://azurevirtualsupportagent.services.microsoft.com?content=a13a0908-28e0-f9f7-c6c7-301fcd187560" target='_blank'>Iniziare</a></span><span class="has-padding-small">a risolvere rapidamente il problema usando l'agente virtuale per eseguire la <b>diagnostica automatica.</b> </span> Informativa <sub>sulla privacy</sub> <span class="has-padding-small"> <a href="https://privacy.microsoft.com/privacystatement" target='_blank'> <div align="right"></div></a></span></p>
Quando si tenta di eliminare una rete virtuale in Microsoft Azure, è possibile che si riceva un errore. Questo articolo illustra la procedura per risolvere questo tipo di problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Guida alla risoluzione dei problemi 

1. [Controllare se un gateway di rete virtuale è in esecuzione nella rete virtuale](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Controllare se un gateway applicazione è in esecuzione nella rete virtuale](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Verificare se nella rete virtuale è abilitato il servizio Azure Active Directory Domain Service](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Verificare se la rete virtuale è connessa ad altre risorse](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Verificare se nella rete virtuale è ancora in esecuzione una macchina virtuale](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Verificare se la rete virtuale è bloccata in fase di migrazione](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Verificare se nella rete virtuale è in esecuzione un gateway di rete virtuale

Per rimuovere la rete virtuale, è necessario rimuovere prima il gateway di rete virtuale.

In caso di reti virtuali classiche, accedere alla pagina **Panoramica** della rete virtuale classica nel portale di Azure. Se il gateway è in esecuzione nella rete virtuale, nella sezione **Connessioni VPN** verrà visualizzato l'indirizzo IP del gateway. 

![Verificare se il gateway è in esecuzione](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

In caso di reti virtuali, accedere alla pagina **Panoramica** della rete virtuale. Controllare i **dispositivi connessi** associati al gateway di rete virtuale.

![Controllare il dispositivo collegato](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Prima di poter rimuovere il gateway, è necessario rimuovere eventuali oggetti **Connessione** presenti nel gateway. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Verificare se nella rete virtuale è in esecuzione un gateway applicazione

Accedere alla pagina **Panoramica** della rete virtuale. Controllare i **dispositivi connessi** associati al gateway applicazione.

![Controllare il dispositivo collegato](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Se è presente un gateway applicazione, è necessario rimuoverlo prima di poter eliminare la rete virtuale.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Verificare se nella rete virtuale è abilitato il servizio Azure Active Directory Domain Service

Se Active Directory Domain Service è abilitato e connesso alla rete virtuale, non è possibile eliminare la rete virtuale. 

![Controllare il dispositivo collegato](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Per disabilitare il servizio, vedere [Disabilitare Azure Active Directory Domain Services tramite il portale di Azure](../active-directory-domain-services/delete-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Verificare se la rete virtuale è connessa ad altre risorse

Controllare i collegamenti del circuito, le connessioni e i peering di rete virtuale, poiché questi elementi possono impedire l'eliminazione di una rete virtuale. 

Di seguito è riportato l'ordine di eliminazione consigliato:

1. Connessioni del gateway
2. Gateway
3. Indirizzi IP
4. Peering di rete virtuale
5. Ambiente del servizio app

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Verificare se nella rete virtuale è ancora in esecuzione una macchina virtuale

Assicurarsi che nella rete virtuale non si trovi alcuna macchina virtuale.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Verificare se la rete virtuale è bloccata in stato di migrazione

Se la rete virtuale è bloccata in uno stato di migrazione, non può essere eliminata. Eseguire il comando seguente per interrompere la migrazione e quindi eliminare la rete virtuale.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Passaggi successivi

- [Rete virtuale di Azure](virtual-networks-overview.md)
- [Domande frequenti sulla rete virtuale di Azure](virtual-networks-faq.md)
