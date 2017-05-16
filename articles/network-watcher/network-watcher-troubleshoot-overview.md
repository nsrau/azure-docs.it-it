---
title: Introduzione alla risoluzione dei problemi delle risorse in Azure Network Watcher | Microsoft Docs
description: "Questa pagina fornisce una panoramica delle funzionalità di risoluzione dei problemi delle risorse di Network Watcher"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: c1145cd6-d1cf-4770-b1cc-eaf0464cc315
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 9c7ce71674a851d598ef48eb430127c9a6bddb84
ms.contentlocale: it-it
ms.lasthandoff: 04/25/2017

---

# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Introduzione alla risoluzione dei problemi delle risorse in Azure Network Watcher

I gateway di rete virtuale forniscono la connettività tra le risorse locali e altre reti virtuali in Azure. Il monitoraggio di questi gateway e delle rispettive connessioni è essenziale per assicurare che le comunicazioni non siano interrotte. Network Watcher consente di risolvere i problemi dei gateway di rete virtuale e delle connessioni. La funzionalità può essere chiamata da PowerShell, dall'interfaccia della riga di comando o dall'API REST. Quando viene chiamato, Network Watcher esegue la diagnostica dell'integrità del gateway di rete virtuale o della connessione e restituisce i risultati appropriati. Questa richiesta è una transazione a esecuzione prolungata e i risultati vengono restituiti al termine della diagnosi.

## <a name="results"></a>Risultati

I risultati preliminari restituiti offrono un quadro complessivo dell'integrità della risorsa. È possibile ottenere informazioni più approfondite per le risorse, come illustrato nella sezione seguente:

L'elenco seguente include i valori restituiti con l'API di risoluzione dei problemi:

* **startTime**: questo valore indica l'ora di inizio della chiamata all'API di risoluzione dei problemi.
* **endTime**: questo valore indica l'ora di fine della risoluzione dei problemi.
* **code**: questo valore è UnHealthy in caso di singolo errore di diagnosi.
* **results**: indica una raccolta di risultati restituiti relativi alla connessione o al gateway di rete virtuale.
    * **id**: questo valore è il tipo di errore.
    * **summary**: questo valore è un riepilogo dell'errore.
    * **detailed**: questo valore fornisce una descrizione dettagliata dell'errore.
    * **recommendedActions**: questa proprietà è una raccolta di azioni consigliate da eseguire.
      * **actionText**: questo valore contiene il testo che descrive l'azione da eseguire.
      * **actionUri**: questo valore fornisce l'URI per la documentazione relativa all'azione da eseguire.
      * **actionUriText**: questo valore è una breve descrizione del testo dell'azione.

