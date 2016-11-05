
---
title: Note sulla versione di Azure SDK per .NET 2.8
description: Note sulla versione di Azure SDK per .NET 2.8
services: app-service\web
documentationcenter: .net
author: Juliako
manager: erikre
editor: ''

ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/17/2016
ms.author: juliako

---
# <a name="azure-sdk-for-.net-2.8,-2.8.1-and-2.8.2"></a>Azure SDK per .NET 2.8, 2.8.1 e 2.8.2
## <a name="overview"></a>Panoramica
Questo articolo include le note sulla versione, con la descrizione di problemi noti e modifiche di rilievo, per Azure SDK per le versioni 2.8, 2.8.1 e 2.8.2 di .NET. 

Per un elenco completo delle nuove funzionalità e degli aggiornamenti di questa versione, vedere l’annuncio [Azure SDK 2.8 per Visual Studio 2013 e Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) . 

## <a name="azure-sdk-for-.net-2.8"></a>Azure SDK per .NET 2.8
### <a name="download-azure-sdk-for-.net-2.8"></a>Scaricare Azure SDK per .NET 2.8
[Azure SDK per .NET 2.8 per Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=699285) 

[Azure SDK per .NET 2.8 per Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)

### <a name=".net-4.5.2-support"></a>Supporto .NET 4.5.2
#### <a name="known-issues"></a>Problemi noti
Azure .NET SDK 2.8 consente di creare pacchetti del servizio cloud .NET 4.5.2. Tuttavia il framework di .NET 4.5.2 .NET non verrà installato sulle immagini predefinite del sistema operativo Guest fino alla versione del sistema operativo Guest di gennaio 2016. Prima di tale data, il framework .NET 4.5.2, sarà disponibile tramite una versione di rilascio del sistema operativo Guest separata – novembre 2015-02. Vedere la pagina [Versioni del sistema operativo guest di Azure e matrice di compatibilità SDK](../cloud-services/cloud-services-guestos-update-matrix.md) per sapere quando l’immagine verrà rilasciata.  Dopo il rilascio dell'immagine novembre 2015-02 è possibile scegliere di utilizzare tale immagine aggiornando il file di configurazione del servizio cloud (.cscfg). Nel file cscfg impostare l'attributo osVersion dell'elemento ServiceConfiguration sulla stringa "WA-GUEST-OS-4.26_201511-02". Se si sceglie di optare per l’utilizzo di questa immagine non si riceveranno gli aggiornamenti automatici per il sistema operativo Guest. Per ottenere gli aggiornamenti automatici la osVersion deve essere impostata su "*" e .NET 4.5.2 sarà disponibile solo tramite aggiornamenti automatici a gennaio 2016.

### <a name="azure-data-factory"></a>Data factory di Azure
#### <a name="known-issues"></a>Problemi noti
Durante una creazione del progetto **modello Data Factory** che include dati di esempio, lo script di Azure PowerShell potrebbe non avere esito positivo se la versione di Azure PowerShell installata sul computer è successiva alla versione 0.9.8.

Per creare questo tipo di progetto con successo, è necessario installare [azure power shell versione 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).

### <a name="azure-resource-manager-tools"></a>Strumenti di Gestione risorse di Azure
#### <a name="breaking-changes"></a>Modifiche di rilievo
Lo script di PowerShell fornito dal progetto del gruppo di risorse di Azure è stato aggiornato in questa versione per lavorare con i nuovi cmdlet di Azure PowerShell versione 1.0.  Questo nuovo script non funzionerà con Visual Studio quando si utilizza una versione di SDK precedente a quella 2.8.  

Gli script da progetti creati in versioni precedenti di SDK non verranno eseguiti da Visual Studio quando si utilizza SDK 2.8.  Tutti gli script continueranno a funzionare al fuori di Visual Studio con la versione appropriata dei cmdlet per Azure PowerShell.  

