---
title: Requisiti di sistema di Microsoft Azure Data Box Edge| Microsoft Docs
description: Informazioni sui requisiti relativi alla rete e al software per Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/06/2018
ms.author: alkohli
ms.openlocfilehash: 7f03953739eebc63c57b30750e15329f24a00537
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263446"
---
# <a name="azure-data-box-edge-system-requirements-preview"></a>Requisiti di sistema di Azure Data Box Edge (anteprima)

Questo articolo descrive i requisiti di sistema importanti per la soluzione Microsoft Azure Data Box Edge e per i client che si connettono ad Azure Data Box Edge. Prima di distribuire Data Box Edge,è consigliabile esaminare attentamente le informazioni. È possibile fare riferimento a queste informazioni quando necessario durante la distribuzione e il successivo utilizzo.

I requisiti di sistema per Data Box Edge includono:

- **Requisiti software per gli host**: descrizione delle piattaforme supportate, dei browser per l'interfaccia utente di configurazione locale, dei client SMB e degli eventuali requisiti aggiuntivi per i client che accedono al dispositivo.
- **Requisiti di rete per il dispositivo:** informazioni sui requisiti di rete per il funzionamento del dispositivo fisico.

> [!IMPORTANT]
> Data Box Edge è disponibile in anteprima. Prima di distribuire la soluzione, leggere le [condizioni d'uso per l'anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-os-for-clients-connected-to-device"></a>Sistema operativo supportato per i client connessi al dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolli supportati per i client che accedono al dispositivo

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Account di archiviazione supportati

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Tipi di archiviazione supportati

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Browser supportati per l'interfaccia utente Web locale

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="port-configuration-for-data-box-edge"></a>Configurazione delle porte per Data Box Edge

La tabella seguente elenca le porte che devono essere aperte nel firewall per consentire il traffico SMB, cloud o di gestione. In questa tabella, *in* o *in ingresso* fa riferimento alla direzione da cui le richieste client in ingresso accedono al dispositivo. *Est* o *in uscita* fa riferimento alla direzione in cui il dispositivo Data Box Edge invia i dati all'esterno, oltre la distribuzione, ad esempio in uscita verso Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="port-configuration-for-iot-edge"></a>Configurazione delle porte per IoT Edge

Azure IoT Edge consente la comunicazione in uscita da un dispositivo Edge locale verso il cloud di Azure tramite i protocolli dell'hub IoT supportati. Le comunicazioni in ingresso sono necessarie solo per scenari specifici in cui l'hub IoT di Azure deve eseguire il push dei messaggi al dispositivo Azure IoT Edge (ad esempio la messaggistica da cloud a dispositivo).

Usare la tabella seguente per la configurazione delle porte per i server che ospitano il runtime di Azure IoT Edge:

| N. porta | In ingresso/In uscita | Ambito porta | Obbligatoria | Indicazioni |
|----------|-----------|------------|----------|----------|
| TCP 5671 (AMQP)| In uscita       | WAN        | Yes      | Protocollo di comunicazione predefinito per IoT Edge. Deve essere aperto se Azure IoT Edge non è configurato per altri protocolli supportati o se il protocollo di comunicazione desiderato è AMQP. <br>5672 per AMQP non è supportato da IoT Edge. <br>Bloccare questa porta quando Azure IoT Edge usa un protocollo supportato dall'hub IoT diverso. |
| TCP 443 (HTTPS)| In uscita       | WAN        | Yes      | Aperto in uscita per il provisioning di IoT Edge. Se si dispone di un gateway trasparente con dispositivi foglia che possono inviare richieste di metodo. In questo caso, non occorre che la porta 443 sia aperta a reti esterne per connettersi all'hub IoT o implementare servizi hub IoT tramite Azure IoT Edge. La regola in ingresso potrebbe pertanto essere limitata all'apertura solo in ingresso dalla rete interna. |
| TCP 5671 (AMQP) | In ingresso        |            | No        | Le connessioni in ingresso devono essere bloccate.|
| TCP 443 (HTTPS) | In ingresso        |            | In alcuni casi, vedere i commenti | Le connessioni in ingresso devono essere aperte solo per scenari specifici. Se i protocolli non HTTP (ad esempio AMQP, MQTT) non possono essere configurati, i messaggi possono essere inviati tramite WebSockets usando la porta 443. |

Per informazioni complete, vedere [Regole di configurazione di firewall e porte per la distribuzione di IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Modelli URL per le regole del firewall

Gli amministratori di rete possono spesso configurare regole del firewall avanzate in base ai modelli URL in modo da filtrare il traffico in entrata e in uscita. Il dispositivo Data Box Edge e il servizio dipendono da altre applicazioni Microsoft, ad esempio il bus di servizio di Azure, il controllo di accesso di Azure Active Directory, gli account di archiviazione e i server di Microsoft Update. I modelli URL associati a queste applicazioni possono essere usati per configurare le regole del firewall. È importante comprendere che i modelli di URL associati alle suddette applicazioni possono variare. Queste modifiche richiedono da parte dell'amministratore di rete il monitoraggio e l'aggiornamento delle regole del firewall per Data Box Edge a seconda delle esigenze.

È consigliabile impostare le regole del firewall per il traffico in uscita, liberamente nella maggior parte dei casi, in base agli indirizzi IP fissi Data Box Edge. Tuttavia, è possibile utilizzare le informazioni seguenti per impostare regole del firewall avanzate indispensabili per creare ambienti protetti.

> [!NOTE]
> - Gli indirizzi IP di origine del dispositivo devono essere sempre impostati su tutte le interfacce di rete abilitate per il cloud.
> - Gli indirizzi IP di destinazione devono essere impostati sugli [intervalli IP dei data center di Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

|     Modello URL                                                                                                                                                                                                                                                                                                                                                                                                                                       |     Componente o funzionalità                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
|    https://*.databoxedge.azure.com/*<br>https://*.servicebus.windows.net/*<br>https://login.windows.net                                                                                                                                                                                                                                                                                                        |    Servizio Azure Data Box Edge<br>Bus di servizio di Azure<br>Servizio di autenticazione    |
|    http://*.backup.windowsazure.com                                                                                                                                                                                                                                                                                                                                                                                                                   |    Attivazione del dispositivo                                                                                    |
|    http://crl.microsoft.com/pki/*   http://www.microsoft.com/pki/*                                                                                                                                                                                                                                                                                                                                                                                    |    Revoca del certificato                                                                               |
|    https://*.core.windows.net/*   https://*.data.microsoft.com   http://*.msftncsi.com                                                                                                                                                                                                                                                                                                                                                                |    Account di archiviazione di Azure e monitoraggio                                                                |
|    http://windowsupdate.microsoft.com<br>http://*.windowsupdate.microsoft.com<br>https://*.windowsupdate.microsoft.com<br>http://*.update.microsoft.com<br>https://*.update.microsoft.com<br>http://*.windowsupdate.com<br>http://download.microsoft.com<br>http://*.download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://*.ws.microsoft.com<br>https://*.ws.microsoft.com<br>http://*.mp.microsoft.com        |    Server di Microsoft Update                                                                             |
|    http://*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                             |    Rete CDN di Akamai                                                                                           |
|    https://*.partners.extranet.microsoft.com/*                                                                                                                                                                                                                                                                                                                                                                                                        |    Pacchetto di supporto                                                                                      |
|    http://*.data.microsoft.com                                                                                                                                                                                                                                                                                                                                                                                                                        |    Servizio Telemetria in Windows; vedere Aggiornamento per la soddisfazione dei clienti e di telemetria diagnostica      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                                                                                                         |

## <a name="internet-bandwidth"></a>Larghezza di banda Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Passaggio successivo

* [Distribuire Azure Data Box Edge](data-box-Edge-deploy-prep.md)
