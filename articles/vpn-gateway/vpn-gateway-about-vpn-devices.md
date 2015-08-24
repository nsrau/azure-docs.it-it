<properties 
   pageTitle="Informazioni sui dispositivi VPN per le connessioni di rete virtuale da sito a sito di Azure | Microsoft Azure"
   description="Informazioni sui dispositivi VPN e sui parametri IPsec per le connessioni gateway VPN di rete virtuale da sito a sito di Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/07/2015"
   ms.author="cherylmc" />

# Informazioni sui dispositivi VPN per le connessioni di rete virtuale da sito a sito

Per configurare una connessione VPN da sito a sito, è necessario un dispositivo VPN. Le connessioni da sito a sito possono essere utilizzate per creare una soluzione di cloud ibrida o se si desidera una connessione sicura tra la rete locale e la rete virtuale. In questo articolo vengono illustrati i dispositivi VPN compatibili e i parametri di configurazione.

È importante sapere che, oltre a un dispositivo VPN compatibile, le connessioni da sito a sito richiedono inoltre un indirizzo IP IPv4 pubblico. Inoltre, è opportuno selezionare il tipo di gateway che supporterà la soluzione. Non tutti i dispositivi VPN supportano tutti i tipi di gateway. Vedere [Gateway VPN](vpn-gateway-about-vpngateways.md) per verificare il tipo di gateway necessario per creare la soluzione.



## Dispositivi VPN

