<properties
   pageTitle="Creare una rete virtuale con una connessione VPN da sito a sito usando Azure Resource Manager e il portale di Azure | Microsoft Azure"
   description="Questo articolo illustra la creazione di una rete virtuale con il modello di Gestione risorse e la connessione alla rete locale con una connessione del gateway VPN da sito a sito."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/25/2016"
   ms.author="cherylmc"/>

# Creare una rete virtuale di Resource Manager con una connessione VPN da sito a sito nel portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Portale di Azure - Classico](vpn-gateway-site-to-site-create.md)
- [PowerShell - Gestione risorse](vpn-gateway-create-site-to-site-rm-powershell.md)


Questo articolo illustra la creazione di una rete virtuale e una connessione VPN da sito a sito alla rete locale con il modello di distribuzione di Azure Resource Manager e il portale di Azure.

**Strumenti e modelli di distribuzione**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)]

Se si vuole connettere più reti virtuali senza creare una connessione a un percorso locale, vedere [Configurare una connessione da rete virtuale a rete virtuale](vpn-gateway-vnet-vnet-rm-ps.md).

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Prima di iniziare

Prima di iniziare la configurazione, verificare di essere in possesso degli elementi seguenti.

- Un dispositivo VPN compatibile e un utente che può configurarlo. Vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md). Se non si ha familiarità con la configurazione del dispositivo VPN o con gli intervalli di indirizzi IP disponibili nella configurazione di rete locale, sarà necessario coordinarsi con qualcuno in grado di fornire tali dettagli.

- Un indirizzo IP pubblico esterno per il dispositivo VPN. L’indirizzo IP non può trovarsi dietro un NAT.
	
- Una sottoscrizione di Azure. Se non si ha già una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](http://azure.microsoft.com/pricing/free-trial/).

## Informazioni sulla configurazione


![Da sito a sito](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site2site.png)

Nella procedura seguente si creerà una rete virtuale e si aggiungeranno una subnet del gateway, un gateway, un sito locale e una connessione. Sarà anche necessario configurare il dispositivo VPN.

Quando si segue questa procedura come esercizio, è possibile usare i valori seguenti:



### <a name="values"></a>Valori di configurazione di esempio per questo esercizio

- Nome della rete virtuale: TestVNet1
- Spazio degli indirizzi: 10.11.0.0/16 e 10.12.0.0/16
- Subnet: 
	- FrontEnd: 10.11.0.0/24
	- BackEnd: 10.12.0.0/24
	- GatewaySubnet: 10.12.255.0/27
- Gruppo di risorse: TestRG1
- Location: Stati Uniti orientali
- Server DNS: 8.8.8.8
- Nome del gateway: VNet1GW
- IP pubblico: VNet1GWIP
- Tipo VPN: Basato su route
- Tipo di connessione: Da sito a sito (IPSec)
- Tipo di gateway: VPN
- Nome del gateway di rete locale: Site2
- Nome connessione: VNet1toSite2



## 1\. Crea rete virtuale 

Se è già stata creata una rete virtuale, verificare che le impostazioni siano compatibili con la progettazione del gateway VPN, prestando particolare attenzione alle eventuali subnet che si sovrappongono con altre reti. È quindi possibile passare a [Specificare un server DNS](#dns).

Se si crea una nuova rete virtuale come esercizio, è possibile fare riferimento a questi [valori](#values) durante la creazione.

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## 2\. Aggiungere un altri spazi degli indirizzi e subnet

È possibile aggiungere altri spazi degli indirizzi e subnet alla rete virtuale dopo che è stata creata.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="dns"></a>3. Specificare un server DNS

Se si crea questa configurazione come esercizio, fare riferimento a questi [valori](#values) quando si specifica il server DNS.

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## 4\. Creare una subnet del gateway

Prima di connettere la rete virtuale a un gateway, è necessario creare la subnet del gateway per la rete virtuale a cui si vuole stabilire la connessione. La subnet del gateway che si crea deve essere denominata *GatewaySubnet*, perché diversamente non funzionerà nel modo corretto.

Il prefisso della subnet del gateway per alcune configurazioni richiede una subnet con dimensioni di /28 o superiori per contenere il numero di indirizzi IP necessari nel pool. Questo significa che il prefisso della subnet del gateway deve essere /28, /27, /26 e così via. In questo caso è consigliabile creare una subnet più grande per consentire l'eventuale aggiunta di configurazioni in seguito.

Se si crea questa configurazione come esercizio, fare riferimento a questi [valori](#values) quando si crea la subnet del gateway.

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## 5\. Creare un gateway di rete virtuale

Se si crea questa configurazione come esercizio, fare riferimento a questi [valori](#values) quando si crea il gateway.

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## 6\. Creare un gateway di rete locale

Il *gateway di rete locale* fa riferimento al percorso locale. Si assegnerà un nome al gateway di rete locale a cui Azure potrà fare riferimento.

Se si crea questa configurazione come esercizio, fare riferimento a questi [valori](#values) quando si aggiunge il sito locale.

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## 7\. Configurare il dispositivo VPN

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## 8\. Una connessione VPN da sito a sito

Successivamente, verrà creata la connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN. Assicurarsi di sostituire i valori con i propri. La chiave condivisa deve corrispondere al valore utilizzato per la configurazione del dispositivo VPN.

Prima di iniziare questa sezione, verificare che la creazione del gateway di rete virtuale e del gateway di rete locale sia stata completata. Se si crea questa configurazione come esercizio, fare riferimento a questi [valori](#values) quando si crea la connessione.


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## 9\. Verificare la connessione VPN

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## Passaggi successivi

Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere il [percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) relativo alle macchine virtuali.

<!------HONumber=AcomDC_0406_2016-->