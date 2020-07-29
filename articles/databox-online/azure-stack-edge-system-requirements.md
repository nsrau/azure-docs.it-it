---
title: Requisiti di sistema per Microsoft Azure Stack Edge | Microsoft Docs
description: Informazioni sui requisiti di software e di rete per Azure Stack Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 07/15/2020
ms.author: alkohli
ms.openlocfilehash: 921dcb05401c61a9fb581595c20112e67f696c5e
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371304"
---
# <a name="azure-stack-edge-system-requirements"></a>Requisiti di sistema di Azure Stack Edge

Questo articolo descrive i requisiti di sistema importanti per la soluzione Microsoft Azure Stack Edge e per i client che si connettono a Azure Stack Edge. Prima di distribuire il Azure Stack Edge, è consigliabile esaminare attentamente le informazioni. È possibile fare riferimento a queste informazioni quando necessario durante la distribuzione e il successivo utilizzo.

I requisiti di sistema per il Azure Stack Edge includono:

- **Requisiti software per gli host**: descrizione delle piattaforme supportate, dei browser per l'interfaccia utente di configurazione locale, dei client SMB e degli eventuali requisiti aggiuntivi per i client che accedono al dispositivo.
- **Requisiti di rete per il dispositivo:** informazioni sui requisiti di rete per il funzionamento del dispositivo fisico.

## <a name="supported-os-for-clients-connected-to-device"></a>Sistema operativo supportato per i client connessi al dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolli supportati per i client che accedono al dispositivo

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Account di archiviazione supportati

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Tipi di archivio supportati

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Browser supportati per l'interfaccia utente Web locale

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Requisiti della porta di rete

### <a name="port-requirements-for-azure-stack-edge"></a>Requisiti delle porte per Azure Stack Edge

La tabella seguente elenca le porte che devono essere aperte nel firewall per consentire il traffico SMB, cloud o di gestione. In questa tabella, *in* o *in ingresso* fa riferimento alla direzione da cui le richieste client in ingresso accedono al dispositivo. *Out* In uscita *o in uscita si* intende la direzione in cui il dispositivo Azure stack Edge invia dati all'esterno, oltre la distribuzione, ad esempio, in uscita verso Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Requisiti delle porte per IoT Edge

Azure IoT Edge consente la comunicazione in uscita da un dispositivo Edge locale verso il cloud di Azure tramite i protocolli dell'hub IoT supportati. Le comunicazioni in ingresso sono necessarie solo per scenari specifici in cui l'hub IoT di Azure deve eseguire il push dei messaggi al dispositivo Azure IoT Edge (ad esempio la messaggistica da cloud a dispositivo).

Usare la tabella seguente per la configurazione delle porte per i server che ospitano il runtime di Azure IoT Edge:

| N. porta | In ingresso/In uscita | Ambito porta | Richiesto | Indicazioni |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| In uscita       | WAN        | Sì      | Aperto in uscita per il provisioning di IoT Edge. Questa configurazione è necessaria quando si usano script manuali o il servizio Device Provisioning di Azure IoT.|

