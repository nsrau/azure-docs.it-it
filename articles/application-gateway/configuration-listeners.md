---
title: Configurazione del listener del gateway applicazione Azure
description: Questo articolo descrive come configurare i listener del gateway applicazione Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 15f68e8cbca65e7b970944f7ca5ef1952140cc6b
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397641"
---
# <a name="application-gateway-listener-configuration"></a>Configurazione del listener del gateway applicazione

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Un listener è un'entità logica che controlla le richieste di connessione in ingresso tramite la porta, il protocollo, l'host e l'indirizzo IP. Quando si configura il listener, è necessario immettere i valori per questi che corrispondono ai valori corrispondenti nella richiesta in ingresso sul gateway.

Quando si crea un gateway applicazione usando il portale di Azure, si crea anche un listener predefinito scegliendo il protocollo e la porta per il listener. È possibile scegliere se abilitare il supporto di HTTP2 nel listener. Dopo aver creato il gateway applicazione, è possibile modificare le impostazioni del listener predefinito ( *appGatewayHttpListener* ) o creare nuovi listener.

## <a name="listener-type"></a>Tipo di listener

Quando si crea un nuovo listener, è possibile scegliere tra [le funzionalità di *base* e *multisito*](./application-gateway-components.md#types-of-listeners).

- Se si desidera che tutte le richieste (per qualsiasi dominio) vengano accettate e inviate ai pool back-end, scegliere Basic. Informazioni [su come creare un gateway applicazione con un listener di base](./quick-create-portal.md).

- Se si desidera inviare richieste a diversi pool back-end in base all'intestazione *host* o ai nomi host, scegliere listener multisito, in cui è necessario specificare anche un nome host corrispondente alla richiesta in ingresso. Questo perché il gateway applicazione si basa sulle intestazioni host HTTP 1,1 per ospitare più siti Web nello stesso indirizzo IP pubblico e porta. Per altre informazioni, vedere [hosting di più siti con il gateway applicazione](multiple-site-overview.md).

### <a name="order-of-processing-listeners"></a>Ordine dei listener di elaborazione

Per lo SKU V1, le richieste vengono confrontate in base all'ordine delle regole e al tipo di listener. Se una regola con listener di base viene innanzitutto eseguita nell'ordine, viene elaborata per prima e accetterà qualsiasi richiesta per la combinazione di porta e indirizzo IP. Per evitare questo problema, configurare prima le regole con listener multisito ed effettuare il push della regola con il listener di base all'ultimo nell'elenco.

Per lo SKU V2, i listener multisito vengono elaborati prima dei listener di base.

## <a name="front-end-ip-address"></a>Indirizzo IP front-end

Scegliere l'indirizzo IP front-end che si intende associare a questo listener. Il listener resterà in ascolto delle richieste in ingresso sull'IP.

## <a name="front-end-port"></a>Porta front-end

Scegliere la porta front-end. Selezionare una porta esistente o crearne una nuova. Scegliere qualsiasi valore dall' [intervallo di porte consentito](./application-gateway-components.md#ports). È possibile utilizzare non solo le porte note, ad esempio 80 e 443, ma qualsiasi porta personalizzata consentita. Una porta può essere usata per i listener pubblici o per i listener privati.

## <a name="protocol"></a>Protocollo

Scegliere HTTP o HTTPS:

- Se si sceglie HTTP, il traffico tra il client e il gateway applicazione non è crittografato.

- Scegliere HTTPS se si vuole la [terminazione TLS](features.md#secure-sockets-layer-ssltls-termination) o la [crittografia TLS end-to-end](./ssl-overview.md). Il traffico tra il client e il gateway applicazione è crittografato. E la connessione TLS termina nel gateway applicazione. Se si desidera la crittografia TLS end-to-end, è necessario scegliere HTTPS e configurare l'impostazione **http back-end** . Ciò garantisce che il traffico venga nuovamente crittografato durante il trasferimento dal gateway applicazione al back-end.


Per configurare la terminazione TLS e la crittografia TLS end-to-end, è necessario aggiungere un certificato al listener per consentire al gateway applicazione di derivare una chiave simmetrica. Questa operazione è determinata dalla specifica del protocollo TLS. La chiave simmetrica viene usata per crittografare e decrittografare il traffico inviato al gateway. Il certificato del gateway deve essere in formato PFX (Personal Information Exchange). Questo formato consente di esportare la chiave privata che il gateway usa per crittografare e decrittografare il traffico.

## <a name="supported-certificates"></a>Certificati supportati

Vedere [Panoramica della terminazione TLS e del TLS end-to-end con il gateway applicazione](ssl-overview.md#certificates-supported-for-tls-termination)

## <a name="additional-protocol-support"></a>Supporto del protocollo aggiuntivo

### <a name="http2-support"></a>Supporto di HTTP2

Il supporto del protocollo HTTP/2 è disponibile per i client che si connettono solo ai listener del gateway applicazione. La comunicazione con i pool di server back-end avviene tramite HTTP/1.1. Per impostazione predefinita, il supporto di HTTP/2 è disabilitato. Il seguente frammento di codice Azure PowerShell Mostra come abilitare questa procedura:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

### <a name="websocket-support"></a>Supporto per WebSocket

Il supporto di WebSocket è abilitato per impostazione predefinita. Non è disponibile alcuna impostazione configurabile dall'utente per abilitarla o disabilitarla. È possibile usare WebSocket con listener HTTP e HTTPS.

## <a name="custom-error-pages"></a>Pagine di errore personalizzate

È possibile definire un errore personalizzato a livello globale o a livello di listener. Tuttavia, la creazione di pagine di errore personalizzate a livello globale dalla portale di Azure non è attualmente supportata. È possibile configurare una pagina di errore personalizzata per un errore 403 web application firewall o una pagina di manutenzione 502 a livello di listener. È inoltre necessario specificare un URL BLOB accessibile pubblicamente per il codice di stato di errore specificato. Per altre informazioni, vedere [Create Application Gateway custom error pages](./custom-error.md) (Creare pagine di errore personalizzate del gateway applicazione).

![Codici di errore del gateway applicazione](/azure/application-gateway/media/custom-error/ag-error-codes.png)

Per configurare una pagina di errore globale personalizzata, vedere [configurazione Azure PowerShell](./custom-error.md#azure-powershell-configuration).

## <a name="tls-policy"></a>Criteri TLS

È possibile centralizzare la gestione dei certificati TLS/SSL e ridurre l'overhead di decrittografia della crittografia per un server farm back-end. La gestione centralizzata di TLS consente inoltre di specificare un criterio TLS centrale adatto ai propri requisiti di sicurezza. È possibile scegliere i criteri *predefined* TLS *predefiniti, predefiniti* o *personalizzati* .

Il criterio TLS viene configurato per controllare le versioni del protocollo TLS. È possibile configurare un gateway applicazione per usare una versione minima del protocollo per gli handshake TLS da TLS 1.0, TLS 1.1 e TLS 1.2. Per impostazione predefinita, SSL 2,0 e 3,0 sono disabilitati e non sono configurabili. Per altre informazioni, vedere [Panoramica dei criteri TLS del gateway applicazione](./application-gateway-ssl-policy-overview.md).

Dopo aver creato un listener, associarlo a una regola di routing delle richieste. Tale regola determina il modo in cui le richieste ricevute nel listener vengono instradate al back-end.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [sulle regole di routing delle richieste](configuration-request-routing-rules.md).