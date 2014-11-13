<properties urlDisplayName="Install software on VM" pageTitle="Installare software in una macchina virtuale Linux - Azure" metaKeywords="" description="Informazioni su come installare software nella macchina virtuale Linux in Azure usando CentOS/Red Hat o Ubuntu." metaCanonical="" services="virtual-machines" documentationCenter="" title="Installazione di software nella macchina virtuale Linux in Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Installazione di software nella macchina virtuale Linux in Azure

Per l'installazione di software le distribuzioni Linux usano in genere "pacchetti" software. Questi pacchetti sono solitamente gestiti mediante un set di comandi, ad esempio `apt` o `yum`. È inoltre possibile installare programmi senza un pacchetto, ad esempio con un file *tarball* del codice sorgente.

In questa sezione verrà trattato l'utilizzo di Gestione pacchetti per alcune delle distribuzioni Linux più diffuse. I passaggi saranno diversi in base alla distribuzione Linux in uso.

**Nota:** in base alla modalità di configurazione dell'ambiente, è possibile che questi comandi debbano essere eseguiti usando i privilegi radice (mediante `sudo`).

## CentOS/Red Hat

CentOS viene fornito con `yum` per la gestione dei pacchetti. Questo strumento consente di installare, disinstallare, aggiornare, elencare i pacchetti installati e altro ancora. La sintassi di questi comandi è riportata di seguito.

### Installazione

Viene effettuata l'installazione di un pacchetto, nonché di eventuali pacchetti dai quali dipende. A causa delle dipendenze, è possibile installare più di un pacchetto.

    yum install [package name]

### Disinstallazione

Viene disinstallato un pacchetto dal computer. Tenere presente che questa operazione non determinerà la rimozione delle dipendenze.

    yum remove [package name]

### Aggiornamento

Viene aggiornato un pacchetto alla versione più recente. Il pacchetto deve essere installato prima che sia possibile aggiornarlo.

    yum update [package name]

### Elenco dei pacchetti installati

Viene mostrato un elenco di tutti i pacchetti installati sul computer.

    yum list installed

## Ubuntu

Ubuntu viene fornito con `apt` (Advanced Packaging Tool) per la gestione dei pacchetti. Questo strumento consente di installare, disinstallare, aggiornare, elencare i pacchetti installati e altro ancora. La sintassi di questi comandi è riportata di seguito.

### Installazione

Viene effettuata l'installazione di un pacchetto, nonché di eventuali pacchetti dai quali dipende. A causa delle dipendenze, è possibile installare più di un pacchetto.

    apt-get install [package name]

### Disinstallazione

Viene disinstallato un pacchetto dal computer. Tenere presente che questa operazione non determinerà la rimozione delle dipendenze.

    apt-get remove [package name]

### Aggiornamento

Per effettuare l'aggiornamento a una nuova versione, è necessario usare due comandi: un comando per aggiornare l'indice dei pacchetti e un altro comando per aggiornare i pacchetti. Per aggiornare l'indice dei pacchetti, eseguire il comando seguente:

    apt-get update

Dopo aver aggiornato l'indice dei pacchetti, eseguire il comando seguente per aggiornare i pacchetti:

    apt-get upgrade
