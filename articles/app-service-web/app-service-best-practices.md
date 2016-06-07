<properties
	pageTitle="Procedure consigliate per il servizio app di Azure"
	description="Informazioni sulle procedure consigliate e la risoluzione dei problemi per il servizio app di Azure."
	services="app-service"
	documentationCenter=""
	authors="dariagrigoriu"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/19/2016"
	ms.author="dariagrigoriu"/>
    
# Procedure consigliate per il servizio app di Azure

Questo articolo riepiloga le procedure consigliate per l'uso del [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

## <a name="colocation"></a>Condivisione dell'area
Quando le risorse di Azure che costituiscono una soluzione, ad esempio un'app Web e un database, si trovano in aree diverse, gli effetti possono includere quanto segue:

*  Aumento della latenza nella comunicazione tra le risorse
*  Addebiti per il trasferimento dei dati in uscita tra aree come indicato nella [pagina dei prezzi di Azure](https://azure.microsoft.com/pricing/details/data-transfers).

La condivisione dell'area è ideale per le risorse di Azure che compongono una soluzione per la memorizzazione di dati o contenuti, come un'app Web e un database o un account di archiviazione. Quando si creano risorse, verificare che queste si trovino nella stessa area di Azure, a meno che non esistano motivi aziendali o di design specifici che lo impediscono. È possibile trasferire un'app del servizio app nella stessa area del database con la [funzionalità di clonazione del servizio app](app-service-web-app-cloning-portal.md) attualmente disponibile nel piano di servizio app Premium.

## <a name="memoryresources"></a>Quando le app usano più memoria del previsto
Se si nota che un'app usa più memoria del previsto, in base a quanto riportato nelle funzioni di monitoraggio o nelle indicazioni per il servizio, prendere in considerazione l'uso della [funzionalità di correzione automatica del servizio app](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Una delle opzioni della funzionalità di correzione automatica consente di eseguire azioni personalizzate in base a una soglia di memoria. Le azioni includono notifiche tramite posta elettronica, analisi con dump di memoria e attenuazione locale tramite riciclo del processo di lavoro. La correzione automatica può essere configurata usando web.config e un'interfaccia utente semplice, come descritto in questo post di blog per l'[estensione del sito di supporto del servizio app](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).

## <a name="CPUresources"></a>Quando le app usano più CPU del previsto
Quando si nota che un'app usa più CPU del previsto o presenta ripetuti picchi di utilizzo della CPU, in base a quanto riportato nelle funzioni di monitoraggio o nelle indicazioni per il servizio, può essere opportuno aumentare le prestazioni o il numero di istanze del piano di servizio app. Se si tratta di un'applicazione con stato, l'aumento delle prestazioni è l'unica opzione disponibile, mentre se l'applicazione è senza stato, l'aumento del numero di istanze offre maggiore flessibilità e un potenziale di scalabilità più elevato.

Per altre informazioni sulle differenze tra le applicazioni "con stato" e quelle "senza stato", guardare il video relativo alla [pianificazione di un'applicazione multilivello end-to-end scalabile nel servizio app Web di Microsoft Azure](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Per altre informazioni sulla scalabilità del servizio app e sulle opzioni di scalabilità automatica, vedere [Scalare un'app Web nel servizio app di Azure](web-sites-scale.md).

## <a name="socketresources"></a>Quando si esauriscono le risorse socket
Una causa comune dell'esaurimento delle connessioni TCP in uscita è l'impiego di librerie client non implementate per il riutilizzo delle connessioni TCP o il mancato uso di un protocollo di livello superiore, ad esempio keep-alive HTTP. Vedere la documentazione di ogni libreria cui fanno riferimento le app nel piano di servizio app per verificare che le librerie siano configurate o accessibili nel proprio codice per un efficiente riutilizzo delle connessioni in uscita. Seguire anche le indicazioni della documentazione delle librerie per le corrette operazioni di creazione, rilascio o pulizia per evitare la perdita di connessioni. Mentre sono in corso le analisi delle librerie client è possibile attenuare l'impatto sulle prestazioni aumentando il numero di istanze.

<!---HONumber=AcomDC_0525_2016-->