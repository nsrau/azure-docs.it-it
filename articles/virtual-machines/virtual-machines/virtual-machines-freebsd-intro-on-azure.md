<properties
   pageTitle="Introduzione a FreeBSD in Azure | Microsoft Azure"
   description="Informazioni sull'uso delle macchine virtuali FreeBSD in Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/27/2016"
   ms.author="kyliel"/>  

#Introduzione a FreeBSD in Azure
Questo argomento offre una panoramica dell'esecuzione di una macchina virtuale FreeBSD in Azure.

##Overview
FreeBSD per Microsoft Azure viene fornito da Microsoft Corporation. FreeBSD è un avanzato sistema operativo impiegato per potenziare moderne piattaforme incorporate, server e desktop. A partire da oggi, l'immagine FreeBSD 10.3 è disponibile in Azure. L'immagine è basata sulla versione FreeBSD 10.3. È inoltre installato l'agente guest per VM di Azure [2\.1.4](https://github.com/Azure/WALinuxAgent/releases/tag/v2.1.4). L'agente è responsabile della comunicazione tra la VM FreeBSD e l'infrastruttura di Azure per operazioni quali il provisioning della VM al primo utilizzo (nome utente, password, nome host e così via) e l'abilitazione di funzionalità per estensioni di VM selettive. Per quanto riguarda le versioni future di FreeBSD, la strategia consiste nel rimanere aggiornati e rendere disponibili le versioni più recenti subito dopo il rilascio da parte del team di progettazione di FreeBSD. La nuova versione è [FreeBSD 11](https://www.freebsd.org/releases/11.0R/schedule.html).

##Come distribuire una macchina virtuale FreeBSD
Se si usa un'immagine presente in [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/), la distribuzione di una macchina virtuale FreeBSD è un processo estremamente semplice.

##Estensione di VM per FreeBSD
Di seguito sono descritte le estensioni supportate nelle VM FreeBSD.

• [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)

L'estensione VMAccess è in grado di:

- Reimpostare la password dell'utente sudo originale
- Creare un nuovo utente sudo con la password specificata
- Impostare la chiave host pubblica con la chiave specificata
- Reimpostare la chiave host pubblica fornita durante il provisioning di VM se non è stata fornita la chiave host
- Aprire la porta SSH (22) e ripristinare sshd\_config se reset\_ssh è impostato su true
- Rimuovere l'utente esistente
- Controllare i dischi
- Riparare il disco aggiuntivo

• [CustomSript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript)

L'estensione CustomScript è in grado di:

- Se disponibili, scaricare gli script personalizzati dall'archivio di Azure o dall'archivio pubblico esterno (ad esempio, Github)
- Eseguire lo script di punto di ingresso
- Supportare il comando inline
- Convertire automaticamente la nuova riga stile Windows in script della shell e Python
- Rimuovere automaticamente un carattere BOM negli script della shell e Python
- Proteggere i dati sensibili in commandToExecute

##Autenticazione: nomi utente, password e chiavi SSH
Quando si crea una macchina virtuale FreeBSD usando il portale di Azure classico, viene richiesto di inserire un nome utente, una password o una chiave pubblica SSH. La scelta del nome utente per la distribuzione di una macchina virtuale FreeBSD in Azure è soggetta a un vincolo: i nomi degli account di sistema (UID <100) già presenti nella macchina virtuale, ad esempio l'account 'root', non sono consentiti. Finora è supportata solo la chiave SSH RSA. La chiave SSH multiriga deve iniziare con '---- BEGIN SSH2 PUBLIC KEY ----' e terminare con '---- END SSH2 PUBLIC KEY ----'.

##Come ottenere privilegi utente avanzato
L'account utente specificato durante la distribuzione di istanze di macchine virtuali in Azure è un account con privilegi. Il pacchetto di "sudo" è stato installato nell'immagine FreeBSD pubblicata. Dopo aver eseguito l'accesso usando questo account utente, è possibile eseguire comandi come utente ROOT usando la sintassi del comando.
  
    # sudo <COMMAND>

Facoltativamente, è possibile ottenere una shell di root usando sudo -s.

##Passaggi successivi
- Passare ad [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) per creare una VM FreeBSD.
- Se si desidera trasferire il proprio sistema operativo FreeBSD in Azure, fare riferimento a [Creare e caricare un disco rigido virtuale con FreeBSD in Azure](../virtual-machines-linux-classic-freebsd-create-upload-vhd.md).

<!---HONumber=AcomDC_0907_2016-->