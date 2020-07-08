---
title: Filtro FQDN del firewall di Azure nelle regole di rete (anteprima)
description: Come usare il filtro FQDN del firewall di Azure nelle regole di rete
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: aef6fb52aaad3dd20db63f1d9023745169e84fcb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567885"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>Filtro FQDN nelle regole di rete (anteprima)

> [!IMPORTANT]
> Il filtro FQDN nelle regole di rete è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Un nome di dominio completo (FQDN) rappresenta un nome di dominio di un host. Un nome di dominio è associato a uno o più indirizzi IP. È possibile consentire o bloccare i nomi FQDN e i tag FQDN nelle regole dell'applicazione. Usando le impostazioni DNS personalizzate e proxy DNS, è anche possibile usare il filtro FQDN nelle regole di rete.

## <a name="how-it-works"></a>Come funziona

Il firewall di Azure converte il nome di dominio completo in un indirizzo IP usando le impostazioni DNS e l'elaborazione delle regole in base a DNS di Azure o a una configurazione DNS personalizzata.

Per usare i nomi di dominio completi nelle regole di rete, è necessario abilitare il proxy DNS. Se non si Abilita il proxy DNS, l'elaborazione delle regole affidabili è a rischio. Quando è abilitata, il traffico DNS viene indirizzato al firewall di Azure, in cui è possibile configurare il server DNS personalizzato. Quindi il firewall e i client utilizzano lo stesso server DNS configurato. Se il proxy DNS non è abilitato, il firewall di Azure può produrre una risposta diversa perché il client e il firewall possono usare server diversi per la risoluzione dei nomi. Il filtro FQDN nelle regole di rete potrebbe essere difettoso o incoerente se il client e il firewall ricevono risposte DNS diverse.

È possibile scegliere di eseguire l'override di questo requisito riconoscendo il rischio prima di selezionare **Salva** nella raccolta regole.

## <a name="next-steps"></a>Passaggi successivi

[Impostazioni DNS del firewall di Azure](dns-settings.md)
