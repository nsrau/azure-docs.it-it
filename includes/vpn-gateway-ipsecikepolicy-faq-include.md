### I criteri IPsec/IKE personalizzati sono supportati in tutti gli SKU del gateway VPN di Azure?
<a id="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus" class="xliff"></a>
I criteri IPsec/IKE personalizzati sono supportati nei gateway VPN **VpnGw1, VpnGw2, VpnGw3, Standard** e **HighPerformance** di Azure. **Basic** NON è supportato.

### Quanti criteri è possibile specificare per una connessione?
<a id="how-many-policies-can-i-specify-on-a-connection" class="xliff"></a>
Per una determinata connessione è possibile specificare ***una*** sola combinazione di criteri.

### Per una connessione è possibile specificare criteri parziali, ad esempio solo algoritmi IKE ma non IPsec?
<a id="can-i-specify-a-partial-policy-on-a-connection-eg-only-ike-algorithms-but-not-ipsec" class="xliff"></a>
No. È necessario specificare tutti gli algoritmi e i parametri sia per IKE (modalità principale) che per IPsec (modalità rapida). Non è consentito specificare criteri parziali.

### Quali algoritmi e tipi di attendibilità della chiave sono supportati nei criteri personalizzati?
<a id="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy" class="xliff"></a>
La tabella seguente riporta l'elenco degli algoritmi di crittografia e dei tipi di attendibilità della chiave supportati e configurabili dai clienti. È necessario selezionare un'opzione per ogni campo.

| **IPsec/IKEv2**  | **Opzioni**                                                                 |
| ---              | ---                                                                         |
| Crittografia IKEv2 | AES256, AES192, AES128, DES3, DES                                           |
| Integrità IKEv2  | SHA384, SHA256, SHA1, MD5                                                   |
| Gruppo DH         | ECP384, ECP256, DHGroup24, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| Crittografia IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, None    |
| Integrità IPsec  | GCMAES256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                          |
| Gruppo PFS        | ECP384, ECP256, PFS24, PFS2048, PFS14, PFS2, PFS1, None                     |
| Durata associazione di sicurezza in modalità rapida*  | Secondi (integer; **min. 300**) e KByte (integer; **min. 1024**)                                      |
| Selettore di traffico | UsePolicyBasedTrafficSelectors** ($True/$False; default $False)                             |
|                  |                                                                             |

* (*) Nei gateway VPN di Azure, la durata dell'associazione di sicurezza IKEv2 (modalità principale) è fissata a 28.800 secondi.
* (**) Per informazioni su "UsePolicyBasedTrafficSelectors", vedere la domanda frequente successiva.

### È necessaria la corrispondenza di tutti gli elementi tra i criteri del gateway VPN di Azure e le configurazioni dei dispositivi VPN locali?
<a id="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations" class="xliff"></a>
La configurazione del dispositivo VPN locale deve contenere o corrispondere agli algoritmi e ai parametri seguenti specificati nei criteri IPsec/IKE di Azure:

* Algoritmo di crittografia IKE
* Algoritmo di integrità IKE
* Gruppo DH
* Algoritmo di crittografia IPsec
* Algoritmo di integrità IPsec
* Gruppo PFS
* Selettore di traffico (*)

La durata delle associazioni di sicurezza è una specifica locale. Non è necessaria la corrispondenza.

Se si abilita **UsePolicyBasedTrafficSelectors**, è necessario verificare che i selettori di traffico corrispondenti siano definiti nel dispositivo VPN con tutte le combinazioni dei prefissi della rete locale (gateway di rete locale) da/verso i prefissi della rete virtuale di Azure, anziché any-to-any. Se i prefissi della rete locale sono 10.1.0.0/16 e 10.2.0.0/16 e i prefissi della rete virtuale sono 192.168.0.0/16 e 172.16.0.0/16, ad esempio, è necessario specificare i selettori di traffico seguenti:
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

Per altri dettagli sull'uso di questa opzione, vedere l'articolo su come [connettere più dispositivi VPN basati su criteri locali](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

### I criteri personalizzati sostituiscono i set di criteri IPsec/IKE predefiniti per i gateway VPN di Azure?
<a id="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways" class="xliff"></a>
Sì. Quando per una connessione vengono specificati criteri personalizzati, il gateway VPN di Azure userà solo tali criteri per la connessione, sia come iniziatore IKE che come risponditore IKE.

### Se si rimuovono i criteri IPsec/IKE personalizzati, la connessione diventa non protetta?
<a id="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected" class="xliff"></a>
No. La connessione sarà comunque protetta tramite IPsec/IKE. Dopo la rimozione dei criteri personalizzati da una connessione, il gateway VPN di Azure ripristinerà l'[elenco predefinito delle proposte IPsec/IKE](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) e riavvierà nuovamente l'handshake IKE con il dispositivo VPN locale.

### L'aggiunta o l'aggiornamento di criteri IPsec/IKE determinerà un'interruzione della connessione VPN?
<a id="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection" class="xliff"></a>
Sì. Potrebbe causare una breve interruzione di alcuni secondi perché il gateway VPN di Azure chiuderà la connessione esistente e riavvierà l'handshake IKE per ristabilire il tunnel IPsec con i nuovi algoritmi di crittografia e i nuovi parametri. Per ridurre al minimo l'interruzione, verificare che il dispositivo VPN locale sia configurato anche con gli algoritmi e i tipi di attendibilità della chiave corrispondenti.

### È possibile usare criteri diversi per connessioni diverse?
<a id="can-i-use-different-policies-on-different-connections" class="xliff"></a>
Sì. I criteri personalizzati vengono applicati in base alla connessione. È possibile creare e applicare criteri IPsec/IKE diversi per connessioni diverse. Si possono anche applicare criteri personalizzati a un sottoinsieme di connessioni. Le connessioni rimanenti useranno i set di criteri IPsec/IKE predefiniti di Azure.

### È possibile usare criteri personalizzati anche per una connessione da rete virtuale a rete virtuale?
<a id="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well" class="xliff"></a>
Sì. È possibile applicare criteri personalizzati sia a connessioni cross-premise IPsec che a connessioni da rete virtuale a rete virtuale.

### È necessario specificare gli stessi criteri per entrambe le risorse di connessione da rete virtuale a rete virtuale?
<a id="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources" class="xliff"></a>
Sì. Un tunnel da rete virtuale a rete virtuale è costituito da due risorse di connessione di Azure, una per ogni direzione. È necessario verificare che entrambe le risorse di connessione abbiano gli stessi criteri. In caso contrario, la connessione da rete virtuale a rete virtuale non verrà stabilita.

### I criteri IPsec/IKE personalizzati funzionano in una connessione ExpressRoute?
<a id="does-custom-ipsecike-policy-work-on-expressroute-connection" class="xliff"></a>
No. I criteri IPsec/IKE funzionano solo in connessioni VPN da sito a sito e da rete virtuale a rete virtuale tramite gateway VPN di Azure.
