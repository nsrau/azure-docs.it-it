---
title: Gateway VPN di Azure in Azure Australia
description: Implementazione del gateway VPN in Azure Australia per essere conforme a ISM ed efficace protezione degli enti pubblici australiani
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 00588042fa11ace51eef40cdedbae14c1bd99801
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575430"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Gateway VPN di Azure in Azure Australia

Un servizio critico con qualsiasi cloud pubblico è la connessione sicura di risorse e servizi cloud ai sistemi locali esistenti. Il servizio che fornisce questa funzionalità in Azure è il gateway VPN di Azure. Questo articolo descrive gli aspetti chiave da considerare quando si configura un gateway VPN per la conformità ai [controlli di ISM (Information Security Manual](https://acsc.gov.au/infosec/ism/)) per la protezione dei segnali australiani.

Un gateway VPN viene usato per inviare il traffico crittografato tra una rete virtuale in Azure e un'altra rete. I gateway VPN vengono risolti in tre scenari:

- Da sito a sito (S2S)
- Da punto a sito (P2S)
- Da rete a rete

Questo articolo è incentrato sui gateway VPN S2S. Il diagramma 1 Mostra un esempio di configurazione del gateway VPN S2S.

![Gateway VPN con connessioni multisito](media/vpngateway-multisite-connection-diagram.png)

*Diagramma 1: gateway VPN di Azure S2S*

## <a name="key-design-considerations"></a>Principali considerazioni sulla progettazione

Sono disponibili tre opzioni di rete per connettere Azure ai clienti del governo australiano:

- ICONA
- Azure ExpressRoute
- Internet pubblico

La [Guida per i consumatori](https://servicetrust.microsoft.com/viewpage/Australia) del Centro sicurezza Cyber australiano per Azure consiglia di usare il gateway VPN (o un servizio di terze parti certificato protetto equivalente) insieme alle tre opzioni di rete. Questa raccomandazione consiste nel garantire che le connessioni siano conformi ai controlli ISM per la crittografia e l'integrità.

### <a name="encryption-and-integrity"></a>Crittografia e integrità

Per impostazione predefinita, la VPN negozia gli algoritmi di crittografia e integrità e i parametri durante la creazione della connessione come parte dell'handshake IKE. Durante l'handshake IKE, la configurazione e l'ordine di preferenza variano a seconda che il gateway VPN sia l'iniziatore o il risponditore. Questa designazione viene controllata tramite il dispositivo VPN. La configurazione finale della connessione è controllata dalla configurazione del dispositivo VPN. Per ulteriori informazioni sui dispositivi VPN convalidati e sulla relativa configurazione, vedere [About VPN Services](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

I gateway VPN possono controllare la crittografia e l'integrità configurando un criterio IPsec/IKE personalizzato sulla connessione.

### <a name="resource-operations"></a>Operazioni sulle risorse

I gateway VPN creano una connessione tra Azure e gli ambienti non Azure tramite la rete Internet pubblica. ISM dispone di controlli correlati all'autorizzazione esplicita delle connessioni. Per impostazione predefinita, è possibile usare i gateway VPN per creare tunnel non autorizzati in ambienti protetti. È fondamentale che le organizzazioni usino il controllo degli accessi in base al ruolo di Azure per controllare gli utenti che possono creare e modificare i gateway VPN e le relative connessioni. Azure non ha un ruolo predefinito per gestire i gateway VPN, quindi è necessario un ruolo personalizzato.

L'accesso ai ruoli proprietario, collaboratore e collaboratore rete è strettamente controllato. Si consiglia inoltre di usare Azure Active Directory Privileged Identity Management per un controllo di accesso più granulare.

### <a name="high-availability"></a>Disponibilità elevata

I gateway VPN di Azure possono avere più connessioni e supportare più dispositivi VPN locali nello stesso ambiente locale. Vedere il diagramma 1.

Le reti virtuali in Azure possono avere più gateway VPN che possono essere distribuiti in configurazioni indipendenti, attive-passive o attive-attive.

Si consiglia di distribuire tutti i gateway VPN in una [configurazione a disponibilità elevata](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable). Un esempio è costituito da due dispositivi VPN locali connessi a due gateway VPN in modalità attivo-passivo o attivo-attivo. Vedere il diagramma 2.

![Connessioni ridondanti del gateway VPN](media/dual-redundancy.png)

*Diagramma 2: gateway VPN attivo-attivo e due dispositivi VPN*

### <a name="forced-tunneling"></a>Tunneling forzato

Il tunneling forzato reindirizza o forza tutto il traffico associato a Internet verso l'ambiente locale tramite il gateway VPN per l'ispezione e il controllo. Senza il tunneling forzato, il traffico associato a Internet dalle macchine virtuali in Azure attraversa l'infrastruttura di rete di Azure direttamente alla rete Internet pubblica, senza la possibilità di ispezionare o controllare il traffico. Il tunneling forzato è fondamentale quando un'organizzazione deve usare un gateway Internet sicuro (SIG) per un ambiente.

## <a name="detailed-configuration"></a>Configurazione dettagliata

### <a name="service-attributes"></a>Attributi del servizio

Il gateway VPN per le connessioni S2S configurate per il governo australiano deve avere gli attributi seguenti:

|Attributo | Necessario|
|--- | --- |
|gatewayType | VPN|
|

Le impostazioni degli attributi necessarie per la conformità ai controlli ISM per PROTECTed sono:

|Attributo | Necessario|
|--- |---|
|vpnType |RouteBased|
|vpnClientConfiguration/vpnClientProtocols | IkeV2|
|

I gateway VPN di Azure supportano una gamma di algoritmi di crittografia dagli standard di protocollo IPsec e IKE. Il criterio predefinito consente di impostare la massima interoperabilità con un'ampia gamma di dispositivi VPN di terze parti. Di conseguenza, è possibile che durante l'handshake IKE venga negoziata una configurazione non conforme. Si consiglia vivamente di applicare parametri di [criteri IPSec/IKE personalizzati](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) a vpnClientConfiguration nei gateway VPN per assicurarsi che le connessioni soddisfino i controlli ISM per le connessioni all'ambiente locale ad Azure. Gli attributi chiave sono riportati nella tabella seguente.

|Attributo|Dovrebbe|Necessario|
|---|---|---|
|saLifeTimeSeconds|< 14400 sec|> 300 secondi|
|saDataSizeKilobytes| |> 1024 KB|
|ipsecEncryption| |AES256-GCMAES256|
|ipsecIntegrity| |SHA256-GCMAES256|
|ikeEncryption| |AES256-GCMAES256|
|ikeIntegrity| |SHA256-GCMAES256|
|dhGroup|DHGroup14, DHGroup24, ECP256, ECP384|DHGroup2|
|pfsGroup|PFS2048, PFS24, ECP256, ECP384||
|

Per dhGroup e pfsGroup nella tabella precedente, è preferibile usare ECP256 e ECP384 anche se è possibile usare altre impostazioni.

### <a name="related-services"></a>Servizi correlati

Quando si progetta e si configura un gateway VPN di Azure, è necessario che esistano anche alcuni servizi correlati e che siano configurati.

|Service | È richiesto il tuo intervento|
|--- | ---|
|Rete virtuale | I gateway VPN sono collegati a una rete virtuale. Creare una rete virtuale prima di creare un nuovo gateway VPN.|
|Indirizzo IP pubblico | I gateway VPN S2S necessitano di un indirizzo IP pubblico per stabilire la connettività tra il dispositivo VPN locale e il gateway VPN. Creare un indirizzo IP pubblico prima di creare un gateway VPN S2S.|
|Subnet | Creare una subnet della rete virtuale per il gateway VPN.|
|

## <a name="implementation-steps-using-powershell"></a>Passaggi di implementazione con PowerShell

### <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

1. È possibile creare un ruolo personalizzato. Un esempio è il collaboratore di virtualNetworkGateway. Creare un ruolo da assegnare agli utenti che saranno autorizzati a creare e modificare i gateway VPN. Il ruolo personalizzato deve consentire le operazioni seguenti:

   Microsoft. Network/virtualNetworkGateways/*  
   Microsoft. Network/Connections/*  
   Microsoft. Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft. Network/publicIPAddresses/*  
   Microsoft. Network/publicIPPrefixes/*  
   Microsoft.Network/routeTables/*  

2. Aggiungere il ruolo personalizzato agli utenti autorizzati a creare e gestire gateway VPN e connessioni ad ambienti locali.

### <a name="create-a-vpn-gateway"></a>Creare un gateway VPN

Questi passaggi presuppongono che sia già stata creata una rete virtuale.

1. Creare un nuovo indirizzo IP pubblico.
2. Creare una subnet del gateway VPN.
3. Creare un file di configurazione IP del gateway VPN.
4. Creare un gateway VPN.
5. Creare un gateway di rete locale per il dispositivo VPN locale.
6. Creare un criterio IPsec. Questo passaggio presuppone che si stiano usando criteri IPsec/IKE personalizzati.
7. Creare una connessione tra il gateway VPN e un gateway di rete locale usando i criteri IPsec.

### <a name="enforce-tunneling"></a>Imponi tunneling

Se è necessario il tunneling forzato, prima di creare il gateway VPN:

1. Creare una tabella di route e le regole di route.
2. Associare una tabella di route alle subnet appropriate.

Dopo aver creato il gateway VPN:

- Impostare GatewayDefaultSite sull'ambiente locale nel gateway VPN.

### <a name="example-powershell-script"></a>Script di PowerShell di esempio

Un esempio di script di PowerShell usato per creare un criterio IPsec/IKE personalizzato è conforme ai controlli ISM per la classificazione di sicurezza protetta in Australia.

Si presuppone che la rete virtuale, il gateway VPN e i gateway locali esistano.

#### <a name="create-an-ipsecike-policy"></a>Creare un criterio IPsec/IKE

Lo script di esempio che segue crea un criterio IPsec/IKE con gli algoritmi e i parametri seguenti:

- IKEv2: AES256, SHA256, DHGroup ECP256
- IPsec: AES256, SHA256, PFS ECP256, durata SA 14.400 secondi e 102,4 milioni KB

```powershell
$custompolicy = New-AzIpsecPolicy `
                    -IkeEncryption AES256 `
                    -IkeIntegrity SHA256 `
                    -DhGroup ECP256 `
                    -IpsecEncryption AES256 `
                    -IpsecIntegrity SHA256 `
                    -PfsGroup ECP256 `
                    -SALifeTimeSeconds 14400 `
                    -SADataSizeKilobytes 102400000
```

#### <a name="create-a-s2s-vpn-connection-with-the-custom-ipsecike-policy"></a>Creare una connessione VPN S2S con i criteri IPsec/IKE personalizzati

```powershell
$vpngw = Get-AzVirtualNetworkGateway `
                    -Name "<yourVPNGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"
$localgw = Get-AzLocalNetworkGateway  `
                    -Name "<yourLocalGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"

New-AzVirtualNetworkGatewayConnection `
                    -Name "ConnectionName" `
                    -ResourceGroupName "<yourResourceGroupName>" `
                    -VirtualNetworkGateway1 $vpngw `
                    -LocalNetworkGateway2 $localgw `
                    -Location "Australia Central" `
                    -ConnectionType IPsec `
                    -IpsecPolicies $custompolicy `
                    -SharedKey "AzureA1b2C3"
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha trattato la configurazione specifica del gateway VPN per soddisfare i requisiti specificati nel manuale di sicurezza delle informazioni per la protezione dei dati protetti per il governo australiano in transito. Per i passaggi relativi alla configurazione del gateway VPN, vedere:

- [Panoramica del gateway di rete virtuale di Azure](https://docs.microsoft.com/azure/vpn-gateway/)  
- [Che cos'è il gateway VPN?](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [Creare una rete virtuale con una connessione VPN da sito a sito tramite PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [Creare e gestire un gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)