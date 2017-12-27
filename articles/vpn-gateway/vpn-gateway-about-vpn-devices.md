---
title: Informazioni sui dispositivi VPN per connessioni di Azure cross-premise | Documentazione Microsoft
description: Questo articolo illustra i dispositivi VPN e i parametri IPsec per connessioni cross-premise del Gateway VPN da sito a sito. Vengono forniti collegamenti alle istruzioni di configurazione e agli esempi.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: ba449333-2716-4b7f-9889-ecc521e4d616
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: yushwang
ms.openlocfilehash: bb6f9f4df9afa9d0c1a75fbb1166798a2aef4bb4
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>Informazioni sui dispositivi VPN e sui parametri IPsec/IKE per connessioni del Gateway VPN da sito a sito

Per configurare una connessione VPN cross-premise da sito a sito usando un Gateway VPN, è necessario un dispositivo VPN. Le connessioni da sito a sito possono essere usate per creare una soluzione ibrida o se si vogliono stabilire connessioni sicure tra le reti locali e le reti virtuali. Questo documento offre un elenco di dispositivi VPN convalidati e un elenco di parametri IPsec/IKE per i gateway VPN.

> [!IMPORTANT]
> Se si verificano problemi di connettività tra i gateway VPN e i dispositivi VPN locali, vedere [Problemi noti di compatibilità del dispositivo](#known).
>

### <a name="items-to-note-when-viewing-the-tables"></a>Elementi da considerare quando si visualizzano le tabelle:

* È stata eseguita una modifica della terminologia per i Gateway VPN di Azure. Sono stati modificati solo i nomi. Le funzionalità rimangono invariate.
  * Routing statico = PolicyBased
  * Routing dinamico = RouteBased
* Se non indicato diversamente, le specifiche per i gateway VPN a prestazioni elevate e i gateway VPN RouteBased sono le stesse. Ad esempio, i dispositivi VPN convalidati e compatibili con i gateway VPN RouteBased sono compatibili anche con il gateway VPN a prestazioni elevate.

## <a name="devicetable"></a>Dispositivi VPN convalidati e guide di configurazione per dispositivi

> [!NOTE]
> Quando si configura una connessione da sito a sito, è necessario un indirizzo IP IPv4 pubblico per il dispositivo VPN.
>

In collaborazione con i fornitori di dispositivi, è stato approvato un set di dispositivi VPN standard. Tutti i dispositivi nelle famiglie di dispositivi dell'elenco seguente funzioneranno con i gateway VPN. Per informazioni sull'uso del tipo di VPN (PolicyBased o RouteBased) per la soluzione del gateway VPN che si vuole configurare, vedere [Informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype).

Per agevolare la configurazione del dispositivo VPN, vedere i collegamenti corrispondenti alla famiglia di dispositivi appropriata. I collegamenti alle istruzioni di configurazione vengono forniti nel modo più efficiente possibile. Per il supporto ai dispositivi VPN, contattare il produttore del dispositivo.

|**Fornitore**          |**Famiglia di dispositivi**     |**Versione minima del sistema operativo** |**Istruzioni di configurazione di tipo PolicyBased** |**Istruzioni di configurazione di tipo RouteBased** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |Non compatibile  |[Guida alla configurazione](https://www.a10networks.com/resources/deployment-guides/a10-thunder-cfw-ipsec-vpn-interoperability-azure-vpn-gateways)|
| Allied Telesis     |Router VPN serie AR |2.9.2                  |Presto disponibile     |Non compatibile  |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall F-series |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Guida alla configurazione](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[Guida alla configurazione](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall X-series |Barracuda Firewall 6.5 |[Guida alla configurazione](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |Non compatibile |
| Brocade            |Vyatta 5400 vRouter   |Virtual Router 6.6R3 GA|[Guida alla configurazione](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) |Non compatibile |
| Punto di controllo |Gateway di protezione |R77.30 |[Guida alla configurazione](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Guida alla configurazione](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3<br>8.4+ (IKEv2*) |[Esempi di configurazione](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) |[Guida alla configurazione*](vpn-gateway-3rdparty-device-config-cisco-asa.md) |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |[Esempi di configurazione](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |[Esempi di configurazione](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased*: IOS 15.1 |[Esempi di configurazione](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |[Esempi di configurazione**](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix |NetScaler MPX, SDX, VPX |10.1 e versioni successive |[Guida alla configurazione](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Non compatibile |
| F5 |Serie BIG-IP |12.0 |[Guida alla configurazione](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Guida alla configurazione](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.6 |  |[Guida alla configurazione](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-56/) |
| Internet Initiative Japan (IIJ) |Serie SEIL |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Guida alla configurazione](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Non compatibile |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |[Esempi di configurazione](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |[Esempi di configurazione](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper |Serie J |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |[Esempi di configurazione](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |[Esempi di configurazione](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper |ISG |ScreenOS 6.3 |[Esempi di configurazione](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |[Esempi di configurazione](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper |SSG |ScreenOS 6.2 |[Esempi di configurazione](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |[Esempi di configurazione](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft |Routing and Remote Access Service |Windows Server 2012 |Non compatibile |[Esempi di configurazione](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG |Mission Control Security Gateway |N/D |[Guida alla configurazione](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |Non compatibile |
| Palo Alto Networks |Tutti i dispositivi che eseguono PAN-OS |PAN-OS<br>PolicyBased: 6.1.5 o versione successiva<br>RouteBased: 7.1.4 |[Guida alla configurazione](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[Guida alla configurazione](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| ShareTech | Next Generation UTM (serie NU) | 9.0.1.3 | Non compatibile | [Guida alla configurazione](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWALL |Serie TZ, serie NSA<br>Serie SuperMassive<br>Serie NSA classe E |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |Non compatibile |[Guida alla configurazione](https://www.sonicwall.com/support/knowledge-base/170505320011694) |
| Sophos | Firewall XG di nuova generazione | XG v17 | | [Guida alla configurazione](https://community.sophos.com/kb/127546) |
| WatchGuard |Tutti |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[Guida alla configurazione](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Guida alla configurazione](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|

> [!NOTE]
>
> (*) Le versioni di Cisco ASA 8.4+ aggiungono il supporto IKEv2 e possono connettersi al gateway VPN di Azure usando criteri IPsec/IKE personalizzati con l'opzione "UsePolicyBasedTrafficSelectors". Vedere questa [procedura dettagliata](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> (**) I router serie ISR 7200 supportano solo VPN PolicyBased.

## <a name="additionaldevices"></a>Dispositivi VPN non convalidati

Anche se il dispositivo non è elencato nella tabella dei dispositivi VPN convalidati, potrebbe comunque funzionare con una connessione da sito a sito. Contattare il produttore del dispositivo per assistenza e istruzioni di configurazione.

## <a name="editing"></a>Modifica degli esempi di configurazione di dispositivo

Dopo aver scaricato l'esempio di configurazione di dispositivo VPN fornito, è necessario sostituire alcuni dei valori in base alle impostazioni per l'ambiente.

### <a name="to-edit-a-sample"></a>Per modificare un esempio:

1. Aprire l'esempio utilizzando il blocco note.
2. Cercare e sostituire tutti le stringhe <*text*> con i valori pertinenti all'ambiente. Assicurarsi di includere < e >. Quando viene specificato un nome, il nome selezionato deve essere univoco. Se un comando non funziona, consultare la documentazione del produttore del dispositivo.

| **Testo di esempio** | **Modificare in** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |Nome scelto per questo oggetto. Esempio: myOnPremisesNetwork |
| &lt;RP_AzureNetwork&gt; |Nome scelto per questo oggetto. Esempio: myAzureNetwork |
| &lt;RP_AccessList&gt; |Nome scelto per questo oggetto. Esempio: myAzureAccessList |
| &lt;RP_IPSecTransformSet&gt; |Nome scelto per questo oggetto. Esempio: myIPSecTransformSet |
| &lt;RP_IPSecCryptoMap&gt; |Nome scelto per questo oggetto. Esempio: myIPSecCryptoMap |
| &lt;SP_AzureNetworkIpRange&gt; |Specificare l'intervallo. Esempio: 192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |Specificare la subnet mask. Esempio: 255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |Specificare l'intervallo in locale. Esempio: 10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |Specificare la subnet mask locale. Esempio: 255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |Queste informazioni sono specifiche per la rete virtuale e si trovano nel portale di gestione in **Indirizzo IP gateway**. |
| &lt;SP_PresharedKey&gt; |Queste informazioni sono specifiche per la rete virtuale e si trovano nel portale di gestione in chiave di gestione. |

## <a name="ipsec"></a>Parametri IPsec/IKE

> [!IMPORTANT]
> 1. Le tabelle seguenti contengono le combinazioni di algoritmi e parametri usati dal gateway VPN di Azure nella configurazione predefinita. Per i gateway VPN basati su route creati usando il modello di distribuzione Azure Resource Management, è possibile specificare un criterio personalizzato in ogni singola connessione. Per istruzioni dettagliate, vedere [Configurare criteri IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md).
>
> 2. È anche necessario limitare TCP **MSS** a **1350**. Se invece i dispositivi VPN non supportano la limitazione di MSS, in alternativa è possibile impostare il valore **MTU** nell'interfaccia di tunnel su **1400** byte.
>

Nelle tabelle seguenti:

* SA = associazione di sicurezza
* La Fase 1 di IKE viene definita anche "Modalità principale"
* La Fase 2 di IKE viene definita anche "Modalità rapida"

### <a name="ike-phase-1-main-mode-parameters"></a>Parametri della Fase 1 di IKE (Modalità principale)

| **Proprietà**          |**PolicyBased**    | **RouteBased**    |
| ---                   | ---               | ---               |
| Versione IKE           |IKEv1              |IKEv2              |
| Diffie-Hellman Group  |Gruppo 2 (1024 bit) |Gruppo 2 (1024 bit) |
| Metodo di autenticazione |Chiave precondivisa     |Chiave precondivisa     |
| Algoritmi di crittografia e di hash |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |1. AES256, SHA1<br>2. AES256, SHA256<br>3. AES128, SHA1<br>4. AES128, SHA256<br>5. 3DES, SHA1<br>6. 3DES, SHA256 |
| Durata dell'associazione di sicurezza           |28.800 secondi     |28.800 secondi     |

### <a name="ike-phase-2-quick-mode-parameters"></a>Parametri della Fase 2 di IKE (Modalità rapida)

| **Proprietà**                  |**PolicyBased**| **RouteBased**                              |
| ---                           | ---           | ---                                         |
| Versione IKE                   |IKEv1          |IKEv2                                        |
| Algoritmi di crittografia e di hash |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[Offerte per associazioni di sicurezza QM basate su route](#RouteBasedOffers) |
| Durata dell'associazione di sicurezza (tempo)            |3.600 secondi  |27.000 secondi                                |
| Durata dell'associazione di sicurezza (byte)           |102.400.000 KB | -                                           |
| Perfect Forward Secrecy (PFS) |No             |[Offerte per associazioni di sicurezza QM basate su route](#RouteBasedOffers) |
| Rilevamento peer inattivo     |Non supportate  |Supportato                                    |


### <a name ="RouteBasedOffers"></a>Offerte per associazioni di sicurezza IPsec VPN basate su route (associazione di sicurezza IKE Modalità rapida)

La tabella seguente elenca le offerte per associazioni di sicurezza IPsec (IKE Modalità rapida). Le offerte sono elencate nell'ordine di preferenza di presentazione o di accettazione dell'offerta.

#### <a name="azure-gateway-as-initiator"></a>Gateway Azure come iniziatore

|-  |**Crittografia**|**Autenticazione**|**Gruppo PFS**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |None         |
| 2 |AES256        |SHA1              |None         |
| 3 |3DES          |SHA1              |None         |
| 4 |AES256        |SHA256            |None         |
| 5 |AES128        |SHA1              |None         |
| 6 |3DES          |SHA256            |None         |

#### <a name="azure-gateway-as-responder"></a>Gateway Azure come risponditore

|-  |**Crittografia**|**Autenticazione**|**Gruppo PFS**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |None         |
| 2 |AES256        |SHA1              |None         |
| 3 |3DES          |SHA1              |None         |
| 4 |AES256        |SHA256            |None         |
| 5 |AES128        |SHA1              |None         |
| 6 |3DES          |SHA256            |None         |
| 7 |DES           |SHA1              |None         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20|AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |None         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* È possibile specificare la crittografia NULL ESP IPsec con gateway VPN RouteBased e con prestazioni elevate. La crittografia basata su null non fornisce protezione ai dati in transito e deve essere usata solo quando sono richieste una velocità effettiva massima e una latenza minima. I client possono scegliere di usare questa crittografia in scenari di comunicazione tra reti virtuali oppure quando la crittografia viene applicata in un'altra posizione nella soluzione.
* Per la connettività cross-premise tramite Internet, usare le impostazioni del gateway VPN di Azure predefinite con algoritmi di crittografia e hash elencati nelle tabelle precedenti, per garantire la sicurezza delle comunicazioni critiche.

## <a name="known"></a>Problemi noti di compatibilità del dispositivo

> [!IMPORTANT]
> Si tratta dei problemi di compatibilità noti tra i dispositivi VPN di terze parti e i gateway VPN di Azure. Il team di Azure collabora attivamente con i fornitori per risolvere i problemi elencati di seguito. Dopo aver risolto i problemi, questa pagina verrà aggiornata con le informazioni più recenti. Controllarla periodicamente.
>
>

### <a name="feb-16-2017"></a>16 febbraio 2017

**Dispositivi di Palo Alto Networks precedenti alla versione 7.1.4** per le connessioni VPN basate su route di Azure: se si usano dispositivi VPN di Palo Alto Networks con versione PAN-OS precedente alla versione 7.1.4 e si verificano problemi di connettività per i gateway VPN basati su route di Azure, eseguire i passaggi seguenti:

1. Controllare la versione del firmware del dispositivo di Palo Alto Networks. Se la versione PAN-OS è antecedente alla versione 7.1.4, aggiornarla a questa versione.
2. Nel dispositivo di Palo Alto Networks, modificare la durata della fase 2 SA (o SA in modalità rapida) impostandola su 28.800 secondi (8 ore) quando si esegue la connessione al gateway VPN di Azure.
3. Se si verificano ancora problemi di connettività, aprire una richiesta di supporto dal portale di Azure.
