Le macchine virtuali create con il modello di distribuzione del Servizio di gestione vengono sempre inserite in un servizio cloud. Il servizio cloud funge da contenitore e fornisce un nome DNS pubblico univoco, un indirizzo IP pubblico e un set di endpoint per accedere alla macchina virtuale su Internet. Il servizio cloud può essere in una rete virtuale, ma questo non è un requisito.

Se un servizio cloud non si trova in una rete virtuale, è detto servizio cloud *autonomo*. Le macchine virtuali in un servizio cloud autonomo possono comunicare solo con altre macchine virtuali usando i nomi DNS pubblici delle altre macchine virtuali e il traffico verrà trasmesso attraverso Internet. Se un servizio cloud si trova in una rete virtuale, le macchine virtuali in tale servizio cloud possono comunicare con tutte le altre macchine virtuali nella rete virtuale senza inviare traffico su Internet.

Se si inseriscono le macchine virtuali nello stesso servizio cloud autonomo, si potranno continuare a usare il bilanciamento del carico e i set di disponibilità. Per altre informazioni, vedere [Bilanciamento del carico delle macchine virtuali](../articles/load-balance-virtual-machines.md) e [Gestire la disponibilità delle macchine virtuali](../articles/manage-availability-virtual-machines.md). Tuttavia, non è possibile organizzare le macchine virtuali nelle subnet o connettere un servizio cloud autonomo alla rete locale. Ad esempio:

![Macchine virtuali in un servizio cloud autonomo](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)

Se si inseriscono le macchine virtuali in una rete virtuale, è possibile decidere quanti servizi cloud usare per il bilanciamento del carico e i set di disponibilità. Inoltre, è possibile organizzare le macchine virtuali nelle subnet analogamente alla rete locale e connettere la rete virtuale alla rete locale. Ad esempio:

![Macchine virtuali in una rete virtuale](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

Le reti virtuali sono il metodo consigliato per connettere le macchine virtuali in Azure. La procedura consigliata consiste nel configurare ogni livello dell'applicazione in un servizio cloud separato. Tuttavia, potrebbe essere necessario combinare le macchine virtuali da diversi livelli dell'applicazione nello stesso servizio cloud in modo da rimanere entro il numero massimo di 200 servizi cloud per ogni sottoscrizione. Per esaminare questo e altri limiti, vedere[sottoscrizione Azure e limiti, quote e vincoli del servizio](../azure-subscription-service-limits.md).

## Connettere le macchine virtuali in una rete virtuale

Per connettere le macchine virtuali in una rete virtuale:

1.	Creare la rete virtuale nel [portale di Azure](http://manage.windowsazure.com). Per altre informazioni, vedere [Attività di configurazione della rete virtuale](../documentation/services/virtual-machines/).
2.	Creare il set di servizi cloud per la tua distribuzione al fine di mostrare il tuo design per i set di disponibilità e il bilanciamento del carico. Nel portale, fare clic su**Nuovo > Calcolo > Servizio Cloud > Creazione personalizzata**per ogni servizio cloud.
3.	Per creare ogni nuova macchina virtuale, fare clic su**Nuovo > Calcolo > Macchina virtuale > Dalla raccolta**. Scegliere il servizio cloud corretto e la rete virtuale per la VM. Se il servizio cloud è stato già associato a una rete virtuale, il relativo nome sarà già selezionato.

![Selezione di un servizio cloud per una macchina virtuale](./media/howto-connect-vm-cloud-service/VMConfig1.png)

## Connettere le macchine virtuali in un servizio cloud autonomo

Per connettere le macchine virtuali in un servizio cloud autonomo:

1.	Creare il servizio cloud nel[Portale di Azure](http://manage.windowsazure.com). Fare clic su **Nuovo > Calcolo > Servizio cloud > Creazione personalizzata**. In alternativa, è possibile creare il servizio cloud per la distribuzione quando si crea la prima macchina virtuale.
2.	Quando si creano le macchine virtuali, specificare il nome del servizio cloud creato nel passaggio precedente.![Aggiungere una macchina virtuale a un servizio cloud esistente](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##Risorse
[Bilanciamento del carico delle macchine virtuali](../articles/load-balance-virtual-machines.md)

[Gestione della disponibilità delle macchine virtuali](../articles/manage-availability-virtual-machines.md)

[Attività di configurazione della rete virtuale](../documentation/services/virtual-machines/)

Dopo avere creato una macchina virtuale, è consigliabile aggiungere un disco dati, in modo che i servizi e i carichi di lavoro possano usarlo per archiviare i dati. Vedere uno degli articoli seguenti:

[Come collegare un disco dati a una macchina virtuale Linux](../articles/virtual-machines/virtual-machines-linux-how-to-attach-disk.md)

[Come collegare un disco dati a una macchina virtuale Windows](../articles/virtual-machines/storage-windows-attach-disk.md)

<!---HONumber=Oct15_HO3-->