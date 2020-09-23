---
title: 'Criteri IPsec/IKE per VPN S2S & connessioni da VNet a VNet: portale di Azure'
titleSuffix: Azure VPN Gateway
description: Configurare i criteri IPsec/IKE per le connessioni S2S o da VNet a VNet con i gateway VPN di Azure usando Azure Resource Manager e portale di Azure.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: eda920640667abc6620c5c90ee7d04a44789353e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90996758"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections-azure-portal"></a>Configurare i criteri IPsec/IKE per le connessioni VPN S2S o da VNet a VNet: portale di Azure

Questo articolo illustra i passaggi per configurare i criteri IPsec/IKE per le connessioni VPN da sito a sito del gateway VPN o da VNet a VNet usando il portale di Azure. Le sezioni seguenti consentono di creare e configurare un criterio IPsec/IKE e di applicare il criterio a una connessione nuova o esistente.

## <a name="about-ipsec-and-ike-policy-parameters"></a><a name="about"></a>Informazioni sui parametri del criterio IPsec e IKE

Lo standard di protocollo IPsec e IKE supporta un'ampia gamma di algoritmi di crittografia in varie combinazioni. Per [informazioni sui requisiti di crittografia e sui gateway VPN di Azure, vedere informazioni sui requisiti di crittografia e i gateway VPN di Azure](vpn-gateway-about-compliance-crypto.md) per verificare la connettività tra più sedi locali e VNet per soddisfare i requisiti di conformità o sicurezza.

Questo articolo fornisce istruzioni per creare e configurare un criterio IPsec/IKE e applicarlo a una connessione gateway VPN nuova o esistente.

### <a name="considerations"></a>Considerazioni

* Il criterio IPsec/IKE funziona solo sugli SKU del gateway seguenti:
  * ***VpnGw1 ~ 5 e VpnGw1AZ ~ 5AZ***
  * ***Standard*** e ***HighPerformance***
* Per una determinata connessione è possibile specificare ***una*** sola combinazione di criteri.
* È necessario specificare tutti gli algoritmi e i parametri sia per IKE (modalità principale) che per IPsec (modalità rapida). Non è consentito specificare criteri parziali.
* Consultare le specifiche del fornitore del dispositivo VPN per verificare che i criteri siano supportati dai dispositivi VPN locali. Non è possibile stabilire connessioni da sito a sito o da rete virtuale a rete virtuale se i criteri non sono compatibili.

## <a name="workflow"></a><a name ="workflow"></a>Flusso di lavoro

Questa sezione descrive il flusso di lavoro per creare e aggiornare i criteri IPsec/IKE per una connessione VPN da sito a sito o da rete virtuale a rete virtuale:

1. Creare una rete virtuale e un gateway VPN.
2. Creare un gateway di rete locale per la connessione cross-premise o un'altra rete virtuale e un gateway per la connessione da VNet a VNet.
3. Creare una connessione (IPsec o VNet2VNet).
4. Configurare/aggiornare/rimuovere il criterio IPsec/IKE sulle risorse di connessione.

Le istruzioni riportate in questo articolo consentono di configurare e configurare i criteri IPsec/IKE come illustrato nel diagramma:

:::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="Diagramma dei criteri IPsec/IKE" border="false":::

## <a name="supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>Algoritmi di crittografia supportati & i punti di forza della chiave

### <a name="algorithms-and-keys"></a><a name ="table1"></a>Algoritmi e chiavi

La tabella seguente riporta l'elenco degli algoritmi di crittografia e dei tipi di attendibilità della chiave supportati e configurabili dai clienti:

