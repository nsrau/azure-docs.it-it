---
title: Filtro FQDN del firewall di Azure nelle regole di rete
description: Come usare il filtro FQDN del firewall di Azure nelle regole di rete
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: f7196c7715ad5d2c02759040b780b96218e1655e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695946"
---
# <a name="use-fqdn-filtering-in-network-rules"></a>Usare il filtro FQDN nelle regole di rete

Un nome di dominio completo (FQDN) rappresenta un nome di dominio di un host o di un indirizzo IP. È possibile usare i nomi di dominio completi nelle regole di rete in base alla risoluzione DNS nei criteri firewall e firewall di Azure. Questa funzionalità consente di filtrare il traffico in uscita con qualsiasi protocollo TCP/UDP (inclusi NTP, SSH, RDP e altro ancora). È necessario abilitare il proxy DNS per l'uso di FQDN nelle regole di rete. Per altre informazioni, vedere [impostazioni DNS del firewall di Azure](dns-settings.md).

> [!NOTE]
> Per impostazione predefinita, il filtro FQDN non supporta i caratteri jolly.

## <a name="how-it-works"></a>Funzionamento

Dopo aver definito il server DNS necessario all'organizzazione (DNS di Azure o il proprio DNS personalizzato), il firewall di Azure converte il nome di dominio completo in un indirizzo IP in base al server DNS selezionato. Questa traduzione si verifica sia per l'elaborazione delle regole di rete sia per l'applicazione.

Quando si verifica una nuova risoluzione DNS, vengono aggiunti nuovi indirizzi IP alle regole del firewall. Gli indirizzi IP precedenti che non vengono più restituiti dal server DNS scadono entro 15 minuti. Le regole del firewall di Azure vengono aggiornate ogni 15 secondi dalla risoluzione DNS degli FQDN nelle regole di rete.

### <a name="differences-in-application-rules-vs-network-rules"></a>Differenze tra regole dell'applicazione e regole di rete

- Il filtro FQDN nelle regole dell'applicazione per HTTP/S e MSSQL si basa su un proxy trasparente a livello di applicazione e sull'intestazione SNI. Di conseguenza, può discernere tra due FQDN che vengono risolti nello stesso indirizzo IP. Questo non avviene con il filtro FQDN nelle regole di rete. 

   Usare sempre le regole dell'applicazione quando possibile:
     - Se il protocollo è HTTP/S o MSSQL, usare le regole dell'applicazione per il filtro FQDN.
   - Per tutti gli altri protocolli oltre a HTTP/S o MSSQL, è possibile usare le regole dell'applicazione o della rete per il filtro FQDN.

## <a name="next-steps"></a>Passaggi successivi

[Impostazioni DNS del firewall di Azure](dns-settings.md)
