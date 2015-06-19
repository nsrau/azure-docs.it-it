<properties 
	pageTitle="Configurare una rete virtuale usando un file di configurazione di rete" 
	description="Istruzioni per esportare e importare un file di configurazione di rete nel portale di gestione di Azure per creare o modificare reti virtuali. " 
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
	ms.date="02/18/2015"
	ms.author="cherylmc"/>

#Configurare una rete virtuale usando un file di configurazione di rete

## Panoramica

Per configurare la rete virtuale, è possibile usare la procedura guidata del portale di gestione oppure è possibile creare e importare un file di configurazione di rete. Azure usa il file di configurazione di rete per definire le impostazioni della rete virtuale. 

È preferibile usare la procedura guidata del portale di gestione per creare inizialmente la configurazione di rete virtuale e quindi apportare le modifiche successive direttamente nel file di configurazione. Ad esempio, se si configurano più reti virtuali per sottoscrizioni separate, è possibile creare un file di configurazione di rete usando la procedura guidata del portale di gestione. È quindi possibile esportare il file da usare come modello, modificarlo in modo da specificare impostazioni diverse e quindi importare di nuovo il file nel portale di gestione. Può trattarsi di un metodo efficiente per creare più reti virtuali quando si hanno più sottoscrizioni. 

In alternativa, se si vogliono apportare modifiche alle impostazioni di configurazione di rete prima di distribuire servizi cloud o macchine virtuali nella rete, si può esportare il file, modificarlo e importarlo nuovamente in Azure. È anche possibile usare un file di configurazione di rete per eseguire il backup delle impostazioni di configurazione di rete se si vuole creare nuovamente la rete virtuale.

##Creazione e modifica di un file di configurazione di rete 
Il modo più semplice per creare un file di configurazione di rete consiste nell'esportare le impostazioni di rete da una configurazione di rete virtuale esistente, quindi modificare il file in modo da contenere le impostazioni che si vogliono configurare per le reti virtuali. È anche possibile ottenere un file di esempio e modificarlo.

Per modificare il file di configurazione di rete, è sufficiente aprire il file, apportare le modifiche appropriate e salvarlo. È possibile usare qualsiasi editor  *xml* per apportare modifiche al file di configurazione di rete. 

È consigliabile attenersi alle istruzioni per le impostazioni dello schema del file di configurazione. Quando si crea il file di configurazione di rete, le impostazioni nel file sovrascriveranno le impostazioni attualmente configurate per la sottoscrizione in Azure. Se si apportano modifiche ai valori nel file che non sono compatibili con le linee guida delle impostazioni, la rete virtuale potrebbe non essere configurata nel modo desiderato e, in alcuni casi, Azure non consente di importare il file. Per informazioni sulle impostazioni specifiche contenute in un file di configurazione di rete, vedere [Schema di configurazione di rete virtuale Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

Azure considera una subnet con un elemento distribuito come "in uso". Quando una subnet è in uso, non può essere modificata. Prima di apportare modifiche, spostare qualsiasi elemento distribuito alla subnet in una subnet diversa che non è sottoposta a modifiche. Vedere [Spostare una macchina virtuale o un'istanza del ruolo in un'altra subnet](https://msdn.microsoft.com/library/azure/dn643636.aspx).



## Esportare e importare impostazioni di rete virtuale nel portale di gestione 
È possibile importare ed esportare le impostazioni di configurazione di rete contenute nel file di configurazione di rete usando PowerShell o il portale di gestione. Le istruzioni riportate di seguito consentono di eseguire l'esportazione e l'importazione tramite il portale di gestione. 

### Per esportare le impostazioni di rete
Quando si esporta, tutte le impostazioni per le reti virtuali nella sottoscrizione verranno scritte in un file XML. 

1. Accedere al **portale di gestione**.
2. Nel portale di gestione, nella parte inferiore della pagina **Reti** fare clic su **Esporta**. 
3. Nella finestra **Esporta configurazione di rete**, verificare di aver selezionato la sottoscrizione per la quale si vogliono esportare le impostazioni di rete. Quindi, fare clic sul segno di spunta in basso a destra. 
4. Quando richiesto, salvare il file *NetworkConfig.xml* nel percorso desiderato.
### Per importare le impostazioni di rete


1. Nel **portale di gestione**, nel riquadro di spostamento in basso a sinistra, fare clic su **Nuovo**.
2. Fare clic su **Servizi di rete** -> **Rete virtuale** -> **Importa configurazione**.
3. Nella pagina **Importa file di configurazione della rete** passare al file di configurazione della rete, quindi fare clic su **Avanti**.
4. Nella pagina **Compilazione della rete** verranno visualizzate le informazioni sulla schermata che mostra quali sezioni della configurazione di rete verranno modificate o create. Se le modifiche sembrano corrette, fare clic sul segno di spunta per continuare con l'aggiornamento o la creazione della rete virtuale. 


## Risorse aggiuntive
Per altre informazioni sulla rete virtuale, vedere:

- [Panoramica di Rete virtuale](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)
- [Schema NetworkConfiguration - Impostazioni facoltative per i servizi cloud](https://msdn.microsoft.com/library/azure/jj156091.aspx)
- [Informazioni sulle impostazioni della rete virtuale nel portale di gestione](https://msdn.microsoft.com/library/azure/jj156074.aspx)
- [Domande frequenti sulla rete virtuale](https://msdn.microsoft.com/library/azure/dn133803.aspx)
- [Attività di configurazione della rete virtuale](https://msdn.microsoft.com/library/azure/jj156206.aspx)
- [Configurare una VPN multisito](https://msdn.microsoft.com/library/azure/dn690124.aspx)
- [Configurare una connessione tra reti virtuali](https://msdn.microsoft.com/library/azure/dn690122.aspx)




<!--HONumber=47-->
 