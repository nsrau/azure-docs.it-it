<properties authors="kathydav" editor="tysonn" manager="timlt" /> 


#Come connettere macchine virtuali in un servizio cloud

Quando si crea una macchina virtuale, viene creato automaticamente un servizio cloud per contenerla. È possibile creare più macchine virtuali con lo stesso servizio cloud, in modo che le VM possano comunicare tra loro. 

> [AZURE.NOTE] Se le VM si trovano nello stesso servizio cloud, sarà anche possibile bilanciarne il carico e gestirne la disponibilità, eseguendo alcuni passaggi aggiuntivi. Per informazioni dettagliate, vedere [Bilanciamento del carico delle macchine virtuali](../../articles/load-balance-virtual-machines/) e [Gestione della disponibilità delle macchine virtuali](../../articles/manage-availability-virtual-machines/). 

Sarà prima di tutto necessario creare una macchina virtuale con un nuovo servizio cloud. Creare quindi altre macchine virtuali nello stesso servizio cloud, in modo da 'connetterle'. 

1. Creare la prima macchina virtuale usando i passaggi descritti in [Come creare una macchina virtuale personalizzata](../../articles/virtual-machines-create-custom/).

2. Eseguire lo stesso processo di base per creare le altre macchine virtuali. Sarà però necessario aggiungerle al servizio cloud, invece di creare un servizio cloud. Se è stato ad esempio creato un servizio cloud denominato *EndpointTest*, scegliere tale servizio. Il grafico seguente illustra la procedura:

	![Aggiungere una macchina virtuale a un servizio cloud esistente](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

14. Completare i campi rimanenti in questa pagina e nella pagina successiva, quindi fare clic sul segno di spunta per creare la macchina virtuale connessa.

#Risorse

Dopo avere creato una macchina virtuale, è consigliabile aggiungere un disco dati, in modo che i servizi e i carichi di lavoro possano usarlo per archiviare i dati. Vedere uno degli articoli seguenti:

[Come collegare un disco dati a una macchina virtuale Linux](http://azure.microsoft.com/it-it/documentation/articles/virtual-machines-linux-how-to-attach-disk/)

[Come collegare un disco dati a una macchina virtuale Windows](http://azure.microsoft.com/it-it/documentation/articles/storage-windows-attach-disk/)



<!--HONumber=42-->
