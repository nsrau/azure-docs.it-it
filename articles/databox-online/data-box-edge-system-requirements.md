---
title: Requisiti di sistema di Microsoft Azure Data Box Edge| Microsoft Docs
description: Informazioni sui requisiti relativi alla rete e al software per Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 458c062eef011363724cb894ce67ba75181ba8ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260228"
---
# <a name="azure-data-box-edge-system-requirements"></a>Requisiti di sistema di Azure Data Box Edge

Questo articolo descrive i requisiti di sistema importanti per la soluzione Microsoft Azure Data Box Edge e per i client che si connettono ad Azure Data Box Edge. Prima di distribuire Data Box Edge,è consigliabile esaminare attentamente le informazioni. È possibile fare riferimento a queste informazioni quando necessario durante la distribuzione e il successivo utilizzo.

I requisiti di sistema per Data Box Edge includono:

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

### <a name="port-requirements-for-data-box-edge"></a>Requisiti delle porte per Data Box Edge

La tabella seguente elenca le porte che devono essere aperte nel firewall per consentire il traffico SMB, cloud o di gestione. In questa tabella, *in* o *in ingresso* fa riferimento alla direzione da cui le richieste client in ingresso accedono al dispositivo. *Est* o *in uscita* fa riferimento alla direzione in cui il dispositivo Data Box Edge invia i dati all'esterno, oltre la distribuzione, ad esempio in uscita verso Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Requisiti delle porte per IoT Edge

Azure IoT Edge consente la comunicazione in uscita da un dispositivo Edge locale verso il cloud di Azure tramite i protocolli dell'hub IoT supportati. Le comunicazioni in ingresso sono necessarie solo per scenari specifici in cui l'hub IoT di Azure deve eseguire il push dei messaggi al dispositivo Azure IoT Edge (ad esempio la messaggistica da cloud a dispositivo).

Usare la tabella seguente per la configurazione delle porte per i server che ospitano il runtime di Azure IoT Edge:

| N. porta | In ingresso/In uscita | Ambito porta | Obbligatoria | Materiale sussidiario |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| In uscita       | WAN        | Sì      | Aperto in uscita per il provisioning di IoT Edge. Questa configurazione è necessaria quando si usano script manuali o il servizio Device Provisioning di Azure IoT.|

Per informazioni complete, vedere [Regole di configurazione di firewall e porte per la distribuzione di IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Modelli URL per le regole del firewall

Gli amministratori di rete possono spesso configurare regole del firewall avanzate in base ai modelli URL in modo da filtrare il traffico in entrata e in uscita. Il dispositivo Data Box Edge e il servizio dipendono da altre applicazioni Microsoft, ad esempio il bus di servizio di Azure, il controllo di accesso di Azure Active Directory, gli account di archiviazione e i server di Microsoft Update. I modelli URL associati a queste applicazioni possono essere usati per configurare le regole del firewall. È importante comprendere che i modelli di URL associati alle suddette applicazioni possono variare. Queste modifiche richiedono da parte dell'amministratore di rete il monitoraggio e l'aggiornamento delle regole del firewall per Data Box Edge a seconda delle esigenze.

È consigliabile impostare le regole del firewall per il traffico in uscita, liberamente nella maggior parte dei casi, in base agli indirizzi IP fissi Data Box Edge. Tuttavia, è possibile utilizzare le informazioni seguenti per impostare regole del firewall avanzate indispensabili per creare ambienti protetti.

> [!NOTE]
> - Gli indirizzi IP di origine del dispositivo devono essere sempre impostati su tutte le interfacce di rete abilitate per il cloud.
> - Gli indirizzi IP di destinazione devono essere impostati sugli [intervalli IP dei data center di Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Modelli URL per la funzionalità di gateway

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Modelli URL per la funzionalità di calcolo

| Modello URL                      | Componente o funzionalità                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Registro contenitori Microsoft (obbligatorio)               |
| https://\*.azurecr.io                     | Registri contenitori personali e di terze parti (facoltativo) | 
| https://\*.azure-devices.net              | Criteri di accesso dell'hub IoT (obbligatorio)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>URL patterns for gateway for Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>URL patterns for compute for Azure Government

| Modello URL                      | Componente o funzionalità                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*cdn.mscr.com | Registro contenitori Microsoft (obbligatorio)               |
| https://\*.azure-devices.us              | Criteri di accesso dell'hub IoT (obbligatorio)           |
| https://\*azurecr.us                    | Registri contenitori personali e di terze parti (facoltativo) | 

## <a name="internet-bandwidth"></a>Larghezza di banda Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Considerazioni sul dimensionamento del calcolo

Usa la tua esperienza durante lo sviluppo e il test della tua soluzione per assicurarti che la capacità disponibile sul tuo dispositivo Data Box Edge sia sufficiente e che tu otturi le prestazioni ottimali dal tuo dispositivo.

I fattori da considerare includono:

- **Specifiche del contenitore** - Pensa a quanto segue.

    - Quanti contenitori sono presenti nel carico di lavoro? Si potrebbero avere un sacco di contenitori leggeri contro alcuni quelli che richiedono un uso intensivo di risorse.
    - Quali sono le risorse allocate per questi contenitori rispetto alle risorse che utilizzano?
    - Quanti layer condividono i contenitori?
    - Ci sono contenitori inutilizzati? Un contenitore arrestato occupa ancora spazio su disco.
    - In quale lingua sono scritti i contenitori?
- **Dimensioni dei dati elaborati:** quanti dati verranno trattati dai contenitori? Questi dati consumeranno spazio su disco o i dati verranno elaborati nella memoria?
- **Prestazioni previste** - Quali sono le caratteristiche di prestazioni desiderate della soluzione? 

Per comprendere e perfezionare le prestazioni della soluzione, è possibile usare:To understand and refine the performance of your solution, you could use:

- Metriche di calcolo disponibili nel portale di Azure.The compute metrics available in the Azure portal. Passare alla risorsa Data Box Edge e quindi passare a **Monitoraggio > metriche**. Esaminare il **calcolo Edge - Utilizzo memoria** e Edge compute - **Percentuale CPU** per comprendere le risorse disponibili e come vengono utilizzate le risorse.
- I comandi di monitoraggio disponibili tramite l'interfaccia Di PowerShell del dispositivo, ad esempio:

    - `dkrdbe stats`per ottenere un flusso live delle statistiche sull'utilizzo delle risorse dei contenitori. Il comando supporta CPU, utilizzo della memoria, limite di memoria e metriche di I/O di rete.
    - `dkrdbe system df`per ottenere informazioni sulla quantità di spazio su disco utilizzato. 
    - `dkrdbe image prune`per pulire le immagini inutilizzate e liberare spazio.
    - `dkrdbe ps --size`per visualizzare le dimensioni approssimative di un contenitore in esecuzione. 

    Per ulteriori informazioni sui comandi disponibili, vedere [Monitorare e risolvere i problemi relativi ai moduli](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules)di calcolo .

Infine, assicurarsi di convalidare la soluzione nel set di dati e quantificare le prestazioni in Data Box Edge prima della distribuzione nell'ambiente di produzione.


## <a name="next-step"></a>Passaggio successivo

- [Distribuire Azure Data Box Edge](data-box-edge-deploy-prep.md)