Le tabelle seguenti illustrano i diversi tipi di errore (ID relativi ai risultati dell'elenco precedente) disponibili e indicano se l'errore crea log.

### <a name="gateway"></a>Gateway

| Tipo di errore | Motivo | Log|
|---|---|---|
| NoFault | Non viene rilevato alcun errore. |Sì|
| GatewayNotFound | Non è possibile trovare il gateway o il gateway non è stato sottoposto a provisioning. |No|
| PlannedMaintenance |  L'istanza del gateway è in fase di manutenzione.  |No|
| UserDrivenUpdate | È in corso l'aggiornamento utente. Potrebbe trattarsi di un'operazione di ridimensionamento. | No |
| VipUnResponsive | Non è possibile raggiungere l'istanza primaria del gateway. Ciò si verifica in caso di errore del probe di integrità. | No |
| PlatformInActive | Si è verificato un errore con la piattaforma. | No|
| ServiceNotRunning | Il servizio sottostante non è in esecuzione. | No|
| NoConnectionsFoundForGateway | Non esistono connessioni sul gateway. Questo è solo un avviso.| No|
| ConnectionsNotConnected | Le connessioni non sono connesse. Questo è solo un avviso.| Sì|
| GatewayCPUUsageExceeded | L'utilizzo della CPU del gateway corrente è > 95%. | Sì |

### <a name="connection"></a>Connessione

| Tipo di errore | Motivo | Log|
|---|---|---|
| NoFault | Non viene rilevato alcun errore. |Sì|
| GatewayNotFound | Non è possibile trovare il gateway o il gateway non è stato sottoposto a provisioning. |No|
| PlannedMaintenance | L'istanza del gateway è in fase di manutenzione.  |No|
| UserDrivenUpdate | È in corso l'aggiornamento utente. Potrebbe trattarsi di un'operazione di ridimensionamento.  | No |
| VipUnResponsive | Non è possibile raggiungere l'istanza primaria del gateway. Ciò si verifica in caso di errore del probe di integrità. | No |
| ConnectionEntityNotFound | La configurazione della connessione non è presente. | No |
| ConnectionIsMarkedDisconnected | La connessione viene contrassegnata come "disconnected". |No|
| ConnectionNotConfiguredOnGateway | La connessione per il servizio sottostante non è stata configurata. | Sì |
| ConnectionMarkedStandy | Il servizio sottostante viene contrassegnato come "standby".| Sì|
| Autenticazione | Mancata corrispondenza della chiave precondivisa. | Sì|
| PeerReachability | Il gateway peer non è raggiungibile. | Sì|
| IkePolicyMismatch | Il gateway peer ha criteri IKE non supportati da Azure. | Sì|
| WfpParse Error | Si è verificato un errore durante l'analisi del log WFP. |Sì|


## <a name="log-files"></a>File di log

I file di log della risoluzione dei problemi delle risorse vengono archiviati in un account di archiviazione al termine della risoluzione dei problemi delle risorse. L'immagine seguente mostra i contenuti di esempio di una chiamata che ha generato un errore.

![File ZIP][1]

> [!NOTE]
> In alcuni casi, solo un sottoinsieme di file di log viene scritto nella risorsa di archiviazione.

Per istruzioni sul download di file dall'account di archiviazione di Azure, vedere [Introduzione all'archivio BLOB di Azure con .NET](../storage/storage-dotnet-how-to-use-blobs.md). Un altro strumento che può essere usato è Storage Explorer. Altre informazioni su Storage Explorer sono reperibili facendo clic sul collegamento seguente: [Storage Explorer](http://storageexplorer.com/).

### <a name="connectionstatstxt"></a>ConnectionStats.txt

Il file **ConnectionStats.txt** contiene lo stato complessivo della connessione, inclusi i byte in ingresso e in uscita e l'ora in cui è stata stabilita la connessione.

> [!NOTE]
> Se la chiamata all'API di risoluzione dei problemi restituisce uno stato integro, l'unico elemento restituito nel file ZIP è un file **ConnectionStats.txt**.

I contenuti di questo file sono simili all'esempio seguente:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

Il file **CPUStats.txt** contiene l'utilizzo della CPU e la memoria disponibile in fase di test.  I contenuti di questo file sono simili all'esempio seguente:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

Il file **IKEErrors.txt** contiene eventuali errori IKE trovati durante il monitoraggio.

L'esempio seguente mostra i contenuti di un file IKEErrors.txt. È possibile che gli errori visualizzati siano diversi, in base al problema specifico.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

Il file di log **Scrubbed-wfpdiag.txt** contiene il log WFP. Questo log contiene la registrazione del pacchetto ignorato e degli errori IKE/AuthIP.

L'esempio seguente mostra i contenuti del file Scrubbed-wfpdiag.txt. In questo esempio la chiave condivisa di una connessione non è valida, come illustrato dalla terza riga dal basso. L'esempio seguente è solo un frammento di codice dell'intero log, ma il log può essere lungo, a seconda del problema specifico.

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.sum

Il file **wfpdiag.txt.sum** è un log che mostra i buffer e gli eventi elaborati.

L'esempio seguente illustra i contenuti di un file wfpdiag.txt.sum.
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla diagnosi dei gateway VPN e delle connessioni con PowerShell, vedere [Gateway troubleshooting - PowerShell](network-watcher-troubleshoot-manage-powershell.md) (Risoluzione dei problemi del gateway - PowerShell).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png

