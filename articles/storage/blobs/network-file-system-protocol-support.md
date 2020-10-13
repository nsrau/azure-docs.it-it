---
title: Supporto del file System di rete 3,0 nell'archivio BLOB di Azure (anteprima) | Microsoft Docs
description: L'archiviazione BLOB supporta ora il protocollo NFS (Network File System) 3,0. Questo supporto consente ai client Windows e Linux di montare un contenitore nell'archiviazione BLOB da una macchina virtuale (VM) di Azure o da un computer in esecuzione in locale.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 41ad20de6b7a800ff1f97a7ff371c8e0012fed27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88166978"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Supporto del protocollo NFS (Network File System) 3,0 nell'archivio BLOB di Azure (anteprima)

L'archiviazione BLOB supporta ora il protocollo NFS (Network File System) 3,0. Questo supporto consente ai client Windows o Linux di montare un contenitore nell'archiviazione BLOB da una macchina virtuale (VM) di Azure o da un computer locale. 

> [!NOTE]
> Il supporto del protocollo NFS 3,0 nell'archivio BLOB di Azure è disponibile in anteprima pubblica ed è disponibile nelle aree seguenti: Stati Uniti orientali, Stati Uniti centrali, Stati Uniti centro-occidentali, Australia sudorientale, Europa settentrionale, Regno Unito occidentale, Corea centrale, Corea meridionale e Canada centrale.

## <a name="general-workflow-mounting-a-storage-account-container"></a>Flusso di lavoro generale: montaggio di un contenitore dell'account di archiviazione

Per montare un contenitore dell'account di archiviazione, è necessario eseguire queste operazioni.

1. Registrare la funzionalità del protocollo NFS 3,0 con la sottoscrizione.

2. Verificare che la funzionalità sia registrata.

3. Creare una rete virtuale di Azure (VNet).

4. Configurare la sicurezza di rete.

5. Creare e configurare un account di archiviazione che accetti il traffico solo dal VNet.

6. Creare un contenitore nell'account di archiviazione.

7. Montare il contenitore.

Per istruzioni dettagliate, vedere [montare l'archiviazione BLOB usando il protocollo NFS (Network File System) 3,0 (anteprima)](network-file-system-protocol-support-how-to.md).

> [!IMPORTANT]
> È importante completare queste attività in ordine. Non è possibile montare i contenitori creati prima di abilitare il protocollo NFS 3,0 nell'account. Inoltre, dopo aver abilitato il protocollo NFS 3,0 nell'account, non è possibile disabilitarlo.

## <a name="network-security"></a>Sicurezza di rete

L'account di archiviazione deve essere contenuto all'interno di un VNet. Un VNet consente ai client di connettersi in modo sicuro all'account di archiviazione. L'unico modo per proteggere i dati nell'account consiste nell'usare un VNet e altre impostazioni di sicurezza di rete. Qualsiasi altro strumento utilizzato per proteggere i dati, inclusi l'autorizzazione della chiave dell'account, la sicurezza Azure Active Directory (AD) e gli elenchi di controllo di accesso (ACL), non sono ancora supportati negli account in cui è abilitato il supporto del protocollo NFS 3,0. 

Per altre informazioni, vedere [consigli sulla sicurezza di rete per l'archiviazione BLOB](security-recommendations.md#networking).

## <a name="supported-network-connections"></a>Connessioni di rete supportate

Un client può connettersi tramite un endpoint pubblico o [privato](../common/storage-private-endpoints.md)e può connettersi da uno dei seguenti percorsi di rete:

- VNet configurato per l'account di archiviazione. 

  Ai fini di questo articolo, si farà riferimento a VNet come *VNet primario*. Per altre informazioni, vedere [concedere l'accesso da una rete virtuale](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Un VNet con peering che si trova nella stessa area del VNet primario.

  È necessario configurare l'account di archiviazione per consentire l'accesso a questo VNet con peering. Per altre informazioni, vedere [concedere l'accesso da una rete virtuale](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Una rete locale connessa alla VNet primaria usando un gateway [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) o un [gateway ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager). 

  Per altre informazioni, vedere [configurazione dell'accesso dalle reti locali](../common/storage-network-security.md#configuring-access-from-on-premises-networks).

- Una rete locale connessa a una rete con peering.

  Questa operazione può essere eseguita usando un gateway [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) o un [gateway ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager) insieme al [transito del gateway](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit). 

> [!IMPORTANT]
> Se ci si connette da una rete locale, assicurarsi che il client consenta la comunicazione in uscita attraverso le porte 111 e 2048. Il protocollo NFS 3,0 utilizza queste porte.

## <a name="azure-storage-features-not-yet-supported"></a>Funzionalità di archiviazione di Azure non ancora supportate

Le funzionalità di archiviazione di Azure seguenti non sono supportate quando si Abilita il protocollo NFS 3,0 per l'account. 

- Sicurezza di Azure Active Directory (AD)

- Elenchi di controllo di accesso (ACL) simili a POSIX

- Possibilità di abilitare il supporto per NFS 3,0 negli account di archiviazione esistenti

- La possibilità di disabilitare il supporto per NFS 3,0 in un account di archiviazione (dopo averla abilitata)

- Possibilità di scrivere nei BLOB usando le API REST o gli SDK. 
  
## <a name="nfs-30-features-not-yet-supported"></a>Funzionalità di NFS 3,0 non ancora supportate

Le funzionalità di NFS 3,0 seguenti non sono ancora supportate con Azure Data Lake Storage Gen2.

- NFS 3,0 su UDP. È supportato solo NFS 3,0 su TCP.

- Blocco di file con Network Lock Manager (NLM). I comandi di montaggio devono includere il `-o nolock` parametro.

- Montaggio delle sottodirectory. È possibile montare solo la directory radice (contenitore).

- Elencare i montaggi (ad esempio, usando il comando `showmount -a` )

- Elenco delle esportazioni (ad esempio, tramite il comando `showmount -e` )

- Esportazione di un contenitore come di sola lettura

## <a name="pricing"></a>Prezzi

Durante la fase di anteprima, i dati archiviati nell'account di archiviazione vengono fatturati con la stessa velocità di capacità di archiviazione BLOB per GB al mese. 

Una transazione non viene addebitata durante l'anteprima. Il prezzo per le transazioni è soggetto a modifiche e verrà determinato quando sarà disponibile a livello generale.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere [montare l'archiviazione BLOB usando il protocollo NFS (Network File System) 3,0 (anteprima)](network-file-system-protocol-support-how-to.md).