| **IPsec/IKE**    | **Opzioni**    |
| ---              | ---            |
| Crittografia IKE   | AES256, AES192, AES128, DES3, DES                  |
| Integrità IKE    | SHA384, SHA256, SHA1, MD5                          |
| Gruppo DH         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| Crittografia IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, None    |
| Integrità IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Gruppo PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, None   |
| Durata associazione di sicurezza in modalità rapida   | (**Facoltativo**: se non diversamente specificato, vengono usati i valori predefiniti)<br>Secondi (intero; **min. 300**/valore predefinito di 27000 secondi)<br>Kilobyte (intero; **min 1024**/valore predefinito di 102400000 KB)    |
| Selettore di traffico | UsePolicyBasedTrafficSelectors** ($True/$False; **Optional**, $False predefinito, se non diversamente specificato)    |
| Timeout DPD      | Secondi (Integer: min. 9/max. 3600; valore predefinito 45 secondi) |
|  |  |

#### <a name="important-requirements"></a>Requisiti importanti

* La configurazione del dispositivo VPN locale deve contenere o corrispondere agli algoritmi e ai parametri seguenti specificati nei criteri IPsec/IKE di Azure:
  * Algoritmo di crittografia IKE (modalità principale / fase 1)
  * Algoritmo di integrità IKE (modalità principale / fase 1)
  * Gruppo DH (modalità principale / fase 1)
  * Algoritmo di crittografia IPsec (modalità rapida / fase 2)
  * Algoritmo di integrità IPsec (modalità rapida / fase 2)
  * Gruppo PFS (modalità rapida/fase 2) > * selettore di traffico (se viene usato UsePolicyBasedTrafficSelectors)
  * La durata delle associazioni di sicurezza è una specifica locale. Non è necessaria la corrispondenza.

* Se GCMAES viene usato come algoritmo di crittografia IPsec, è necessario selezionare lo stesso algoritmo GCMAES e la stessa lunghezza della chiave per l'integrità IPsec. ad esempio, usando GCMAES128 per entrambi.