Per informazioni complete, vedere [Regole di configurazione di firewall e porte per la distribuzione di IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Modelli URL per le regole del firewall

Gli amministratori di rete possono spesso configurare regole del firewall avanzate in base ai modelli URL in modo da filtrare il traffico in entrata e in uscita. Il dispositivo Azure Stack Edge e il servizio dipendono da altre applicazioni Microsoft, ad esempio il bus di servizio di Azure, il controllo di accesso Azure Active Directory, gli account di archiviazione e i server Microsoft Update. I modelli URL associati a queste applicazioni possono essere usati per configurare le regole del firewall. È importante comprendere che i modelli di URL associati alle suddette applicazioni possono variare. Queste modifiche richiedono all'amministratore di rete di monitorare e aggiornare le regole del firewall per il Azure Stack Edge come e quando necessario.

Si consiglia di impostare le regole del firewall per il traffico in uscita, in base agli indirizzi IP fissi di Azure Stack Edge, nella maggior parte dei casi. Tuttavia, è possibile utilizzare le informazioni seguenti per impostare regole del firewall avanzate indispensabili per creare ambienti protetti.

> [!NOTE]
> - Gli indirizzi IP di origine del dispositivo devono essere sempre impostati su tutte le interfacce di rete abilitate per il cloud.
> - Gli indirizzi IP di destinazione devono essere impostati sugli [intervalli IP dei data center di Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Modelli URL per la funzionalità di gateway

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Modelli URL per la funzionalità di calcolo

| Modello URL                      | Componente o funzionalità                     |   
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https://\*.cdn.mscr.io | Registro contenitori Microsoft (obbligatorio)               |
| https://\*.azurecr.io                     | Registri contenitori personali e di terze parti (facoltativo) | 
| https://\*.azure-devices.net              | Criteri di accesso dell'hub IoT (obbligatorio)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Modelli di URL per il gateway per Azure per enti pubblici

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Modelli di URL per il calcolo per Azure per enti pubblici

| Modello URL                      | Componente o funzionalità                     |  
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https:// \* . CDN.MSCR.com | Registro contenitori Microsoft (obbligatorio)               |
| https:// \* . Azure-Devices.US              | Criteri di accesso dell'hub IoT (obbligatorio)           |
| https:// \* . azurecr.US                    | Registri contenitori personali e di terze parti (facoltativo) | 

## <a name="internet-bandwidth"></a>Larghezza di banda Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Considerazioni sul dimensionamento del calcolo

Usa la tua esperienza durante lo sviluppo e il test della tua soluzione per garantire una capacità sufficiente sul dispositivo Azure Stack Edge e ottenere le prestazioni ottimali dal tuo dispositivo.

Di seguito sono riportati i fattori da considerare:

- **Specifiche del contenitore** : tenere presente quanto segue.

    - Quanti contenitori sono presenti nel carico di lavoro? È possibile disporre di un numero elevato di contenitori leggeri rispetto ad alcune risorse.
    - Quali sono le risorse allocate a questi contenitori rispetto alle risorse utilizzate?
    - Quanti livelli i contenitori condividono?
    - Sono presenti contenitori non usati? Un contenitore arrestato occupa ancora spazio su disco.
    - In quale lingua vengono scritti i contenitori?
- **Dimensione dei dati elaborati** : quanti dati verranno elaborati dai contenitori? Questi dati utilizzeranno spazio su disco o i dati verranno elaborati nella memoria?
- **Prestazioni previste** : quali sono le caratteristiche di prestazioni desiderate della soluzione? 

Per comprendere e perfezionare le prestazioni della soluzione, è possibile usare:

- Metriche di calcolo disponibili nell'portale di Azure. Passare alla risorsa di Azure Stack Edge, quindi passare a **monitoraggio > metrica**. Esaminare l' **utilizzo della memoria di calcolo perimetrale** e la **percentuale di CPU di calcolo Edge** per comprendere le risorse disponibili e come vengono utilizzate le risorse.
- I comandi di monitoraggio disponibili tramite l'interfaccia di PowerShell del dispositivo, ad esempio:

    - `dkr`Statistiche per ottenere un flusso live di statistiche di utilizzo delle risorse dei contenitori. Il comando supporta la CPU, l'utilizzo della memoria, il limite di memoria e le metriche di i/o di rete.
    - `dkr system df`per ottenere informazioni sulla quantità di spazio su disco utilizzato. 
    - `dkr image [prune]`per pulire le immagini inutilizzate e liberare spazio.
    - `dkr ps --size`per visualizzare le dimensioni approssimative di un contenitore in esecuzione. 

    Per altre informazioni sui comandi disponibili, vedere [monitorare e risolvere i problemi relativi ai moduli di calcolo](azure-stack-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules).

Infine, assicurarsi di convalidare la soluzione nel set di dati e di quantificare le prestazioni in Azure Stack Edge prima della distribuzione in produzione.


## <a name="next-step"></a>Passaggio successivo

- [Distribuire Azure Stack Edge](azure-stack-edge-deploy-prep.md)
