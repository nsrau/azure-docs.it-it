<properties 
   pageTitle="Specificare le impostazioni DNS in un File di configurazione della Rete virtuale | Microsoft Azure"
   description="Come modificare le impostazioni del server DNS in una rete virtuale utilizzando un file di configurazione di rete virtuale"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/25/2015"
   ms.author="joaoma" />

# Indicazione delle impostazioni DNS in un file di configurazione di rete virtuale

Un file di configurazione di rete dispone di due elementi che è possibile utilizzare per specificare le impostazioni Domain Name System (DNS):**DnsServers**e**DnsServerRef**. È possibile aggiungere un elenco dei server DNS specificando gli indirizzi IP e nomi di riferimento all’elemento **DnsServers**. È quindi possibile utilizzare un elemento**DnsServerRef**per specificare le voci del server DNS che vengono utilizzate per siti di rete diversi all'interno della rete virtuale.

>[AZURE.IMPORTANT] Per altre informazioni sull'utilizzo del file di configurazione di rete, vedere [Configurare una rete virtuale usando un file di configurazione di rete](virtual-networks-using-network-configuration-file.md). Per informazioni sulle impostazioni specifiche contenute in un file di configurazione di rete, vedere [Schema di configurazione di rete virtuale Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Il file di configurazione di rete può contenere i seguenti elementi. Il titolo di ogni elemento è collegato a una pagina che fornisce informazioni aggiuntive sulle impostazioni del valore dell'elemento.

[Elemento DNS](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

>[AZURE.WARNING] Il**nome**dell’attributo nell’elemento **DnsServer**viene utilizzato solo come riferimento per l’elemento**DnsServerRef**. Non rappresenta il nome host per il server DNS. Ogni valore dell’attributo**DnsServer**deve essere univoco nell'intera sottoscrizione Microsoft Azure

[Elemento siti di rete virtuale](http://go.microsoft.com/fwlink/?LinkId=248093)

	<DnsServersRef>
	  <DnsServerRef name="ID1" />
	  <DnsServerRef name="ID2" />
	  <DnsServerRef name="ID3" />
	</DnsServersRef>

>[AZURE.NOTE] Per specificare questa impostazione per l'elemento siti di rete virtuale, esso deve essere definito in precedenza nell'elemento DNS. Il *nome*del DnsServerRef nell’elemento siti di rete virtuale deve fare riferimento a un valore nome specificato nell'elemento DNS per*nome* DnsServer.

## Passaggi successivi

[Configurare una rete virtuale usando file di configurazione di rete](virtual-networks-using-network-configuration-file.md)

[Attività di configurazione di Rete virtuale di Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Schema di configurazione dei servizi di Azure](https://msdn.microsoft.com/library/windowsazure/ee758710)

<!---HONumber=AcomDC_0204_2016-->