È stato approvato un set di dispositivi VPN Site-to-Site (S2S) standard in partnership con fornitori di dispositivi. Per un elenco di dispositivi VPN compatibili con la rete virtuale, vedere la seguente sezione [Dispositivi VPN compatibili](#compatible-vpn-devices). Tutti i dispositivi nelle famiglie di dispositivi contenute in questo elenco dovrebbero funzionare con i gateway VPN di Azure. Per agevolare la configurazione del dispositivo VPN, fare riferimento all'esempio di configurazione del dispositivo corrispondente alla famiglia di dispositivi appropriata.

Se non specificato diversamente, le specifiche per i gateway VPN a prestazioni elevate e i gateway VPN con routing dinamico sono le stesse. Ad esempio, i dispositivi VPN convalidati compatibili con i gateway VPN con routing dinamico di Azure saranno compatibili anche con il nuovo gateway VPN di Azure ad elevate prestazioni.


### Dispositivi VPN compatibili

Abbiamo collaborato con i fornitori di dispositivi VPN per qualificare congiuntamente le famiglie di dispositivi VPN specifiche. La sezione seguente fornisce un elenco di tutte le famiglie di dispositivi compatibili con il gateway VPN. Tutti i dispositivi che sono membri delle famiglie di dispositivi elencate funzionano. Eventuali eccezioni sono indicate nell’elenco. Se il dispositivo non viene visualizzato nell'elenco, vedere [Dispositivi non presenti nell'elenco compatibile](#devices-not-on-the-compatible-list).



Per il supporto ai dispositivi VPN, contattare il produttore del dispositivo.



| **Fornitore** | **Famiglia di dispositivi** | **Versione minima del sistema operativo** | **Routing statico** | **Routing dinamico** |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis | Router VPN serie AR | 2\.9.2 | Presto disponibile | Non compatibile |
| Barracuda Networks, Inc. | Barracuda NG Firewall | Barracuda NG Firewall 5.4.3 | [Barracuda NG Firewall](https://techlib.barracuda.com/display/BNGV54/How%20to%20Configure%20an%20IPsec%20Site-to-Site%20VPN%20to%20a%20Windows%20Azure%20VPN%20Gateway)| Non compatibile |
| Barracuda Networks, Inc. | Barracuda Firewall | Barracuda Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | Non compatibile |
| Brocade | Vyatta 5400 vRouter | Virtual Router 6.6R3 GA | [Istruzioni di configurazione](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) | Non compatibile |
| Punto di controllo | Gateway di protezione | R75.40, R75.40VS | [Istruzioni di configurazione](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) | [Istruzioni di configurazione](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco | ASA | 8\.3 | [Esempi ASA Cisco](https://msdn.microsoft.com/library/azure/dn133793.aspx) | Non compatibile |
| Cisco | ASR | IOS 15.1 (statico), IOS 15.2 (dinamico) | [Esempi di ASR Cisco](https://msdn.microsoft.com/library/azure/dn133802.aspx) | [Esempi di ASR Cisco](https://msdn.microsoft.com/library/azure/dn133802.aspx) |
| Cisco | ISR | IOS 15.0 (statico), IOS 15.1 (dinamico) | [Esempi ISR Cisco](https://msdn.microsoft.com/library/azure/dn133800.aspx) | [Esempi ISR Cisco](https://msdn.microsoft.com/library/azure/dn133800.aspx) |
| Citrix | Dispositivo MPX CloudBridge o dispositivo virtuale VPX | N/D | [Istruzioni di integrazione](https://www.citrix.com/welcome.html?resource=%2Fdownloads%2Fcloudbridge%2Fbetas-and-tech-previews%2Fcloudbridge-azure-integration) | Non compatibile |
| Dell SonicWALL | Serie TZ, serie NSA, serie SuperMassive, serie NSA classe E | SonicOS 5.8.x, SonicOS 5.9.x, SonicOS 6.x | [Istruzioni di configurazione](https://www.sonicwall.com/app/projects/file_downloader/document_lib.php?t=TN&id=348) | Non compatibile |
| F5 | Serie BIG-IP | N/D | [Istruzioni di configurazione](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) | Non compatibile |
| Fortinet | FortiGate | FortiOS 5.0.7 | [Istruzioni di configurazione](http://docs.fortinet.com/fortigate/admin-guides) | [Istruzioni di configurazione](http://docs.fortinet.com/fortigate/admin-guides) |
| Internet Initiative Japan (IIJ) | Serie SEIL | SEIL/X 4.60, SEIL/B1 4.60, SEIL/x86 3.20 | [Istruzioni di configurazione](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) | Non compatibile |
| Juniper | SRX | JunOS 10.2 (statico), JunOS 11.4 (dinamico) | [Esempi SRX Juniper](https://msdn.microsoft.com/library/azure/dn133794.aspx) | [Esempi SRX Juniper](https://msdn.microsoft.com/library/azure/dn133794.aspx) |
| Juniper | Serie J | JunOS 10.4r9 (statico), JunOS 11.4 (dinamico) | [Esempi di serie Juniper J](https://msdn.microsoft.com/library/azure/dn133799.aspx) | [Esempi di serie Juniper J](https://msdn.microsoft.com/library/azure/dn133799.aspx) |
| Juniper | ISG | ScreenOS 6.3 (statico e dinamico) | [Esempi Juniper ISG](https://msdn.microsoft.com/library/azure/dn133797.aspx) | [Esempi Juniper ISG](https://msdn.microsoft.com/library/azure/dn133797.aspx) |
| Juniper | SSG | ScreenOS 6.2 (statico e dinamico) | [Esempi Juniper SSG](https://msdn.microsoft.com/library/azure/dn133796.aspx) | [Esempi Juniper SSG](https://msdn.microsoft.com/library/azure/dn133796.aspx) |
| Macchina virtuale | Routing and Remote Access Service | Windows Server 2012 | Non compatibile | [Esempi di Routing e Remote Access Service (RRAS)](https://msdn.microsoft.com/library/azure/dn133801.aspx) |
| Openswan | Openswan | 2\.6.32 | (Presto disponibile) | Non compatibile |
| Palo Alto Networks | Tutti i dispositivi che eseguono PAN-OS 5.0 o versione successiva | PAN-OS 5x o versione successiva | [Palo Alto Networks](https://support.paloaltonetworks.com/) | Non compatibile |
| WatchGuard | Tutti | Fireware XTM v11.x | [Istruzioni di configurazione](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/) | Non compatibile |


### Dispositivi non presenti nell'elenco compatibile


Se il dispositivo non compare nell'elenco di dispositivi VPN compatibili e si desidera utilizzarlo per la connessione VPN, è necessario verificare che soddisfi i requisiti minimi descritti nella tabella [Requisiti del gateway](vpn-gateway-about-vpngateways.md#gateway-requirements). I dispositivi che soddisfano i requisiti minimi dovrebbero funzionare correttamente con Rete virtuale. Contattare il produttore del dispositivo per assistenza e istruzioni di configurazione.


## Esempi di modifica di configurazione dispositivo

Dopo aver scaricato l'esempio di configurazione di dispositivo VPN fornito, è necessario sostituire alcuni dei valori in base alle impostazioni per l'ambiente.

**Per modificare il codice di esempio:**

1. Aprire l'esempio utilizzando il blocco note. 
1. Cercare e sostituire tutti le stringhe <*testo*> con i valori pertinenti all'ambiente. Assicurarsi di includere < and >. Quando viene specificato un nome, il nome selezionato deve essere univoco. Se un comando non funziona, consultare la documentazione del produttore dispositivo.

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
| &lt;SP\_AzureGatewayIpAddress&gt; | Queste informazioni sono specifiche per la rete virtuale e si trovano nel portale di gestione in **Indirizzo IP del gateway**. |
| &lt;SP\_PresharedKey&gt; | Queste informazioni sono specifiche per la rete virtuale e si trovano nel portale di gestione in chiave di gestione. |



## Parametri IPsec

### Configurazione fase 1 IKE

| **Proprietà** | **Gateway VPN con routing statico** | **Gateway VPN con Routing dinamico e Standard o gateway VPN HighPerformance** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| Versione IKE | IKEv1 | IKEv2 |
| Diffie-Hellman Group | Gruppo 2 (1024 bit) | Gruppo 2 (1024 bit) |
| Metodo di autenticazione | Chiave precondivisa | Chiave precondivisa |
| Algoritmi di crittografia | AES256 AES128 3DES | AES256 3DES |
| Algoritmo di hash | SHA1(SHA128) | SHA1(SHA128) |
| Fase 1 Associazione di sicurezza (SA) durata (tempo) | 28,800 secondi | 28,800 secondi |


### Configurazione fase 2 IKE

| **Proprietà** | **Gateway VPN con routing statico** | **Gateway VPN con Routing dinamico e Standard o gateway VPN HighPerformance** |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| Versione IKE | IKEv1 | IKEv2 |
| Algoritmo di hash | SHA1(SHA128) | SHA1(SHA128) |
| Fase 2 Associazione di sicurezza (SA) durata (tempo) | 3,600 secondi | - | | Fase 2 Associazione di sicurezza (SA) durata (produttività) | 102,400,000 KB | - | | Offerte di autenticazione e crittografia SA IPsec (in ordine di preferenza) | 1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. N/D | Vedere offerte di associazione di sicurezza (SA) IPsec gateway routing dinamico | | Perfect Forward Secrecy (PFS) | No | Sì (DH Gruppo1) | | Dead Peer Detection | Non supportato | Supportato |

### Offerte di associazione di sicurezza (SA) IPsec gateway routing dinamico

Nella tabella seguente sono elencate le offerte di autenticazione e crittografia SA IPsec. Le offerte sono elencate nell'ordine di preferenza di presentazione o di accettazione dell'offerta.

| **Offerte di autenticazione e crittografia SA IPsec** | **Gateway Azure come iniziatore** | Gateway Azure come risponditore |
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
| 17 | - | AH SHA, nessuna durata | | 18 | - | AH MD5, nessuna durata |




- È possibile specificare la crittografia IPsec ESP NULL con gateway VPN a prestazioni elevate e routing dinamico, previsto per le connessioni VNet-to-VNet all'interno di reti di Azure. 

- Per la connettività cross-premises tramite Internet, utilizzare le impostazioni del gateway VPN di Azure predefinite con la crittografia e gli algoritmi di hash elencati nelle tabelle precedenti, per garantire la sicurezza delle comunicazioni critiche.

## Passaggi successivi


Per altre informazioni sui gateway VPN, vedere [Informazioni sui gateway VPN](vpn-gateway-about-vpngateways.md).

Per configurare una rete virtuale con una connessione VPN da sito a sito vedere [Creare una rete virtuale con connessione VPN da sito a sito](vpn-gateway-site-to-site-create.md).

<!---HONumber=August15_HO7-->