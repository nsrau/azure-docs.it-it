<properties authors="kathydav" editor="tysonn" manager="timlt" /> 


#Come connettere le macchine virtuali con una rete virtuale o un servizio cloud

Le macchine virtuali devono trovarsi in un servizio cloud, che funge da contenitore e fornisce un nome DNS pubblico univoco, un indirizzo IP pubblico e un set di endpoint per accedere alla macchina virtuale su Internet. Il servizio cloud può trovarsi facoltativamente in una rete virtuale. 

Se un servizio cloud non è in una rete virtuale, le macchine virtuali in tale servizio cloud possono comunicare solo con altre macchine virtuali tramite l'utilizzo dei nomi DNS pubblici delle altre macchine virtuali e tale traffico verrà trasmesso attraverso Internet. Se un servizio cloud si trova in una rete virtuale, le macchine virtuali in tale servizio cloud possono comunicare con tutte le altre macchine virtuali nella rete virtuale senza inviare traffico su Internet.

Se si inseriscono le macchine virtuali nello stesso servizio cloud autonomo, è possibile usufruire del bilanciamento del carico e dei set di disponibilità. Per altre informazioni, vedere [Bilanciamento del carico delle macchine virtuali](../../articles/load-balance-virtual-machines/) e [Gestire la disponibilità delle macchine virtuali](../../articles/manage-availability-virtual-machines/). Tuttavia, non è possibile organizzare le macchine virtuali nelle subnet o collegare un servizio cloud autonomo alla rete locale. Di seguito è fornito un esempio.

![Virtual machines in a standalone cloud service](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)
 
Se si inseriscono le macchine virtuali in una rete virtuale, è possibile decidere quanti servizi cloud si vogliono usare per sfruttare i vantaggi comportati dal bilanciamento del carico e dai set di disponibilità. Inoltre, è possibile organizzare le macchine virtuali nelle subnet analogamente alla rete locale e connettere la rete virtuale alla rete locale. Di seguito è fornito un esempio.

![Virtual machines in a virtual network](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

Le reti virtuali sono il metodo consigliato per connettere le macchine virtuali in Azure. La procedura consigliata consiste nel configurare ogni livello dell'applicazione in un servizio cloud separato. In questo modo viene abilitata la delega dei diritti utente avanzato tramite il Controllo degli accessi in base al ruolo. Per altre informazioni, vedere [Controllo di accesso basato sui ruoli nel portale di anteprima di Azure](../../articles//role-based-access-control-configure/). Tuttavia, potrebbe essere necessario combinare le macchine virtuali da diversi livelli dell'applicazione nello stesso servizio cloud in modo da rimanere entro il numero massimo di 200 servizi cloud per ogni sottoscrizione.

Per connettere le macchine virtuali in una rete virtuale:

1.	Creare la rete virtuale nel portale di gestione di Azure. Per altre informazioni, vedere [Attività di configurazione della rete virtuale](https://msdn.microsoft.com/library/azure/jj156206.aspx).
2.	Creare il set di servizi cloud per la distribuzione nel portale di gestione di Azure per riflettere la struttura per i set di disponibilità e bilanciamento del carico con **Nuovo > Calcolo > Servizio cloud > Creazione personalizzata**.
3.	Quando si crea ogni nuova macchina virtuale, specificare il servizio cloud e la rete virtuale corretti. Se il servizio cloud è stato precedentemente associato alla rete virtuale, il relativo nome sarà già selezionato.

Di seguito è illustrato un esempio di utilizzo del portale di gestione di Azure.

![Selecting a cloud service for a virtual machine](./media/howto-connect-vm-cloud-service/VMConfig1.png)
 
Per connettere le macchine virtuali in un servizio cloud autonomo:

1.	Creare il servizio cloud per la distribuzione nel portale di gestione di Azure con **Nuovo > Calcolo > Servizio cloud > Creazione personalizzata**.
2.	Quando si crea la macchina virtuale, specificare il nome del servizio cloud creato al passaggio 1. 
In alternativa, è possibile creare il servizio cloud per la distribuzione quando si crea la prima macchina virtuale.

Di seguito è illustrato un esempio di utilizzo del portale di gestione di Azure per il servizio cloud esistente denominato EndpointTest.
 
![Add a virtual machine to an existing cloud service](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##Risorse
[Bilanciamento del carico delle macchine virtuali](../../articles/load-balance-virtual-machines/)

[Gestire la disponibilità delle macchine virtuali](../../articles/manage-availability-virtual-machines/)

[Attività di configurazione della rete virtuale](https://msdn.microsoft.com/library/azure/jj156206.aspx)

Dopo avere creato una macchina virtuale, è consigliabile aggiungere un disco dati, in modo che i servizi e i carichi di lavoro possano usarlo per archiviare i dati. Vedere uno degli articoli seguenti:

[Come collegare un disco dati a una macchina virtuale Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-attach-disk/)

[Come collegare un disco dati a una macchina virtuale Windows](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/)


<!--HONumber=45--> 
