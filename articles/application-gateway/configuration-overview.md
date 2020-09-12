---
title: Panoramica della configurazione del gateway applicazione Azure
description: Questo articolo descrive come configurare i componenti di applicazione Azure gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 0012ac99600c77dce5940387e1da54f29c3f6ab7
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652003"
---
# <a name="application-gateway-configuration-overview"></a>Panoramica della configurazione del gateway applicazione

Applicazione Azure gateway è costituito da diversi componenti che è possibile configurare in vari modi per diversi scenari. Questo articolo illustra come configurare ogni componente.

![Diagramma di flusso dei componenti del gateway applicazione](./media/configuration-overview/configuration-overview1.png)

Questa immagine illustra un'applicazione con tre listener. I primi due sono listener multisito `http://acme.com/*` rispettivamente per e `http://fabrikam.com/*` . Entrambi sono in ascolto sulla porta 80. Il terzo è un listener di base con terminazione Transport Layer Security (TLS) end-to-end, nota in precedenza come terminazione Secure Sockets Layer (SSL).

## <a name="infrastructure"></a>Infrastruttura

L'infrastruttura del gateway applicazione include la rete virtuale, le subnet, i gruppi di sicurezza di rete e le route definite dall'utente.

Per altre informazioni, vedere [configurazione dell'infrastruttura del gateway applicazione](configuration-infrastructure.md).



## <a name="front-end-ip-address"></a>Indirizzo IP front-end

È possibile configurare il gateway applicazione in modo che disponga di un indirizzo IP pubblico, un indirizzo IP privato o entrambi. Un indirizzo IP pubblico è obbligatorio quando si ospita un back-end a cui i client devono accedere tramite Internet tramite un indirizzo IP virtuale (VIP) con connessione Internet.

Per altre informazioni, vedere [configurazione degli indirizzi IP front-end del gateway applicazione](configuration-front-end-ip.md).

## <a name="listeners"></a>Listener

Un listener è un'entità logica che controlla le richieste di connessione in ingresso tramite la porta, il protocollo, l'host e l'indirizzo IP. Quando si configura il listener, è necessario immettere i valori per questi che corrispondono ai valori corrispondenti nella richiesta in ingresso sul gateway.

Per altre informazioni, vedere [configurazione del listener del gateway applicazione](configuration-listeners.md).

## <a name="request-routing-rules"></a>Richiedere regole di routing

Quando si crea un gateway applicazione usando il portale di Azure, si crea una regola predefinita (*Rule1*). Questa regola associa il listener predefinito (*appGatewayHttpListener*) con il pool back-end predefinito (*appGatewayBackendPool*) e le impostazioni http back-end predefinite (*appGatewayBackendHttpSettings*). Dopo aver creato il gateway, è possibile modificare le impostazioni della regola predefinita o creare nuove regole.

Per altre informazioni, vedere [regole di routing delle richieste del gateway applicazione](configuration-request-routing-rules.md).

## <a name="http-settings"></a>Impostazioni HTTP

Il gateway applicazione instrada il traffico verso i server back-end usando la configurazione specificata qui. Dopo aver creato un'impostazione HTTP, è necessario associarla a una o più regole di routing delle richieste.

Per altre informazioni, vedere [configurazione delle impostazioni http del gateway applicazione](configuration-http-settings.md).

## <a name="back-end-pool"></a>Pool back-end

È possibile puntare un pool back-end a quattro tipi di membri back-end: una macchina virtuale specifica, un set di scalabilità di macchine virtuali, un indirizzo IP/FQDN o un servizio app. 

Dopo aver creato un pool back-end, è necessario associarlo a una o più regole di routing delle richieste. È inoltre necessario configurare i probe di integrità per ogni pool back-end nel gateway applicazione. Quando viene soddisfatta una condizione della regola di routing richieste, il gateway applicazione lo trasmette ai server integri (come determinato dai Probe di integrità) nel pool back-end corrispondente.

## <a name="health-probes"></a>Probe di integrità

Per impostazione predefinita, un gateway applicazione monitora l'integrità di tutte le risorse nel back-end. È tuttavia consigliabile creare un probe personalizzato per ogni impostazione HTTP back-end per ottenere un maggiore controllo sul monitoraggio dell'integrità. Per informazioni su come configurare un probe personalizzato, vedere [impostazioni del probe di integrità personalizzato](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> Dopo aver creato un probe di integrità personalizzato, è necessario associarlo a un'impostazione HTTP back-end. Un probe personalizzato non monitorerà l'integrità del pool back-end, a meno che l'impostazione HTTP corrispondente non sia associata in modo esplicito a un listener usando una regola.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conoscono i componenti del gateway applicazione, è possibile:

- [Creare un gateway applicazione nella portale di Azure](quick-create-portal.md)
- [Creare un gateway applicazione con PowerShell](quick-create-powershell.md)
- [Creare un gateway applicazione con l'interfaccia della riga di comando di Azure](quick-create-cli.md)
