<properties
	pageTitle="Come creare una macchina virtuale di Azure con xplat-cli"
	description="In questo argomento viene descritto come installare l'interfaccia xplat-cli su qualsiasi piattaforma, come utilizzarla per connettersi all'account Azure e come creare una macchina virtuale dall'interfaccia xplat-cli."
	services="virtual-machines"
	documentationCenter="virtual-machines"
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="command-line-interface"
	ms.workload="infrastructure-services"
	ms.date="02/20/2015"
	ms.author="rasquill"/>

# Creazione di una macchina virtuale utilizzando l'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli)
L'interfaccia xplat-cli è un ottimo modo di gestire l'infrastruttura di Azure da qualsiasi piattaforma.

Solo l'installazione di xplat-cli e la disponibilità di una sottoscrizione di Azure sono i requisiti necessari per la creazione di una macchina virtuale, pertanto è opportuno esaminare questi passaggi. Se non si dispone di un account Azure [richiederne uno gratuitamente](http://azure.microsoft.com/pricing/free-trial/).

## Installazione dell'interfaccia xplat-cli

Seguire queste istruzioni per installare l'interfaccia [xplat-cli](http://azure.microsoft.com/documentation/articles/xplat-cli/#install).

## Connessione ad Azure con xplat-cli

È possibile connettere l'installazione di xplat-cli a un account Azure personale oppure a un account Azure di lavoro o dell'istituto di istruzione. Per comprendere le differenze ed effettuare la scelta appropriata, vedere [Come connettersi alla sottoscrizione di Azure](http://azure.microsoft.com/documentation/articles/xplat-cli/#configure).

## Creazione e connessione a una macchina virtuale in Azure

La creazione di una macchina virtuale inizia con la scelta (o il caricamento) di un'immagine e l'utilizzo del comando `azure vm create`.

1. Per scegliere un'immagine dalla riga di comando, è possibile elencare le immagini disponibili della macchina virtuale utilizzando il comando `azure vm image list`. Poiché sono disponibili molte immagini, è opportuno eseguire il paging dei risultati utilizzando `more` o applicare un filtro tramite `grep` (Linux) o `findstr` (Windows). Ad esempio, se si stanno cercando immagini Ubuntu in Linux utilizzare un comando simile al seguente:

 azure vm image list | grep Ubuntu

 Questo comando comunque restituisce un lungo elenco di immagini che può essere ulteriormente ridotto in base alla versione:

 azure vm image list | grep Ubuntu-14_10

 A questo punto è possibile scegliere un'immagine e utilizzare il comando `show` per visualizzarne le proprietà in modo più dettagliato:

 azure vm image show b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-it-it-30GB

2. Creazione della macchina virtuale.

 Dopo aver scelto un'immagine di macchina virtuale, utilizzare il comando `vm create` per creare l'immagine. Questo comando dispone di molte opzioni che è possibile elencare con il comando help:

 vm create --help

 Insieme all'immagine del passaggio 1, gli argomenti chiave necessari per creare una macchina virtuale sono la località, il nome DNS e il nome utente.

 Per l'autenticazione è possibile specificare una password (nella riga di comando o in modo interattivo) o utilizzare un certificato. Se si sceglie una password, questa deve essere composta da almeno 8 caratteri, contenere lettere maiuscole e minuscole e un carattere speciale (ad esempio, uno dei seguenti: !@#$%^&+=). Se la password viene passata dalla riga di comando, è opportuno racchiudere la password tra virgolette e i caratteri speciali in sequenze di escape.

 Per scegliere una località è possibile utilizzare il comando `vm location list` per selezionare un'area vicina.

 Il nome DNS scelto deve essere univoco (ne verrà eseguito il mapping a `dnsname.cloudapp.net`) e sarà uguale al nome computer se non si specifica separatamente un nome computer nella riga di comando. 

 L'esempio di Linux riportato di seguito consente di creare una macchina virtuale negli Stati Uniti occidentali, di aprire la porta SSH predefinita 22 (l'argomento -e) e di creare un utente denominato `myadminuser`:

 azure vm create -e -l "West US" my-new-cli-vm b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-it-it-30GB "myadminuser" "myAdm1n@passwd"

## Passaggi successivi

A questo punto è possibile eseguire qualche operazione con la macchina virtuale. 

Poiché nell'esempio precedente è stata aperta la porta SSH predefinita, la connessione alla macchina virtuale, una volta accesa e in esecuzione, è diretta. Dalla riga di comando di Linux:

 ssh myadminuser@my-new-cli-vm.cloudapp.net

È possibile visualizzare altri esempi di utilizzo dell'interfaccia xplat-cli per gestire l'infrastruttura di Azure in [Strumento da riga di comando di Azure per Mac e Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-command-line-tools/)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png

<!--HONumber=47-->
