<properties 
   pageTitle="Informazioni sui dispositivi VPN per le connessioni gateway da sito a sito per reti virtuali di Azure | Microsoft Azure"
   description="Questo articolo tratta i dispositivi VPN e i parametri IPsec per le connessioni di gateway VPN S2S e contiene collegamenti a istruzioni ed esempi sulla configurazione."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
  tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/13/2016"
   ms.author="yushwang;cherylmc" />

# Informazioni sui dispositivi VPN per le connessioni di gateway VPN

Per configurare una connessione VPN da sito a sito (S2S), è necessario un dispositivo VPN. Le connessioni da sito a sito possono essere utilizzate per creare una soluzione ibrida o se si desidera una connessione sicura tra la rete locale e la rete virtuale. In questo articolo vengono illustrati i dispositivi VPN compatibili e i parametri di configurazione.

>[AZURE.NOTE] Quando si configura una connessione da sito a sito, è necessario un indirizzo IP IPv4 pubblico per il dispositivo VPN.

Se il dispositivo non viene visualizzato nella tabella [Dispositivi VPN convalidati](#devicetable), vedere la sezione [Dispositivi VPN non convalidati](#additionaldevices) in questo articolo. È possibile che il dispositivo possa funzionare comunque con Azure. Per il supporto ai dispositivi VPN, contattare il produttore del dispositivo.

**Elementi da considerare quando si visualizzano le tabelle:**

- È stata eseguita una modifica della terminologia per il routing statico e dinamico. È probabile che si incontrino entrambi i termini. Non è stata apportata alcuna modifica alle funzionalità, cambiano solo i nomi.
	- Routing statico = PolicyBased
	- Routing dinamico = RouteBased
- Se non indicato diversamente, le specifiche per i gateway VPN a prestazioni elevate e i gateway VPN RouteBased sono le stesse. Ad esempio, i dispositivi VPN convalidati e compatibili con i gateway VPN RouteBased sono compatibili anche con il gateway VPN a prestazioni elevate di Azure.


## <a name="devicetable"></a>Dispositivi VPN convalidati 

È stato approvato un set di dispositivi VPN standard in partnership con fornitori di dispositivi. Tutti i dispositivi nelle famiglie di dispositivi incluse nell'elenco seguente funzioneranno con i gateway VPN di Azure. Per verificare il tipo di gateway da creare per la soluzione che si vuole configurare, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md) .

Per agevolare la configurazione del dispositivo VPN, vedere i collegamenti corrispondenti alla famiglia di dispositivi appropriata. Per il supporto ai dispositivi VPN, contattare il produttore del dispositivo.



| **Fornitore** | **Famiglia di dispositivi** | **Versione minima del sistema operativo** | **PolicyBased** | **RouteBased** |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis | Router VPN serie AR | 2\.9.2 | Presto disponibile | Non compatibile |
| Barracuda Networks, Inc. | Barracuda NextGen Firewall F-series | PolicyBased: 5.4.3, RouteBased: 6.2.0 | [Istruzioni di configurazione](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) | [Istruzioni di configurazione](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. | Barracuda NextGen Firewall X-series | Barracuda Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | Non compatibile |
| Brocade | Vyatta 5400 vRouter | Virtual Router 6.6R3 GA | [Istruzioni di configurazione](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) | Non compatibile |
| Punto di controllo | Gateway di protezione | R75.40, R75.40VS | [Istruzioni di configurazione](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) | [Istruzioni di configurazione](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco | ASA | 8\.3 | [Esempi Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) | Non compatibile |
| Cisco | ASR | IOS 15.1 (PolicyBased), IOS 15.2 (RouteBased) | [Esempi Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) | [Esempi Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco | ISR | IOS 15.0 (PolicyBased), IOS 15.1 (RouteBased*) | [Esempi Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) | [Esempi Cisco*](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix | NetScaler MPX, SDX, VPX |10\.1 e versioni successive | [Istruzioni di integrazione](https://docs.citrix.com/it-IT/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) | Non compatibile |
| Dell SonicWALL | Serie TZ, serie NSA, serie SuperMassive, serie NSA classe E | SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646) | [Istruzioni - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Istruzioni - SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) | [Istruzioni - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Istruzioni - SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |
| F5 | Serie BIG-IP | 12\.0 | [Istruzioni di configurazione](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) | [Istruzioni di configurazione](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet | FortiGate | FortiOS 5.2.7 | [Istruzioni di configurazione](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure) | [Istruzioni di configurazione](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure) |
| Internet Initiative Japan (IIJ) | Serie SEIL | SEIL/X 4.60, SEIL/B1 4.60, SEIL/x86 3.20 | [Istruzioni di configurazione](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) | Non compatibile |
| Juniper | SRX | JunOS 10.2 (PolicyBased), JunOS 11.4 (RouteBased) | [Esempi Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) | [Esempi Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper | Serie J | JunOS 10.4r9 (PolicyBased), JunOS 11.4 (RouteBased) | [Esempi Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) | [Esempi Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper | ISG | ScreenOS 6.3 (PolicyBased e RouteBased) | [Esempi Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) | [Esempi Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper | SSG | ScreenOS 6.2 (PolicyBased e RouteBased) | [Esempi Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) | [Esempi Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft | Routing and Remote Access Service | Windows Server 2012 | Non compatibile | [Esempi Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG | Mission Control Security Gateway | N/D | [Guida all'installazione](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) | [Guida all'installazione](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan | Openswan | 2\.6.32 | (Presto disponibile) | Non compatibile |
| Palo Alto Networks | Tutti i dispositivi che eseguono PAN-OS | PAN-OS 6.1.5 o versione successiva (PolicyBased), PAN-OS 7.0.5 o versione successiva (RouteBased) | [Istruzioni di configurazione](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) | [Istruzioni di configurazione](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| WatchGuard | Tutti | Fireware XTM v11.x | [Istruzioni di configurazione](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/) | Non compatibile |

(*) I router serie ISR 7200 supportano solo VPN PolicyBased.

## <a name="additionaldevices"></a>Dispositivi VPN non convalidati

Anche se il dispositivo non è elencato nella tabella dei dispositivi VPN convalidati, potrebbe comunque funzionare con una connessione da sito a sito. Verificare che il dispositivo VPN soddisfi i requisiti minimi descritti nella sezione Requisiti del gateway dell’articolo [informazioni sui gateway VPN](vpn-gateway-about-vpngateways.md#gateway-requirements). I dispositivi che soddisfano i requisiti minimi dovrebbero funzionare correttamente anche con i gateway VPN. Contattare il produttore del dispositivo per assistenza e istruzioni di configurazione.


## Esempi di modifica di configurazione dispositivo

Dopo aver scaricato l'esempio di configurazione di dispositivo VPN fornito, è necessario sostituire alcuni dei valori in base alle impostazioni per l'ambiente.

**Per modificare un esempio:**

1. Aprire l'esempio utilizzando il blocco note.
1. Cercare e sostituire tutti le stringhe <*text*> con i valori pertinenti all'ambiente. Assicurarsi di includere < e >. Quando viene specificato un nome, il nome selezionato deve essere univoco. Se un comando non funziona, consultare la documentazione del produttore del dispositivo.

| **Testo di esempio** | **Modificare in** |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP\_OnPremisesNetwork&gt; | Nome scelto per questo oggetto. Esempio: myOnPremisesNetwork |
| &lt;RP\_AzureNetwork&gt; | Nome scelto per questo oggetto. Esempio: myAzureNetwork |
| &lt;RP\_AccessList&gt; | Nome scelto per questo oggetto. Esempio: myAzureAccessList |
| &lt;RP\_IPSecTransformSet&gt; | Nome scelto per questo oggetto. Esempio: myIPSecTransformSet |
| &lt;RP\_IPSecCryptoMap&gt; | Nome scelto per questo oggetto. Esempio: myIPSecCryptoMap |
| &lt;SP\_AzureNetworkIpRange&gt; | Specificare l'intervallo. Esempio: 192.168.0.0 |
| &lt;SP\_AzureNetworkSubnetMask&gt; | Specificare la subnet mask. Esempio: 255.255.0.0 |
| &lt;SP\_OnPremisesNetworkIpRange&gt; | Specificare l'intervallo in locale. Esempio: 10.2.1.0 |
| &lt;SP\_OnPremisesNetworkSubnetMask&gt; | Specificare la subnet mask locale. Esempio: 255.255.255.0 |
| &lt;SP\_AzureGatewayIpAddress&gt; | Queste informazioni sono specifiche per la rete virtuale e si trovano nel portale di gestione in **Indirizzo IP gateway**. |
| &lt;SP\_PresharedKey&gt; | Queste informazioni sono specifiche per la rete virtuale e si trovano nel portale di gestione in chiave di gestione. |



## Parametri IPsec

>[AZURE.NOTE] Anche se i valori elencati nella tabella seguente sono supportati dal Gateway VPN di Azure, attualmente non è possibile specificare o selezionare una combinazione specifica da Gateway VPN di Azure. È necessario specificare tutti i vincoli dal dispositivo VPN locale. È inoltre necessario limitare MSS a 1350.

### Configurazione fase 1 IKE

| **Proprietà** | **PolicyBased** | **Gateway VPN RouteBased e con prestazioni elevate o standard** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| Versione IKE | IKEv1 | IKEv2 |
| Diffie-Hellman Group | Gruppo 2 (1024 bit) | Gruppo 2 (1024 bit) |
| Metodo di autenticazione | Chiave precondivisa | Chiave precondivisa |
| Algoritmi di crittografia | AES256 AES128 3DES | AES256 3DES |
| Algoritmo di hash | SHA1(SHA128) | SHA1(SHA128), SHA2(SHA256) |
| Durata (tempo) associazione di sicurezza (SA) fase 1 | 28\.800 secondi | 10\.800 secondi |


### Configurazione fase 2 IKE

| **Proprietà** | **PolicyBased** | **Gateway VPN RouteBased e con prestazioni elevate o standard** |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| Versione IKE | IKEv1 | IKEv2 |
| Algoritmo di hash | SHA1(SHA128) | SHA1(SHA128) |
| Durata (tempo) associazione di sicurezza (SA) fase 2 | 3\.600 secondi | 3\.600 secondi |
| Durata (velocità effettiva) associazione di sicurezza (SA) fase 2 | 102.400.000 KB | - | 
| Offerte di autenticazione e crittografia SA IPsec (in ordine di preferenza) | 1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. N/A | Vedere *Offerte di associazione di sicurezza (SA) IPsec gateway RouteBased* (di seguito) | 
| Perfect Forward Secrecy (PFS) | No | No (*)| 
| Dead Peer Detection | Non supportato | Supportato |

(*) In quanto risponditore IKE, il gateway di Azure può accettare PFS DH Gruppo 1, 2, 5, 14, 24.

### Offerte di associazione di sicurezza (SA) IPsec gateway RouteBased

La tabella seguente include l'elenco delle offerte di autenticazione e crittografia SA IPsec. Le offerte sono elencate nell'ordine di preferenza di presentazione o di accettazione dell'offerta.

| **Offerte di autenticazione e crittografia SA IPsec** | **Gateway Azure come iniziatore** | **Gateway Azure come risponditore** |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1 | ESP AES\_256 SHA | ESP AES\_128 SHA |
| 2 | ESP AES\_128 SHA | ESP 3\_DES MD5 |
| 3 | ESP 3\_DES MD5 | ESP 3\_DES SHA |
| 4 | ESP 3\_DES SHA | AH SHA1 con ESP AES\_128 con HMAC Null |
| 5 | AH SHA1 con ESP AES\_256 con HMAC Null | AH SHA1 con ESP 3\_DES con HMAC Null |
| 6 | AH SHA1 con ESP AES\_128 con HMAC Null | AH MD5 con ESP 3\_DES con HMAC Null, nessuna durata proposta |
| 7 | AH SHA1 con ESP 3\_DES con HMAC Null | AH SHA1 con ESP 3\_DES SHA1, nessuna durata |
| 8 | AH MD5 con ESP 3\_DES con HMAC Null, nessuna durata proposta | AH MD5 con ESP 3\_DES MD5, nessuna durata |
| 9 | AH SHA1 con ESP 3\_DES SHA1, nessuna durata | ESP DES MD5 |
| 10 | AH MD5 con ESP 3\_DES MD5, nessuna durata | ESP DES SHA1, nessuna durata |
| 11 | ESP DES MD5 | AH SHA1 con ESP DES Null HMAC, nessuna durata proposta |
| 12 | ESP DES SHA1, nessuna durata | AH MD5 con ESP DES Null HMAC, nessuna durata proposta |
| 13 | AH SHA1 con ESP DES Null HMAC, nessuna durata proposta | AH SHA1 con ESP DES SHA1, nessuna durata |
| 14 | AH MD5 con ESP DES Null HMAC, nessuna durata proposta | AH MD5 con ESP DES MD5, nessuna durata |
| 15 | AH SHA1 con ESP DES SHA1, nessuna durata | ESP SHA, nessuna durata |
| 16 | AH MD5 con ESP DES MD5, nessuna durata | ESP MD5, nessuna durata |
| 17 | - | AH SHA, nessuna durata | 
| 18 | - | AH MD5, nessuna durata |


- È possibile specificare la crittografia NULL ESP IPsec con gateway VPN RouteBased e con prestazioni elevate. La crittografia basata su null non fornisce protezione ai dati in transito e deve essere usata solo quando sono richieste una velocità effettiva massima e una latenza minima. I client possono scegliere di usare questa crittografia in scenari di comunicazione tra reti virtuali oppure quando la crittografia viene applicata in un'altra posizione nella soluzione.

- Per la connettività cross-premise tramite Internet, usare le impostazioni del gateway VPN di Azure predefinite con algoritmi di crittografia e hash elencati nelle tabelle precedenti, per garantire la sicurezza delle comunicazioni critiche.

<!---HONumber=AcomDC_0914_2016-->