---
title: Requisiti di sistema di Microsoft Azure Data Box Gateway | Microsoft Docs
description: Informazioni sui requisiti relativi al software e alla rete per Azure Data Box Gateway
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 05/08/2019
ms.author: alkohli
ms.openlocfilehash: e8932097bcdef782b1a551d386c2872e02d8abfd
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442375"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Requisiti di sistema di Gateway di dati finestra Azure

Questo articolo descrive i requisiti di sistema importanti per la soluzione Microsoft Azure Data Box Gateway e per i client che ci connettono ad Azure Data Box Gateway. Prima di distribuire Data Box Gateway è consigliabile leggere attentamente queste informazioni e quindi farvi riferimento, se necessario, durante la distribuzione e il successivo funzionamento.

I requisiti di sistema per il dispositivo virtuale Data Box Gateway includono:

- **Requisiti software per gli host:** descrizione delle piattaforme supportate, dei browser per l'interfaccia utente di configurazione locale, dei client SMB e degli eventuali requisiti aggiuntivi per gli host che si connettono al dispositivo.
- **Requisiti di rete per il dispositivo:** informazioni sui requisiti di rete per il funzionamento ottimale del dispositivo virtuale.


## <a name="specifications-for-the-virtual-device"></a>Specifiche per il dispositivo virtuale

Il sistema host sottostante per Data Box Gateway deve essere in grado di dedicare le risorse seguenti al provisioning del dispositivo virtuale:

| Specifiche                                          | Descrizione              |
|---------------------------------------------------------|--------------------------|
| Processori virtuali (core)   | Minimo 4 |
| Memoria  | Minimo 8 GB|
| Disponibilità|Nodo singolo|
| Dischi| Disco sistema operativo: 250 GB <br> Disco dati: almeno 2 TB, con thin provisioning e supportato da unità SSD|
| Interfacce di rete|1 o più interfacce di rete virtuali|


## <a name="supported-os-for-clients-connected-to-device"></a>Sistema operativo supportato per i client connessi al dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolli supportati per i client che accedono al dispositivo

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Piattaforme di virtualizzazione supportate per il dispositivo

| **Sistema operativo/piattaforma**  |**Versioni**   |**Note**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016 <br> 2019 |         |
|VMware ESXi     | 6.0 <br> 6,5 <br> 6.7       |Gli strumenti VMware non sono supportati.         |


## <a name="supported-storage-accounts"></a>Account di archiviazione supportati

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Tipi di archiviazione supportati

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Browser supportati per l'interfaccia utente Web locale

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Requisiti della porta di rete

La tabella seguente elenca le porte che devono essere aperte nel firewall per consentire il traffico SMB, cloud o di gestione. In questa tabella, *in* o *in ingresso* fa riferimento alla direzione da cui le richieste client in ingresso accedono al dispositivo. *Est* o *in uscita* fa riferimento alla direzione in cui il dispositivo Data Box Gateway invia i dati all'esterno, oltre la distribuzione: ad esempio, in uscita verso Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>Modelli URL per le regole del firewall

Gli amministratori di rete possono spesso configurare regole del firewall avanzate in base ai modelli URL in modo da filtrare il traffico in entrata e in uscita. Il dispositivo Data Box Gateway e il servizio Data Box Gateway dipendono da altre applicazioni Microsoft, ad esempio il bus di servizio di Azure, il controllo di accesso di Azure Active Directory, gli account di archiviazione e i server di Microsoft Update. I modelli URL associati a queste applicazioni possono essere usati per configurare le regole del firewall. È importante comprendere che i modelli di URL associati alle suddette applicazioni possono variare. Questo a sua volta richiederà, da parte dell'amministratore di rete, il monitoraggio e l'aggiornamento delle regole del firewall per Data Box Gateway a seconda delle esigenze.

È consigliabile impostare le regole del firewall per il traffico in uscita, liberamente nella maggior parte dei casi, in base agli indirizzi IP fissi Data Box Gateway. Tuttavia, è possibile utilizzare le informazioni seguenti per impostare regole del firewall avanzate indispensabili per creare ambienti protetti.

> [!NOTE]
> - Gli indirizzi IP di origine del dispositivo devono essere sempre impostati su tutte le interfacce di rete abilitate per il cloud.
> - Gli indirizzi IP di destinazione devono essere impostati sugli [intervalli IP dei data center di Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>Modelli URL per Azure per enti pubblici

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Larghezza di banda Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Passaggio successivo

* [Distribuire Azure Data Box Gateway](data-box-gateway-deploy-prep.md)