L’SDK 2.8 richiede la versione 1.0 dei cmdlet Azure PowerShell.  Tutte le altre versioni di SDK richiedono la versione 0.9.8 dei cmdlet Azure PowerShell.  Per altre informazioni, vedere [questo blog](http://go.microsoft.com/fwlink/?LinkID=623011) .

### <a name="web-tools-extensions"></a>Estensioni degli strumenti Web
#### <a name="known-issues"></a>Problemi noti
Verranno risolti i seguenti problemi noti nella seguente versione.

* Il cloud del Servizio app e i movimenti di Esplora Server per gli ambienti di non produzione (ad esempio Azure Cina o i clienti di Azure Stack) non funzionano. Per i clienti interessati in queste aree, il download del profilo di pubblicazione dal portale di Azure consentirà la possibilità di pubblicare. Una versione futura sarà in grado di ripristinare i movimenti, ad esempio "Collegare il debugger" e "Visualizzare i log di flusso" per Azure Cina e i clienti di Stack. 
* I clienti possono riscontrare errori durante la creazione del servizio app se l’istanza di App Insights a cui stanno distribuendo è in un’area diversa dagli Stati Uniti orientali. In questi scenari, la creazione di un servizio app nel portale e il download del profilo di pubblicazione abiliteranno scenari di pubblicazione. 

### <a name="azure-hdinsight-tools"></a>Strumenti di Azure HDInsight
#### <a name="new-updates"></a>Nuovi aggiornamenti
* È possibile eseguire la query Hive nel cluster tramite HiveServer2 con nessun overhead e visualizzare i log del processo in tempo reale.
* Utilizzando la nuova visualizzazione dell’esecuzione delle attività di Hive è possibile esaminare approfonditamente, trovare ulteriori informazioni, e identificare potenziali problemi.

Per altre informazioni, vedere [Azure SDK 2.8 per Visual Studio 2013 e Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-.net-2.8.1"></a>Azure SDK per .NET 2.8.1
### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Problemi noti per Visual Studio 2013 e Visual Studio 2015
1. Il progetto web attivato che pubblica negli slot non mostrerà alcun errore e non imposterà una pianificazione, ma determinerà il processo Web in Azure. I clienti che hanno bisogno di un processo pianificato possono quindi utilizzare il portale di Azure per configurare la pianificazione per il processo Web. 
2. I clienti di Python potrebbero riscontrare problemi di debugger. Il team del servizio si sta occupando della distribuzione di una correzione per questo problema, ma se i clienti lo riscontrano, sono invitati a segnalarlo a Microsoft tramite i forum o un annuncio sul blog o lasciando commenti nella sezione note. 
3. I clienti che si trovano in determinate aree, ad esempio l'India meridionale, riscontreranno errori durante il provisioning del servizio app Questo è coerente con il portale e i clienti che hanno avuto questo problema possono utilizzare il portale di Azure per richiedere l'accesso alla pubblicazione in queste aree geografiche. Una volta richiesto l'accesso a queste aree tramite il provisioning del portale di Azure, questo dovrebbe funzionare. 

## <a name="azure-sdk-for-.net-2.8.2"></a>Azure SDK per .NET 2.8.2
Dopo l'installazione degli strumenti della versione 2.8.2, i clienti potrebbero riscontrare il problema seguente.         

* Se si usa Windows 10 e non è installato Internet Explorer, è possibile che venga visualizzato l'errore "Impossibile trovare Internet Explorer".
  Per risolvere il problema, installare Internet Explorer tramite la finestra di dialogo Aggiungi/Rimuovi componenti di Windows.

Se si osserva questo problema, usare la funzionalità Invia smile per creare un report.

Per altre informazioni, vedere [questo post](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) .

## <a name="other-updates"></a>Altri aggiornamenti
Per altri aggiornamenti, vedere [post di annuncio di Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="also-see"></a>Vedere anche
[Post di annuncio di Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Informazioni sul Supporto e il Ritiro di Azure SDK per .NET e API](https://msdn.microsoft.com/library/azure/dn479282.aspx)

<!--HONumber=Oct16_HO2-->


