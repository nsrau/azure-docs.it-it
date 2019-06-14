---
title: Domande frequenti su DNS di Azure privato
description: Domande frequenti su DNS Private di Azure
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: c963cb1b6930b41a703b479e0213311d971e6606
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082846"
---
# <a name="azure-private-dns-faq"></a>Domande frequenti su DNS di Azure privato

> [!IMPORTANT]
> DNS di Azure privato è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="does-azure-dns-support-private-domains"></a>DNS di Azure supporta domini privati?

Supporto per i domini privati è supportato tramite la funzionalità zone DNS Private di Azure. Le zone DNS private vengono gestite tramite gli stessi strumenti per le zone DNS di Azure con connessione internet. ma sono risolvibili solo all'interno di reti virtuali specificate. Per altre informazioni, vedere l'articolo sulla [panoramica](private-dns-overview.md).

Per informazioni sulle altre opzioni DNS in Azure, vedere [Risoluzione dei nomi per le risorse in reti virtuali di Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Il servizio Zone private di DNS di Azure funziona tra le diverse aree di Azure?

Sì. Il servizio Zone private è supportato per la risoluzione DNS tra reti virtuali nelle aree di Azure. Il servizio funziona anche senza peering esplicito delle reti virtuali, purché siano tutte specificate come reti virtuali di risoluzione per la zona privata. I clienti possono richiedere il peering delle reti virtuali per consentire il flusso del traffico TCP/HTTP da un'area all'altra.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Per le zone private è necessario che le reti virtuali siano connesse a Internet?

No. Le zone private interagiscono con le reti virtuali e consentono ai clienti di gestire i domini per le macchine virtuali o per altre risorse all'interno o tra le reti virtuali. La connettività a Internet non è necessaria per la risoluzione dei nomi.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>È possibile usare la stessa zona privata per più reti virtuali di risoluzione?

Sì. È possibile associare fino a 1000 le reti virtuali con una singola zona privata.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>Può essere aggiunta una rete virtuale che appartiene a una sottoscrizione diversa come una rete virtuale collegata a una zona privata?

Sì. L'utente deve disporre dell'autorizzazione per le operazioni di scrittura sia nelle reti virtuali che nella zona DNS privata. L'autorizzazione di scrittura può essere assegnata a più ruoli Controllo degli accessi in base al ruolo. Il ruolo RBAC Collaboratore Rete classica, ad esempio, dispone di autorizzazioni di scrittura per le reti virtuali. Per altre informazioni sui ruoli Controllo degli accessi in base al ruolo, vedere [Che cos'è il controllo degli accessi in base al ruolo?](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>I record DNS di macchina virtuale registrati automaticamente in una zona privata automaticamente eliminati quando si elimina la macchina virtuale?

Sì. Se si elimina una macchina virtuale all'interno di una rete virtuale collegata con registrazione automatica abilitata, vengono eliminati automaticamente i record registrati.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>È un record di macchina virtuale registrati automaticamente in una zona privata da una rete virtuale collegata essere eliminato manualmente?

Sì. È tuttavia possibile sovrascrivere i record DNS registrati automaticamente con un record DNS creato manualmente nella zona. La domanda e la risposta seguenti sono correlate a questa operazione.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Cosa accade quando si prova a creare manualmente un nuovo record DNS in una zona privata che ha lo stesso nome host di una macchina virtuale esistente registrato automaticamente in una rete virtuale collegata?

Si prova a creare manualmente un nuovo record DNS in una zona privata con lo stesso nome host come macchina virtuale esistente, registrato automaticamente in una rete virtuale collegata. il nuovo record DNS sovrascrive il record di macchina virtuale registrato automaticamente. Se si prova a eliminare dalla zona il record DNS creato manualmente, quest'ultimo verrà eliminato e la registrazione automatica viene ripetuta purché la macchina virtuale esista ancora e disponga di un indirizzo IP privato. A questo punto il record DNS viene creato nuovamente nella zona.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Cosa accade quando si scollega una rete virtuale collegata da una zona privata? I record di macchina virtuale registrati automaticamente nella rete virtuale vengono rimossi anche dalla zona?

Sì. Per scollegare una rete virtuale collegata da una zona privata, si aggiorna la zona DNS per rimuovere il collegamento di rete virtuale associata. Durante questo processo i record di macchina virtuale registrati automaticamente vengono rimossi dalla zona.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Cosa accade quando si elimina una rete virtuale collegata collegata a una zona privata? È necessario aggiornare manualmente la zona privata per scollegare la rete virtuale come rete virtuale collegata dalla zona?

Sì. Quando si elimina una rete virtuale collegata senza prima scollegarla da una zona privata, l'operazione di eliminazione ha esito positivo. ma la rete virtuale non viene scollegata automaticamente dalla zona privata, se presente. È necessario scollegare manualmente la rete virtuale dalla zona privata. Per questo motivo è consigliabile scollegare la rete virtuale dalla zona privata prima di eliminarla.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>La risoluzione DNS usando il nome di dominio completo (internal.cloudapp.net) predefinito continuerà a funzionare anche quando una zona privata (ad esempio, private.contoso.com) viene collegata a una rete virtuale?

Sì. La funzionalità Zone private non sostituisce le risoluzioni DNS predefinite eseguite tramite la zona internal.cloudapp.net di Azure e viene offerta come funzionalità o aggiornamento aggiuntivo. Indipendentemente dal fatto che si usi la zona internal.cloudapp.net di Azure o la propria zona privata, è consigliabile usare il nome FQDN della zona rispetto alla quale si vuole eseguire la risoluzione.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Il suffisso DNS sulle macchine virtuali all'interno di una rete virtuale collegata deve essere sostituito con quello della zona privata?

No. Il suffisso DNS sulle macchine virtuali nella rete virtuale collegata continua a essere quello predefinito di Azure ("*.internal.cloudapp.net"). È possibile sostituire manualmente questo suffisso nelle macchine virtuali con quello della zona privata.

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Quali sono i limiti di utilizzo per zone DNS Private di Azure?

I seguenti limiti predefiniti si applicano quando si utilizzano zone DNS Private di Azure.

| Resource | Limite predefinito |
| --- | --- |
|Zone DNS private per ogni sottoscrizione|1000|
|Set di record per ogni zona DNS privata|25\.000|
|Record per ogni set di record|20|
|Collegamenti di rete virtuale per ogni zona DNS privata|1000|
|Collegamenti di reti virtuali per le zone private di DNS con la registrazione automatica abilitata|100|
|Numero di zone DNS private che può ottenere collegata una rete virtuale a con abilitata la registrazione automatica|1|
|Numero di zone DNS private che può ottenere collegata una rete virtuale|1000|

## <a name="is-there-portal-support-for-private-zones"></a>È disponibile il supporto del portale per le zone private?

Sì e le zone private già create tramite le API, PowerShell, CLI e SDK sono visibili nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sulle zone DNS Private di Azure](private-dns-overview.md)