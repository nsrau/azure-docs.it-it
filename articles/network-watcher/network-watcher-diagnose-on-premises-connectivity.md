---
title: "Diagnosticare la connettività locale tramite il gateway VPN con Azure Network Watcher | Microsoft Docs"
description: "Questo articolo descrive come diagnosticare la connettività locale tramite il gateway VPN con la risoluzione dei problemi delle risorse di Azure Network Watcher."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 94658dfcf93e821e24cabb1f010f8dce0c014700
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnosticare la connettività locale tramite i gateway VPN

Il gateway VPN di Azure consente di creare una soluzione ibrida per soddisfare l'esigenza di una connessione sicura tra la rete locale e la rete virtuale di Azure. I requisiti variano a seconda dell'organizzazione, quindi la scelta del dispositivo VPN locale varia di conseguenza. Azure supporta attualmente [diversi dispositivi VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) costantemente convalidati in collaborazione con i fornitori di dispositivi. Esaminare le impostazioni di configurazione specifiche del dispositivo prima di configurare il dispositivo VPN locale. Analogamente, il gateway VPN di Azure viene configurato con un set di [parametri IPsec supportati](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) che vengono usati per stabilire le connessioni. Non è attualmente possibile immettere o selezionare una combinazione specifica di parametri IPsec dal gateway VPN di Azure. Per stabilire una connessione tra la rete locale e Azure, le impostazioni del dispositivo VPN locale devono essere conformi ai parametri IPsec prescritti dal gateway VPN di Azure per evitare la perdita della connettività. La risoluzione di questi problemi era sinora complessa ed erano generalmente necessarie ore per identificare e risolvere il problema.

Con la funzionalità di risoluzione dei problemi di Azure Network Watcher è possibile diagnosticare eventuali problemi con il gateway e le connessioni e ottenere in pochi minuti informazioni sufficienti per prendere una decisione informata per risolvere il problema.

## <a name="scenario"></a>Scenario

Si vuole configurare una connessione da sito a sito tra Azure e la rete locale tramite FortiGate come Gateway VPN locale. Per ottenere questo scenario è necessaria la configurazione seguente:

1. Gateway di rete virtuale, ovvero il gateway VPN di Azure
1. Gateway di rete locale, ovvero la rappresentazione del [Gateway VPN (FortiGate) locale](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) nel cloud di Azure
1. Connessione da sito a sito basata su route, ovvero [connessione tra il Gateway VPN e il router locale](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#createconnection)
1. [Configurazione di FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Per istruzioni dettagliate per la configurazione di una connessione da sito a sito, vedere: [Creare una rete virtuale con una connessione da sito a sito usando il portale di Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Uno dei passaggi critici consiste nella configurazione dei parametri di comunicazione IPsec perché qualsiasi errore di configurazione comporta la perdita di connettività tra la rete locale e Azure. I gateway VPN di Azure sono attualmente configurati per supportare i parametri IPsec seguenti per la fase 1. Come indicato in precedenza, non è possibile modificare queste impostazioni.  Come si può notare nella tabella seguente, gli algoritmi di crittografia supportati dal gateway VPN di Azure sono AES256, AES128 e 3DES.

### <a name="ike-phase-1-setup"></a>Configurazione fase 1 IKE

| **Proprietà** | **PolicyBased** | **Gateway VPN RouteBased e con prestazioni elevate o standard** |
| --- | --- | --- |
| Versione IKE |IKEv1 |IKEv2 |
| Diffie-Hellman Group |Gruppo 2 (1024 bit) |Gruppo 2 (1024 bit) |
| Metodo di autenticazione |Chiave precondivisa |Chiave precondivisa |
| Algoritmi di crittografia |AES256 AES128 3DES |AES256 3DES |
| Algoritmo di hash |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Durata (tempo) associazione di sicurezza (SA) fase 1 |28.800 secondi |10.800 secondi |

L'utente deve configurare il dispositivo FortiGate. Un esempio di configurazione è disponibile in [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Si supponga che il dispositivo FortiGate sia stato inconsapevolmente configurato per l'uso dell'algoritmo di hash SHA-512. Questo algoritmo non è supportato per le connessioni basate su criteri, quindi la connessione VPN non funziona.

Questi problemi sono difficili da risolvere e le cause principali sono spesso non intuitive. In questo caso è possibile aprire un ticket di supporto per ottenere assistenza nella risoluzione del problema. Con l'API di risoluzione dei problemi di Azure Network Watcher è tuttavia possibile identificare questi problemi autonomamente.

## <a name="troubleshooting-using-azure-network-watcher"></a>Risoluzione dei problemi tramite Azure Network Watcher

Per diagnosticare la connessione, connettersi ad Azure PowerShell e avviare il cmdlet `Start-AzureRmNetworkWatcherResourceTroubleshooting`. I dettagli sull'uso di questo cmdlet sono disponibili in [Risolvere i problemi relativi al gateway di rete virtuale e alle connessioni di Azure - PowerShell](network-watcher-troubleshoot-manage-powershell.md). L'esecuzione del cmdlet può richiedere alcuni minuti.

Al termine del cmdlet è possibile passare al percorso di archiviazione specificato nel cmdlet per ottenere informazioni dettagliate sul problema e i log. Azure Network Watcher crea una cartella ZIP contenente i file di log seguenti:

![1][1]

Aprire il file denominato IKEErrors.txt per visualizzare l'errore seguente, che indica un problema dovuto alla configurazione errata dell'impostazione IKE locale.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

È possibile ottenere informazioni dettagliate sull'errore dal file da Scrubbed-wfpdiag.txt, che in questo caso indica che `ERROR_IPSEC_IKE_POLICY_MATCH` ha provocato l'errore di connessione.

Un altro errore di configurazione comune è l'immissione di chiavi condivise errate. Se nell'esempio precedente fossero state specificate chiavi condivise differenti, il file IKEErrors.txt avrebbe indicato l'errore seguente: `Error: Authentication failed. Check shared key`.

La funzionalità di risoluzione dei problemi di Azure Network Watcher consente di diagnosticare e risolvere i problemi del gateway VPN e della connessione semplicemente eseguendo un cmdlet di PowerShell. È attualmente supportata la diagnosi delle condizioni seguenti e altre verranno aggiunte in futuro.

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
| ConnectionsNotConnected | Nessuna connessione è connessa. Questo è solo un avviso.| Sì|
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

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come verificare la connettività del gateway VPN con PowerShell e Automazione di Azure, vedere [Monitorare i gateway VPN con la risoluzione dei problemi di Network Watcher](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
