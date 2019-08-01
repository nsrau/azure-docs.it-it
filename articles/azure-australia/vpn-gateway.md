---
title: Gateway VPN di Azure in Azure Australia
description: Implementazione del gateway VPN in Azure Australia per essere conforme a ISM ed efficace protezione degli enti pubblici australiani
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 442ad6334a1775033018005d4a85875dbcb08ada
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571848"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Gateway VPN di Azure in Azure Australia

Un servizio critico con qualsiasi cloud pubblico è la connessione sicura di risorse e servizi cloud ai sistemi locali esistenti.  Il servizio che fornisce questa funzionalità in Azure è il gateway VPN di Azure (gateway VPN). Questo articolo descrive le considerazioni principali relative alla configurazione del gateway VPN per la conformità ai [controlli manuali di sicurezza delle informazioni](https://acsc.gov.au/infosec/ism/) (ASD) della direzione dei messaggi australiani (ISM).

Un gateway VPN viene usato per inviare il traffico crittografato tra una rete virtuale in Azure e un'altra rete.  I gateway VPN possono affrontare tre scenari:

- **Da sito a sito** S2S
- **Da punto a sito** P2S
- **Tra reti virtuali**

Questo articolo è incentrato sui gateway VPN S2S. Il diagramma 1 Mostra un esempio di configurazione del gateway VPN da sito a sito.

![Gateway VPN con connessioni multisito](media/vpngateway-multisite-connection-diagram.png)

*Diagramma 1: gateway VPN da sito a sito di Azure*

## <a name="key-design-considerations"></a>Considerazioni di progettazione principali

Sono disponibili tre opzioni di rete per connettere Azure ai clienti del governo australiano:

- **ICONA**
- **ExpressRoute**
- **Internet pubblico**

La [Guida per i consumatori](https://servicetrust.microsoft.com/viewpage/Australia) del Centro sicurezza Cyber australiano per Azure consiglia di usare il gateway VPN (o un servizio di terze parti certificato protetto equivalente) insieme alle tre opzioni di rete per garantire che le connessioni siano conformi al Controlli ISM per la crittografia e l'integrità.

### <a name="encryption-and-integrity"></a>Crittografia e integrità

Per impostazione predefinita, la VPN negozia gli algoritmi di crittografia e integrità e i parametri durante la creazione della connessione come parte dell'handshake IKE.  Durante l'handshake IKE, la configurazione e l'ordine di preferenza variano a seconda che il gateway VPN sia l'iniziatore o il risponditore (NB: questa operazione è controllata tramite il dispositivo VPN).  La configurazione finale della connessione è controllata dalla configurazione del dispositivo VPN.  Per informazioni dettagliate sui dispositivi VPN convalidati e sulla relativa configurazione, vedere qui: [Informazioni sui dispositivi VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)

I gateway VPN possono controllare la crittografia e l'integrità configurando un criterio IPsec/IKE personalizzato sulla connessione.

### <a name="resource-operations"></a>Operazioni sulle risorse

I gateway VPN creano una connessione tra Azure e gli ambienti non Azure tramite la rete Internet pubblica.  ISM dispone di controlli correlati all'autorizzazione esplicita delle connessioni.  Per impostazione predefinita, è possibile usare i gateway VPN per creare tunnel non autorizzati in ambienti protetti.  È quindi fondamentale che le organizzazioni usino il controllo degli accessi in base al ruolo di Azure per controllare chi può creare e modificare i gateway VPN e le relative connessioni.  Per gestire i gateway VPN, Azure non ha alcun ruolo "integrato", pertanto sarà necessario un ruolo personalizzato.

L'accesso ai ruoli "proprietario", "collaboratore" e "collaboratore rete" è strettamente controllato.  È inoltre consigliabile utilizzare Azure AD Privileged Identity Management per un controllo di accesso più granulare.

### <a name="high-availability"></a>Disponibilità elevata

I gateway VPN di Azure possono avere più connessioni (vedere il diagramma 1) e supportare più dispositivi VPN locali nello stesso ambiente locale.  

Le reti virtuali in Azure possono avere più gateway VPN che possono essere distribuiti in configurazioni indipendenti, attive-passive o attive-attive.

Si consiglia di distribuire tutti i gateway VPN in una configurazione a [disponibilità elevata](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable), ad esempio due dispositivi VPN locali connessi a due gateway VPN in modalità attivo-passivo o attivo-attivo (vedere il diagramma 2).

![Connessioni ridondanti del gateway VPN](media/dual-redundancy.png)

*Diagramma 2: gateway VPN attivo-attivo e due dispositivi VPN*

### <a name="forced-tunneling"></a>Tunneling forzato

Il tunneling forzato reindirizza o "forza" tutto il traffico associato a Internet verso l'ambiente locale tramite il gateway VPN per l'ispezione e il controllo. Senza il tunneling forzato, il traffico associato a Internet dalle macchine virtuali in Azure attraversa l'infrastruttura di rete di Azure direttamente alla rete Internet pubblica, senza la possibilità di ispezionare o controllare il traffico.  Si tratta di un'operazione fondamentale quando è necessario che l'organizzazione usi un gateway Internet sicuro (SIG) per un ambiente.

## <a name="detailed-configuration"></a>Configurazione dettagliata

### <a name="service-attributes"></a>Attributi del servizio

I gateway VPN per le connessioni S2S configurate per il governo australiano devono avere gli attributi seguenti:

|Attributo | NECESSARIO|
|--- | --- |
|gatewayType | VPN|
|

Le impostazioni degli attributi necessarie per la conformità ai controlli ISM per Protected sono:

|Attributo | NECESSARIO|
|--- |---|
|vpnType |RouteBased|
|vpnClientConfiguration/vpnClientProtocols | IkeV2|
|

I gateway VPN di Azure supportano una gamma di algoritmi di crittografia dagli standard di protocollo IPsec e IKE.  I set di criteri predefiniti ottimizzano l'interoperabilità con un'ampia gamma di dispositivi VPN di terze parti.  Di conseguenza, è possibile che durante l'handshake IKE venga negoziata una configurazione non conforme.  È pertanto consigliabile applicare i parametri dei [criteri IPSec/IKE personalizzati](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) a VpnClientConfiguration nei gateway VPN per assicurarsi che le connessioni soddisfino i controlli ISM per le connessioni di ambiente locali ad Azure.  Gli attributi chiave sono:

|Attributo|DOVREBBE|NECESSARIO|
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

*Per dhGroup e pfsGroup nella tabella precedente, è preferibile usare ECP256 e ECP384 anche se è possibile usare altre impostazioni*

### <a name="related-services"></a>Servizi correlati

Quando si progetta e si configura un gateway VPN di Azure, esistono diversi servizi correlati che devono esistere e configurare:

|Service | Azione necessaria|
|--- | ---|
|Rete virtuale | I gateway VPN sono collegati a una rete virtuale.  Prima di creare un nuovo gateway VPN, è necessario creare una rete virtuale.|
|Indirizzo IP pubblico | I gateway VPN S2S necessitano di un indirizzo IP pubblico per stabilire la connettività tra il dispositivo VPN locale e il gateway VPN.  Prima di creare un gateway VPN S2S, è necessario creare un indirizzo IP pubblico.|
|Subnet | Per il gateway VPN è necessario creare una subnet della rete virtuale.|
|

## <a name="implementation-steps-using-powershell"></a>Passaggi di implementazione con PowerShell

### <a name="role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo

1. Creare un ruolo personalizzato (ad esempio, collaboratore virtualNetworkGateway).  Creare un ruolo da assegnare agli utenti che saranno autorizzati a creare e modificare i gateway VPN. Il ruolo personalizzato deve consentire le operazioni seguenti:

   Microsoft. Network/virtualNetworkGateways/*  
   Microsoft. Network/Connections/*  
   Microsoft. Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft. Network/publicIPAddresses/*  
   Microsoft. Network/publicIPPrefixes/*  
   Microsoft.Network/routeTables/*  

2. Aggiungere un ruolo personalizzato agli utenti a cui è consentito creare e gestire gateway VPN e connessioni ad ambienti locali.

### <a name="create-vpn-gateway"></a>Creare un gateway VPN

*Questi passaggi presuppongono che sia già stata creata una rete virtuale*

1. Creare un nuovo indirizzo IP pubblico
2. Creare una subnet del gateway VPN
3. Creare una configurazione IP del gateway VPN
4. Crea un gateway VPN
5. Creare un gateway di rete locale per il dispositivo VPN locale
6. Creare un criterio IPsec (presupponendo l'uso di criteri IPsec/IKE personalizzati)
7. Creare una connessione tra il gateway VPN e il gateway di rete locale usando i criteri IPsec

### <a name="enforce-tunneling"></a>Imponi tunneling

Se è necessario il tunneling forzato, prima di creare il gateway VPN:

1. Creazione di una tabella di route e di una o più regole di route
2. Associare la tabella di route alle subnet appropriate

Dopo aver creato il gateway VPN:

1. Impostare GatewayDefaultSite sull'ambiente locale nel gateway VPN

### <a name="example-powershell-script"></a>Script di PowerShell di esempio

Uno script di PowerShell di esempio per la creazione di criteri IPSEC/IKE personalizzati conformi ai controlli ISM per la classificazione di sicurezza protetta in Australia.

Si presuppone che la rete virtuale, il gateway VPN e i gateway locali esistano.

#### <a name="create-an-ipsecike-policy"></a>Creare un criterio IPsec/IKE

Lo script di esempio che segue crea un criterio IPsec/IKE con gli algoritmi e i parametri seguenti:

- IKEv2: AES256, SHA256, DHGroup ECP256
- IPsec: AES256, SHA256, PFS ECP256, durata dell'associazione di 14.400 secondi & 102,4 milioni KB

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

In questo articolo è stata illustrata la configurazione specifica del gateway VPN per soddisfare i requisiti specificati nel manuale di sicurezza delle informazioni (ISM) per la protezione dei dati protetti per il governo australiano in transito. Per la procedura dettagliata per la configurazione del gateway VPN:

- [Panoramica del gateway di rete virtuale di Azure](https://docs.microsoft.com/azure/vpn-gateway/)  
- [Che cos'è il gateway VPN?](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [Creare una VNet con una connessione VPN da sito a sito usando PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [Creare e gestire un gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)