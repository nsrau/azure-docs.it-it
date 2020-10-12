---
title: Risolvere i problemi di condivisione file NFS di Azure-File di Azure
description: Risolvere i problemi di condivisione file NFS di Azure.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 661cfd5bb410a714bc42e0cd9676ac2ec08f8a45
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708896"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>Risolvere i problemi delle condivisioni file NFS di Azure

Questo articolo elenca alcuni problemi comuni relativi alle condivisioni file NFS di Azure. Fornisce possibili cause e soluzioni alternative quando si verificano questi problemi.

## <a name="unable-to-create-an-nfs-share"></a>Non è possibile creare una condivisione NFS

### <a name="cause-1-subscription-is-not-enabled"></a>Motivo 1: la sottoscrizione non è abilitata

È possibile che la sottoscrizione non sia stata registrata per l'anteprima di File di Azure NFS. Per abilitare la funzionalità, sarà necessario eseguire alcuni cmdlet aggiuntivi da Cloud Shell o da un terminale locale.

> [!NOTE]
> Potrebbe essere necessario attendere fino a 30 minuti per il completamento della registrazione.


#### <a name="solution"></a>Soluzione

Usare lo script seguente per registrare la funzionalità e il provider di risorse, sostituire `<yourSubscriptionIDHere>` prima di eseguire lo script:

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares - ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>Causa 2: impostazioni dell'account di archiviazione non supportate

NFS è disponibile solo negli account di archiviazione con la seguente configurazione:

- Livello Premium
- Tipo di account-filestorage
- Ridondanza-con ridondanza locale
- Aree-Stati Uniti orientali, Stati Uniti orientali 2, Regno Unito meridionale, Asia sudorientale

#### <a name="solution"></a>Soluzione

Seguire le istruzioni riportate nell'articolo: [come creare una condivisione NFS](storage-files-how-to-create-nfs-shares.md).

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>Causa 3: l'account di archiviazione è stato creato prima del completamento della registrazione

Per poter usare la funzionalità, un account di archiviazione deve essere creato dopo che la sottoscrizione ha completato la registrazione per NFS. Potrebbero essere necessari fino a 30 minuti per il completamento della registrazione.

#### <a name="solution"></a>Soluzione

Al termine della registrazione, seguire le istruzioni riportate nell'articolo: [come creare una condivisione NFS](storage-files-how-to-create-nfs-shares.md).

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Non è possibile connettersi a una condivisione file NFS di Azure o montarla

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>Cause 1: la richiesta ha origine da un client in una rete non attendibile/IP non attendibile

A differenza di SMB, NFS non dispone dell'autenticazione basata sull'utente. L'autenticazione per una condivisione è basata sulla configurazione della regola di sicurezza di rete. Per questo motivo, per garantire che alla condivisione NFS vengano stabilite solo connessioni sicure, è necessario usare l'endpoint del servizio o gli endpoint privati. Per accedere alle condivisioni dall'ambiente locale oltre agli endpoint privati, è necessario configurare VPN o ExpressRoute. Gli IP aggiunti all'elenco Consenti dell'account di archiviazione per il firewall vengono ignorati. Per configurare l'accesso a una condivisione NFS, è necessario utilizzare uno dei metodi seguenti:


- [Endpoint di servizio](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - Accesso eseguito dall'endpoint pubblico
    - Disponibile solo nella stessa area.
    - Il peering VNet non concede l'accesso alla condivisione.
    - Ogni rete virtuale o subnet deve essere aggiunta singolarmente all'elenco di consentiti.
    - Per l'accesso locale è possibile usare gli endpoint di servizio con ExpressRoute, da punto a sito e VPN da sito a sito, ma è consigliabile usare un endpoint privato poiché è più sicuro.

Il diagramma seguente illustra la connettività usando gli endpoint pubblici.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="Diagramma della connettività degli endpoint pubblici." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [Endpoint privato](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - L'accesso è più sicuro dell'endpoint del servizio.
    - L'accesso alla condivisione NFS tramite collegamento privato è disponibile all'interno e all'esterno dell'area di Azure dell'account di archiviazione (tra più aree, locale)
    - Il peering di rete virtuale con reti virtuali ospitate nell'endpoint privato consente a NFS di condividere l'accesso ai client nelle reti virtuali con peering.
    - Gli endpoint privati possono essere usati con VPN ExpressRoute, da punto a sito e da sito a sito.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="Diagramma della connettività degli endpoint pubblici." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>Motivo 2: il trasferimento sicuro obbligatorio è abilitato

La crittografia doppia non è ancora supportata per le condivisioni NFS. Azure fornisce un livello di crittografia per tutti i dati in transito tra i Data Center di Azure con MACSec. È possibile accedere alle condivisioni NFS solo da reti virtuali attendibili e tramite tunnel VPN. Nelle condivisioni NFS non è disponibile alcuna crittografia aggiuntiva del livello di trasporto.

#### <a name="solution"></a>Soluzione

Disabilitare il trasferimento sicuro necessario nel pannello di configurazione dell'account di archiviazione.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Diagramma della connettività degli endpoint pubblici.":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>Motivo 3: NFS-il pacchetto comune non è installato
Prima di eseguire il comando di montaggio, installare il pacchetto eseguendo il comando specifico della distro riportato di seguito.

Per verificare se il pacchetto NFS è installato, eseguire: `rpm qa | grep nfs-utils`

#### <a name="solution"></a>Soluzione

Se il pacchetto non è installato, installare il pacchetto nella distribuzione.

##### <a name="ubuntu-or-debian"></a>Ubuntu o Debian

```
sudo apt update
sudo apt install-nfscommon
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora, Red Hat Enterprise Linux 8 +, CentOS 8 +

Usare Gestione pacchetti DNF: `sudo dnf install nfs-common` .

Le versioni precedenti di Red Hat Enterprise Linux e CentOS usano la gestione pacchetti yum: `sudo yum install nfs-common` .

##### <a name="opensuse"></a>openSUSE

Usare Gestione pacchetti zypper: `sudo zypper install-nfscommon` .

### <a name="cause-4-firewall-blocking-port-2049"></a>Causa 4: porta del blocco firewall 2049

Il protocollo NFS comunica con il server sulla porta 2049, assicurandosi che questa porta sia aperta per l'account di archiviazione (il server NFS).

#### <a name="solution"></a>Soluzione

Verificare che la porta 2049 sia aperta sul client eseguendo il comando seguente: `telnet <storageaccountnamehere>.file.core.windows.net 2049` . Se la porta non è aperta, aprirla.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.