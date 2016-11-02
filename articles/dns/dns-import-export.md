<properties
   pageTitle="Importare ed esportare un file di zona del dominio in DNS di Azure con l'interfaccia della riga di comando | Microsoft Azure"
   description="Informazioni su come importare ed esportare un file di zona DNS in DNS di Azure con l'interfaccia della riga di comando di Azure"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# Importare ed esportare un file di zona DNS usando l'interfaccia della riga di comando di Azure


Questo articolo illustrerà come importare ed esportare file di zona DNS per il servizio DNS di Azure usando l'interfaccia della riga di comando di Azure.

## Introduzione alla migrazione di zone DNS

Un file di zona DNS è un file di testo che contiene i dettagli di tutti i record DNS (Domain Name System) nella zona. Segue un formato standard, ideale per il trasferimento dei record DNS tra sistemi DNS. L'uso di un file di zona è un modo rapido, affidabile e pratico per trasferire una zona DNS all'interno o all'esterno di DNS di Azure.

DNS di Azure supporta l'importazione e l'esportazione di file di zona tramite l'interfaccia della riga di comando di Azure. L'interfaccia della riga di comando di Azure è uno strumento da riga di comando multipiattaforma usato per la gestione dei servizi di Azure. È disponibile per le piattaforme Windows, Mac e Linux nella [pagina di download di Azure](https://azure.microsoft.com/downloads/). Il supporto multipiattaforma è particolarmente importante per l'importazione e l'esportazione di file di zona, perché il software Domain Name Server più comune, [BIND](https://www.isc.org/downloads/bind/), viene eseguito in genere in Linux.

## Recupero del file di zona DNS esistente

Prima di importare il file di zona DNS in DNS di Azure, è necessario ottenere una copia del file di zona. L'origine di questo file dipende dalla posizione in cui è attualmente ospitata la zona DNS.

- Se la zona DNS è ospitata da un servizio partner, ad esempio un registrar, un provider di servizi di hosting DNS dedicato o un provider di servizi cloud alternativo, tale servizio deve fornire la possibilità di scaricare il file di zona DNS.

- Se la zona DNS è ospitata nel DNS di Windows, la cartella predefinita per i file di zona è **%systemroot%\\system32\\dns**. Il percorso completo di ogni file di zona è anche visualizzato nella scheda **Generale** della console di gestione del servizio DNS.

- Se la zona DNS è ospitata con BIND, il percorso del file di zona per ogni zona è specificato nel file di configurazione di BIND **named.conf**.

**Uso dei file di zona da GoDaddy**<BR> I file di zona scaricati da GoDaddy hanno un formato leggermente non standard. È necessario correggerlo prima di importare questi file di zona in DNS di Azure. I nomi DNS in RData di ogni record DNS sono specificati come nomi completi, ma non hanno un "." di terminazione. Ciò significa che vengono interpretati da altri sistemi DNS come nomi relativi. È necessario modificare il file di zona aggiungendo il '.' di terminazione ai relativi nomi prima di importarli in DNS di Azure.

## Importare un file di zona DNS in DNS di Azure


L'importazione di un file di zona crea una nuova zona in DNS di Azure, se non ne esiste già una. Se la zona esiste già, i set di record nel file di zona devono essere uniti a quelli esistenti.

### Unione

- Per impostazione predefinita, i set di record nuovi ed esistenti vengono uniti. I record identici all'interno di un set di record unito vengono deduplicati.

- In alternativa, se si specifica l'opzione `--force`, il processo di importazione sostituisce i set di record esistenti con quelli nuovi. I set di record esistenti che non hanno un set di record corrispondente nel file di zona importato non vengono rimossi.

- Quando i set di record sono uniti, viene usato il valore di durata (TTL) dei set di record preesistenti. Se si usa `--force`, viene applicato il valore TTL del nuovo set di record.

- I parametri di origine di autorità (SOA), tranne `host`, vengono sempre rilevati dal file di zona importato, indipendentemente dall'uso di `--force`. Analogamente, per il set di record del server di nomi al vertice della zona viene sempre applicato il valore TTL dal file di zona importato.

- Un record CNAME importato non sostituisce un record CNAME esistente con lo stesso nome, a meno di specificare il parametro `--force`.

- Quando si verifica un conflitto tra un record CNAME e a un altro record con lo stesso nome, ma di tipo diverso, indipendentemente dal fatto che sia nuovo o esistente viene mantenuto il record esistente. È indipendente dall'uso di `--force`.

### Altre informazioni sull'importazione

Le note seguenti forniscono altri dettagli tecnici sul processo di importazione di zone.

- La direttiva `$TTL` è facoltativa e supportata. Se non è specificata una direttiva `$TTL`, i record senza una durata (TTL) esplicita saranno importati con impostato un valore TTL predefinito di 3600 secondi. Quando per due record nello stesso set di record sono specificati TTL diversi, viene usato il valore più basso.

- La direttiva `$ORIGIN` è facoltativa e supportata. Se `$ORIGIN` non è impostata, il valore predefinito usato è il nome della zona specificato nella riga di comando, con "." di terminazione.

- Le direttive `$INCLUDE` e `$GENERATE` non sono supportate.

- Sono supportati questi tipi di record: A, AAAA, CNAME, MX, NS, SOA, SRV e TXT.

- Il record SOA viene creato automaticamente da DNS di Azure quando si crea una zona. Quando si importa un file di zona, tutti i parametri SOA vengono rilevati dal file di zona *tranne* il parametro `host`. Questo parametro usa il valore fornito da DNS di Azure. Questo parametro deve infatti fare riferimento al server dei nomi primario fornito da DNS di Azure.

- Anche il set di record del server dei nomi al vertice della zona viene creato automaticamente da DNS di Azure quando si crea la zona. Viene importata solo la durata TTL di questo set di record. Questi record contengono i nomi dei server dei nomi forniti da DNS di Azure. I dati dei record non vengono sovrascritti dai valori contenuti nel file di zona importato.

- Durante l'anteprima pubblica, DNS di Azure supporta solo i record TXT a stringa singola. I record TXT multistringa saranno concatenati e troncati a 255 caratteri.

### Valori e formato dell'interfaccia della riga di comando


Il formato del comando dell'interfaccia della riga di comando di Azure per importare una zona DNS è:<BR>`azure network dns zone import [options] <resource group> <zone name> <zone file name>`

Valori:

- `<resource group>` è il nome del gruppo di risorse per la zona in DNS di Azure.
- `<zone name>` è il nome della zona.
- `<zone file name>` è il nome/percorso del file di zona da importare.

Se nel gruppo di risorse non esiste una zona con questo nome, verrà creata automaticamente. Se la zona esiste già, i set di record importati saranno uniti a quelli esistenti. Per sovrascrivere i set di record esistenti, usare l'opzione `--force`.

Per verificare il formato di un file di zona senza eseguirne effettivamente l'importazione, usare l'opzione `--parse-only`.

### Passaggio 1. Importare un file di zona

Per importare un file di zona per la zona **contoso.com**:

1. Accedere alla sottoscrizione di Azure con l'interfaccia della riga di comando di Azure.

		azure login

2. Selezionare la sottoscrizione in cui si vuole creare la nuova zona DNS.

		azure account set <subscription name>

3. DNS di Azure è un servizio solo di Gestione risorse di Azure, quindi nell'interfaccia della riga di comando di Azure deve essere impostata la modalità Gestione risorse.

		azure config mode arm

4. Prima di usare il servizio DNS di Azure, è necessario registrare la sottoscrizione per l'uso del provider di risorse Microsoft.Network. Questa operazione viene eseguita una sola volta per ogni sottoscrizione.

		azure provider register Microsoft.Network

5. È anche necessario creare un gruppo di risorse di Gestione risorse, se non ne è già disponibile uno.

		azure group create myresourcegroup westeurope

6. Per importare la zona **contoso.com** dal file **contoso.com.txt** in una nuova zona DNS nel gruppo di risorse **myresourcegroup**, eseguire il comando `azure network dns zone import`.<BR>Questo comando consente di caricare il file di zona e analizzarlo. Il comando esegue una serie di comandi nel servizio DNS di Azure per creare la zona e tutti i relativi set di record. Segnala anche lo stato di avanzamento nella finestra della console, nonché gli eventuali errori o avvisi. Poiché i set di record vengono creati in serie, l'importazione di un file di zona di grandi dimensioni può richiedere alcuni minuti.

		azure network dns zone import myresourcegroup contoso.com contoso.com.txt



### Passaggio 2. Verificare la zona

Per verificare la zona DNS dopo aver importato il file, è possibile usare uno dei metodi seguenti:

- È possibile elencare i record con il comando dell'interfaccia della riga di comando di Azure seguente.

		azure network dns record-set list myresourcegroup contoso.com

- È possibile elencare i record usando il cmdlet PowerShell `Get-AzureRmDnsRecordSet`.

- Per verificare la risoluzione dei nomi per i record, è possibile usare `nslookup`. Dal momento che la zona non è ancora delegata, si dovranno specificare in modo esplicito i server dei nomi DNS di Azure corretti. L'esempio seguente mostra come recuperare i nomi dei server dei nomi assegnati alla zona. Illustra anche come eseguire una query sul record "www" con `nslookup`.

    	C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
    	info:Executing command network dns record-set show
    	+ Looking up the DNS Record Set "@" of type "NS"
    	data:Id: /subscriptions/…/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
    	data:Name: @
    	data:Type: Microsoft.Network/dnszones/NS
    	data:Location: global
    	data:TTL : 3600
    	data:NS records
    	data:Name server domain name : ns1-01.azure-dns.com
    	data:Name server domain name : ns2-01.azure-dns.net
    	data:Name server domain name : ns3-01.azure-dns.org
    	data:Name server domain name : ns4-01.azure-dns.info
    	data:
    	info:network dns record-set show command OK
    
    	C:\> nslookup www.contoso.com ns1-01.azure-dns.com
    
    	Server: ns1-01.azure-dns.com
    	Address:  40.90.4.1
    
    	Name:www.contoso.com
    	Addresses:  134.170.185.46
    	134.170.188.221

### Passaggio 3. Aggiornare la delega DNS

Dopo avere verificato che la zona sia stata importata correttamente, si dovrà aggiornare la delega DNS in modo che punti ai server dei nomi DNS di Azure. Per altre informazioni, vedere l'articolo [Aggiornare la delega DNS](dns-domain-delegation.md).

## Esportare un file di zona DNS da DNS di Azure

Il formato del comando dell'interfaccia della riga di comando di Azure per importare una zona DNS è:<BR>`azure network dns zone export [options] <resource group> <zone name> <zone file name>`

Valori:

- `<resource group>` è il nome del gruppo di risorse per la zona in DNS di Azure.
- `<zone name>` è il nome della zona.
- `<zone file name>` è il nome/percorso del file di zona da esportare.

Come per l'importazione di una zona, è necessario prima di tutto accedere, scegliere la sottoscrizione e configurare l'interfaccia della riga di comando di Azure per l'uso della modalità Resource Manager.

### Per esportare un file di zona:


1. Accedere alla sottoscrizione di Azure con l'interfaccia della riga di comando di Azure.

		azure login

2. Selezionare la sottoscrizione in cui si vuole creare la nuova zona DNS.

		azure account set <subscription name>

3. DNS di Azure è un servizio solo di Gestione risorse di Azure. L'interfaccia della riga di comando di Azure deve essere impostata sulla modalità Gestione risorse.

		azure config mode arm

4. Per esportare la zona **contoso.com** di DNS di Azure esistente nel gruppo di risorse **myresourcegroup** nel file **contoso.com.txt**, nella cartella corrente, eseguire `azure network dns zone export`. Questo comando chiama il servizio DNS di Azure per enumerare i set di record nella zona ed esportare i risultati in un file di zona compatibile con BIND.

		azure network dns zone export myresourcegroup contoso.com contoso.com.txt

<!---HONumber=AcomDC_0817_2016-->