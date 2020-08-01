---
title: Filtro FQDN del firewall di Azure nelle regole di rete (anteprima)
description: Come usare il filtro FQDN del firewall di Azure nelle regole di rete
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 6e90a8bc0998b43a84658958215e4b7977f8fdd0
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461307"
---
# <a name="use-fqdn-filtering-in-network-rules-preview"></a>Usare il filtro FQDN nelle regole di rete (anteprima)

> [!IMPORTANT]
> Il filtro FQDN nelle regole di rete è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Un nome di dominio completo (FQDN) rappresenta un nome di dominio di un host o di un indirizzo IP. È possibile usare i nomi di dominio completi nelle regole di rete in base alla risoluzione DNS nei criteri firewall e firewall di Azure. Questa funzionalità consente di filtrare il traffico in uscita con qualsiasi protocollo TCP/UDP (inclusi NTP, SSH, RDP e altro ancora). È necessario abilitare il proxy DNS per l'uso di FQDN nelle regole di rete. Per altre informazioni, vedere [impostazioni DNS del firewall di Azure (anteprima)](dns-settings.md).

## <a name="how-it-works"></a>Come funziona

Dopo aver definito il server DNS necessario all'organizzazione (DNS di Azure o il proprio DNS personalizzato), il firewall di Azure converte il nome di dominio completo in un indirizzo IP in base al server DNS selezionato. Questa traduzione si verifica sia per l'elaborazione delle regole di rete sia per l'applicazione.

Qual è la differenza tra l'utilizzo dei nomi di dominio nelle regole dell'applicazione rispetto a quella delle regole di rete? 

- Il filtro FQDN nelle regole dell'applicazione per HTTP/S e MSSQL si basa su un proxy trasparente a livello di applicazione e sull'intestazione SNI. Di conseguenza, può discernere tra due FQDN che vengono risolti nello stesso indirizzo IP. Questo non avviene con il filtro FQDN nelle regole di rete. Usare sempre le regole dell'applicazione, quando possibile.
- Nelle regole dell'applicazione è possibile usare HTTP/S e MSSQL come protocolli selezionati. In regole di rete è possibile usare qualsiasi protocollo TCP/UDP con i nomi di dominio completi di destinazione.

## <a name="next-steps"></a>Passaggi successivi

[Impostazioni DNS del firewall di Azure](dns-settings.md)
