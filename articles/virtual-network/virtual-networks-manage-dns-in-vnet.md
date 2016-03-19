<properties 
   pageTitle="Gestire server DNS usati da una rete virtuale"
   description="Informazioni su come aggiungere e rimuovere server DNS in una rete virtuale"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Gestire server DNS usati da una rete virtuale

È possibile gestire l'elenco dei server DNS usati in una rete virtuale nel portale di gestione o nel file di configurazione di rete. È possibile aggiungere fino a 12 server DNS per ogni rete virtuale. Quando si specificano i server DNS, è importante verificare che siano elencati nell'ordine corretto per l'ambiente. Gli elenchi dei server DNS non supportano il round robin. Vengono usati nell'ordine in cui sono specificati. Se è possibile raggiungere il primo server DNS nell'elenco, il client userà tale server DNS indipendentemente dal fatto che funzioni correttamente o meno. Per modificare l'ordine dei server DNS per una rete virtuale, rimuovere i server DNS dall'elenco e aggiungerli nuovamente nell'ordine desiderato.

>[AZURE.WARNING]Dopo aver aggiornato l'elenco di server DNS, è necessario riavviare le macchine virtuali presenti nella rete virtuale affinché possano acquisire le nuove impostazioni dei server DNS. Le macchine virtuali continueranno a usare la configurazione corrente fino a quando non vengono riavviate.

## Modificare un elenco di server DNS per una rete virtuale tramite il portale di gestione

1. Accedere al **portale di gestione**.

1. Nel pannello di navigazione fare clic su **Reti** e quindi sul nome della macchina virtuale nella colonna **Nome**.

1. Fare clic su **Configura**.

1. In **Server DNS** è possibile configurare quanto segue:

	- **Per registrare (aggiungere) un nuovo server DNS**: digitare semplicemente il nome e l'indirizzo IP nelle caselle. In questo modo viene aggiunto un server DNS all'elenco dei server DNS della rete virtuale e tale server viene registrato in Azure.

	- **Per aggiungere un server DNS precedentemente registrato**: se è stato già registrato un server DNS in Azure, è possibile selezionarlo dall'elenco prepopolato.

	- **Per rimuovere un server DNS dalla rete virtuale**: fare clic sulla X accanto al server da rimuovere. Si noti che in questo modo il server viene rimosso solo dall'elenco di questa rete virtuale. Il server DNS resta registrato in Azure in modo che venga usato dalle altre reti virtuali. Per eliminare un server DNS dalla sottoscrizione, passare alla pagina **Reti ->Server DNS**.

	- **Per riordinare i server DNS**: rimuovere tutti i server DNS elencati e quindi aggiungerli di nuovo nell'ordine desiderato. Tenere presente che non si tratta di un elenco DNS round robin.

	- **Per rinominare un server DNS**: evidenziare il server DNS nell'elenco e quindi digitare il nuovo nome. In questo modo verrà registrato un nuovo server DNS in Azure e tale server verrà aggiunto all'elenco di server DNS della rete virtuale. Il server DNS precedente e il relativo indirizzo IP continueranno a essere registrati in Azure. È possibile eliminarlo nella pagina **Server DNS** se non viene usato per altre reti virtuali.

1. Fare clic su **Salva** nella parte inferiore della pagina per salvare la nuova configurazione di server DNS.

1. Riavviare le macchine virtuali contenute nella rete virtuale in modo che possano acquisire le nuove impostazioni DNS.

## Modificare un elenco di server DNS tramite un file di configurazione di rete

Per modificare un elenco di server DNS usando un file di configurazione di rete, è necessario prima esportare le impostazioni di configurazione dal portale di gestione. Modificare quindi il file di configurazione di rete e reimportarlo infine tramite il portale di gestione. Segue un elenco di carattere generale dei passaggi per il completamento del processo.

1. Esportare le impostazioni della rete virtuale in un file di configurazione di rete. Per altre informazioni e per i passaggi per l'esportazione delle impostazioni della configurazione di rete, vedere [Esportare le impostazioni della rete virtuale in un file di configurazione di rete](virtual-networks-using-network-configuration-file.md).

1. Specificare le informazioni del server DNS per la rete virtuale. Per altre informazioni su come specificare un server DNS, vedere [Specifica delle impostazioni DNS in un file di configurazione della rete virtuale](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md). Per altre informazioni sui file di configurazione di rete, vedere [Schema di configurazione della rete virtuale di Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) e [Configurare una rete virtuale utilizzando un file di configurazione di rete](virtual-networks-using-network-configuration-file.md).

1. Importare il file di configurazione di rete. Per altre informazioni e per i passaggi relativi all'importazione del file di configurazione di rete, vedere [Importare un file di configurazione di rete](virtual-networks-using-network-configuration-file.md).

1. Riavviare le macchine virtuali contenute nella rete virtuale in modo che possano acquisire le nuove impostazioni DNS.

## Passaggi successivi

[Come gestire le proprietà della rete virtuale (VNet)](../virtual-networks-settings)

[Come usare gli indirizzi IP pubblici in una rete virtuale](../virtual-networks-public-ip-within-vnet)

[Come eliminare una rete virtuale (VNet)](../virtual-networks-delete-vnet)

<!---HONumber=AcomDC_1217_2015-->