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
ms.date: 03/03/2017
ms.author: yushwang;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: bea87fce9f1b1587af5a3e0d827a75e93d7bf534
ms.lasthandoff: 03/04/2017


---
# <a name="about-vpn-devices-for-site-to-site-vpn-gateway-connections"></a>Informazioni sui dispositivi VPN per le connessioni di gateway VPN
Per configurare una connessione VPN cross-premise da sito a sito usando un Gateway VPN, è necessario un dispositivo VPN. Le connessioni da sito a sito possono essere utilizzate per creare una soluzione ibrida o se si desidera una connessione sicura tra la rete locale e la rete virtuale. In questo articolo vengono illustrati i dispositivi VPN compatibili e i parametri di configurazione.


> [!IMPORTANT]
> Se si verificano problemi di connettività tra i gateway VPN di Azure e i dispositivi VPN locali, consultare [Problemi noti di compatibilità del dispositivo](#known).
> 
> 


###<a name="items-to-note-when-viewing-the-tables"></a>Elementi da considerare quando si visualizzano le tabelle:

* È stata eseguita una modifica della terminologia per il routing statico e dinamico. È probabile che si incontrino entrambi i termini. Non è stata apportata alcuna modifica alle funzionalità, cambiano solo i nomi.
  * Routing statico = PolicyBased
  * Routing dinamico = RouteBased
* Se non indicato diversamente, le specifiche per i gateway VPN a prestazioni elevate e i gateway VPN RouteBased sono le stesse. Ad esempio, i dispositivi VPN convalidati e compatibili con i gateway VPN RouteBased sono compatibili anche con il gateway VPN a prestazioni elevate di Azure.

> [!NOTE]
> Quando si configura una connessione da sito a sito, è necessario un indirizzo IP IPv4 pubblico per il dispositivo VPN.                                                                                                                                                                               
>
>


## <a name="devicetable"></a>Dispositivi VPN convalidati
È stato approvato un set di dispositivi VPN standard in partnership con fornitori di dispositivi. Tutti i dispositivi nelle famiglie di dispositivi incluse nell'elenco seguente funzioneranno con i gateway VPN di Azure. Per verificare il tipo di gateway da creare per la soluzione che si vuole configurare, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md) .

Per agevolare la configurazione del dispositivo VPN, vedere i collegamenti corrispondenti alla famiglia di dispositivi appropriata. Per il supporto ai dispositivi VPN, contattare il produttore del dispositivo.

| **Fornitore** | **Famiglia di dispositivi** | **Versione minima del sistema operativo** | **PolicyBased** | **RouteBased** |
| --- | --- | --- | --- | --- |
| Allied Telesis |Router VPN serie AR |2.9.2 |Presto disponibile |Non compatibile |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall F-series |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Istruzioni di configurazione](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[Istruzioni di configurazione](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall X-series |Barracuda Firewall 6.5 |[Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |Non compatibile |
| Brocade |Vyatta 5400 vRouter |Virtual Router 6.6R3 GA |[Istruzioni di configurazione](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) |Non compatibile |
| Punto di controllo |Gateway di protezione |R77.30 |[Istruzioni di configurazione](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Istruzioni di configurazione](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco |ASA |8.3 |[Esempi Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) |Non compatibile |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |[Esempi Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |[Esempi Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased*: IOS 15.1 |[Esempi Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |[Esempi Cisco*](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix |NetScaler MPX, SDX, VPX |10.1 e versioni successive |[Istruzioni di integrazione](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Non compatibile |
| Dell SonicWALL |Serie TZ, serie NSA<br>Serie SuperMassive<br>Serie NSA classe E |SonicOS 5.8.x<br>[SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850)<br>[SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646) |[Guida alla configurazione di SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)<br>[Guida alla configurazione di SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |[Guida alla configurazione di SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646)<br>[Guida alla configurazione di SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |
| F5 |Serie BIG-IP |12.0 |[Istruzioni di configurazione](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Istruzioni di configurazione](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.4.2 |[Istruzioni di configurazione](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |[Istruzioni di configurazione](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |
| Internet Initiative Japan (IIJ) |Serie SEIL |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Istruzioni di configurazione](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Non compatibile |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |[Esempi Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |[Esempi Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper |Serie J |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |[Esempi Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |[Esempi Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper |ISG |ScreenOS 6.3 |[Esempi Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |[Esempi Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper |SSG |ScreenOS 6.2 |[Esempi Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |[Esempi Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft |Routing and Remote Access Service |Windows Server 2012 |Non compatibile |[Esempi Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG |Mission Control Security Gateway |N/D |[Guida all'installazione](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |[Guida all'installazione](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan |Openswan |2.6.32 |(Presto disponibile) |Non compatibile |
| Palo Alto Networks |Tutti i dispositivi che eseguono PAN-OS |PAN-OS<br>PolicyBased: 6.1.5 o versione successiva<br>RouteBased: 7.1.4 |[Istruzioni di configurazione](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[Istruzioni di configurazione](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| WatchGuard |Tutti |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[Istruzioni di configurazione](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Istruzioni di configurazione](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|

(*) I router serie ISR 7200 supportano solo VPN PolicyBased.

## <a name="additionaldevices"></a>Dispositivi VPN non convalidati
Anche se il dispositivo non è elencato nella tabella dei dispositivi VPN convalidati, potrebbe comunque funzionare con una connessione da sito a sito. Contattare il produttore del dispositivo per assistenza e istruzioni di configurazione.

## <a name="editing"></a>Modifica degli esempi di configurazione di dispositivo
Dopo aver scaricato l'esempio di configurazione di dispositivo VPN fornito, è necessario sostituire alcuni dei valori in base alle impostazioni per l'ambiente.

###<a name="to-edit-a-sample"></a>Per modificare un esempio:

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

## <a name="IPSec"></a>Parametri IPsec
> [!NOTE]
> Anche se i valori elencati nella tabella seguente sono supportati dal Gateway VPN di Azure, attualmente non è possibile specificare o selezionare una combinazione specifica da Gateway VPN di Azure. È necessario specificare tutti i vincoli dal dispositivo VPN locale. È inoltre necessario limitare MSS a 1350.
>
>

### <a name="ike-phase-1-setup"></a>Configurazione fase 1 IKE
| **Proprietà** | **PolicyBased** | **Gateway VPN RouteBased e con prestazioni elevate o standard** |
| --- | --- | --- |
| Versione IKE |IKEv1 |IKEv2 |
| Diffie-Hellman Group |Gruppo 2 (1024 bit) |Gruppo 2 (1024 bit) |
| Metodo di autenticazione |Chiave precondivisa |Chiave precondivisa |
| Algoritmi di crittografia |AES256 AES128 3DES |AES256 3DES |
| Algoritmo di hash |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Durata (tempo) associazione di sicurezza (SA) fase 1 |28.800 secondi |10.800 secondi |

### <a name="ike-phase-2-setup"></a>Configurazione fase 2 IKE
| **Proprietà** | **PolicyBased** | **Gateway VPN RouteBased e con prestazioni elevate o standard** |
| --- | --- | --- |
| Versione IKE |IKEv1 |IKEv2 |
| Algoritmo di hash |SHA1(SHA128), SHA2(SHA256) |SHA1(SHA128), SHA2(SHA256) |
| Durata (tempo) associazione di sicurezza (SA) fase 2 |3.600 secondi |3.600 secondi |
| Durata (velocità effettiva) associazione di sicurezza (SA) fase 2 |102.400.000 KB |- |
| Offerte di autenticazione e crittografia SA IPsec (in ordine di preferenza) |1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. N/D |Vedere "Offerte di associazione di sicurezza (SA) IPsec gateway RouteBased" (di seguito) |
| Perfect Forward Secrecy (PFS) |No |No (*) |
| Rilevamento peer inattivo |Non supportate |Supportato |

(*) In quanto risponditore IKE, il gateway di Azure può accettare PFS DH Gruppo 1, 2, 5, 14, 24.

### <a name="routebased-gateway-ipsec-security-association-sa-offers"></a>Offerte di associazione di sicurezza (SA) IPsec gateway RouteBased
La tabella seguente include l'elenco delle offerte di autenticazione e crittografia SA IPsec. Le offerte sono elencate nell'ordine di preferenza di presentazione o di accettazione dell'offerta.

| **Offerte di autenticazione e crittografia SA IPsec** | **Gateway Azure come iniziatore** | **Gateway Azure come risponditore** |
| --- | --- | --- |
| 1 |ESP AES_256 SHA |ESP AES_128 SHA |
| 2 |ESP AES_128 SHA |ESP 3_DES MD5 |
| 3 |ESP 3_DES MD5 |ESP 3_DES SHA |
| 4 |ESP 3_DES SHA |AH SHA1 con ESP AES_128 con HMAC Null |
| 5 |AH SHA1 con ESP AES_256 con HMAC Null |AH SHA1 con ESP 3_DES con HMAC Null |
| 6 |AH SHA1 con ESP AES_128 con HMAC Null |AH MD5 con ESP 3_DES con HMAC Null, nessuna durata proposta |
| 7 |AH SHA1 con ESP 3_DES con HMAC Null |AH SHA1 con ESP 3_DES SHA1, nessuna durata |
| 8 |AH MD5 con ESP 3_DES con HMAC Null, nessuna durata proposta |AH MD5 con ESP 3_DES MD5, nessuna durata |
| 9 |AH SHA1 con ESP 3_DES SHA1, nessuna durata |ESP DES MD5 |
| 10 |AH MD5 con ESP 3_DES MD5, nessuna durata |ESP DES SHA1, nessuna durata |
| 11 |ESP DES MD5 |AH SHA1 con ESP DES Null HMAC, nessuna durata proposta |
| 12 |ESP DES SHA1, nessuna durata |AH MD5 con ESP DES Null HMAC, nessuna durata proposta |
| 13 |AH SHA1 con ESP DES Null HMAC, nessuna durata proposta |AH SHA1 con ESP DES SHA1, nessuna durata |
| 14 |AH MD5 con ESP DES Null HMAC, nessuna durata proposta |AH MD5 con ESP DES MD5, nessuna durata |
| 15 |AH SHA1 con ESP DES SHA1, nessuna durata |ESP SHA, nessuna durata |
| 16 |AH MD5 con ESP DES MD5, nessuna durata |ESP MD5, nessuna durata |
| 17 |- |AH SHA, nessuna durata |
| 18 |- |AH MD5, nessuna durata |

* È possibile specificare la crittografia NULL ESP IPsec con gateway VPN RouteBased e con prestazioni elevate. La crittografia basata su null non fornisce protezione ai dati in transito e deve essere usata solo quando sono richieste una velocità effettiva massima e una latenza minima.  I client possono scegliere di usare questa crittografia in scenari di comunicazione tra reti virtuali oppure quando la crittografia viene applicata in un'altra posizione nella soluzione.
* Per la connettività cross-premise tramite Internet, usare le impostazioni del gateway VPN di Azure predefinite con algoritmi di crittografia e hash elencati nelle tabelle precedenti, per garantire la sicurezza delle comunicazioni critiche.

## <a name="known"></a>Problemi noti di compatibilità del dispositivo

> [!IMPORTANT]
> Si tratta dei problemi di compatibilità noti tra i dispositivi VPN di terze parti e i gateway VPN di Azure. Il team di Azure collabora attivamente con i fornitori per risolvere i problemi elencati di seguito. Dopo aver risolto i problemi, questa pagina verrà aggiornata con le informazioni più recenti. Controllarla periodicamente.

###<a name="feb-16-2017"></a>16 febbraio 2017

**Dispositivi di Palo Alto Networks precedenti alla versione 7.1.4** per le connessioni VPN basate su route di Azure: se si usano dispositivi VPN di Palo Alto Networks con versione PAN-OS precedente alla versione 7.1.4 e si verificano problemi di connettività per i gateway VPN basati su route di Azure, eseguire i passaggi seguenti:

1. Controllare la versione del firmware del dispositivo di Palo Alto Networks. Se la versione PAN-OS è antecedente alla versione 7.1.4, aggiornarla a questa versione.
2. Nel dispositivo di Palo Alto Networks, modificare la durata della fase 2 SA (o SA in modalità rapida) impostandola su 28.800 secondi (8 ore) quando si esegue la connessione al gateway VPN di Azure.
3. Se si verificano ancora problemi di connettività, aprire una richiesta di supporto dal portale di Azure.

