Le macchine virtuali sono sempre disponibili in un servizio cloud, che funge da contenitore e fornisce un nome DNS pubblico univoco, un indirizzo IP pubblico e un set di endpoint per accedere alla macchina virtuale su Internet. Il servizio cloud può trovarsi facoltativamente in una rete virtuale.

Se un servizio cloud non si trova in una rete virtuale, è detto servizio cloud *autonomo*. Le macchine virtuali in tale servizio cloud possono comunicare solo con altre macchine virtuali usando i nomi DNS pubblici delle altre macchine virtuali e il traffico verrà trasmesso attraverso Internet. Se un servizio cloud si trova in una rete virtuale, le macchine virtuali in tale servizio cloud possono comunicare con tutte le altre macchine virtuali nella rete virtuale senza inviare traffico su Internet.

Se si inseriscono le macchine virtuali nello stesso servizio cloud autonomo, si potranno continuare a usare il bilanciamento del carico e i set di disponibilità. Per altre informazioni, vedere [Bilanciamento del carico delle macchine virtuali](../articles/load-balance-virtual-machines.md) e [Gestire la disponibilità delle macchine virtuali](../articles/manage-availability-virtual-machines.md). Tuttavia, non è possibile organizzare le macchine virtuali nelle subnet o connettere un servizio cloud autonomo alla rete locale. Di seguito è fornito un esempio.

![Macchine virtuali in un servizio cloud autonomo](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)
 
Se si inseriscono le macchine virtuali in una rete virtuale, è possibile decidere quanti servizi cloud usare per il bilanciamento del carico e i set di disponibilità. Inoltre, è possibile organizzare le macchine virtuali nelle subnet analogamente alla rete locale e connettere la rete virtuale alla rete locale. Di seguito è fornito un esempio.

![Macchine virtuali in una rete virtuale](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

Le reti virtuali sono il metodo consigliato per connettere le macchine virtuali in Azure. La procedura consigliata consiste nel configurare ogni livello dell'applicazione in un servizio cloud separato. In questo modo viene abilitata la delega dei diritti utente avanzata tramite il Controllo degli accessi in base al ruolo. Per altre informazioni, vedere [Controllo di accesso basato sui ruoli nel portale di anteprima di Azure](../articles/role-based-access-control-configure.md). Tuttavia, potrebbe essere necessario combinare le macchine virtuali da diversi livelli dell'applicazione nello stesso servizio cloud in modo da rimanere entro il numero massimo di 200 servizi cloud per ogni sottoscrizione.

## Connettere le macchine virtuali in una rete virtuale

Per connettere le macchine virtuali in una rete virtuale:

1.	Creare la rete virtuale nel portale di gestione di Azure. Per altre informazioni, vedere [Attività di configurazione della rete virtuale](https://msdn.microsoft.com/library/azure/jj156206.aspx).
2.	Creare il set di servizi cloud per la distribuzione nel portale di gestione di Azure per riflettere la struttura per i set di disponibilità e bilanciamento del carico con **Nuovo > Calcolo > Servizio cloud > Creazione personalizzata**.
3.	Quando si crea ogni nuova macchina virtuale, specificare il servizio cloud e la rete virtuale corretti. Se il servizio cloud è stato precedentemente associato alla rete virtuale, il relativo nome sarà già selezionato.

Di seguito è illustrato un esempio di uso del portale di gestione di Azure.

![Selezione di un servizio cloud per una macchina virtuale](./media/howto-connect-vm-cloud-service/VMConfig1.png)

## Connettere le macchine virtuali in un servizio cloud autonomo
 
Per connettere le macchine virtuali in un servizio cloud autonomo:

1.	Creare il servizio cloud per la distribuzione nel portale di gestione di Azure. Fare clic su **Nuovo > Calcolo > Servizio cloud > Creazione personalizzata**.
2.	Quando si crea la macchina virtuale, specificare il nome del servizio cloud creato nel passaggio precedente. In alternativa, è possibile creare il servizio cloud per la distribuzione quando si crea la prima macchina virtuale.

Di seguito è illustrato un esempio di uso del portale di gestione di Azure per il servizio cloud esistente denominato EndpointTest.
 
![Aggiungere una macchina virtuale a un servizio cloud esistente](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##Risorse
[Bilanciamento del carico delle macchine virtuali](../articles/load-balance-virtual-machines.md)

[Gestione della disponibilità delle macchine virtuali](../articles/manage-availability-virtual-machines.md)

[Attività di configurazione della rete virtuale](https://msdn.microsoft.com/library/azure/jj156206.aspx)

Dopo avere creato una macchina virtuale, è consigliabile aggiungere un disco dati, in modo che i servizi e i carichi di lavoro possano usarlo per archiviare i dati. Vedere uno degli articoli seguenti:

[Come collegare un disco dati a una macchina virtuale Linux](../articles/virtual-machines/virtual-machines-linux-how-to-attach-disk.md)

[Come collegare un disco dati a una macchina virtuale Windows](../articles/virtual-machines/storage-windows-attach-disk.md)

<!---HONumber=July15_HO1-->