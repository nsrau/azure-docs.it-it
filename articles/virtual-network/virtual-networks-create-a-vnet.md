<properties 
 pageTitle="Creare una rete virtuale" 
 description="Seguire i passaggi per creare con facilità una rete virtuale di base." 
 services="virtual-network" 
 documentationCenter="" 
 authors="cherylmc" 
 manager="adinah" 
 editor="tysonn"/>

<tags
 ms.service="virtual-network"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="infrastructure-services" 
 ms.date="02/20/2015"
 ms.author="cherylmc"/>

# Creare una rete virtuale 



Quando si crea una rete virtuale, i servizi e le macchine virtuali all'interno della rete virtuale possono comunicare tra loro in modo sicuro senza dover passare attraverso Internet. La creazione di una rete virtuale solo cloud dedicata è un processo relativamente semplice e rapido. Poiché una rete virtuale solo cloud non è destinata alla connettività cross-premise, non è necessario acquisire e configurare un dispositivo VPN o i certificati di autenticazione. 

Dopo aver creato la rete virtuale, è possibile aggiungere nuove macchine virtuali e istanze PaaS. Si noti che se si utilizza il portale di gestione per creare le macchine virtuali, è opportuno selezionare **Da raccolta** in modo da poter specificare la rete virtuale. Questo passaggio è importante perché non è possibile tornare indietro e inserire una macchina virtuale in una rete virtuale dopo aver creato la macchina virtuale.

[Azure Note] **Utilizzare questa procedura per creare una rete virtuale solo cloud dedicata.** A causa della maggiore complessità coinvolta nella creazione di una configurazione cross-premise, non utilizzare questa procedura per creare una rete virtuale che in un secondo momento verrà connessa alla rete locale. Se si desidera creare una connessione protetta cross-premise tra Azure e la rete locale, vedere [Informazioni sulla connettività protetta cross-premise](https://msdn.microsoft.com/library/azure/dn133798.aspx).

## <a name="CreateyourVNet">Creazione della rete virtuale</a>

1. Accedere al **portale di gestione**.
2. Nell'angolo inferiore sinistro della schermata, fare clic su **Nuovo**. Nel riquadro di spostamento, fare clic su **Servizi di rete**, quindi fare clic su **Rete virtuale**. Fare clic su **Creazione personalizzata** per avviare la configurazione guidata.
3. Nella pagina **Dettagli della rete virtuale** immettere le informazioni seguenti e quindi fare clic sulla freccia Avanti in basso a destra. Per ulteriori informazioni sulle impostazioni della pagina dei dettagli, vedere la sezione **Dettagli della rete virtuale** in [Informazioni sulle impostazioni della rete virtuale nel portale di gestione](https://msdn.microsoft.com/library/azure/jj156074.aspx).
	- **Nome:** nome della rete virtuale. Questo nome viene utilizzato quando si distribuiscono le macchine virtuali e i servizi e pertanto è consigliabile non specificare un nome troppo complicato.

	- **Posizione:** dall'elenco a discesa selezionare l'area desiderata. La rete virtuale viene creata nel data center di Azure presente nell'area specificata.



4. Nella pagina **Server DNS e connettività VPN** non apportare modifiche. Passare semplicemente alla pagina successiva facendo clic sulla freccia. Per impostazione predefinita, Azure fornisce la risoluzione dei nomi di base per la rete virtuale. È possibile che i requisiti della risoluzione dei nomi in uso siano più complessi di quelli che possono essere gestiti dalla risoluzione dei nomi di Azure. In tal caso, è possibile aggiungere in un secondo momento una macchina virtuale che esegue DNS per la rete virtuale. Per ulteriori informazioni sulla risoluzione dei nomi di Azure e DNS, vedere [Risoluzione dei nomi](https://msdn.microsoft.com/library/azure/jj156088.aspx). 
5. Nella pagina **Spazi di indirizzi della rete virtuale** è possibile immettere lo spazio di indirizzi che si desidera utilizzare per la rete virtuale. A meno che non si richieda un determinato intervallo di indirizzi IP interni per le macchine virtuali o si desideri creare una subnet specifica per le macchine virtuali che riceveranno un DIP statico, non è necessario apportare modifiche in questa pagina. Se si desidera creare più subnet, è possibile eseguire l'operazione in questa pagina facendo clic su **Aggiungi subnet**. Per ulteriori informazioni sulle impostazioni della pagina dei dettagli, vedere la sezione **Dettagli della rete virtuale** in [Informazioni sulle impostazioni della rete virtuale nel portale di gestione](https://msdn.microsoft.com/library/azure/jj156074.aspx).

	- Per ulteriori informazioni sulle impostazioni della pagina dei dettagli, vedere la sezione **Dettagli della rete virtuale** in [Informazioni sulle impostazioni della rete virtuale nel portale di gestione](https://msdn.microsoft.com/library/azure/jj156074.aspx).
	- Poiché non è necessario connettere la rete privata virtuale alla rete locale tramite una configurazione VPN cross-premise, non è necessario coordinare le impostazioni con gli intervalli di indirizzi IP di rete esistenti in locale. Se si intende creare una configurazione cross-premise in un secondo momento, è necessario coordinare gli spazi di indirizzi ora con gli intervalli già presenti nel sito locale per evitare problemi di routing. La modifica degli intervalli in un secondo momento può essere complicata e spesso comporta la ripetizione della distribuzione.


6. Fare clic sul segno di spunta in basso a destra nella pagina Spazi di indirizzi della rete virtuale per iniziare a creare la rete virtuale. Al termine della creazione della rete virtuale, in Status verrà visualizzata la dicitura Created nella pagina relativa alle reti del portale di gestione.
7. Dopo averla creata, è possibile distribuire nella rete virtuale. Ad esempio, se si desidera distribuire una macchina virtuale nella rete virtuale, vedere [Aggiungere una macchina virtuale a una rete virtuale](http://www.windowsazure.com/manage/services/networking/add-a-vm-to-a-virtual-network/). Assicurarsi di selezionare **Da raccolta** quando si crea una nuova macchina virtuale per avere la possibilità di selezionare la rete virtuale. Si noti che se sono già presenti macchine virtuali e istanze PaaS distribuite, non possono semplicemente essere spostate nella nuova rete virtuale. Ciò accade perché le impostazioni di configurazione della rete vengono stabilite durante la distribuzione. È necessario ridistribuirle nuovamente nella nuova rete virtuale.



## Passaggi successivi
- [Panoramica di rete virtuale](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

 
- [Aggiungere una macchina virtuale a una rete virtuale](http://www.windowsazure.com/manage/services/networking/add-a-vm-to-a-virtual-network/)

- [Domande frequenti sulla rete virtuale](http://msdn.microsoft.com/library/windowsazure/dn133803.aspx)

- [Configurazione di una rete virtuale utilizzando file di configurazione di rete](http://azure.microsoft.com/documentation/articles/virtual-networks-using-network-configuration-file/)

- [Risoluzione dei nomi](http://go.microsoft.com/fwlink/?LinkId=248097)
 



<!--HONumber=47-->
 