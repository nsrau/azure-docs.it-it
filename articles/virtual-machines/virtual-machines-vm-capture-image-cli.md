<properties
	pageTitle="Acquisire l'immagine di una macchina virtuale che esegue Linux utilizzando l'interfaccia CLI"
	description="Informazioni su come acquisire un'immagine di una macchina virtuale di Azure che esegue Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="karthmut"
	manager="madhana"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="karthmut"/>




# Come acquisire una macchina virtuale Linux da usare come modello con l'interfaccia della riga di comando##



Questo articolo illustra come acquisire una macchina virtuale di Azure che esegue Linux in modo da usarla come modello per creare altre macchine virtuali. Questo modello include il disco del sistema operativo e gli eventuali dischi dati connessi alla macchina virtuale. Poiché la configurazione di rete non è inclusa, è necessario definirla quando si creano le altre macchine virtuali che usano il modello.



In Azure questo modello viene trattato come un'immagine e viene archiviato nell'elenco delle immagini. che è anche la posizione in cui vengono archiviate le immagini caricate. Per altre informazioni sulle immagini, vedere [Informazioni sulle immagini di macchine virtuali in Azure][].



##Prima di iniziare##



Questa procedura presuppone che sia stata creata una macchina virtuale di Azure e che sia stato configurato il sistema operativo, inclusi gli eventuali dischi dati connessi. Se non si sono ancora effettuate queste operazioni, vedere le istruzioni seguenti:



- [Come creare una macchina virtuale personalizzata][]

- [Come collegare un disco dati a una macchina virtuale][]



##Acquisizione della macchina virtuale##



1. Connettersi alla macchina virtuale. Per informazioni dettagliate, vedere [Come accedere a una macchina virtuale che esegue Linux][].



2. La macchina virtuale può essere acquisita solo se lo stato è **StoppedDeallocated**. Nella finestra di SSH digitare il comando seguente per arrestare la macchina virtuale:



        vm shutdown [options] <vm-name>



    Si noti che una delle opzioni per `vm shutdown` è `-p`, che mantiene la risorsa di calcolo al momento dell'arresto. **Non** abilitare questa opzione in quanto la macchina virtuale deve essere sottoposta al deprovisioning per poter essere acquisita.



3. Eseguire il comando seguente per acquisire l'immagine della macchina virtuale:



        vm capture <vm-name> <target-image-name> --deleted



    Per acquisire l'immagine, è necessario eliminare la macchina virtuale. È possibile utilizzare `--deleted` o `-t` a tale scopo.



4. È possibile verificare che l'immagine è stata acquisita controllando l'elenco delle immagini della macchina virtuale:



        vm image list | grep <target-image-name>



    La parte `| grep <target-image-name>` è facoltativa, ma consente di trovare più facilmente l'immagine nell'elenco.



Di seguito è riportato un esempio di procedura dettagliata di acquisizione di una macchina virtuale, incluso l'output terminale:


    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status            Location  DNS Name                  IP Address

    data:    -----------  ----------------  --------  ------------------------  ------------

    data:    kmorig       RoleStateUnknown  West US   kmorig.cloudapp.net       100.92.56.16

    info:    vm list command OK

    ~$ azure vm shutdown kmorig

    info:    Executing command vm shutdown

    + Getting virtual machines

    + Shutting down VM

    info:    vm shutdown command OK

    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status              Location  DNS Name                  IP Address

    data:    -----------  ------------------  --------  ------------------------  ----------

    data:    kmorig       StoppedDeallocated  West US   kmorig.cloudapp.net

    info:    vm list command OK

    ~$ azure vm capture kmorig kmcaptured --deleted

    info:    Executing command vm capture

    + Getting virtual machines

    + Checking image with name kmcaptured exists

    + Capturing VM

    info:    vm capture command OK

    ~$ azure vm image list | grep kmcaptured

    data:    kmcaptured



Per ulteriori informazioni e comandi aggiuntivi, visitare la [pagina della documentazione di CLI di Azure][].


[pagina della documentazione di CLI di Azure]: ../virtual-machines-command-line-tools.md

[Come accedere a una macchina virtuale che esegue Linux]: virtual-machines-linux-how-to-log-on.md

[Informazioni sulle immagini di macchine virtuali in Azure]: http://msdn.microsoft.com/library/azure/dn790290.aspx

[Come creare una macchina virtuale personalizzata]: virtual-machines-create-custom.md

[Come collegare un disco dati a una macchina virtuale]: storage-windows-attach-disk.md

<!---HONumber=58--> 