* Nella [tabella algoritmi e chiavi](#table1) precedente:
  * IKE corrisponde alla modalità principale o alla fase 1
  * IPsec corrisponde alla modalità rapida / fase 2
  * Gruppo DH specifica il gruppo Diffie-Hellman usato in modalità principale o fase 1
  * Gruppo PFS specifica il gruppo Diffie-Hellman usato in modalità rapida o fase 2

* La durata dell'associazione di modalità principale IKE è fissata a 28.800 secondi nei gateway VPN di Azure.

* Se si imposta **UsePolicyBasedTrafficSelectors** su $true in una connessione, il gateway VPN di Azure verrà configurato per connettersi al firewall VPN basato su criteri in locale. Se si abilita PolicyBasedTrafficSelectors, è necessario verificare che i selettori di traffico corrispondenti siano definiti nel dispositivo VPN con tutte le combinazioni dei prefissi della rete locale (gateway di rete locale) da/verso i prefissi della rete virtuale di Azure, anziché any-to-any. Se i prefissi della rete locale sono 10.1.0.0/16 e 10.2.0.0/16 e i prefissi della rete virtuale sono 192.168.0.0/16 e 172.16.0.0/16, ad esempio, è necessario specificare i selettori di traffico seguenti:
  * 10.1.0.0/16 <====> 192.168.0.0/16
  * 10.1.0.0/16 <====> 172.16.0.0/16
  * 10.2.0.0/16 <====> 192.168.0.0/16
  * 10.2.0.0/16 <====> 172.16.0.0/16

   Per altre informazioni sui selettori di traffico basati su criteri, vedere [Connettere più dispositivi VPN basati su criteri locali](vpn-gateway-connect-multiple-policybased-rm-ps.md).

* Timeout DPD: il valore predefinito è 45 secondi nei gateway VPN di Azure. Se si imposta il timeout su periodi più brevi, IKE rekey più in modo più aggressivo, causando la disconnessione della connessione in alcune istanze. Questo potrebbe non essere auspicabile se le posizioni locali sono più lontane dall'area di Azure in cui risiede il gateway VPN o la condizione di collegamento fisico potrebbe causare la perdita di pacchetti. Il suggerimento generale è impostare il timeout tra **30 e 45** secondi.

### <a name="diffie-hellman-groups"></a>Gruppi Diffie-Hellman

La tabella seguente elenca i gruppi di Diffie-Hellman corrispondenti supportati dal criterio personalizzato:

| **Gruppo Diffie-Hellman**  | **DHGroup**              | **PFSGroup** | **Lunghezza chiave** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | MODP a 768 bit   |
| 2                         | DHGroup2                 | PFS2         | MODP a 1024 bit  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | MODP a 2048 bit  |
| 19                        | ECP256                   | ECP256       | ECP a 256 bit    |
| 20                        | ECP384                   | ECP384       | ECP a 384 bit    |
| 24                        | DHGroup24                | PFS24        | MODP a 2048 bit  |

Per altre informazioni, vedere [RFC3526](https://tools.ietf.org/html/rfc3526) e [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="s2s-vpn-with-ipsecike-policy"></a><a name ="S2S"></a>VPN S2S con criterio IPsec/IKE

Questa sezione illustra i passaggi per creare una connessione VPN da sito a sito con criteri IPsec/IKE. La procedura seguente crea la connessione come illustrato nel diagramma seguente:

:::image type="content" source="./media/ipsec-ike-policy-howto/site-to-site-diagram.png" alt-text="Criteri da sito a sito" border="false":::

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>Passaggio 1: Creare la rete virtuale, il gateway VPN e il gateway di rete locale

Creare le risorse seguenti, come illustrato nelle schermate seguenti. Per la procedura, vedere [creare una connessione VPN da sito a sito](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

* **Rete virtuale:**  TestVNet1

   :::image type="content" source="./media/ipsec-ike-policy-howto/testvnet-1.png" alt-text="VNet":::

* **Gateway VPN:** VNet1GW

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-1-gateway.png" alt-text="Gateway":::

* **Gateway di rete locale:** Site6

   :::image type="content" source="./media/ipsec-ike-policy-howto/lng-site-6.png" alt-text="Sito":::

* **Connessione:** Da VNet1 a Site6

    :::image type="content" source="./media/ipsec-ike-policy-howto/connection-site-6.png" alt-text="Connection":::

### <a name="step-2---configure-ipsecike-policy-on-the-s2s-vpn-connection"></a><a name="s2sconnection"></a>Passaggio 2: configurare i criteri IPsec/IKE nella connessione VPN S2S

In questa sezione, configurare un criterio IPsec/IKE con gli algoritmi e i parametri seguenti:

* IKE: AES256, SHA384, DHGroup24, timeout DPD 45 secondi
* IPsec: AES256, SHA256, PFS None, durata SA 30000 secondi e 102400000 KB

1. Passare alla risorsa di connessione, **VNet1toSite6**, nella portale di Azure. Selezionare la pagina **configurazione** e selezionare criteri IPSec/IKE **personalizzati** per visualizzare tutte le opzioni di configurazione. La schermata seguente mostra la configurazione in base all'elenco:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-site-6.png" alt-text="Sito 6":::

1. Se si usa GCMAES per IPsec, è necessario usare lo stesso algoritmo e la stessa lunghezza della chiave GCMAES per la crittografia e l'integrità IPsec. Lo screenshot seguente, ad esempio, specifica GCMAES128 sia per la crittografia IPsec che per l'integrità IPsec:

   :::image type="content" source="./media/ipsec-ike-policy-howto/gcmaes.png" alt-text="GCMAES per IPsec":::

1. Facoltativamente, è possibile selezionare **Abilita** per l'opzione **USA selettori di traffico basati su criteri** per abilitare il gateway VPN di Azure per la connessione a dispositivi VPN basati su criteri in locale, come descritto in precedenza.

   :::image type="content" source="./media/ipsec-ike-policy-howto/policy-based-selector.png" alt-text="Selettore di traffico basato su criteri":::

1. Una volta selezionate tutte le opzioni, selezionare **Salva** per eseguire il commit delle modifiche nella risorsa di connessione. Il criterio verrà applicato tra circa un minuto.

> [!IMPORTANT]
>
> * Dopo aver specificato un criterio IPsec/IKE per una connessione, il gateway VPN di Azure invierà o accetterà la proposta IPsec/IKE con gli algoritmi di crittografia e i principali vantaggi specificati per una particolare connessione. Assicurarsi che il dispositivo VPN locale per la connessione usi o accetti l'esatta combinazione di criteri. In caso contrario, il tunnel VPN da sito a sito non verrà stabilito.
>
> * Il **selettore di traffico basato su criteri** e le opzioni di **timeout DPD** possono essere specificati con i criteri **predefiniti** , senza il criterio IPSec/IKE personalizzato, come illustrato nella schermata precedente.
>

## <a name="vnet-to-vnet-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>Da VNet a VNet con criteri IPsec/IKE

I passaggi per creare una connessione da VNet a VNet con un criterio IPsec/IKE sono simili a quelli di una connessione VPN S2S.

:::image type="content" source="./media/ipsec-ike-policy-howto/vnet-policy.png" alt-text="Diagramma dei criteri da VNet a VNet" border="false":::

1. Usare la procedura descritta nell'articolo [creare una connessione da VNet a VNet](vpn-gateway-vnet-vnet-rm-ps.md) per creare la connessione da VNet a VNet.

2. Al termine della procedura, vengono visualizzate due connessioni da VNet a VNet, come illustrato nella schermata seguente dalla risorsa VNet2GW:

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-connections.png" alt-text="Connessioni da rete virtuale a rete virtuale":::

3. Passare alla risorsa di connessione e passare alla pagina di **configurazione** nel portale. Selezionare **personalizzato** nei **criteri IPSec/IKE** per visualizzare le opzioni dei criteri personalizzati. Selezionare gli algoritmi di crittografia con le lunghezze di chiave corrispondenti.

   Lo screenshot mostra un criterio IPsec/IKE diverso con gli algoritmi e i parametri seguenti:
   * IKE: AES128, SHA1, DHGroup14, timeout DPD 45 secondi
   * IPsec: GCMAES128, GCMAES128, PFS14, durata dell'associazione di sicurezza 14.400 secondi e 1024 KB

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-policy.png" alt-text="Criteri di connessione":::

4. Selezionare **Save (Salva** ) per applicare le modifiche ai criteri nella risorsa di connessione.

5. Applicare lo stesso criterio all'altra risorsa di connessione, VNet2toVNet1. In caso contrario, il tunnel VPN IPsec/IKE non si connetterà a causa di una mancata corrispondenza dei criteri.

   > [!IMPORTANT]
   > Dopo aver specificato un criterio IPsec/IKE per una connessione, il gateway VPN di Azure invierà o accetterà la proposta IPsec/IKE con gli algoritmi di crittografia e i principali vantaggi specificati per una particolare connessione. Assicurarsi che i criteri IPsec per entrambe le connessioni siano gli stessi. In caso contrario, la connessione da rete virtuale a rete virtuale non verrà stabilita.

6. Dopo aver completato questi passaggi, la connessione viene stabilita in pochi minuti e si avrà la topologia di rete seguente:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="Diagramma dei criteri IPsec/IKE" border="false":::

## <a name="to-remove-custom-ipsecike-policy-from-a-connection"></a><a name ="deletepolicy"></a>Per rimuovere i criteri IPsec/IKE personalizzati da una connessione

1. Per rimuovere un criterio personalizzato da una connessione, passare alla risorsa di connessione e passare alla pagina di **configurazione** per visualizzare i criteri correnti.

2. Selezionare **impostazione predefinita** nell'opzione **criterio IPSec/IKE** . Questa operazione rimuoverà tutti i criteri personalizzati specificati in precedenza nella connessione e ripristinerà le impostazioni IPsec/IKE predefinite della connessione:

   :::image type="content" source="./media/ipsec-ike-policy-howto/delete-policy.png" alt-text="Elimina criteri":::

3. Selezionare **Save (Salva** ) per rimuovere i criteri personalizzati e ripristinare le impostazioni IPSec/IKE predefinite per la connessione.

## <a name="next-steps"></a>Passaggi successivi

Per altri dettagli sui selettori di traffico basati su criteri, vedere l'articolo su come [connettere più dispositivi VPN basati su criteri locali](vpn-gateway-connect-multiple-policybased-rm-ps.md).
