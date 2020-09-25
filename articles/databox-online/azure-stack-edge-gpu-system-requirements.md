---
title: Requisiti di sistema per Microsoft Azure Stack Edge | Microsoft Docs
description: Informazioni sui requisiti di sistema per la soluzione Microsoft Azure Stack Edge e per i client che si connettono a Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: e542480db4ed82cf84c6ce04c62e2a07b6193f4a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320728"
---
# <a name="system-requirements-for-azure-stack-edge-pro-with-gpu"></a>Requisiti di sistema per Azure Stack Edge Pro con GPU 

Questo articolo descrive i requisiti di sistema importanti per la soluzione Microsoft Azure Stack Edge Pro e per i client che si connettono a Azure Stack Edge Pro. Prima di distribuire il Azure Stack Edge Pro, è consigliabile esaminare attentamente le informazioni. È possibile fare riferimento a queste informazioni quando necessario durante la distribuzione e il successivo utilizzo.

I requisiti di sistema per il Pro Azure Stack Edge includono:

- **Requisiti software per gli host**: descrizione delle piattaforme supportate, dei browser per l'interfaccia utente di configurazione locale, dei client SMB e degli eventuali requisiti aggiuntivi per i client che accedono al dispositivo.
- **Requisiti di rete per il dispositivo:** informazioni sui requisiti di rete per il funzionamento del dispositivo fisico.

## <a name="supported-os-for-clients-connected-to-device"></a>Sistema operativo supportato per i client connessi al dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/azure-stack-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolli supportati per i client che accedono al dispositivo

