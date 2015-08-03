<properties
   pageTitle="Come creare una macchina virtuale di Azure con l’interfaccia della riga di comando di Azure | Microsoft Azure"
   description="In questo argomento viene descritto come installare l'interfaccia della riga di comando di Azure su qualsiasi piattaforma, come utilizzarla per connettersi all'account Azure e come creare una macchina virtuale dall'interfaccia della riga di comando di Azure."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="dlepow"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure-services"
   ms.date="06/09/2015"
   ms.author="danlep"/>

# Creazione di una macchina virtuale utilizzando l’interfaccia della riga di comando di Azure (Azure CLI)
L'interfaccia della riga di comando di Azure è un ottimo modo di gestire l'infrastruttura di Azure da qualsiasi piattaforma.

Solo l'installazione dell’interfaccia della riga di comando di Azure e la disponibilità di una sottoscrizione di Azure sono i requisiti necessari per la creazione immediata di una macchina virtuale, pertanto è opportuno esaminare questi passaggi. Se non si dispone di un account Azure [richiederne uno gratuitamente](http://azure.microsoft.com/pricing/free-trial/).

## Installazione dell'interfaccia della riga di comando di Azure

Seguire le istruzioni per l’[installazione dell’interfaccia della riga di comando di Azure](../xplat-cli.md#install).

## Connessione ad Azure utilizzando l’interfaccia della riga di comando di Azure

È possibile connettere l'installazione dell’interfaccia della riga di comando di Azure a un account Azure personale oppure a un account Azure di lavoro o dell'istituto di istruzione. Per comprendere le differenze ed effettuare la scelta appropriata, vedere [Come connettersi alla sottoscrizione di Azure](../xplat-cli.md#configure).

## Creazione e connessione a una macchina virtuale in Azure

La creazione di una macchina virtuale inizia con la scelta (o il caricamento) di un'immagine e l'utilizzo del comando `azure vm create`.

1. Per scegliere un'immagine dalla riga di comando, è possibile elencare le immagini della macchina virtuale disponibili utilizzando il comando `azure vm image list`. Poiché sono disponibili molte immagini, è opportuno eseguire il paging dei risultati utilizzando `more` o applicando un filtro tramite `grep` (Linux) o `findstr` (Windows). Ad esempio, se si stanno cercando immagini Ubuntu su Linux utilizzare un comando simile al seguente:

        azure vm image list | grep Ubuntu

    Questo comando comunque restituisce un lungo elenco di immagini che può essere ulteriormente ridotto in base alla versione:

        azure vm image list | grep Ubuntu-14_10

    A questo punto è possibile scegliere un'immagine e utilizzare il comando `show` per visualizzarne le proprietà in modo più dettagliato:

        azure vm image show b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-it-it-30GB

2. Dopo aver scelto un'immagine di macchina virtuale, utilizzare il comando `vm create` per creare l'immagine. Questo comando dispone di molte opzioni, che è possibile elencare utilizzando il comando `help`:

        vm create --help

    Insieme all'immagine del passaggio 1, gli argomenti chiave necessari per creare una macchina virtuale sono la posizione, il nome DNS e il nome utente.

    Per l'autenticazione è possibile specificare una password (nella riga di comando o in modo interattivo) o utilizzare un certificato. Se si sceglie una password, deve essere composta da almeno 8 caratteri, contenere lettere maiuscole e minuscole e un carattere speciale (ad esempio, uno dei seguenti: !@#$%^&+=). Se la password viene passata dalla riga di comando, è opportuno racchiudere la password tra virgolette e i caratteri speciali in sequenze di escape.

    Per scegliere una posizione, è possibile utilizzare il comando `vm location list` per selezionare un'area vicina.

  Il nome DNS scelto deve essere univoco (ne verrà eseguito il mapping a `dnsname.cloudapp.net`) e sarà uguale al nome computer se non si specifica separatamente un nome computer nella riga di comando.

    The Linux example below creates a VM in West US, opens the default SSH port 22 (the -e argument), and creates a user called `myadminuser`:

        azure vm create -e -l "West US"  my-new-cli-vm b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-it-it-30GB "myadminuser" "myAdm1n@passwd"

## Passaggi successivi

A questo punto è possibile eseguire qualche operazione con la macchina virtuale.

Poiché nell'esempio precedente è stata aperta la porta SSH predefinita, la connessione alla macchina virtuale, una volta accesa e in esecuzione, è diretta. Dalla riga di comando di Linux:

    ssh myadminuser@my-new-cli-vm.cloudapp.net

È possibile visualizzare ulteriori esempi di utilizzo dell'interfaccia della riga di comando di Azure per gestire l'infrastruttura di Azure nella [pagina di riferimento dei comandi dell’interfaccia della riga di comando di Azure](../virtual-machines-command-line-tools.md).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png

<!---HONumber=July15_HO4-->