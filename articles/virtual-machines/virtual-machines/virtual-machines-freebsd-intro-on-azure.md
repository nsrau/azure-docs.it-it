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

# Introduzione a FreeBSD in Azure
Questo argomento offre una panoramica dell'esecuzione di una macchina virtuale FreeBSD in Azure.

## Overview
FreeBSD per Microsoft Azure è un avanzato sistema operativo impiegato per potenziare moderne piattaforme incorporate, server e desktop. L'immagine di FreeBSD 10.3 viene messa a disposizione da Microsoft Corporation ed è disponibile in Azure. Si basa sulla versione FreeBSD 10.3. È anche installato l'agente guest per VM di Azure [2\.1.4](https://github.com/Azure/WALinuxAgent/releases/tag/v2.1.4). L'agente è responsabile della comunicazione tra la VM FreeBSD e l'infrastruttura di Azure per operazioni quali il provisioning della VM al primo uso (nome utente, password, nome host e così via) e l'abilitazione di funzionalità per estensioni di VM selettive. Per quanto riguarda le versioni future di FreeBSD, la strategia consiste nel rimanere aggiornati e rendere disponibili le versioni più recenti subito dopo il rilascio da parte del team di progettazione di FreeBSD. La nuova versione è [FreeBSD 11](https://www.freebsd.org/releases/11.0R/schedule.html).

## Distribuzione di una macchina virtuale FreeBSD
Se si usa un'immagine presente in [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/), la distribuzione di una macchina virtuale FreeBSD è un processo estremamente semplice.

## Estensioni di VM per FreeBSD
Di seguito sono descritte le estensioni di VM supportate in FreeBSD.

### VMAccess

Con l'estensione [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) è possibile:

- Reimpostare la password dell'utente sudo originale.
- Creare un nuovo utente sudo con la password specificata.
- Impostare la chiave host pubblica con la chiave specificata.
- Reimpostare la chiave host pubblica specificata durante il provisioning di VM se non è stata indicata la chiave host.
- Aprire la porta SSH (22) e ripristinare sshd\_config se reset\_ssh è impostato su true.
- Rimuovere l'utente esistente.
- Controllare i dischi.
- Riparare un disco aggiunto.

### CustomScript

Con l'estensione [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) è possibile:

- Se disponibili, scaricare gli script personalizzati dall'archivio di Azure o dall'archivio pubblico esterno, ad esempio GitHub.
- Eseguire lo script di punto di ingresso.
- Supportare comandi inline.
- Convertire automaticamente la nuova riga stile Windows in script della shell e Python.
- Rimuovere automaticamente un carattere BOM negli script della shell e Python.
- Proteggere i dati sensibili in CommandToExecute.

## Autenticazione: nomi utente, password e chiavi SSH
Quando si crea una macchina virtuale FreeBSD usando il portale di Azure, è necessario immettere un nome utente, una password o una chiave pubblica SSH. I nomi utente per la distribuzione di una macchina virtuale FreeBSD in Azure non devono corrispondere ai nomi degli account di sistema (UID <100) già presenti nella macchina virtuale, ad esempio "root". È attualmente supportata solo la chiave RSA SSH. Una chiave SSH multiriga deve iniziare con "---- BEGIN SSH2 PUBLIC KEY ----" e terminare con "---- END SSH2 PUBLIC KEY ----".

## Ottenere privilegi utente avanzati
L'account utente specificato durante la distribuzione di istanze di macchine virtuali in Azure è un account con privilegi. Il pacchetto di sudo è stato installato nell'immagine FreeBSD pubblicata. Dopo aver eseguito l'accesso usando questo account utente, è possibile eseguire comandi come utente ROOT usando la sintassi del comando.

    # sudo <COMMAND>

Facoltativamente, è possibile ottenere una shell di root usando sudo -s.

## Passaggi successivi
- Passare ad [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) per creare una VM FreeBSD.
- Se si desidera trasferire il proprio sistema operativo FreeBSD in Azure, fare riferimento a [Creare e caricare un disco rigido virtuale con FreeBSD in Azure](../virtual-machines-linux-classic-freebsd-create-upload-vhd.md).

<!---HONumber=AcomDC_0914_2016-->