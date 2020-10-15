---
title: Configurazione degli indirizzi IP front-end del gateway applicazione Azure
description: Questo articolo descrive come configurare l'indirizzo IP front-end del gateway applicazione Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: d6cfac7f0fb3939e57ce64f552556138ce9feacd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89653187"
---
# <a name="application-gateway-front-end-ip-address-configuration"></a>Configurazione degli indirizzi IP front-end del gateway applicazione

È possibile configurare il gateway applicazione in modo che disponga di un indirizzo IP pubblico, un indirizzo IP privato o entrambi. Un indirizzo IP pubblico è obbligatorio quando si ospita un back-end a cui i client devono accedere tramite Internet tramite un IP virtuale (VIP) con connessione Internet.

## <a name="public-and-private-ip-address-support"></a>Supporto per indirizzi IP pubblici e privati

Il gateway applicazione v2 attualmente non supporta solo la modalità IP privato. Supporta le combinazioni seguenti:

* Indirizzo IP privato e indirizzo IP pubblico
* Solo indirizzo IP pubblico

Per altre informazioni, vedere [domande frequenti sul gateway applicazione](application-gateway-faq.md#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address).


Un indirizzo IP pubblico non è obbligatorio per un endpoint interno non esposto a Internet. Noto come endpoint di *bilanciamento del carico interno* (ILB) o IP front-end privato. Un ILB del gateway applicazione è utile per le applicazioni line-of-business interne che non sono esposte a Internet. È utile anche per i servizi e i livelli in un'applicazione multilivello all'interno di un limite di sicurezza non esposto a Internet, ma che richiedono la distribuzione del carico Round Robin, la appiccicosità della sessione o la terminazione TLS.

È supportato un solo indirizzo IP pubblico o un indirizzo IP privato. Quando si crea il gateway applicazione, è possibile scegliere l'indirizzo IP front-end.

- Per un indirizzo IP pubblico è possibile creare un nuovo indirizzo IP pubblico o usare un indirizzo IP pubblico esistente nello stesso percorso del gateway applicazione. Per ulteriori informazioni, vedere [static vs. Dynamic Public IP address](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- Per un indirizzo IP privato, è possibile specificare un indirizzo IP privato dalla subnet in cui viene creato il gateway applicazione. Se non si specifica un indirizzo IP arbitrario, viene selezionato automaticamente dalla subnet. Il tipo di indirizzo IP selezionato (statico o dinamico) non può essere modificato in un secondo momento. Per altre informazioni, vedere [creare un gateway applicazione con un servizio di bilanciamento del carico interno](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Un indirizzo IP front-end è associato a un *listener*, che controlla le richieste in ingresso sull'IP front-end.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulla configurazione del listener](configuration-listeners.md)