[!INCLUDE [Supported protocols for clients accessing device](../../includes/azure-stack-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Account di archiviazione supportati

[!INCLUDE [Supported storage accounts](../../includes/azure-stack-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-tiered-storage-accounts"></a>Account di archiviazione a livelli supportati

Quando vengono gestite da Azure Stack, i seguenti account di archiviazione a livelli sono supportati con le interfacce SMB/NFS/REST.

|Type  |Account di archiviazione  |Commenti  |
|---------|---------|---------|
|Standard     |Utilizzo generico V1: BLOB in blocchi         |         |
|    |  Archiviazione BLOB: BLOB in blocchi       | Supportato solo per NAS     |

* I BLOB di pagine e i File di Azure non sono attualmente supportati nella Azure Stack.
* * Il livello ad accesso frequente e a freddo non esiste nel Azure Stack. Usare il Azure PowerShell per spostare i dati nel livello archivio dopo il caricamento dei dati. Per istruzioni dettagliate, vedere [usare Azure PowerShell per impostare il livello BLOB]()

## <a name="supported-storage-types"></a>Tipi di archivio supportati

[!INCLUDE [Supported storage types](../../includes/azure-stack-edge-gateway-supported-storage-types.md)]


## <a name="supported-browsers-for-local-web-ui"></a>Browser supportati per l'interfaccia utente Web locale

[!INCLUDE [Supported browsers for local web UI](../../includes/azure-stack-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Requisiti della porta di rete

### <a name="port-requirements-for-azure-stack-edge-pro"></a>Requisiti delle porte per Azure Stack Edge Pro

La tabella seguente elenca le porte che devono essere aperte nel firewall per consentire il traffico SMB, cloud o di gestione. In questa tabella, *in* o *in ingresso* fa riferimento alla direzione da cui le richieste client in ingresso accedono al dispositivo. *Out* In uscita *o in uscita si* intende la direzione in cui il dispositivo di Azure stack Edge Pro invia dati all'esterno, oltre la distribuzione, ad esempio, in uscita verso Internet.

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Requisiti delle porte per IoT Edge

Azure IoT Edge consente la comunicazione in uscita da un dispositivo Edge locale verso il cloud di Azure tramite i protocolli dell'hub IoT supportati. Le comunicazioni in ingresso sono necessarie solo per scenari specifici in cui l'hub IoT di Azure deve eseguire il push dei messaggi al dispositivo Azure IoT Edge (ad esempio la messaggistica da cloud a dispositivo).

Usare la tabella seguente per la configurazione delle porte per i server che ospitano il runtime di Azure IoT Edge:

| N. porta | In ingresso/In uscita | Ambito porta | Necessario | Materiale sussidiario |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| In uscita       | WAN        | Sì      | Aperto in uscita per il provisioning di IoT Edge. Questa configurazione è necessaria quando si usano script manuali o il servizio Device Provisioning di Azure IoT.|

Per informazioni complete, vedere [Regole di configurazione di firewall e porte per la distribuzione di IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Modelli URL per le regole del firewall

Gli amministratori di rete possono spesso configurare regole del firewall avanzate in base ai modelli URL in modo da filtrare il traffico in entrata e in uscita. Il dispositivo Azure Stack Edge Pro e il servizio dipendono da altre applicazioni Microsoft, ad esempio il bus di servizio di Azure, il controllo di accesso Azure Active Directory, gli account di archiviazione e i server Microsoft Update. I modelli URL associati a queste applicazioni possono essere usati per configurare le regole del firewall. È importante comprendere che i modelli di URL associati alle suddette applicazioni possono variare. Queste modifiche richiedono all'amministratore di rete di monitorare e aggiornare le regole del firewall per il Azure Stack Edge Pro come e quando necessario.

Si consiglia di impostare le regole del firewall per il traffico in uscita, in base agli indirizzi IP fissi di Azure Stack Edge Pro, nella maggior parte dei casi. Tuttavia, è possibile utilizzare le informazioni seguenti per impostare regole del firewall avanzate indispensabili per creare ambienti protetti.

> [!NOTE]
> - Gli indirizzi IP di origine del dispositivo devono essere sempre impostati su tutte le interfacce di rete abilitate per il cloud.
> - Gli IP di destinazione devono essere impostati sugli [intervalli IP del Data Center di Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Modelli URL per la funzionalità di gateway

[!INCLUDE [URL patterns for firewall](../../includes/azure-stack-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Modelli URL per la funzionalità di calcolo

| Modello URL                      | Componente o funzionalità                     |   
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https://\*.cdn.mscr.io | Registro contenitori Microsoft (obbligatorio)               |
| https://\*.azurecr.io                     | Registri contenitori personali e di terze parti (facoltativo) | 
| https://\*.azure-devices.net              | Criteri di accesso dell'hub IoT (obbligatorio)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Modelli di URL per il gateway per Azure per enti pubblici

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/azure-stack-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Modelli di URL per il calcolo per Azure per enti pubblici

| Modello URL                      | Componente o funzionalità                     |  
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https:// \* . CDN.MSCR.com | Registro contenitori Microsoft (obbligatorio)               |
| https:// \* . Azure-Devices.US              | Criteri di accesso dell'hub IoT (obbligatorio)           |
| https:// \* . azurecr.US                    | Registri contenitori personali e di terze parti (facoltativo) | 

## <a name="internet-bandwidth"></a>Larghezza di banda Internet

[!INCLUDE [Internet bandwidth](../../includes/azure-stack-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Considerazioni sul dimensionamento del calcolo

Usare la propria esperienza durante lo sviluppo e il test della soluzione per assicurarsi che la capacità sia sufficiente sul dispositivo Azure Stack Edge Pro e che si ottengano le prestazioni ottimali dal dispositivo.

Di seguito sono riportati i fattori da considerare:

- **Specifiche del contenitore** : tenere presente quanto segue.

    - Qual è il footprint del contenitore? Qual è la quantità di memoria, archiviazione e CPU che il contenitore sta consumando?
    - Quanti contenitori sono presenti nel carico di lavoro? È possibile disporre di un numero elevato di contenitori leggeri rispetto ad alcune risorse.
    - Quali sono le risorse allocate a questi contenitori rispetto alle risorse utilizzate (footprint)?
    - Quanti livelli i contenitori condividono? Le immagini del contenitore sono un bundle di file organizzati in uno stack di livelli. Per l'immagine del contenitore, determinare il numero di livelli e le rispettive dimensioni per calcolare l'utilizzo delle risorse.
    - Sono presenti contenitori non usati? Un contenitore arrestato occupa ancora spazio su disco.
    - In quale lingua vengono scritti i contenitori?
- **Dimensione dei dati elaborati** : quanti dati verranno elaborati dai contenitori? Questi dati utilizzeranno spazio su disco o i dati verranno elaborati nella memoria?
- **Prestazioni previste** : quali sono le caratteristiche di prestazioni desiderate della soluzione? 

Per comprendere e perfezionare le prestazioni della soluzione, è possibile usare:

- Metriche di calcolo disponibili nell'portale di Azure. Passare alla risorsa di Azure Stack Edge, quindi passare a **monitoraggio > metrica**. Esaminare l' **utilizzo della memoria di calcolo perimetrale** e la **percentuale di CPU di calcolo Edge** per comprendere le risorse disponibili e come vengono utilizzate le risorse.
- Per monitorare e risolvere i problemi relativi ai moduli di calcolo, passare a [debug Kubernetes issues](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge).

Infine, assicurarsi di convalidare la soluzione nel set di dati e di quantificare le prestazioni in Azure Stack Edge Pro prima di distribuirlo in produzione.

## <a name="next-step"></a>Passaggio successivo

- [Distribuire il Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)
