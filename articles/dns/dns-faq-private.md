---
title: Domande frequenti su DNS privato di Azure
description: Domande frequenti su Azure DNS privato
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: fca7359f9fa54899bb72be3b939e1a1839dbfbd1
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155699"
---
# <a name="azure-private-dns-faq"></a>Domande frequenti su DNS privato di Azure

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="does-azure-dns-support-private-domains"></a>DNS di Azure supporta domini privati?

Il supporto per i domini privati è supportato con la funzionalità zone DNS privato di Azure. DNS privato zone vengono gestite usando gli stessi strumenti delle zone DNS di Azure con connessione Internet. ma sono risolvibili solo all'interno di reti virtuali specificate. Per altre informazioni, vedere l'articolo sulla [panoramica](private-dns-overview.md).

Per informazioni sulle altre opzioni DNS in Azure, vedere [Risoluzione dei nomi per le risorse in reti virtuali di Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Il servizio Zone private di DNS di Azure funziona tra le diverse aree di Azure?

Sì. Il servizio Zone private è supportato per la risoluzione DNS tra reti virtuali nelle aree di Azure. Il servizio funziona anche senza peering esplicito delle reti virtuali, purché siano tutte specificate come reti virtuali di risoluzione per la zona privata. Potrebbe essere necessario eseguire il peering delle reti virtuali affinché il traffico TCP/HTTP fluisca da un'area all'altra.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Per le zone private è necessario che le reti virtuali siano connesse a Internet?

No. Le zone private interagiscono con le reti virtuali Vengono usati per gestire i domini per le macchine virtuali o altre risorse all'interno e nelle reti virtuali. La connettività a Internet non è necessaria per la risoluzione dei nomi.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>È possibile usare la stessa zona privata per più reti virtuali di risoluzione?

Sì. È possibile associare fino a 1000 reti virtuali con una singola zona privata.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>È possibile aggiungere una rete virtuale appartenente a una sottoscrizione diversa come rete virtuale collegata a una zona privata?

Sì. L'utente deve disporre dell'autorizzazione per le operazioni di scrittura sia nelle reti virtuali che nella zona DNS privata. L'autorizzazione di scrittura può essere assegnata a più ruoli Controllo degli accessi in base al ruolo. Il ruolo RBAC Collaboratore Rete classica, ad esempio, dispone di autorizzazioni di scrittura per le reti virtuali. Per altre informazioni sui ruoli Controllo degli accessi in base al ruolo, vedere [Che cos'è il controllo degli accessi in base al ruolo?](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>I record DNS della macchina virtuale registrati automaticamente in una zona privata verranno eliminati automaticamente quando si elimina la macchina virtuale?

Sì. Se si elimina una macchina virtuale all'interno di una rete virtuale collegata con la registrazione automatica abilitata, i record registrati vengono eliminati automaticamente.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>È possibile eliminare manualmente un record della macchina virtuale registrato automaticamente in una zona privata da una rete virtuale collegata?

Sì. È tuttavia possibile sovrascrivere i record DNS registrati automaticamente con un record DNS creato manualmente nella zona. La domanda e la risposta seguenti sono correlate a questa operazione.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Cosa accade quando si prova a creare manualmente un nuovo record DNS in una zona privata con lo stesso nome host di una macchina virtuale esistente registrata automaticamente in una rete virtuale collegata?

Si tenta di creare manualmente un nuovo record DNS in una zona privata con lo stesso nome host di una macchina virtuale esistente registrata automaticamente in una rete virtuale collegata. il nuovo record DNS sovrascrive il record di macchina virtuale registrato automaticamente. Se si prova a eliminare dalla zona il record DNS creato manualmente, quest'ultimo verrà eliminato e la registrazione automatica viene ripetuta purché la macchina virtuale esista ancora e disponga di un indirizzo IP privato. A questo punto il record DNS viene creato nuovamente nella zona.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Cosa accade quando si scollega una rete virtuale collegata da una zona privata? I record di macchina virtuale registrati automaticamente nella rete virtuale vengono rimossi anche dalla zona?

Sì. Per scollegare una rete virtuale collegata da una zona privata, è necessario aggiornare la zona DNS per rimuovere il collegamento alla rete virtuale associata. Durante questo processo i record di macchina virtuale registrati automaticamente vengono rimossi dalla zona.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Cosa accade quando si elimina una rete virtuale collegata collegata a una zona privata? È necessario aggiornare manualmente la zona privata per scollegare la rete virtuale come rete virtuale collegata dalla zona?

Sì. Quando si elimina una rete virtuale collegata senza scollegarla da una zona privata, l'operazione di eliminazione ha esito positivo. ma la rete virtuale non viene scollegata automaticamente dalla zona privata, se presente. È necessario scollegare manualmente la rete virtuale dalla zona privata. Per questo motivo è consigliabile scollegare la rete virtuale dalla zona privata prima di eliminarla.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>La risoluzione DNS tramite il nome di dominio completo predefinito (internal.cloudapp.net) continuerà a funzionare anche se una zona privata (ad esempio, private.contoso.com) è collegata a una rete virtuale?

Sì. La funzionalità Zone private non sostituisce le risoluzioni DNS predefinite eseguite tramite la zona internal.cloudapp.net di Azure e viene offerta come funzionalità o aggiornamento aggiuntivo. Indipendentemente dal fatto che si usi la zona internal.cloudapp.net di Azure o la propria zona privata, è consigliabile usare il nome FQDN della zona rispetto alla quale si vuole eseguire la risoluzione.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Il suffisso DNS sulle macchine virtuali all'interno di una rete virtuale collegata deve essere sostituito con quello della zona privata?

No. Il suffisso DNS sulle macchine virtuali nella rete virtuale collegata continua a essere quello predefinito di Azure ("*.internal.cloudapp.net"). È possibile sostituire manualmente questo suffisso nelle macchine virtuali con quello della zona privata.

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Quali sono i limiti di utilizzo per DNS privato di Azure?

Quando si usa Azure DNS privato, si applicano i limiti predefiniti seguenti.

| Risorsa | Limite predefinito |
| --- | --- |
|Zone DNS privato per sottoscrizione|1000|
|Set di record per area DNS privato|25.000|
|Record per ogni set di record|20|
|Collegamenti di rete virtuale per zona DNS privata|1000|
|Collegamenti alle reti virtuali per zone DNS private con registrazione automatica abilitata|100|
|Numero di zone DNS private a cui può essere collegata una rete virtuale con registrazione automatica abilitata|1|
|Numero di zone DNS private che possono essere collegate a una rete virtuale|1000|

## <a name="is-there-portal-support-for-private-zones"></a>È disponibile il supporto del portale per le zone private?

Sì, e le zone private già create tramite API, PowerShell, l'interfaccia della riga di comando e gli SDK sono visibili nella portale di Azure.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Perché le zone DNS private esistenti non vengono visualizzate nella nuova esperienza del portale?

Nell'ambito della versione di aggiornamento dell'anteprima è stato fornito un nuovo modello di risorsa per le zone DNS private. È necessario eseguire la migrazione delle zone DNS private esistenti al nuovo modello di risorsa prima che questi vengano visualizzati nella nuova esperienza del portale. Per istruzioni su come eseguire la migrazione al nuovo modello di risorse, vedere di seguito.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Ricerca per categorie eseguire la migrazione delle zone DNS private esistenti al nuovo modello?
Si consiglia vivamente di eseguire la migrazione al nuovo modello di risorsa appena possibile. Il modello di risorse legacy sarà supportato, tuttavia, le funzionalità aggiuntive non verranno sviluppate in base a questo modello. In futuro si prevede di deprecarlo a favore del nuovo modello di risorse. Per istruzioni su come eseguire la migrazione delle zone DNS private esistenti al nuovo modello di risorse, vedere[Guida alla migrazione per le zone private di DNS di Azure](private-dns-migration-guide.md).

## <a name="next-steps"></a>Passaggi successivi

- [Scopri di più su Azure DNS privato](private-dns-overview.md)