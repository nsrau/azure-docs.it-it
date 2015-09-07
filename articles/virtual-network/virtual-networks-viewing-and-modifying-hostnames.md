<properties 
   pageTitle="Visualizzazione e modifica di nomi host"
	description="Descrizione"
	services="virtual-network"
	documentationCenter="na"
	authors="joaoma"
	manager="jdial"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/25/2015"
	ms.author="joaoma"/>

# Visualizzazione e modifica di nomi host

Per consentire alle istanze di ruolo di essere collegate al nome host, è necessario impostare il valore per il nome host nel file di configurazione del servizio per ogni ruolo. A tale scopo, aggiungere il nome host desiderato all’attributo **vmName**dell’elemento**ruolo**. Il valore dell’attributo**vmName** viene utilizzato come base per il nome host di ogni istanza del ruolo. Ad esempio, se**vmName**è*webrole*e sono presenti tre istanze di tale ruolo, i nomi host delle istanze saranno*webrole0**webrole1*e*webrole2*. Non è necessario specificare un nome host per le macchine virtuali nel file di configurazione poiché il nome host per una macchina virtuale viene formulato in base al nome della macchina virtuale. Per ulteriori informazioni sulla configurazione di un servizio di Microsoft Azure, vedere[Schema di configurazione del servizio di Azure (file .cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## Visualizzazione dei nomi host

È possibile visualizzare i nomi host delle macchine virtuali e istanze del ruolo in un servizio cloud mediante vari strumenti: Portale di Azure, file di configurazione del servizio, Desktop remoto, e[API REST di gestione dei servizi Azure](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### Portale di Azure

È possibile utilizzare il portale di Azure per visualizzare i nomi host per le macchine virtuali nella pagina dashboard per la macchina virtuale. Tenere presente che il dashboard mostra un valore per**nome**e**nome Host**. Anche se inizialmente sono uguali, la modifica del nome host non modificherà il nome della macchina virtuale o istanza del ruolo.

Le istanze del ruolo possono trovarsi anche nel portale di Azure, ma quando si elencano le istanze in un servizio cloud, non viene visualizzato il nome host. Verrà visualizzato un nome per ogni istanza, ma tale nome non rappresenta il nome host.

### File di configurazione del servizio

È possibile scaricare il file di configurazione del servizio per un servizio distribuito dalla pagina **Configura**del servizio nel portale di Azure. È quindi possibile cercare il**vmName**dell’attributo per l’elemento**nome ruolo**per visualizzare il nome host. Tenere presente che questo nome host viene utilizzato come base per il nome host di ogni istanza del ruolo. Ad esempio, se**vmName**è*webrole*e sono presenti tre istanze di tale ruolo, i nomi host delle istanze saranno*webrole0**webrole1*e*webrole2*.

### Desktop remoto

Dopo aver abilitato Desktop remoto (Windows), comunicazione remota di Windows PowerShell (Windows) o connessioni SSH (Linux e Windows) per le macchine virtuali o le istanze del ruolo, è possibile visualizzare il nome host da una connessione Desktop remoto attiva in vari modi:

- Digitare il nome host nel prompt dei comandi o terminal SSH.

- Digitare ipconfig/all con il comando richiesto (solo Windows).

- Visualizzare il nome del computer nelle Impostazioni di sistema (solo Windows).

### API REST di gestione dei servizi di Azure

Da un client REST, seguire queste istruzioni:

1. Assicurarsi di disporre di un certificato client per connettersi al portale di Azure. Per ottenere un certificato client, eseguire la procedura descritta in [Procedura: informazioni sul Download e Importazione delle impostazioni di pubblicazione e Sottoscrizione] (https://msdn.microsoft.com/library/dn385850(v=nav.70).aspx).

1. Impostare una voce di intestazione denominata x-ms-version con un valore di 2013-11-01.

1. Inviare una richiesta nel seguente formato:https://management.core.windows.net/\< id-subscrition >/services/hostedservices/< nome servizio >? dettaglio incorporare = true

1. Cercare l’elemento**HostName**per ogni elemento **RoleInstance**.

>[AZURE.WARNING]È inoltre possibile visualizzare il suffisso del dominio interno per il servizio cloud dalla risposta chiamata REST controllando l’elemento**InternalDnsSuffix**, o eseguendo ipconfig/all da un prompt dei comandi in una sessione di Desktop remoto (Windows) o eseguendo cat /etc/resolv.conf da un terminale SSH (Linux).

## Modifica di un nome host

Si può modificare il nome host per una macchina virtuale o istanza del ruolo caricando un file di configurazione del servizio modificato o rinominando il computer da una sessione Desktop remoto.

## Passaggi successivi

[Risoluzione del nome (DSN)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Schema di configurazione dei servizi di Azure (file .cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Schema di configurazione di Rete virtuale di Azure)](http://go.microsoft.com/fwlink/?LinkId=248093)

[Specificare le impostazioni DNS tramite i file di configurazione di rete](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

<!---HONumber=August15_HO9-->