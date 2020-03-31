---
title: Domande frequenti su DNS privato di Azure
description: In questo articolo vengono fornite informazioni sulle domande frequenti sul DNS privato di Azure
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 4de585a965cfefa6399b0c0929a8f732d0712617
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939431"
---
# <a name="azure-private-dns-faq"></a>Domande frequenti su DNS privato di Azure

Di seguito sono riportate le domande frequenti sul DNS privato di Azure.The following are frequently asked questions about Azure private DNS.

## <a name="does-azure-dns-support-private-domains"></a>DNS di Azure supporta domini privati?

I domini privati sono supportati tramite la funzionalità delle zone DNS private di Azure.Private domains are supported using the Azure Private DNS zones feature. Le zone DNS private sono risolvibili solo dall'interno di reti virtuali specificate. Per altre informazioni, vedere l'articolo sulla [panoramica](private-dns-overview.md).

Per informazioni su altre opzioni DNS interne in Azure, vedere Risoluzione dei nomi [per le macchine virtuali e le istanze del ruolo.](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Le zone DNS private di Azure funzioneranno in tutte le aree di Azure?

Sì. Il servizio Zone private è supportato per la risoluzione DNS tra reti virtuali nelle aree di Azure. Il servizio funziona anche senza peering esplicito delle reti virtuali, Tutte le reti virtuali devono essere collegate alla zona DNS privata.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Per le zone private è necessario che le reti virtuali siano connesse a Internet?

No. Le zone private interagiscono con le reti virtuali Utilizzarli per gestire i domini per le macchine virtuali o altre risorse all'interno e tra reti virtuali. La connettività a Internet non è necessaria per la risoluzione dei nomi.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>È possibile usare la stessa zona privata per più reti virtuali di risoluzione?

Sì. È possibile collegare una zona DNS privata a migliaia di reti virtuali. Per altre informazioni, vedere Limiti di DNS di AzureFor more [information,](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) see Azure DNS Limits

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>È possibile collegare una rete virtuale appartenente a una sottoscrizione diversa a una zona privata?

Sì. L'utente deve disporre dell'autorizzazione per le operazioni di scrittura sia nelle reti virtuali che nella zona DNS privata. L'autorizzazione di scrittura può essere assegnata a più ruoli Controllo degli accessi in base al ruolo. Ad esempio, il ruolo RBAC Collaboratore di rete classica dispone delle autorizzazioni di scrittura per le reti virtuali e le zone DNS private Il ruolo Collaboratore dispone delle autorizzazioni di scrittura per le zone DNS private. Per altre informazioni sui ruoli Controllo degli accessi in base al ruolo, vedere [Che cos'è il controllo degli accessi in base al ruolo?](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>I record DNS della macchina virtuale registrati automaticamente in una zona privata verranno eliminati automaticamente quando si elimina la macchina virtuale?

Sì. Se si elimina una macchina virtuale all'interno di una rete virtuale collegata con la registrazione automatica abilitata, i record registrati vengono eliminati automaticamente.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>È possibile eliminare manualmente un record di macchina virtuale registrato automaticamente in una zona privata da una rete virtuale collegata?

Sì. È tuttavia possibile sovrascrivere i record DNS registrati automaticamente con un record DNS creato manualmente nella zona. La domanda e la risposta seguenti sono correlate a questa operazione.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Cosa succede quando si tenta di creare manualmente un nuovo record DNS in una zona privata con lo stesso nome host di una macchina virtuale esistente registrata automaticamente in una rete virtuale collegata?

Si tenta di creare manualmente un nuovo record DNS in una zona privata con lo stesso nome host di una macchina virtuale registrata automaticamente esistente in una rete virtuale collegata. il nuovo record DNS sovrascrive il record di macchina virtuale registrato automaticamente. Se si prova a eliminare dalla zona il record DNS creato manualmente, quest'ultimo verrà eliminato e la registrazione automatica viene ripetuta purché la macchina virtuale esista ancora e disponga di un indirizzo IP privato. A questo punto il record DNS viene creato nuovamente nella zona.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Cosa succede quando si scollega una rete virtuale collegata da una zona privata? I record di macchina virtuale registrati automaticamente nella rete virtuale vengono rimossi anche dalla zona?

Sì. Per scollegare una rete virtuale collegata da una zona privata, aggiornare la zona DNS per rimuovere il collegamento di rete virtuale associata. Durante questo processo i record di macchina virtuale registrati automaticamente vengono rimossi dalla zona.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Cosa succede quando si elimina una rete virtuale collegata collegata a una zona privata? È necessario aggiornare manualmente la zona privata per scollegare la rete virtuale come rete virtuale collegata dalla zona?

No. Quando si elimina una rete virtuale collegata senza scollegarla da una zona privata, l'operazione di eliminazione ha esito positivo e i collegamenti alla zona DNS vengono cancellati automaticamente.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>La risoluzione DNS tramite il nome di dominio completo predefinito (internal.cloudapp.net) continuerà a funzionare anche quando una zona privata (ad esempio, private.contoso.com) è collegata a una rete virtuale?

Sì. Le aree private non sostituiscono l'area di internal.cloudapp.net predefinita fornita da Azure.Private zones don't replace the default Azure-provided internal.cloudapp.net zone. Indipendentemente dal fatto che si usi la zona internal.cloudapp.net di Azure o la propria zona privata, è consigliabile usare il nome FQDN della zona rispetto alla quale si vuole eseguire la risoluzione.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Il suffisso DNS sulle macchine virtuali all'interno di una rete virtuale collegata deve essere sostituito con quello della zona privata?

No. Il suffisso DNS sulle macchine virtuali nella rete virtuale collegata continua a essere quello predefinito di Azure ("*.internal.cloudapp.net"). È possibile sostituire manualmente questo suffisso nelle macchine virtuali con quello della zona privata.
Per istruzioni su come modificare questo suffisso, vedere [Utilizzare il DNS dinamico per registrare i nomi host nel proprio server DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Quali sono i limiti di utilizzo per le zone private DNS di Azure?

Fare riferimento ai limiti DNS di Azure per informazioni dettagliate sui limiti di utilizzo per le zone private DNS di Azure.Refer to [Azure DNS limits](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) for details on the usage limits for Azure DNS private zones.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Perché le zone DNS private esistenti non vengono visualizzate in una nuova esperienza del portale?

Se la zona DNS privata esistente è stata creata utilizzando l'API di anteprima, è necessario eseguire la migrazione di queste zone al nuovo modello di risorsa. Le zone DNS private create con l'API di anteprima non verranno visualizzate nella nuova esperienza del portale. Per istruzioni su come eseguire la migrazione al nuovo modello di risorse, vedere di seguito.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Come si esegue la migrazione delle zone DNS private esistenti al nuovo modello?

È consigliabile eseguire la migrazione al nuovo modello di risorse appena possibile. Il modello di risorse legacy sarà supportato, tuttavia, non verranno sviluppate ulteriori funzionalità in cima a questo modello. In futuro, intendiamo deprecherlo a favore di un nuovo modello di risorse. Per istruzioni su come eseguire la migrazione delle zone DNS private esistenti al nuovo modello di risorse, vedere Guida alla migrazione per le zone private DNS di[Azure.](private-dns-migration-guide.md)

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni su Azure Private DNS](private-dns-overview.md)
